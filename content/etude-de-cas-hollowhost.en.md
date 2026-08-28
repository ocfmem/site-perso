+++
title = "HollowHost Case Study"
description = "How I designed HollowHost, a 100% serverless platform that turns a GitHub repository into a hosted AI agent on AWS. Event-driven architecture, IAM-enforced multi-tenant isolation and Terraform industrialization."
date = 2026-08-28T09:00:00+02:00
draft = false
url = '/en/projects/hollowhost/'
Toc = true
+++

[HollowHost](https://use.hollowhost.com/) is a SaaS I designed and built end to end. It turns a GitHub repository into a hosted AI agent: the user connects their repository, configures environment variables and secrets from a web dashboard, then triggers executions on demand or on a cron schedule. They never see the infrastructure. No instance to size, no CI pipeline to write, no image registry to manage.

The platform offers two execution modes: **AI Jobs**, on-demand or scheduled executions powered by AWS Lambda, and **Daemons**, persistent runtimes for agents that need to stay alive.

**The platform in numbers**

- **100%** serverless on the platform side
- **57** Lambda functions (API and workers)
- **22** Terraform modules
- **1** dedicated IAM role per deployed agent
- **3** environment families (dev, staging, production)

## The problem

The whole difficulty of the project fits in one sentence: *run arbitrary code, written by customers, in a way that is safe, automated and economically viable.*

That sentence breaks down into five structuring constraints.

**Untrusted code, isolated customers.** Every agent runs code I do not control. A faulty isolation would let one customer read another's data or secrets. It is the most critical risk of the product, and it cannot be patched in afterwards: it shapes the architecture.

**A leak-free secrets chain.** GitHub tokens and customer API keys travel through account creation, the Docker build and execution. None of these links may leave a secret in plain text, not in the database, not in an image layer, not in an API response.

**From Git repository to deployed service, with no human involved.** Repository validation, image build, permission creation and deployment must chain automatically, survive partial failures and report their state to the user in real time.

**A cost that follows usage.** Hundreds of agents can exist without ever running. The architecture had to bring the cost of an idle agent close to zero, which ruled out any model based on reserved servers.

**Reproducible environments.** Every developer, staging and production had to be able to spin up the same complete platform, with no configuration drift between them.

## The architecture

The architecture rests on a simple principle: every long operation is asynchronous, every trust boundary is materialized by IAM, and every resource exists in Terraform before it exists in AWS.

```text
 CloudFront + S3 + WAF ────▶ API Gateway + Cognito
          │
          │ authenticated requests
          ▼
 Lambda (Python) ────▶ DynamoDB ────▶ SQS
          │
          │ publishing an agent
          ▼
 Step Functions ────▶ CodeBuild ────▶ ECR ────▶ Container Lambda
          │
          │ on-demand or scheduled executions
          ▼
 EventBridge Scheduler ────▶ CloudWatch + SNS
```

### A fully serverless control plane

The API is served by API Gateway with JWT authentication through Cognito, and is only reachable through CloudFront thanks to an origin lock. Behind it, 57 Python Lambda functions (one per endpoint or queue consumer) share a common library shipped as a Lambda Layer: Pydantic models, data access repositories, observability via Lambda Powertools.

Data lives in a single DynamoDB table using single-table design, where tenant isolation is enforced by IAM rather than by application code. In other words, isolation is not a convention every developer has to remember to follow: AWS enforces it.

### A delivery pipeline orchestrated by Step Functions

Publishing an agent triggers a state machine that chains the creation of the ECR registry, the Docker image build by CodeBuild (customer repository clone, dependencies, packaging), the creation of the dedicated permissions, then the creation or update of the containerized Lambda function.

Each transition updates the status in the database (`VALIDATING → VALIDATED → DEPLOYING → DEPLOYED`), which gives the user real-time visibility and the system a resume point in case of failure. Upstream steps (GitHub access validation, queuing the publication) go through SQS, so no API call ever blocks on a long operation.

### Per-tenant security, enforced by IAM

Every deployed agent receives **its own IAM execution role**, generated from its identifiers and built on least privilege: it only accesses its own resources (its secrets, its logs, its image, its data partition), and nothing in its policy allows it to move laterally in the account or escalate its privileges.

Secrets follow the same principle. Encrypted at rest, they are injected at build time without ever being written into image layers (via Docker build secrets), and the API never returns their values, only masked versions. GitHub tokens are neither stored in the database nor returned by the API.

### Scheduling, execution and observability

Scheduled executions rely on EventBridge Scheduler, with per-agent cron scheduling synchronized on every configuration update. Every run is traced in the database (status, duration, paginated history), its logs are isolated per agent in CloudWatch and surfaced in the dashboard. On the operations side, CloudWatch alarms wired to SNS notify the team of platform anomalies.

### Full industrialization through Terraform

The entire infrastructure is described in 22 Terraform modules, one per AWS service, composed into three environment families: a complete environment per developer, a staging and a production. Remote state on S3, deployments tooled through a Makefile (backend build, frontend build injected with Terraform outputs, CloudFront invalidation), and tested guardrails: drift between declared IAM policies and those actually in place is detected by the test suite, in both directions.

## What this project demonstrates

**Event-driven serverless architecture.** API Gateway, Lambda, single-table DynamoDB, SQS and Step Functions composed into an asynchronous system where cost strictly follows usage and no operation ever blocks the user.

**Multi-tenant security engineering.** Isolation through a per-agent generated IAM role, systematic least privilege and a Docker build chain with no secret leakage.

**Industrialized Infrastructure as Code.** 22 Terraform modules, environments reproducible on demand, policy drift tests and a deployment chain tooled all the way to CDN invalidation.

**Full-stack product platform.** A React 19 dashboard (TanStack Query, Cognito/Amplify) and a Pydantic-typed Python backend, covered by unit and property-based tests. Cloud expertise in the service of a usable product.

---

A SaaS platform, serverless architecture or AI agent project in mind? [Let's talk](/en/contact).
