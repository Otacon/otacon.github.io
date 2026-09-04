+++
date = 2026-09-07T08:30:00+00:00
title = "LLMs won't make you a Software Engineer"
description = "AI can write code faster than ever, but it cannot hand you the judgment, scars, and responsibility that real engineering requires."
slug = "llms-wont-make-you-a-software-engineer"
authors = ['Orfeo']
tags = ["software engineering"]
categories = []
externalLink = ''
series = []
+++

There's this phrase I keep hearing everywhere now:

> democratising software development

I understand why people like it. It sounds like we are taking something that used to belong to a small priesthood of
engineers and finally handing it to everyone else. As someone who loves building things, I should be happy about that.
And mostly, I am. But the phrase still makes me uncomfortable.

The word democracy comes from the Greek δημοκρατία (dēmokratía)—δῆμος (dêmos, "the people") and κράτος (krátos,
"power"). At its heart, democracy is about handing decision-making power to the people.

But that isn't really what's happening in software. There is no secret power being hoarded that we suddenly need to
share. An accountant doesn't need a vote on a database schema, and a product manager isn't being oppressed just because
they aren't designing a locking strategy. In essence: expertise isn't a monopoly.

What we're really talking about is accessibility. Programming is getting easier to consume. People who used to need a
deep understanding of languages and frameworks can now just describe what they want and let a machine translate that
into working code.

That is genuinely exciting. I have used these tools enough to know they can feel almost magical. They remove friction,
they make experiments cheaper, and they let you stay in the flow for longer.

But making programming easier to get into is not the same thing as making software engineering unnecessary.

## Software engineering was never about typing code

There seems to be this assumption that syntax was the big wall. Engineers knew the secret code words, everyone else had
the ideas, and now that the wall is gone, suddenly we're all software engineers.

I used to believe a softer version of this myself. Early in my career, every new language felt like a mountain. Then, at
some point, the mountain moved. The syntax became the easy part. The hard part became everything around it.

Behind the code, there is a whole universe of stuff: data structures, algorithms, concurrency, databases, distributed
systems, architecture, security, testing, observability, performance, reliability, failure recovery. And the list goes
on. Not to mention the constant trade-offs and judgment calls that come with all of it.

After spending enough time dealing with real production systems, engineers learn when the technically beautiful solution
is actually the wrong one. They learn when a system really needs to scale, when duplicating code is cheaper than
building a fancy abstraction, and when shipping something small today is worth more than building a masterpiece three
months from now.

I have made the wrong call on all of these at some point. Most engineers have. That is where the judgement comes from.
Not from reading the docs, but from living with the consequences.

LLMs can generate code. They can't generate experience.

## Faster code is not necessarily better software

LLMs are absolutely changing the economics of programming: code that used to take hours can now appear in seconds.
Prototypes can be built insanely fast, and any professional should be happy to have tools that remove the boring,
mechanical work. I know I am. I do not miss writing boilerplate by hand just to prove I understand the pattern.

Speed matters, but it shouldn't come at the cost of quality. A smaller MVP can choose to support fewer features, but
whatever it does support should still work reliably. This is something I keep having to remind myself of, because the
temptation to ship "just one more thing" is very real.

Why does this matter? Because getting software to work once is just the start. It also needs to be understandable,
maintainable, and reliable as it grows. Generated code makes adding more software incredibly cheap; understanding what
already exists and carefully reshaping it is still just as hard.

And reliability isn't just an engineering thing. It's a business thing.

Software that breaks all the time might have been cheap to build, but the cost doesn't just vanish. It just moves. It
shows up in support tickets, incidents, lost customers, and a damaged reputation.

## Where is the user in all of this?

Not that long ago, product development was obsessed with delighting users, reducing friction, and making people happy.
Maybe I am romanticising it a bit, but I remember that being the centre of the conversation much more often.

Now, so much of the conversation is about how fast we can ship, how much we can automate, and how many people we can cut
from the process. The user is starting to feel like an afterthought.

Imagine booking a flight with travel insurance, only to realise the insurance starts the week after you get home. You
contact support and get a chatbot that can't grasp the problem. Frustrated, you turn to X hoping to reach an actual
human, only to get another automated reply.

This kind of thing drives me mad because, from the inside, it probably looks efficient. Fewer support agents. Faster
response times. Nice charts in a dashboard somewhere.

Software built with the help of LLMs fails the user, and the response to that failure is ... another LLM! Meanwhile,
internally, the numbers look fantastic.

But the customer just knows the product doesn't work.

There's a word for this pattern: enshittification. It's the slow degradation of products and services as other
incentives start to matter more than the people using them.

{{< youtube T4Upf_B9RLQ >}}

## Accessibility is not expertise

Would you let someone operate on you just because better tools made surgery easier to perform? Obviously, software
engineering isn't surgery. Most software bugs aren't life or death, and I do not want to overdramatise what we do for a
living.

The principle is the same: making a discipline **easier to access doesn't remove the need for expertise**.

Software does sit behind companies, jobs, payments, logistics, and travel. It's woven into huge parts of the economy.
Poor engineering erodes customer trust, spikes operational costs, damages a company's ability to evolve, and eventually
hurts the people whose livelihoods depend on that business succeeding.

So, by all means: make programming accessible. Let product managers prototype. Let analysts automate their work. Let
designers build. Let engineers generate code instead of writing boilerplate by hand. And if someone wants to become a
software engineer, even better. Study the fundamentals. Build things. Break them. Fix them. Gain experience. There is
nothing stopping them from doing that. But having access to the tools is not the same as mastering the discipline.

LLMs can lower some costs, they can't make the consequences of bad software disappear. That is the part I keep coming
back to. The more code we can produce, the more we need people who know which code should exist in the first place.

**As the cost of writing code approaches zero, the value of judgment, experience, and good engineering becomes more
important than ever.**
