+++
date = 2026-07-31T08:30:00+00:00
title = "Announcing Kassette: a Kotlin Multiplatform NES Emulator"
description = "...and that's how it begins..."
slug = "announcing-kassette-a-kotlin-multiplatform"
authors = ['Orfeo']
tags = ["ai", "kmp", "nes", "emulation", "kotlin"]
categories = []
externalLink = ''
series = ["kassette"]
+++

## The passion

I’ve always had a passion for technology, and like many children of my generation, it probably started with video games.

I still remember unboxing our NES on Christmas morning and watching my dad connect it to the big TV in the living room.
A few minutes later, the whole family was playing *Super Mario Bros.* We kept going for about four hours straight.
<!-- @formatter:off -->
{{< figure src="/images/posts/announcing-kassette-a-kotlin-multiplatform/image-1.png" alt="" caption="Super Mario Bros running on Kassette">}}
<!-- @formatter:on-->
The following Christmas, I received my second game: *The Battle of Olympus*. It was an early action-platforming
role-playing game set in Ancient Greece. You had to wander around, speak to characters, collect hints and somehow work
out where to go next.

There was only one small problem: the game was entirely in English. Neither my dad nor I spoke English.

<!-- @formatter:off -->
{{< figure src="/images/posts/announcing-kassette-a-kotlin-multiplatform/image-2.png" alt="" caption="Battle Of Olympus running on Kassette">}}
<!-- @formatter:on-->

So our strategy was mostly based on guessing and random exploration. That night I eventually gave up and went to bed. My
dad did not. The following morning, my mum found him still sitting in front of the television.

“What are you still doing there?” she asked. “I have to defeat Gaea,” he replied, completely seriously, “but I’ve been
trying all night and I can’t understand how to wake her up!”

That story has stayed with me ever since. It probably says a lot about where I inherited my stubbornness from.

## The MVP

More recently, I bought a broken NES on eBay and managed to repair it. I also brought all my old cartridges back from
Italy, which immediately made me want to play them again.

I had wanted to write my own emulator for years. Over time, I studied the NES hardware in quite a bit of detail: how the
CPU works, how memory is organised, how the system bus connects everything together, how cartridges are built, how
graphics are rendered, and how all those tiny components cooperate to make a little plumber jump. The individual
concepts are not rocket science. The difficult part is that there is a ton of code to write!

I already work full-time as a software engineer. Finding enough spare time for another programming project is not easy.
And even when I do have some free time, spending the rest of the day in front of another monitor is not always the most
appealing option.

At the same time, I wanted a practical playground where I could experiment with AI-assisted software development. Not
another tiny demo or disposable proof of concept, but something complex enough to expose the strengths, weaknesses and
occasional creative interpretations of coding agents.

An emulator seemed perfect. I wrote a detailed MVP document describing the system, the architecture and the initial
functionality I wanted. Then I handed it to Codex. A few minutes later, I had the first version of the emulator up and
running.

It was not quite playable, but it worked. After a few more prompts, some debugging and a little human supervision, games
started running.

## Why this blog?

I’ve wanted to start a blog for a while, mainly as a place to document random software-engineering topics, experiments
and lessons learned.

This project gives me the perfect excuse.

Instead of writing one enormous post about the entire emulator, I’m planning to publish a series of smaller articles.
Each one will focus on a particular challenge or decision I encounter while developing it.

Some of the topics will include:
- Software architecture and how the emulator is structured;
- Kotlin Multiplatform and platform-specific integrations;
- Product decisions, especially deciding what **not** to build;
- Learning to code with LLMs: what works, what doesn’t, and where things get interesting;

## What comes next?

This is only the introduction.

In the next posts, I’ll start digging into the technical details, the architectural decisions and the many small
problems involved in convincing modern hardware to behave like a console released in the 1980s.

[Here](/posts/a-plumber-two-prompts-and-3000-lines/) you can find the next post of the saga.

In the meantime, you can take a look at the source code on GitHub or try the WebAssembly version directly in your
browser.
- **Source code:** [GitHub](https://github.com/Otacon/Kassette)
- **Play online:** [WebAssembly emulator](https://otacon.github.io/Kassette/)

Just keep in mind that the emulator is still a work in progress. It may not work perfectly out of the box, and bugs are
not only possible, but they are currently part of the experience.

Let the emulation begin!
