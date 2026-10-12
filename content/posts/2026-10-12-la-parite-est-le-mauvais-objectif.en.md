+++
title = "Parity is the wrong goal."
description = "A developer cloned seven Adobe applications with an AI, and it does not work. Why his failure is the best argument for building your own software."
slug = "2026-10-12-parity-is-the-wrong-goal"
date = 2026-10-12T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["AI", "Development", "Management"]
+++

I have taken up the piano. Do not ask me why, I do not know myself. I picked up a small MIDI keyboard and started messing about with it.

I needed a tool. Something to memorise where the keys are, work on chords, recognise notes by ear, and record myself quickly to listen back.

Those applications exist. They are good, they are well known, they cost about the price of a coffee a month.

I did not try them.

I could see myself browsing the store, installing, testing, uninstalling, installing something else, eventually settling on one, and discovering three weeks later that the feature I needed sits behind a paywall. I ran the whole sequence in my head, and I opened my code editor.

An hour later I had my tool. And an hour is generous.

This is not a whim. Ever since I learned to code I have been building my own tools. Cron jobs, small mobile apps, odds and ends that never leave my own machines and that answer exactly the need I had that day. The models invented nothing here, they only lengthened the list of what I can afford to build.

So I did not refuse to buy. I knew, straight away, that I could make myself something bespoke.

Which raises a question I find harder and harder to wave away: at what point does building cost less than buying? And I am not talking about the price of a licence.

## "Software is over"

On 7 October, a developer called Brandon Thomas published Artcraft: seven open source applications cloning Photoshop, Illustrator, Premiere, Lightroom, After Effects, InDesign and Acrobat Pro. Written in Rust, produced with Claude Opus 5.5, free, no subscription. The trigger is openly stated, it is Adobe's cancellation fees, which earned the company a lawsuit from the US government and a 150 million dollar settlement last March.

His conclusion fits in three words: *software is over*. He goes on to announce that we will replace Google Search and build our own smartphones.

One man. A few weeks. Seven professional applications. Eighteen months ago that sentence made no sense at all.

Except it does not work.

His own words: *super early alpha*. The Hacker News thread about the project was flagged by the community, which is not nothing coming from the audience most favourably disposed to an open source Rust project. People who installed the software report that basic functions do not respond, one of them noting that a Photoshop file with a smart object comes out completely broken, and that a web application thirteen years old does better. The original promise, 100% feature parity within a month, was revised days later to 99% in months rather than years.

And let us be fair: four days after those disastrous first attempts, another tester finds that day's build broadly usable. It is iterating fast.

I should tell you where I stand. I have never replaced a vendor's software at a client. What I have is a piano practice tool built in an hour, and an older story I will come back to further down. What follows is therefore a bet, not a field report.

## Nobody needs 100%

Artcraft's failure is not a failure of skill. Thomas has fifteen years in the trade, about a decade of it on large Rust projects, and we will come back to that detail, because it is not one.

It is a failure of target.

He set himself the hardest thing there is: reaching feature parity with a general-purpose product used by millions of people. A commenter on the thread put it better than I could. Aiming for parity means misunderstanding the very nature of the software you are copying.

Because a feature list was never written for one user. It is the sum of twenty years of requests from every customer at once. Photoshop is big because it has to serve the photographer, the graphic designer, the illustrator, the printer and the editor, across thirty countries and two operating systems.

You are only one of those people.

Back to the piano for a second. I did not need 4% of those applications. I needed one thing they do not do and never will: a practice tool, with no lessons, no song catalogue, no progression and no badges. Nobody sells it to me, because you cannot build a subscription on that.

So what is missing is not a piece of the product. It is what the product cannot be.

In your company, that thing has a name. It is the Excel files orbiting the software. They are not the symptom of a mess that needs tidying. They are the specification, written by use, of everything the vendor did not cover.

## It already worked without AI

A few years ago, well before the models arrived in our code editors, I was part of a story I rarely tell.

A project was bogging down at a very large insurance company. An employee, a data expert and product manager, convinced management to stop the bleeding and restart the whole thing from zero. Three of us: him, and two senior developers, one of whom was me.

It worked. Completely.

And let us say straight away what that story does not mean. The people on the original project were not bad, and we were not better than them. We simply had something they could no longer have.

At the time, we told ourselves our advantages were a clean stack and clean code. That is wrong, or rather it is the other way round. The stack was clean and the code was clean because we were starting from zero. They were consequences, not causes.

The only real advantage fits in two words: **no inertia**.

That is what gets forgotten when you compare a vendor to what you could build yourself. Their product is not heavy because their developers are bad. It is heavy because it serves all of their customers at once, because it has to stay compatible with itself, and because it drags along twenty years of edge cases, not one of which concerns you.

You will never be better than them at their job. You will simply be lighter, because you serve one customer and remember nothing.

And the hard part of that story was not technical. It was convincing a management team to stop something that had already been paid for.

## What the vendor was carrying for you

Now the other side, because an article without one would be a sales brochure, and I am the first to benefit from it.

A vendor does not only sell you features. They absorb, on your behalf, a regulatory burden that never sleeps: electronic invoicing, payroll, tax changes, obligations that shift by decree. They carry maintenance over ten years. They are contractually liable, which means that if something goes wrong there is somebody to put in front of a judge.

And we have to be honest about what is being offered instead. Leaving a vendor is not escaping dependency. It is swapping one for another. You move from a supplier you can sue to a contractor you hope will still pick up the phone in three years.

There is one more risk nobody talks about. Thomas set himself a strict rule, written into the instructions he gives his model: never open or disassemble Adobe's software, only observe its behaviour. That is the clean room method, and it is legally serious. Except that a commenter pointed out what the discipline does not cover. The model writing the code was trained on just about every piece of open source code in existence. The cleanliness applies to the human, not to the machine. For a company having its core tooling rewritten, that question is not theoretical.

One word, finally, for the vendors themselves, who get treated as adversaries rather too quickly. They solved a real problem, and for thirty years they solved it for people who had no other option. Nobody could afford bespoke software. What has changed is not their quality. It is the cost of building.

## What I would do, and what I do not promise

Here is the bet, and I believe in it enough to write it down before finding out whether I am right.

The contracting trade has long consisted of configuring someone else's tool, integrating it, keeping it standing at your premises. I think it will increasingly consist of building the one that does not exist. Not a clone of your ERP: the thing your ERP will never do, and that you currently keep in a spreadsheet.

What tips the balance, incidentally, is not the cost of development, which has always been the argument. It is the cost of evaluation. The specification, the three vendors in the running, the demos, the pilot, the negotiation, the data migration: add up what you spend before you have even signed. That is the figure to compare against, not the price of a licence.

And since I have admitted I stand to gain from this, here are the conditions under which I would do it.

The code belongs to you. So does the documentation. You have to be able to replace me, and I have to have given you the means to do so without me. **A contractor who does not prepare for their own replacement is not selling you software, they are selling you a dependency.** Which is precisely what you were trying to get away from.

I have not yet replaced a vendor's tool at a client. I think it will be one of the big subjects of the coming years, and I have written this article to say so before being proved right or wrong.

In the meantime, I have a piano practice tool that can do exactly four things. It looks like nothing, nobody else will ever use it, and it does exactly what I ask of it. Now I am eyeing my MK2s suspiciously. I am sure there is something to be done there, something that would fit precisely what I want. Without rebuilding Serato.

Parity was never the goal.

## Sources

- [Ars Technica, "Software is over: Bold AI developer takes aim at Adobe with open source clones"](https://arstechnica.com/ai/2026/10/software-is-over-bold-ai-developer-takes-aim-at-adobe-with-open-source-clones/) — the original coverage, Artcraft's pivot and the legal risk of imitating an interface.
- [The Hacker News thread about the project](https://news.ycombinator.com/item?id=49958850) — the hands-on reports, the revision of the parity promise, and the clean room critique.
- [Brandon Thomas's announcement on Reddit](https://www.reddit.com/r/SaaS/comments/1wwnhbt/ive_created_open_source_100_rust_cleanroom/) — the original promise, in his own terms.
- [The Artcraft applications](https://getartcraft.com/apps) — to make up your own mind.
- [TechSpot, on Adobe's cancellation fees](https://www.techspot.com/news/114136-software-over-developer-uses-ai-build-free-adobe.html) — the US government lawsuit and the 150 million dollar settlement.
