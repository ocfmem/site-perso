+++
title = "Projects"
slug = "projects"
description = "Selection of SaaS, cloud and AI projects on which Meddy Menzikoff was involved in architecture and delivery."
date = 2026-05-06T15:10:00+02:00
draft = false
Toc = false
+++
Some examples of projects on which I have had the opportunity to work in recent years.

## [HollowHost](https://hollowhost.com/)
**Role: Cloud & AI Architect / SaaS Lead Developer**

*HollowHost is a platform for deploying AI agents in one click from a GitHub repository, without managing the underlying infrastructure.*

Design and development of a SaaS dedicated to hosting AI agents.

Main contributions:
- Definition of a **100% serverless stack** (API Gateway, Lambda, DynamoDB, Step Functions).
- Design of the **multi-tenant isolation** model, with a dedicated IAM role per deployed agent.
- Automated deployment pipeline, from GitHub repository to production service with no human involved.
- **Terraform** industrialization (22 modules, reproducible environments).
- Development of business agents (Google Ads, SEO, daily reporting).

**[Read the full case study →](/en/projects/hollowhost/)**

## [SoundisiaK Studio](https://soundisiak.studio/)
**Role: CTO and AWS serverless expert**

*SoundisiaK Studio is a collaboration platform for music production, with version management, workflow monitoring and teamwork.*

Overall technical support on the development and AWS Cloud Architecture of the product.

Main contributions:
- Implementation of a **100% serverless stack**: Cognito, API Gateway, Lambda (Python + Docker), Step Functions, DynamoDB, S3, EventBridge, SQS/SNS.
- Design of the **serverless audio pipeline** (containerized transcoding in Lambda, lossless derivatives, waveforms).
- Private delivery through **signed CloudFront URLs**, with automatically expiring share links.
- **Stripe** integration through an EventBridge partner bus and state machines (subscriptions, quotas).
- Management of Front-End development in **Next.js** (TypeScript) and implementation of CI/CD pipelines (GitHub Actions).

**[Read the full case study →](/en/projects/soundisiak/)**

## [AdsWizard](https://www.adswizard.com/fr/)
**Role: AWS & Data Platform Architect**

*AdsWizard is an AI-powered advertising intelligence platform that collects and analyzes digital ads in real time (Meta, Google Ads, TikTok, YouTube).*

Complete audit and design of a large-scale data platform.

Main contributions:
- AWS usage audit and compliance plan (security, IAM).
- Design and deployment of **Data Pipeline V2**: ECS, S3 Data Store, Firehose, SNS/SQS, Athena, Glue, Quicksight, Lambda.
- Preparing the platform for scaling up x10 to x100 on data ingestion and processing.

## [Politês](https://www.polites.app/)
**Role: Technical Architect / Database**

*Politês is a real-time citizen survey application that allows users to ask questions, respond freely and visualize opinions.*

Supporting the team on the backend and the design of the data model.

Main contributions:
- Design and optimization of complex SQL queries on **PostgreSQL**.
- Data security and user anonymization.
- Deployment and architecture on **Google Cloud Platform (GCP)**.

## [Listn](https://pitch.listn.live/)
**Role: CTO / DevOps Azure**

*Listn is a music promotion platform that connects artists to radio stations, playlists and influencers globally.*

Support for cloud infrastructure consolidation, front-end redesign and the development of new features.

Main contributions:
- Management of the front-end redesign in **Next.js**.
- Environment provisioning with Terraform and deployment on **Microsoft Azure**.
- Development of new backend features (Node.js, PostgreSQL).
