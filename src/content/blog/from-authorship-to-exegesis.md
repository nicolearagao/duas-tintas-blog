---
title: "From Authorship to Exegesis: Code as a Speech Act in the Age of AI"
description: "As AI-generated code becomes part of daily engineering work, the engineer's role evolves from authorship to exegesis."
pubDate: 2026-04-19
category: "professional"
lang: "en"
tags: ["IA", "carreira", "linguística", "engenharia de software"]
---

With the daily use of AI at work, I've been noticing a shift that at first felt like mere discomfort: I read far more code than I write. And the more this repeats, the more I question what exactly has changed - not in the tool, but in my role. The feeling that if I'm not typing, I'm not producing, is persistent, and I know many engineers have felt the same. Recently, in a conversation with my colleague Rafael Jeffman, I realized this discomfort had a name and a root.

Before engineering, I studied Literature - I was always the humanities girl, surrounded by books. My home office still has more bookshelves than monitors. Linguistics was my favorite subject, and for years that knowledge seemed archived. Until AI made it relevant again.

Looking through that lens - after months of thinking the profession was going to disappear - what I see is nearly the opposite. AI has made visible a dimension of the profession we've always underestimated: software engineering is deeply linguistic in nature. Now that the machine handles the first draft, we're left with the work that was always the hardest: making sure the code says what it needs to say.

In linguistics and literary theory, this work has a name: exegesis - the critical interpretation of a text, searching for meaning that isn't on the surface. That's exactly what we do when we read AI-generated code.

## Code as a Performative Act

To understand why reviewing AI code is so different from reviewing any other document, it's worth reaching for a distinction from the philosophy of language.

J.L. Austin, in How to Do Things with Words, distinguished two types of utterance. A constative describes reality: "the door is open." A performative alters reality: "I declare the session open." A performative isn't true or false - it's successful or unsuccessful. And its success depends on context, authority, and intention.

Code is performative. An UPDATE on the database doesn't describe a change - it causes the change. A deploy doesn't report that the system changed - it changes the system. Every line of code that reaches production is an act that alters the state of the world.

Austin distinguished two layers in every speech act: the **locutionary** - the utterance itself, grammatically correct - and the **illocutionary** - the intention behind it, what the speaker aims to bring about.

This is where AI gets interesting. An LLM produces locutionary acts with impressive competence: syntactically correct, idiomatic code that compiles and often runs. But an LLM has no intention. It doesn't mean anything when it generates an UPDATE - it calculates the most probable sequence of tokens. The illocutionary act is absent.

We are the ones who assign the intention. When we review AI-generated code and decide that an UPDATE should go to production, we are the ones assuming the illocutionary force of the act: "this command expresses my intention, and I take responsibility for what it will do to the system."

## Frames: When the Syntax Is Right and the Meaning Is Wrong

If Austin explains what code does (perform), we still need to understand how meaning works within code. And since we're already dusting off my college linguistics courses, let me bring in one more: Charles Fillmore's Frame Semantics.

Fillmore argued that we don't understand words in isolation - we understand words within scenarios. The word "buy" automatically activates a complete frame: buyer, seller, goods, price, transaction. You don't need to mention all of these elements; they're implicit in the frame. Anyone who hears "she bought" already assumes there was a seller, a price, and something being sold.

In code, the same principles apply. A `Payment` isn't just a class with attributes. In your system, that name carries an entire frame: what happens when two payments arrive at the same time? Can the same request charge twice? How long to wait for the bank gateway before giving up? How to reverse a charge? Who audits? None of these elements need to be in the class name - but every experienced engineer who reads `Payment` in their context activates them instantly.

AI doesn't do this. It operates on statistical patterns from training data. When it generates code for a `Payment`, it tends to activate the most common frame - the one that appears in most tutorials and public repositories. That frame is, typically, the simplest: an object with fields, basic validations, a CRUD. It's syntactically correct and semantically incomplete for your system.

The result is code that passes the compiler but fails in context. The `Payment` class exists, the types are correct, the API works - but the frame is that of a tutorial, not a real financial system. Concurrency wasn't considered. Idempotency isn't there. The engineer who accepts this code without checking the frame is importing the assumptions of a generic context into a specific domain.

The engineer's job, in this scenario, is semantic curation: verifying that the frame the AI activated matches the frame of the real system. This requires domain knowledge that no LLM possesses - because your system's frame isn't in the training data. It lives in the team's heads, in architecture decisions that never became documentation, in the accumulated experience of those who've seen that `Payment` break in production. Programming languages are logical, but they exist to represent a world that isn't: business domains are subjective, contextual, full of exceptions no tutorial anticipates. This knowledge is built by living the system, not by training on public code.

## What This Changes in Practice

If code is a performative act and meaning depends on frames, the engineer's role in the age of AI redefines itself in concrete terms:

**Reading AI code is harder than writing your own.** When you write, you externalize a mental model you already have - the intention and the frame are already in your head. When you read AI-generated code, you need to reconstruct a mental model that never existed. There's no intention to trace, no author to consult. You construct the meaning on your own, then validate whether that meaning is compatible with your system. This is cognitively more expensive - not due to incompetence, but due to the nature of the process.

**I have the impression that seniority, today, is measured more by the ability to read than to write.** The machine is very good at generating syntactically correct code. What it doesn't do is doubt its own output. A junior engineer and a senior engineer receive the same AI-generated code - the difference is what each one asks before accepting it. The junior asks "does it work?". The senior asks "does it work in my context?". That second question is entirely linguistic: it's a frame check.

**The value lies in semantic precision, not in production speed.** The question that defines an engineer has shifted from "how fast can you deliver code?" to "how precise is your understanding of what this code does in the system?". Writing fast has become a commodity. Reading with precision is a rare and hard-won skill.

I don't believe we're losing relevance. I believe we're being forced to exercise the part of the job that was always the hardest and the most valuable - and that, because it was invisible, never received the recognition it deserved. AI hasn't replaced us. It has made evident that typing was never the hardest part of the work.

And if you're like me, someone who still feels genuine pleasure in writing code from scratch - that pleasure doesn't have to disappear. But what we do with it, and how it fits into an increasingly exegetical routine, is a conversation for another post.

*P.S. Funny enough, this article involved a lot more writing than reading - I originally wrote it in Brazilian Portuguese and used AI to help translate it. At least I practiced what I preach: I read every line before accepting it.*
