+++
title = "Étude de cas HollowHost"
description = "Comment j'ai conçu HollowHost, une plateforme 100 % serverless qui transforme un dépôt GitHub en agent IA hébergé sur AWS. Architecture événementielle, isolation multi-tenant par IAM et industrialisation Terraform."
date = 2026-08-28T09:00:00+02:00
draft = false
url = '/projets/hollowhost/'
Toc = true
+++

[HollowHost](https://use.hollowhost.com/) est un SaaS que j'ai conçu et développé de bout en bout. Il transforme un dépôt GitHub en agent IA hébergé: l'utilisateur connecte son dépôt, configure ses variables d'environnement et ses secrets depuis un dashboard web, puis déclenche des exécutions à la demande ou sur planification cron. Il ne voit jamais l'infrastructure. Pas d'instance à dimensionner, pas de pipeline CI à écrire, pas de registre d'images à gérer.

La plateforme propose deux modes d'exécution: les **AI Jobs**, des exécutions à la demande ou planifiées portées par AWS Lambda, et les **Daemons**, des runtimes persistants pour les agents qui doivent rester en vie.

**La plateforme en chiffres**

- **100 %** serverless côté plateforme
- **57** fonctions Lambda (API et workers)
- **22** modules Terraform
- **1** rôle IAM dédié par agent déployé
- **3** familles d'environnements (dev, staging, production)

## Le problème

Toute la difficulté du projet tient en une phrase: *exécuter du code arbitraire, écrit par des clients, de façon sûre, automatisée et économiquement viable.*

Cette phrase se décompose en cinq contraintes structurantes.

**Du code non maîtrisé, des clients cloisonnés.** Chaque agent exécute du code que je ne contrôle pas. Une isolation défaillante permettrait à un client de lire les données ou les secrets d'un autre. C'est le risque le plus critique du produit, et il ne se traite pas après coup: il conditionne l'architecture.

**Une chaîne de secrets sans fuite.** Tokens GitHub et clés API des clients traversent la création du compte, le build Docker et l'exécution. Aucun de ces maillons ne doit laisser un secret en clair, ni en base, ni dans une couche d'image, ni dans une réponse API.

**Du dépôt Git au service déployé, sans humain.** Validation du dépôt, build de l'image, création des permissions et déploiement doivent s'enchaîner automatiquement, survivre aux échecs partiels et rendre compte de leur état en temps réel à l'utilisateur.

**Un coût qui suit l'usage.** Des centaines d'agents peuvent exister sans s'exécuter. L'architecture devait ramener le coût d'un agent inactif à quasi zéro, ce qui excluait tout modèle à base de serveurs réservés.

**Des environnements reproductibles.** Chaque développeur, le staging et la production devaient pouvoir instancier la même plateforme complète, sans dérive de configuration entre eux.

## L'architecture

L'architecture repose sur un principe simple: chaque opération longue est asynchrone, chaque frontière de confiance est matérialisée par IAM, et chaque ressource existe dans Terraform avant d'exister dans AWS.

```text
 CloudFront + S3 + WAF ────▶ API Gateway + Cognito
          │
          │ requêtes authentifiées
          ▼
 Lambda (Python) ────▶ DynamoDB ────▶ SQS
          │
          │ publication d'un agent
          ▼
 Step Functions ────▶ CodeBuild ────▶ ECR ────▶ Lambda conteneur
          │
          │ exécutions à la demande ou planifiées
          ▼
 EventBridge Scheduler ────▶ CloudWatch + SNS
```

### Un plan de contrôle entièrement serverless

L'API est portée par API Gateway avec authentification JWT via Cognito, et n'est joignable qu'à travers CloudFront grâce à un verrou d'origine. Derrière, 57 fonctions Lambda Python (une par endpoint ou consommateur de file) partagent une bibliothèque commune déployée en Lambda Layer: modèles Pydantic, repositories d'accès aux données, observabilité via Lambda Powertools.

Les données vivent dans une table DynamoDB unique en modélisation single-table, où le cloisonnement par tenant est appliqué par IAM plutôt que par du code applicatif. Autrement dit, l'isolation n'est pas une convention que chaque développeur doit penser à respecter: c'est AWS qui la fait respecter.

### Un pipeline de livraison orchestré par Step Functions

Publier un agent déclenche une machine d'états qui enchaîne la création du registre ECR, le build de l'image Docker par CodeBuild (clone du dépôt client, dépendances, packaging), la création des permissions dédiées, puis la création ou la mise à jour de la fonction Lambda conteneurisée.

Chaque transition met à jour le statut en base (`VALIDATING → VALIDATED → DEPLOYING → DEPLOYED`), ce qui donne à l'utilisateur une visibilité en temps réel et au système un point de reprise en cas d'échec. Les étapes amont (validation d'accès GitHub, mise en file de la publication) passent par SQS, de sorte qu'aucun appel API ne bloque jamais sur une opération longue.

### Une sécurité par tenant, appliquée par IAM

Chaque agent déployé reçoit un **rôle d'exécution IAM qui lui est propre**, généré à partir de ses identifiants et construit sur le moindre privilège: il n'accède qu'à ses propres ressources (ses secrets, ses logs, son image, sa partition de données), et rien dans sa politique ne lui permet de se déplacer latéralement dans le compte ni d'élever ses privilèges.

Les secrets suivent le même principe. Chiffrés au repos, ils sont injectés au moment du build sans jamais être écrits dans les couches d'image (via les secrets de build Docker), et l'API ne retourne jamais leurs valeurs, seulement des versions masquées. Les tokens GitHub ne sont ni stockés en base ni renvoyés par l'API.

### Ordonnancement, exécution et observabilité

Les exécutions planifiées s'appuient sur EventBridge Scheduler, avec une planification cron gérée par agent et synchronisée à chaque mise à jour de configuration. Chaque run est tracé en base (statut, durée, historique paginé), ses logs sont isolés par agent dans CloudWatch et restitués dans le dashboard. Côté exploitation, des alarmes CloudWatch branchées sur SNS notifient l'équipe des anomalies de la plateforme.

### Une industrialisation complète par Terraform

L'intégralité de l'infrastructure est décrite dans 22 modules Terraform, un par service AWS, composés en trois familles d'environnements: un environnement complet par développeur, un staging et une production. État distant sur S3, déploiements outillés par Makefile (build backend, build frontend injecté avec les outputs Terraform, invalidation CloudFront), et garde-fous testés: la dérive entre les politiques IAM déclarées et celles réellement en place est détectée par la suite de tests, dans les deux sens.

## Ce que ce projet démontre

**Architecture serverless événementielle.** API Gateway, Lambda, DynamoDB single-table, SQS et Step Functions composés en un système asynchrone où le coût suit strictement l'usage et où aucune opération ne bloque l'utilisateur.

**Ingénierie de sécurité multi-tenant.** Isolation par rôle IAM généré par agent, moindre privilège systématique et chaîne de build Docker sans fuite de secrets.

**Infrastructure as Code industrialisée.** 22 modules Terraform, environnements reproductibles à la demande, tests de non-dérive des politiques et chaîne de déploiement outillée jusqu'à l'invalidation CDN.

**Plateforme produit full-stack.** Un dashboard React 19 (TanStack Query, Cognito/Amplify) et un backend Python typé Pydantic, testés par tests unitaires et property-based. L'expertise cloud au service d'un produit utilisable.

---

Un projet de plateforme SaaS, d'architecture serverless ou d'agents IA ? [Parlons-en](/contact).
