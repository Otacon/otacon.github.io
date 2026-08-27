+++
date = 2026-08-27T14:25:10+01:00
title = 'Doing the wrong thing, correctly'
description = 'Mutability, bit packing, and other crimes committed in the name of performance'
slug = 'doing-the-wrong-thing-correctly'
authors = ['Orfeo']
tags = ['ai', 'kmp', 'nes', 'emulation', 'kotlin']
categories = []
externalLink = ''
series = ['kassette']
+++

In the previous post, Kassette finally became multiplatform. It ran on desktop, it ran in the browser, and technically
it even ran on my phone. At roughly 5 FPS.

After profiling the emulator, the problem turned out not to be one spectacularly slow algorithm. It was memory churn:
lots of tiny allocations happening in code that runs over and over again while producing every frame.

So the next part of the project became an exercise in making Kotlin allocate as little as possible. Which, occasionally,
meant writing code I would normally consider bad practice.

## Mutability over immutability

We are usually encouraged to prefer immutable state. Instead of changing an existing object, create a new one:

```kotlin
state = state.copy(PC = state.PC + 1)
```

## Mutability over immutability

We are usually encouraged to prefer immutable state. Instead of changing an existing object, create a new one:

```kotlin
state = state.copy(PC = state.PC + 1)
```

This is clean and predictable, but a NES CPU changes state constantly. Registers, flags, the program counter, the stack
pointer, and other values are updated while executing every instruction.

Creating a new state object for each change would introduce allocations in one of the hottest parts of the emulator.

So Kassette mutates the existing state directly:

```kotlin
state.PC = (state.PC + 1).u16()
state.SP = (state.SP - 1).u8()
state.A = value
```

One state object, continuously mutated. Immutability is still a great default. It just stops being such an attractive
one when the state changes millions of times per second.

## Allocate once, reuse forever

The same principle applies to buffers. The PPU eventually needs to produce a 256×240 image. Allocating a new framebuffer
for every frame would mean creating another large array every 1/60th of a second. Instead, Kassette allocates two of
them once:

```kotlin
protected val outputBuffers = Array(2) { IntArray(NesConstants.ScreenPixelCount) }
protected var currentOutputBuffer = outputBuffers[0]
```

The PPU writes into one buffer while the other can be consumed by the renderer, then swaps them. This pattern ended up
throughout the performance-sensitive parts of the emulator: when memory has a predictable size and lifetime, allocate it
once and keep reusing it. It is less elegant than creating fresh values, but much friendlier to the garbage collector.

## Sometimes an Int is a data structure

Another common preference is to represent concepts with meaningful types.

The CPU status register, for example, contains several independent flags: carry, zero, interrupt, overflow, negative,
and so on. A more explicit representation could look like this:

```kotlin
data class ProcessorStatus(
    val carry: Boolean,
    val zero: Boolean,
    val interrupt: Boolean,
    val overflow: Boolean,
    val negative: Boolean,
)
```

Kassette instead represents all of them with one integer:

```kotlin
object PSFlags {
    const val Carry = 0x01
    const val Zero = 0x02
    const val Interrupt = 0x04
    const val Overflow = 0x40
    const val Negative = 0x80
}
```

Checking and updating state becomes bit manipulation:
```kotlin
state.PS = state.PS or PSFlags.Interrupt 
val zero = (state.PS and PSFlags.Zero) != 0
```

It is less expressive than a richer type, but it avoids extra objects and also happens to closely resemble how the
actual 6502 stores its processor status. In hot code, that trade-off is often worth it.

