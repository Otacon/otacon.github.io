+++
date = 2026-08-27T14:25:10+01:00
title = "Doing the wrong thing, correctly"
description = "Mutability, bit packing, and other crimes committed in the name of performance"
slug = "doing-the-wrong-thing-correctly"
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++

In the previous post, Kassette finally became multiplatform. It ran on desktop, it ran in the browser, and technically it even ran on my phone. At roughly 5 FPS.

After profiling the emulator, the problem turned out not to be one spectacularly slow algorithm. It was memory churn: lots of tiny allocations happening in code that runs over and over again while producing every frame.

So the next part of the project became an exercise in making Kotlin allocate as little as possible. Which, occasionally, meant writing code I would normally consider bad practice.

## Mutability over immutability

We are usually encouraged to prefer immutable state. Instead of changing an existing object, create a new one:

```kotlin
state = state.copy(PC = state.PC + 1)
```