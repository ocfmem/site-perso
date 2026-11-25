+++
title = "Heads or tails, you pay for it."
description = "I removed a feature from my own product: hosting autonomous agents produced unpredictable results. The more work you hand to agents, the more a bad result costs. Why a bounded swarm replaced the harness."
slug = "2026-11-25-heads-or-tails-you-pay-for-it"
date = 2026-11-25T09:00:00+01:00
draft = false
author = "Meddy Menzikoff"
tags = ["AI", "Development"]
ogImage = "/img/swarm-agents-ia-hollowhost-og.jpg"
ogImageAlt = "HollowHost dashboard of an eight-agent AI system scheduled daily, with eight successful daily runs"
+++

I removed a feature from my own product.

HollowHost, the platform I am building, turns a GitHub repository into a hosted AI agent. It offered two ways to run an agent: jobs, executed on demand or on a schedule, and a daemon mode, which kept the agent alive permanently. That second mode is where harnesses ran, those general-purpose agents like Hermes that you hand an objective and that work out how to reach it.

We removed it.

The results were poor, token consumption was high in that usage, and above all there was that heads-or-tails quality: the same agent, on the same task, would succeed one day and fail the next, with no way of knowing why. For a platform whose whole job is running agents, that was bad publicity. The daemon mode was damaging the product's image.

I already knew that verdict. Before building the system that now publishes a piece of SEO-optimised content every working day with no human in the loop, I had given the same job to Hermes. Same outcome.

What I took from it fits in one sentence: **the problem was not the tool. It was where the control lived.**

## The black box

A harness does one thing remarkably well. You give it an objective, it reasons, explores, calls its tools, corrects itself. When you are sitting in front of it, reading what it does and stepping in at the right moment, it is an impressive collaborator.

That same property becomes a problem as soon as nobody is watching.

At six in the morning, when the article does not come out, the failure is not a step. It is a paragraph of reasoning lost somewhere in a long context. You cannot attach it to anything, so you cannot fix anything: you rerun it, and you hope. That is the exact definition of a coin toss.

And it is not only a readability problem. A team of researchers, notably from Stanford and the University of Michigan, measured how much agents consume on coding tasks. Models systematically underestimate what they are going to spend, and their predictions are at best moderately correlated with reality. The agent does not know what it will cost. Neither do you, until it has finished.

This is not a flaw in Hermes. It is a use it was not built for. I wrote in August, about the MCP protocol, that an architecture is judged by one simple question: who can prove what was asked. A harness left on its own cannot answer it.

## More tokens do not make better text

To claw back quality, you add validation loops. The agent rereads its text, judges it, rewrites it, rereads it. On every pass it is the same model grading itself, and on every pass the bill goes up.

The same study makes an observation that struck me: spending more tokens does not make the agent more accurate. Quality peaks at an intermediate cost, then plateaus. Those are coding tasks, not writing, but the curve looks exactly like what I saw.

I drew from it the most structural decision in the system that replaced Hermes: quality is not recovered downstream, it is built upstream.

For that I relied on STORM, a method published by a Stanford team that puts most of the work into preparing a text, before any writing happens. Research and writing run in two separate jobs. The first produces a file of sourced facts. The second is not allowed to look for information anywhere other than that file.

Then, three distinct roles. A writer. A critic, running on a different model with its own instructions, to break the complacency of a model rereading itself. And a fact-checker, which holds every figure up against the research file.

The loop between writer and critic is capped at two revisions. The final check grants only a second chance: on the second failure, the article is discarded and I get an alert.

## Control changes place

The system that now publishes SEO content for two products has eleven agents, and no boss.

There is no orchestrator. Each agent is woken by its own schedule, reads a shared state, decides whether it has something to do, writes its result and stops. The production line is commanded by nobody: it emerges from the state.

Four of the eleven agents use no language model at all. They monitor, publish and audit, in an entirely deterministic way. The model only steps in where it brings judgement.

That is the whole difference with the harness. Control no longer lives in a reasoning trace nobody will ever reread. It lives in a state a human can open and read, and when something fails, it fails at a step that has a name.

The usual objection: a harness is set up in an hour, while a system like this is a major build. That used to be true. It no longer is. With today's models, writing a dozen agents meant to run in the cloud on a recurring task takes a few days.

But what the model writes is the code. It does not decide that the critic should run on another model, or where to draw the line on what the system may touch. **Code has become cheap. Decisions have not.** That is where expertise goes, and it is what separates a reliable system from a lottery.

The architecture is described in detail in [the case study](/en/projects/seo-growth-swarm/).

## A guardrail is coded, not requested

In a harness, a guardrail is an instruction. "Never publish without checking the links." "Never touch a file outside the blog folder." An instruction lives in a prompt, and a model can always reason its way around it, especially on the twelfth iteration.

In the system that replaced Hermes, no guardrail is an instruction. **They are fuses.**

The first quality check does not spend a single token: complete metadata, length within bounds, illustration present, internal links verified against the site's real index, no invented URL. A mechanical defect is caught without ever calling a model.

A figure missing from the research file is treated as a hallucination, and the article is rejected.

Every published file has to sit inside the blog folder, otherwise the run fails and alerts me. The system is structurally incapable of touching the application's code.

Nothing is merged until the article actually responds online.

Every loop has an iteration cap and a time budget. Never a loop that runs until it is satisfied.

And there is one guardrail I did not even write. This system runs on HollowHost as jobs, and a job stops after fifteen minutes, whatever happens. No loop can run away for longer than that. The daemon mode had no such limit. It is precisely the one we removed.

<figure style="width:100%">
<img src="/img/swarm-agents-ia-hollowhost.webp" alt="HollowHost dashboard of an eight-agent AI system scheduled daily at 7 AM: job execution graph and history of eight successful daily runs" width="1223" height="719" loading="lazy" decoding="async" style="height:auto">
<figcaption>Another agent system I run on HollowHost: a daily briefing made of eight bounded jobs. Eight days in a row, eight out of eight succeeded, in about five minutes.</figcaption>
</figure>

## The workshop and the factory

I will not tell you to give up on harnesses, and for good reason: I am using one as I write these lines. A coding assistant like Claude Code belongs to the same family as Hermes, a general-purpose agent you hand an objective and that reasons its way towards it.

The difference is that I am sitting in front of it. Tools like this are what we use today to write the agents of a swarm, and it is from one of them that I deploy and manage mine: the command line interfaces of AWS or HollowHost make it possible to run agents in the cloud without leaving your terminal.

So the harness is not the rival of the bounded system. It is the workshop. It excels when someone is watching, so you use it at the moment someone is watching: at design time. Then the factory runs on its own, within limits you have written.

It is not a choice between two tools, it is a division over time.

And the more work you hand to agents, the more that division matters. In the workshop, a bad toss costs a rerun. In the factory, it costs an article published under your name, a client watching the agent fail, or a product's image.

Heads or tails, you pay for it.

*PS: This blog, for its part, does not come out of a growth swarm. I write it with the help of a model, but nothing here is automated: the irregularity of my posting schedule is proof enough.*

## Sources

- [Bai et al., "How Do AI Agents Spend Your Money? Analyzing and Predicting Token Consumption in Agentic Coding Tasks"](https://arxiv.org/abs/2604.22750) — preprint, April 2026: models underestimate their own consumption, and more tokens do not bring more accuracy. Measured on coding tasks.
- [Shao et al., "Assisting in Writing Wikipedia-like Articles From Scratch with Large Language Models"](https://arxiv.org/abs/2402.14207) — STORM, Stanford, NAACL 2024: the method that puts the work into preparing the text.
- [SEO Growth Swarm case study](/en/projects/seo-growth-swarm/) — the full architecture of the system described here.
- [HollowHost case study](/en/projects/hollowhost/) — the platform it runs on.
