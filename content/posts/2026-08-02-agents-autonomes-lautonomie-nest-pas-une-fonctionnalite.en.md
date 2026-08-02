+++
title = "Autonomous agents: autonomy is not a feature"
description = "Hermes Agent, OpenClaw: agents that pick their own path are booming. Why I do the opposite, and why autonomy is also the attack surface."
slug = "2026-08-02-autonomous-agents-autonomy-is-not-a-feature"
date = 2026-08-02T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["AI", "Development"]
+++

In February 2026, Nous Research released Hermes Agent. Three months later the project had passed 140,000 stars on GitHub, the fastest growth the agent ecosystem has ever seen. The principle: you hand it a goal in plain language, the agent breaks it into steps on its own, picks from a library of more than forty tools, and iterates until it reckons it is done.

I like a good story too. But I build them, agents. It takes up a growing share of my work, and the more of them I build, the less autonomy I grant them.

## What "autonomous" actually means, technically

Behind the word sits a very simple mechanism, and that is precisely the problem. You give the model a goal. The model decides the steps. It executes, observes, decides on the next one, and starts again, until it judges it has finished. No path is written down anywhere. The path is produced at runtime, once, and it will not be the same one next time.

What you gain: not having to anticipate the cases. What you give up in exchange, and hear far less about: knowing what the system is going to do.

Researchers at Carnegie Mellon built a benchmark worth the detour, TheAgentCompany. It is a complete fake software company: its GitLab, its OwnCloud, its project management tool, its internal chat, right down to simulated colleagues the agent has to prise information out of in order to move forward. 175 long professional tasks, from development to finance by way of HR.

The best model tested completes **30.3%** of them autonomously. The runner-up, 26.3%. A GPT-4o drops to 8.6%.

You can read that result as "not ripe yet." I read it differently: it is what unbounded goal decomposition produces when it meets the mess of the real world.

## Autonomy is not a feature that happens to have flaws

The failure rate we could live with: you re-run it. The real cost lies elsewhere, and it is twofold.

First, non-reproducibility. When an autonomous agent fails, it does not fail the same way twice. You cannot replay the trajectory, you cannot bisect, you cannot set a breakpoint at step 7, because there is no step 7: there was a step 7 that one time. Debugging becomes archaeology.

Then security, and this is where the subject stops being theoretical. OpenClaw, the local agent that runs permanently on its user's machine and wakes itself up, became within months the case study of the genre, dissected by IBM X-Force and by several research teams. The most telling vulnerability is called ClawJacked: a single malicious web page was enough to hijack a local instance and silently exfiltrate data from it. Not through some exotic parsing bug: by making use of the agent's autonomy, which was doing exactly its job.

The problem is not that OpenClaw is badly written. It is structural: every degree of freedom granted to the agent is also granted to whoever knows how to talk to it. An autonomous agent with access to files, credentials and the network is an attack surface that makes its own decisions.

## Logs are not traceability

Non-reproducibility is a developer's problem. Traceability is a company's problem, and it is usually the one that tips the decision.

An autonomous agent produces logs in quantity: every tool call, every model response, every token consumed. What it does not produce is a chain of decisions tied to a known process. Knowing that the agent called such and such an API at 2:03 p.m. does not tell you why it called it, nor what it discarded along the way.

The tooling does exist, and it is good. LangSmith, LangChain's observability platform, traces every model call, every tool, every step of a run, and it has become LangGraph's default tracing backend. But a tracing tool only gives back what you feed it. On a designed graph the trace reads: every node carries a name, every transition matches a step I intended, and reviewing an incident amounts to following the process. On an autonomous loop the same tool produces a flat sequence of calls whose intent has to be guessed after the fact. Same tooling, two entirely different levels of legibility.

And the difference is not cosmetic. When money goes out, when data is modified, when a message is sent, you have to be able to say what triggered it, on what basis, and at which point in the process. For audit, for compliance, and quite simply to be accountable. A path written in advance answers that by construction.

## Designing the reasoning path

That is why I do the opposite of the trend. I am not trying to make my agents more autonomous: I am trying to design their reasoning path.

For at least three years now I have been working with AI on the satellite tasks of building a SaaS: SEO, ad campaigns, reporting, user journey analysis, competitive monitoring, triaging customer feedback, tracking infrastructure costs. Everything that is not the product, and that still eats half the time when you publish software. Along the way I have tried just about everything: prompts that grew longer and more convoluted (by the way, do you still know any prompt engineers?), LangGraph graphs, Claude's built-in tools, and of course agents of the Hermes kind, the ones you hand a goal and watch walk off.

Here is an example I can tell, because it is my own. On HollowHost, one of my products, I automated the monitoring, analysis and adjustment of the Google Ads campaigns. From a distance it is exactly the kind of task you would hand to an autonomous agent without a second thought: a goal that states itself in one sentence, an API on tap, and a loop that has to run without me. I could have turned an agent loose on it with the keys to the account.

I did not, for a reason that is anything but theoretical: every turn of that loop spends real money. An agent that decides its own steps also decides its own budget.

So the path is written. Fetching the data is not entrusted to the model: it is deterministic code, which goes and gets exactly the metrics I decided mattered, at the moment I decided they were worth looking at. The analysis, on the other hand, is genuine interpretation work, and that is where the model belongs: here are the figures, here is the history, what is moving and why. Tight scope, output in a shape I know. Adjustment, finally, is the only step that touches the wallet, and the only one I bounded with actual numbers: a bid on a keyword can only move between a minimum and a maximum I set, and keyword additions and removals are capped in number over a given period. The agent can correct the trajectory. It cannot rebuild the account.

None of this decomposition is original, and that is precisely the point: it generalises. I apply the same grid to the other automations I have been working on for several years, around the business of publishing software. From my successes and my failures, I have drawn a few lessons.

Writing the path instead of letting it emerge means explicit state transitions: from this state, with this result, you can only go there. Bounded steps, where the model handles the ambiguity of one precise problem. Checkpoints, some of them human, placed where an error is expensive and nowhere else. And state persistence, so that an interrupted task resumes where it left off instead of replaying everything. That is very precisely what a LangGraph tools up, with its edges and its conditional routing: boundaries the model cannot cross. This is not an implementation detail, it is an architectural choice.

The interesting part is that the recommendation comes from the people selling the models. Anthropic, in its own engineering guide, advises looking for the simplest possible solution, even if that means building no agentic system at all. When a vendor explains how to consume less of their product, it is worth listening.

## Autonomy makes you interchangeable

That leaves the argument that matters most to me. The deterministic path I have just described is not plumbing installed around the intelligence: it is where I put my domain expertise. Deciding which metrics count and which are just noise, at what point it is relevant to look at them, what constitutes a drift and what is merely a seasonal variation: none of that can be deduced from a goal stated in one sentence. It comes from what I know about the business, and from what my past mistakes taught me.

An autonomous agent, meanwhile, will produce what every autonomous agent produces: the average of what the model has read. The path I write is exactly what nobody else has. That is where the little something that sets you apart lives.

## So when do you let go of the reins?

I am not saying autonomy is useless. It has a domain of validity, simply a narrower one than the story goes: when inputs vary too much for a path to be written in advance, when the task is exploratory by nature, when the cost of an error is low and a human looks at the result before it has any effect. Search, sort, propose: yes. Decide, commit, spend: no.

The rule I apply fits in one sentence: the workflow is the default answer, and it is up to the agent to prove it brings something deterministic logic cannot produce.

## The flight plan

There is an industry that has been practising autonomy at scale for fifty years, and that settled the question long before we did. An autopilot is an extremely autonomous system: it holds a heading, an altitude, flies an approach, continuously corrects for disturbances nobody had anticipated. And it has never, not once, chosen its destination. It follows a flight plan filed in advance, with waypoints, imposed altitudes and minima. Someone stays responsible, and can take back control at any moment.

Aviation has never confused autonomy with freedom.

Designing an agent's reasoning path is not putting it on a leash. It is what makes it shippable, and it is the only thing that tells it apart from the one next door.

## Sources

- [TheAgentCompany: Benchmarking LLM Agents on Consequential Real World Tasks](https://arxiv.org/abs/2412.14161) — the Carnegie Mellon benchmark and its autonomous completion rates.
- [Anthropic — "Building Effective Agents"](https://www.anthropic.com/engineering/building-effective-agents) — the workflow / agent distinction and the case for starting simple.
- [IBM X-Force — what OpenClaw reveals about agentic AI security risks](https://www.ibm.com/think/x-force/what-openclaw-reveals-about-agentic-ai-security-risks).
- [The Hacker News — OpenClaw flaws enabling prompt injection and data exfiltration](https://thehackernews.com/2026/03/openclaw-ai-agent-flaws-could-enable.html).
- [Barracuda — what security teams need to know about agentic AI](https://blog.barracuda.com/2026/04/09/openclaw-security-risks-agentic-ai).
- ["Uncovering Security Threats and Architecting Defenses in Autonomous Agents: A Case Study of OpenClaw"](https://arxiv.org/html/2603.12644v1) — the academic case study.
- [NVIDIA — on Hermes Agent and self-improving agents](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/).
- [LangChain — the 2026 agent framework landscape](https://www.langchain.com/resources/ai-agent-frameworks) — on state persistence and conditional routing.
- [LangSmith](https://www.langchain.com/langsmith) — the observability and tracing platform used as an example.
