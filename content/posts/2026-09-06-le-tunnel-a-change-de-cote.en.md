+++
title = "The tunnel has switched sides."
description = "Clients now turn up with forty pages of AI-generated specs. The intent is excellent, the result ignores what already exists. Because a spec never had its value in the document."
slug = "2026-09-06-the-tunnel-has-switched-sides"
date = 2026-09-06T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["AI", "Development", "Management"]
+++

A client sends me forty pages of specifications. The document is clean: table of contents, numbering, acceptance criteria. Page thirty-one, a data model. Eleven tables, foreign keys included.

Problem: that model describes a system which does not exist. Not a future system or an evolution of it, but an imaginary one. Plausible and coherent, but unrelated to the one we have been building for months.

Let me be clear straight away, because it is the heart of the matter: this client is doing what seems obvious to them. They spent time on that document. They produced it because they believed that handing developers a complete specification was the best thing they could do for the project.

And there is something else I understand without any effort: AI has given them the means to get involved in the technical build as they never could before. We developers know exactly how exhilarating that is. I find it hard to hold it against them.

## The intent is good, and that is what makes the problem interesting

For fifteen years we asked for specs. We complained about three-line requests in a ticket, about requirements that changed once development had started, about business decisions we only got by asking three times. The business heard us. It now turns up with forty pages.

Nobody is cheating. That is what makes the phenomenon interesting, and it is also why it will not correct itself: every signal tells the project owner that they did good work.

Except the document has three defects that recur with metronomic regularity. It is verbose. It contradicts itself from one section to the next because nobody reads forty pages with the attention they give to four.

And it ignores what already exists.

The first two defects cost reading time; the third costs the project. This defect announces itself: code, or a data model. When a specification contains either, it has stopped describing a need. It is prescribing a solution, based on a system its author never read.

## The spec was not the input to design, it was the output

A specification never had its value in the document. Its value was the negotiation that produced it.

That negotiation is where the business discovers what its idea costs: this one, three days; that one, three months, because the orders table is shared with billing. It is also where engineering discovers what the business actually wants, which is almost never what it asked for. The document that came out of it was only the minutes of that discovery.

Nothing new here, and that is exactly the problem: we have known it for twenty-five years. In 2001, Ron Jeffries formalised the three Cs of the user story (*Card, Conversation, Confirmation*) precisely to distinguish the "social" story from the documentary practices that came before. The card is not the requirement: it is the pretext for the conversation in which the requirement gets built. In 2014, Jeff Patton summed the matter up in a sentence that became the most quoted line of the book: shared documents are not shared understanding. Patton illustrates it with the telephone game, and with the Mars Climate Orbiter probe, lost in 1999 over a units mix-up everyone thought was obvious.

So where does knowledge of the existing system live? Nowhere in a document. It lives in that conversation. Generating the specification upfront and alone therefore does not shorten design: it deletes it. And you discover the deletion at implementation time, which is to say at the most expensive point.

## The tunnel has switched sides

Tunnel vision, in this trade, has always meant the same risk: an engineering team puts its head down and comes back out three months later with something that was not what was needed. The whole agile arsenal (reviews, demos, discovery workshops, a PO embedded in the team) was invented to cut windows into that tunnel.

AI has just dug the same tunnel on the other side of the table. The project owner now works alone, with an interlocutor that never contradicts them and congratulates them on their brilliant ideas, that does not know their system unless it is handed over (access to the databases, to the code, and so on), and that never asks them why. They come out with forty pages and the conviction that they have made progress.

Nobody has a ritual for detecting that, for a simple reason: we never needed to protect the business from itself. All our safeguards face the other way.

The cost does not disappear. It changes hands. The time saved in writing is handed back to the engineering team in the form of rebuttal: reading, understanding the intent, identifying what is unfeasible, explaining it, proving it. With a side effect that poisons the relationship: whoever says no becomes the obstacle. The project owner produced, engineering objected. On paper, everyone can see who moved forward and who slowed things down.

## Writing the spec first is not the problem. Having it written is.

Let us be honest about the state of the art: I am swimming against the current here. Writing the specification first and having it implemented afterwards has become a named and defended practice, *spec-driven development*. Thoughtworks made it one of the notable engineering practices of 2025, Microsoft publishes on it. This is not a fad.

I have nothing against it. What I dispute is how it is being read. Everyone who has looked at the practice closely says the same thing: its value lies in the thinking produced **while writing** the specification, not in the tooling around it nor in the resulting document. Generating the specification therefore removes exactly the part that had value, and keeps the layout.

History has already settled this, incidentally. Waterfall did not fail because specifications are bad, but because discovering that a specification was wrong came too late and cost too much. **Producing them ten times faster does not move that point by a millimetre: it only increases the number of pages written before you get there.**

That leaves the scenario I dread, and it is anything but theoretical: the engineering team using AI in turn to interpret the specification. AI writes, AI interprets, and no human has ever confronted intent with reality. What comes out of that loop is not a document, it is the product. And nobody will be able to point to the moment the misunderstanding got in. The market is already selling the missing link, by the way: tools that analyse your AI-generated specification to detect its vague areas and implicit assumptions. An AI to guess what the AI meant.

## What I say now

I do not send the document back. I do three things, in this order.

I tell the client plainly that inconsistent material does not save me time: it costs me time. It is the most useful sentence of the exchange, and the one most rarely spoken, because it sounds ungracious. It is not: as long as they believe they are helping me, they will carry on.

Then I guide them, on good practice and on the right formats for this kind of document. What is expected, what is not, how far their ground extends. Describing a need, a use case, a business rule, an edge case: yes, and it is valuable. Deciding on a data model: no. That is not precision, it is an architectural decision made without the necessary information.

And I remind them of something obvious that both sides of the table forget: I have AI tools too. I understand the intent, but they would be better off spending their tokens on the business side. Producing the architecture, the code, the data model is my job, and I am equipped for it. What nobody can do in their place is judge what a feature is worth to their company.

None of this is a formatting problem, and no prompt will fix it. The specification can perfectly well be generated. I do it for OCF's internal projects, in a slightly unusual format, admittedly, but I do it. I will tell you about that format in a future post. Which changes nothing about the principle: it is only worth anything as the minutes of a conversation that took place, never as its replacement. The whole difference comes down to one question: does the document arrive before the discussion, or after?

My proposal: let us talk first. Because back in the days of paper, a useless specification would at least prop a door open. A PDF does not even have that going for it.

## Sources

- [Agile Alliance — "The Three C's"](https://agilealliance.org/glossary/three-cs/) — the model Ron Jeffries proposed in 2001 to distinguish the "social" user story from documentary practices.
- [Jeff Patton — *User Story Mapping*](https://www.goodreads.com/work/quotes/41559956-user-story-mapping-discover-the-whole-story-build-the-right-product) — the source of "shared documents aren't shared understanding," and the telephone game analogy.
- ["Why Documents Fail," a talk by Jeff Patton](https://urbanhogberg.wordpress.com/2015/02/24/why-documents-fail-a-presentation-by-jeff-patton/) — the short version of the argument.
- [Gojko Adzic — *Specification by Example*](https://gojko.net/books/specification-by-example/) — shared understanding as the deliverable, reached through concrete examples rather than a document.
- [Thoughtworks — "Spec-driven development"](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices) — the analysis of the practice, and why executable code remains the source of truth.
- [Microsoft — "Spec-Driven Development: a spec-first approach to AI-native engineering"](https://developer.microsoft.com/blog/spec-driven-development-ai-native-engineering/) — the favourable view, to make up your own mind.
- [Jérémy Decool — "Attention au bruit documentaire généré par l'IA"](https://www.jdecool.fr/blog/2026/07/15/attention-au-bruit-documentaire-genere-par-l-ia.html) — on the *why* that generation never reaches (in French).
