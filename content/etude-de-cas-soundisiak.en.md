+++
title = "SoundisiaK Studio Case Study"
description = "How I built SoundisiaK Studio, a 100% serverless music collaboration SaaS platform on AWS. Workspaces and track versions, lossless audio pipeline in Lambda, secure multitrack streaming and orchestrated billing."
date = 2026-08-04T09:00:00+02:00
draft = false
url = '/en/projects/soundisiak/'
Toc = true
+++

[SoundisiaK Studio](https://soundisiak.studio/) is a collaboration platform for music production that I designed and built as CTO. It gives artists, producers and production teams a shared studio in the browser: organize your projects, upload your tracks, listen to them in multitrack and move forward together to the final mix.

**The platform in numbers**

- **100%** serverless, no server operated
- **28** Lambda functions
- **7** nested CloudFormation stacks
- **4** Step Functions state machines
- **21** documented test plan sections

## A studio organized in workspaces

Everything starts with the workspace, the home of an artistic entity, whether a solo artist or a band. It gathers the members and their roles, the projects (albums, EPs, singles), their tracks and the full history of each title's audio versions. The version is the product's central object: every iteration of a track stacks up in a history you can replay, compare and comment on, instead of scattering into `final_v3_really_final.wav` files in a drive.

Collaboration follows the reality of a production. Workspace members have graduated roles matching their responsibilities. An external contributor, say a mixing engineer or a guest musician, can be invited onto one specific track without accessing the rest of the catalog. Each title carries its progress status, from concept to final mix, giving an at-a-glance picture of an album in the making.

Sharing with the outside world is a feature in its own right. A version is sent through a secure listening link with a limited lifetime that expires on its own. And every link lives with its statistics: plays and downloads are counted, so you know whether the mix sent to a label or a manager was actually opened, and how many times.

Quality, for its part, is non-negotiable: the platform works in lossless from end to end, from the uploaded file to playback in the browser. What you hear is faithful to the mix, not a degraded web version.

The business model relies on multi-tier Stripe subscriptions, with quotas enforced per plan: storage, number of projects, members, versions per track.

## The problem

Beneath this shared-studio experience, the whole technical difficulty lies in the raw material: *professional uncompressed audio, heavy, versioned, that must be processed, protected and streamed multitrack, without operating a single server.*

That raw material imposes five structuring constraints.

**Lossless audio as the starting point.** WAV files weighing tens of MB per minute per track, to ingest, analyze and convert into streaming derivatives, in an ephemeral Lambda environment, with no transcoding farm.

**Multitrack streaming synchronized to the sample.** The player must play several tracks perfectly aligned, including on iOS, where available codecs and per-page memory ceilings demand specific audio engineering.

**Serving private files without exposing them.** Tracks belong to the artists: no public bucket, yet smooth listening for rights holders and share links that expire on their own.

**Reliable billing without a webhook server.** The subscription lifecycle (sign-up, tier change, cancellation, payment failure) must be handled in a durable, replayable way, with no webhook endpoint to host and monitor.

**Reproducible, safe deployments.** Seven interdependent CloudFormation stacks and function versions to keep consistent: the slightest silent drift had to be detected and fixed automatically.

## The architecture

The architecture follows the path of the sound: an uploaded file triggers events, processing and derivatives, never a server waiting around. Each boundary (authentication, delivery, billing) is entrusted to the AWS managed service that carries it best.

Everything is event-driven. A user action goes through the CDN and a managed API, and only ever runs ephemeral functions, billed by the millisecond. Uploading an audio file publishes an event that wakes up the processing chain and produces the listening derivatives. Long lifecycles, billing and accounts alike, are carried by durable state machines that consume events as they flow and resume after a failure. No building block waits around: each one wakes up on an event, does its part and shuts down.

### A serverless control plane in composed stacks

The backend is described in AWS SAM as seven nested CloudFormation stacks (databases, storage, functions, authentication, API, events, state machines), deployed in one move in a guaranteed dependency order. Twenty-eight Python Lambda functions share a common code base, and authentication is delegated to Cognito, Google sign-in included. Splitting into stacks required solving real circular dependencies between these building blocks.

### An event-driven audio pipeline

Dropping a file into the audio bucket publishes an SNS event that triggers processing: an arm64 containerized Lambda embeds a transcoding chain whose version and fingerprint are pinned at build time, so it never changes silently. It analyzes the file, produces the lossless streaming derivatives and the waveform data. A reconciliation function detects and regenerates missing derivatives, and S3 lifecycles drive automatic cleanup (trash, expired public files).

```text
 WAV upload ──▶ S3 (SNS event)
    └─▶ Transcoding Lambda (arm64 container)
         └─▶ sliced FLAC derivatives + waveforms ──▶ S3
              └─▶ listening via signed CloudFront URL (time-limited)
```

### Private delivery and a demanding multitrack player

No audio file is public: delivery goes through signed CloudFront URLs, backed by a dedicated key group, with share links that expire automatically. CDN caching does the rest: listening derivatives are served close to the listener, playback starts immediately, and repeated plays of the same track hit neither storage nor processing again. On the playback side, the format choices (dropping mp3, WAV as the source of truth, FLAC as the streaming format) and the player architecture were the subject of documented decision work: a single-clock playback engine to guarantee sample-accurate inter-track synchronization, sliced streaming to bound the memory footprint, and iOS's real limits (codecs, per-page memory ceilings) verified browser by browser.

### Billing and lifecycles orchestrated by state machines

Stripe events land natively in AWS through an EventBridge partner bus: no webhook server to expose, sign or supervise. Two Step Functions state machines consume these events to keep customers and subscriptions up to date, enforce each tier's quotas and absorb failures with retries. Two more orchestrate account creation and the complete deletion of a user's data (subscription cancellation, DynamoDB purge, S3 purge), a compliance requirement handled as a durable workflow rather than a script.

### Industrialization and continuous quality

A complete environment recreates itself from scratch in a few documented commands. The deployment detects and fixes SAM's known drifts by itself, such as an alias that would keep pointing to stale code after an update of the shared code base. Quality rests on three tiers: unit tests, automated end-to-end tests in the browser, and a 21-section non-regression plan documented with versioned results.

## What this project demonstrates

**Serverless media processing.** A complete lossless transcoding pipeline (analysis, derivatives, waveforms, reconciliation) running entirely in Lambda, with a cost that strictly follows the volume processed.

**Event-driven AWS architecture.** S3, SNS, EventBridge (including a Stripe partner bus) and Step Functions composed into durable, replayable workflows, from a file upload to an account cancellation.

**Secure content delivery.** Signed CloudFront URLs, expiring share links with play and download statistics, CDN caching and per-subscription quotas: the artists' intellectual property protected without sacrificing listening fluidity.

**Cutting-edge web audio engineering.** Reasoned format decisions, a single-clock multitrack playback engine, measured iOS constraints: rare front-end expertise in the service of studio-grade requirements.

---

A SaaS platform, media processing or serverless architecture project in mind? [Let's talk](/en/contact).
