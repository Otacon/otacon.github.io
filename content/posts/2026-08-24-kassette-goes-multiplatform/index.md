+++
date = 2026-08-24T08:30:00+00:00
title = "Kassette goes Multiplatform"
description = "Taking a JVM NES emulator to desktop and the browser with Kotlin Multiplatform"
slug = "kassette-goes-multiplatform"
authors = ['Orfeo']
tags = ["ai", "kmp", "nes", "emulation", "kotlin"]
categories = []
externalLink = ''
series = ["kassette"]
+++

From the beginning, I wanted Kassette to be multiplatform. An emulator seemed like a suitably unreasonable way to test
that idea.

So far, though, Kassette has been JVM-only. And an emulator is not exactly the easiest application to make
multiplatform: it needs sound, GPU rendering, UI, keyboard input, controller support, and all sorts of platform-specific
integrations. So, could Kassette actually run across platforms?

I decided to find out by targeting desktop and WebAssembly. WASM was particularly interesting because, if it worked,
Kassette could run directly in the browser. No installation, no setup, no executable downloaded from some random person
on the internet. Just open a page and play.

But before worrying about audio, controllers, CRT shaders, or performance, I had to answer a much simpler question:
**can I even render a single NES frame?**

One existential crisis at a time. Let’s break this down into smaller problems!

# NES Core

To encourage myself, I started with the easiest piece of the puzzle: the NES core.

Luckily, this was also the most boring part from a multiplatform perspective. The emulator core has no dependency on the
operating system or any platform-specific API. It is just logic and arithmetic built on top of simple data structures
and types such as `Byte`, `Int`, `ByteArray`, and `IntArray`.

In other words, there was almost nothing to port.

I created a Kotlin Multiplatform module, moved the existing core into `commonMain`, and that was basically it. No
abstractions, no platform-specific implementations, no drama.

A surprisingly reassuring copy-paste exercise.

# SKIA rendering

The renderer was the first real deal breaker.

Kassette originally used OpenGL, which was perfectly fine for the JVM version, but not something I could simply drag
into a Kotlin Multiplatform project and expect to work everywhere. If I could not find a way to render the NES
framebuffer from common code, the whole multiplatform experiment was basically over.

The obvious candidate was **[Skia](https://skia.org/)**. Skia is a 2D graphics library used by projects such as Chromium
and Android, with both CPU and GPU-backed rendering. More importantly for me, Compose Multiplatform uses Skiko, which in
turn uses Skia. That meant I could potentially use the same graphics stack for both the emulator output and the UI
around it.

That was particularly appealing because Kassette does not only need to draw a game frame. It also needs menus,
configuration screens, controls, overlays, and all the usual application UI. Having both live in the same multiplatform
ecosystem sounded almost suspiciously convenient.

At this stage I only cared about two things:

- rendering the NES framebuffer pixel by pixel;
- applying shaders for post-processing effects, such as a CRT effect;

The first one was straightforward to get working — although, as I would later discover, getting it to run fast enough
was a completely different story.

The second turned into a surprisingly long argument with Codex.

For quite a while, Codex was adamant that shaders were either unavailable or not a sensible option with Skia. Eventually
I pointed it directly at the Skia documentation for runtime effects, after which it produced a “working” solution.

Then I realised it had implemented the renderer twice: once for desktop and once for WASM. The implementations were
essentially identical, yet it stubbornly refused to move them into common code.

After another round of persuasion and refactoring, the renderer ended up where I wanted it: a single implementation in
common code, capable of rendering the NES framebuffer and applying GPU shaders on both targets.

At that point, the multiplatform idea stopped feeling theoretical. Kassette could actually draw a frame.

# Keyboard

Keyboard input was, thankfully, much less dramatic.

The JVM version of Kassette used AWT to listen for key events, so this part mainly required replacing the old
implementation with Compose’s keyboard APIs. Compose Multiplatform already exposed everything I needed, which meant
there was no reason to introduce a platform-specific abstraction just for keyboard input.

As a result, the entire implementation now lives in common code and works across both desktop and WASM.

After the renderer, this felt almost suspiciously easy.

# Audio and Controllers

Audio and controller support were a different story. Unlike keyboard input, there was no single multiplatform API I
could rely on, so both needed a common abstraction backed by platform-specific implementations.

For audio, the frontend exposes a shared interface, with each target providing its own implementation: OpenAL on desktop
and the Web Audio API in the browser.

Controllers follow the same pattern. Desktop and browsers expose gamepads through different APIs, so some
platform-specific code is unavoidable.

This is also the least polished part of the multiplatform port. I still haven’t managed to get controller support
working consistently across all platforms, so there is definitely more work to do here.

Still, this felt like an acceptable compromise: the emulator logic remains shared, while the relatively small pieces
that actually talk to the operating system or browser stay platform-specific.

# Performance

On desktop, both the JVM version and the WASM build performed well. The first real problem appeared when I tried the
WASM version on my phone: Kassette was running at roughly 5 FPS.

That made performance the next major problem to investigate. Phones and tablets are the primary computing devices for a
huge number of people, so a browser version of Kassette that barely worked on mobile would have missed the point
entirely.

## Production optimisations

One important detail was that I was testing development builds. Kotlin/WASM production builds go through additional
optimisation passes, including Binaryen optimisations.

That gave me a useful target: if I could get the development build close enough to 60 FPS, the production build should
have enough headroom to run smoothly on a wide range of mobile devices.

## Finding the bottleneck

I started profiling the application and isolating different parts of the system: audio, rendering, input handling, and
finally the emulator core itself.

The LLMs helped narrow the problem down to the core, and some of their suggestions improved performance along the way.
But those incremental optimisations were nowhere near enough to close the gap between 5 FPS and something actually
playable.

So I started looking more closely at the hot paths: tight loops, repeated work, and especially memory allocations. That
eventually led me to the real problem: **memory churn**.

Kotlin makes it very easy to allocate objects without really thinking about it. A lot of perfectly normal, idiomatic
code creates temporary objects behind the scenes:

```kotlin
// Creates a Pair
val position = x to y

// Creates a new state object
val newState = state.copy(foo = bar)

// Creates a new array and copies the contents
val newBuffer = buffer.copyOf()

// Creates a new List
val visibleSprites = sprites.filter { it.visible }

// Creates a List of Pairs
val combined = xs.zip(ys)
```

None of these is inherently bad. In normal application code, I would happily write most of them and move on with my
life. An emulator is a slightly different environment.

The problem was not one `Pair`, one copied state object, or one temporary list. It was the same small allocations
happening over and over again in code executed while emulating every frame. And then doing all of that **60 times per
second**. An allocation that happens once is irrelevant. An allocation that happens 1,000 times while producing a frame
becomes 60,000 allocations every second.

Suddenly, all those harmless little temporary objects are not so harmless anymore.

For perhaps the first time in my career, **immutability had become my worst enemy**.

# Learnings and Conclusions

After all the experiments, wrong turns, duplicated renderers, shaders, and performance investigations, the most
important result is pretty simple: **it actually works.**

Kassette now runs as a Kotlin Multiplatform application on desktop and in the browser, with the emulator core, UI,
renderer, and even GPU shaders shared across both targets.

What started as a slightly unreasonable experiment turned into a working multiplatform NES emulator. I’m pretty happy
about that.

{{< giphy "61j4pcgdgxKOAnJsif" >}}

## Good practices are contextual

One thing this project reinforced for me is that good practices are not universal.

Immutability, expressive APIs, extra abstractions, and cleaner-looking code are often great defaults — until they end up
in a hot path running thousands of times per frame, 60 times per second. Sometimes the “better” solution is simply the
one that fits the constraints you actually have.

## LLMs are better at execution than direction

This project confirmed something I had already seen before: LLMs are great once the problem is well defined, but much
less reliable when they have to choose the technical direction.

Skia was another example. Codex pushed back on shaders and duplicated the renderer across desktop and WASM until I
provided the architectural direction.

So, not a new lesson — just another confirmation: AI can help you move fast, but you still need to know where you’re
going.

Enjoy!

- **Source code:** [GitHub](https://github.com/Otacon/Kassette)
- **Play online:** [WebAssembly emulator](https://otacon.github.io/Kassette/)
