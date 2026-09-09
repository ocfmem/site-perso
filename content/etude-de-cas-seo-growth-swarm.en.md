+++
title = "SEO Growth Swarm Case Study"
description = "How I designed a system of 11 autonomous AI agents that writes and publishes one blog article every working day, with no human in the loop. Orchestrator-free multi-agent architecture, bounded LLM loops and automated publishing with circuit breakers."
date = 2026-09-09T09:00:00+02:00
draft = false
url = '/en/projects/seo-growth-swarm/'
Toc = true
+++

The SEO Growth Swarm is a system of **11 autonomous AI agents** that I designed and built end to end. It handles the complete SEO growth of a product, from competitive monitoring to the article deployed in production: performance analysis, topic discovery, editorial planning, research, writing, illustration, quality control, publishing and monitoring. The pace is **one article every working day, with no human in the loop**.

There is no orchestrator, no conductor, no queue between the agents. Each AI agent is woken by its own cron, reads the shared state, decides whether it has anything to do, writes its result and exits. The pipeline emerges from the state, not from a central controller.

The system runs in production today on two distinct products, with the same code deployed twice and zero lines of code specific to either one.

**The swarm in numbers**

- **11** autonomous AI agents, deployed as **14** scheduled jobs
- **3** parallel production lines: preparation, content, monitoring
- **1** article published every working day, quota guaranteed by the system
- **2** products served by the same code, told apart by a single environment variable
- **0** humans in the execution loop: control happens upstream, in the configuration

## The problem

Running a fully automated editorial pipeline is not a prompting problem. It is a reliability problem, and it comes down to four structural constraints.

**An LLM left on its own produces mediocre content, and nobody sees it.** In full-auto, there is no reviewer to penalize lazy writing, hallucinated figures or an article that repeats the previous one. Quality therefore has to be a *mechanical* property of the system, not a hope placed in the model.

**An unbounded agent loop is a time bomb.** Iterating until satisfied means, in production, a run that never ends and a bill that never stops. Every loop needed an explicit iteration ceiling and a time budget, on a platform that hard-kills any job after 900 seconds.

**Automated publishing touches a production repository.** The publishing agent writes into the monorepo of a real application. A scope error, a file committed outside the blog directory, is not a failed article: it is an incident.

**A system of 14 independent jobs cannot assume any ordering.** A cron can be skipped, a run can fail, a retry can happen at any time. Without strict idempotence, the system produces duplicates or deadlocks.

## The architecture

The founding principle is the **blackboard pattern**: the AI agents never talk to each other directly. They read and write a structured shared state, here Notion, which serves at once as coordination bus, configuration store and human interface.

```text
 CONFIGURATION (edited by the human, upstream)
 rules · guardrails · semantic scope · style guide · thresholds · backlog
        │
        ▼
 PREPARATION (Sunday)
 competitor-watch ────▶ reporter + explorer ────▶ strategist ────▶ planner
        │
        │ editorial plan frozen, one topic per working day
        ▼
 PRODUCTION (Monday to Friday)
 writer (research ────▶ write) ────▶ illustrator ────▶ strategist (single gate)
        │
        │ draft approved
        ▼
 PUBLISHING
 executor ────▶ git branch ────▶ CI ────▶ article online ────▶ Slack
        │
        ▼
 MONITORING (continuous, no LLM)
 watcher · backlink-scout · tech-auditor ────▶ Slack (alerts)
```

### Propose, validate, execute: three families of AI agents

Separating the roles is what makes the system safe.

The agents that **propose** (reporter, explorer, competitor-watch) analyze real data: Search Console, Plausible, rankings, competitor SERPs. They emit typed proposals, and never decide.

The **strategist is the single gate** of the swarm. It alone validates, across all three production lines, the optimization actions, the article topics and the complete drafts. It applies the rules written by the human in the configuration.

The agents that **execute** have no LLM at all. They mechanically apply decisions that have already been validated, with circuit breakers. Four of the eleven agents are in fact fully deterministic (monitoring, execution, backlink watch, technical audit): the LLM is used only where it brings judgment.

### A two-stage quality gate

This is the answer to the first constraint. Before an article can be published, it goes through a two-step control.

The first stage consumes **no tokens**: complete frontmatter, length within bounds, illustration present, internal links checked against the real corpus index, no invented URLs. A mechanical defect is caught without ever calling a model.

The second stage is an **LLM rubric with six criteria** (search intent, accuracy, originality, absence of *slop*, tone, internal linking) with a 0.7 threshold on each. The evaluation runs with the **accuracy reference** in context: the sourced facts gathered during the research phase. A figure absent from the research dossier is a hallucination, and it is treated as one.

On rejection, the gate produces actionable remarks, triggers **one** revision by the writing module, then re-validates. A single cycle, never two: on the second failure the article is dropped and the anomaly escalates to Slack.

### Writer and critic are not the same model

The writing agent follows the **STORM** pattern (Stanford): quality comes from the depth of the *pre-writing*, not from the writing loop. It works in two separate phases, carried by two distinct jobs: an agentic research phase that produces a dossier of sourced facts, then a writing phase that is no longer allowed to look for information anywhere but in that dossier.

The writing itself pits three distinct LLM roles against each other: a **writer**, a **critic** instantiated on a different model with its own prompt (to break the self-indulgence of a model reviewing its own output) and a **fact-checker** that confronts every quantified claim with the reference. The writing and critique loop is bounded to two revisions, the research loop to five iterations with a monitored time budget. Never a loop that runs until satisfied.

Model selection is **per-role routing**, not a code constant: each role (analysis, validation, mechanical tasks, writing) reads its model identifier from an environment variable. Moving writing up to a frontier model, or validation down to a cheaper one, happens without redeployment.

### Automated publishing with circuit breakers

The publishing agent clones the product repository with a *shallow* and *sparse checkout* of the blog subdirectory alone, creates a `release_blog/<timestamp>` branch, commits the article and its assets, then triggers the deployment workflow through the GitHub API.

Two circuit breakers frame the operation. A **scope breaker**: every committed file must live under the blog subdirectory, otherwise the run fails and alerts. The swarm is structurally incapable of touching the application code. And an **integration breaker**: the CI. The agent watches the run by polling, verifies that the article URL actually responds online, and only merges the branch into `main` after that confirmation. `main` remains the exact reference of what is in production, and a failed branch stays in place for debugging without ever having touched it.

### Idempotence and clean degradation

No agent assumes an execution order. State pages are created *get or create*, the publishing agent looks for approved and unpublished drafts through a persisted state machine, the topic backlog is a status registry. A retry is always safe, a replayed job duplicates nothing.

When an input is missing (a week never prepared, a day with no planned topic, an unavailable data source), the agent performs a clean *no-op* or degrades while recording the missing source, but always produces its artifact. Every Slack escalation carries a "Default" line: what will happen if nobody acts. The system never blocks waiting for a human.

### Multi-tenant: zero custom code

The identity of the product being served (domain, blog repository, landing pages, competitors, tracked keywords, language, tone, style guide, quotas) exists **nowhere in the code**. It comes from a single environment variable pointing at the product configuration space.

Deploying the swarm on a new product means one environment file and a configuration tree generated by script. Half a day, most of it spent waiting on external access, and not a single line of code changed. That is what made it possible to go from one product to two without touching the repository.

## The stack

Python 3.13 and **LangGraph** (`StateGraph` with Pydantic state), Pydantic structured outputs on **every** LLM call with a deterministic fallback when the model fails, OpenAI API with per-role model routing. Notion for coordination and configuration. Google Search Console, Plausible, DataForSEO and BrightData for observation and research. GitHub API for publishing, Slack for human reception. Image generation for visuals and charts as pure SVG generated in code. All of it deployed as scheduled jobs on [HollowHost](/en/projects/hollowhost/).

## What this project demonstrates

**Multi-agent systems engineering.** Eleven autonomous AI agents coordinated through shared state following the blackboard pattern, with no orchestrator and no direct communication, a strict separation between proposing, validating and executing, and a single validation gate.

**Reliability of LLM-based systems.** Bounded loops with iteration ceilings and time budgets, structured outputs with deterministic fallback, writer, critic and fact-checker separated across distinct models, a two-stage quality control with one stage entirely token-free, and per-role model routing driven by configuration.

**End-to-end automation with circuit breakers.** A chain that runs from observation data to the article deployed in production with no human involved, protected by a scope breaker at the repository level, the CI as an integration breaker, and strict idempotence that makes every retry safe.

**Multi-tenant product architecture.** A single repository, zero client-specific code, the whole service identity carried by configuration. All of it validated by a second production deployment with no code modification.

---

An AI agent, LLM automation or multi-agent architecture project in mind? [Let's talk](/en/contact).
