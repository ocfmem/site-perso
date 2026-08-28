+++
title = "Étude de cas SoundisiaK Studio"
description = "Comment j'ai construit SoundisiaK Studio, une plateforme SaaS de collaboration musicale 100 % serverless sur AWS. Workspaces et versions de morceaux, pipeline audio lossless en Lambda, streaming multipiste sécurisé et facturation orchestrée."
date = 2026-08-04T09:00:00+02:00
draft = false
url = '/projets/soundisiak/'
Toc = true
+++

[SoundisiaK Studio](https://soundisiak.studio/) est une plateforme de collaboration pour la production musicale que j'ai conçue et développée en tant que CTO. Elle donne aux artistes, producteurs et équipes de production un studio partagé dans le navigateur: on y organise ses projets, on y dépose ses morceaux, on les écoute en multipiste et on avance ensemble jusqu'au mix final.

**La plateforme en chiffres**

- **100 %** serverless, aucun serveur exploité
- **28** fonctions Lambda
- **7** stacks CloudFormation imbriquées
- **4** machines d'états Step Functions
- **21** sections de plan de test documentées

## Un studio organisé en workspaces

Tout part du workspace, l'espace de travail d'une entité artistique, qu'il s'agisse d'un artiste solo ou d'un groupe. Il rassemble les membres et leurs rôles, les projets (albums, EP, singles), leurs morceaux et l'historique complet des versions audio de chaque titre. La version est l'objet central du produit: chaque itération d'un morceau vient s'empiler dans un historique que l'on réécoute, compare et commente, plutôt que de s'éparpiller en fichiers `final_v3_vraiment_final.wav` dans un drive.

La collaboration suit la réalité d'une production. Les membres d'un workspace disposent de rôles gradués selon leurs responsabilités. Un intervenant extérieur, un ingénieur de mix ou un musicien invité par exemple, peut être convié sur un morceau précis sans accéder au reste du catalogue. Chaque titre porte son état d'avancement, du concept au mix final, ce qui donne en un coup d'œil la photographie d'un album en cours.

Le partage vers l'extérieur est une fonctionnalité à part entière. Une version s'envoie par un lien d'écoute sécurisé, à durée de vie limitée, qui expire de lui-même. Et chaque lien vit avec ses statistiques: écoutes et téléchargements sont comptabilisés, si bien que l'on sait si le mix envoyé à un label ou à un manager a vraiment été ouvert, et combien de fois.

La qualité, elle, ne se négocie pas: la plateforme travaille en lossless de bout en bout, du fichier déposé jusqu'à l'écoute dans le navigateur. Ce que l'on entend est fidèle au mix, pas une version dégradée pour le web.

Le modèle économique repose sur des abonnements Stripe à plusieurs paliers, avec des quotas appliqués par plan: stockage, nombre de projets, membres, versions par morceau.

## Le problème

Sous cette expérience de studio partagé, toute la difficulté technique tient à la matière première: *de l'audio professionnel non compressé, lourd, versionné, qu'il faut traiter, protéger et diffuser en streaming multipiste, sans exploiter le moindre serveur.*

Cette matière première impose cinq contraintes structurantes.

**L'audio lossless comme point de départ.** Des fichiers WAV de plusieurs dizaines de Mo par minute et par piste, à ingérer, analyser et convertir en dérivés de diffusion, dans un environnement Lambda éphémère, sans ferme de transcodage.

**Un streaming multipiste synchronisé à l'échantillon près.** Le lecteur doit jouer plusieurs pistes parfaitement alignées, y compris sur iOS, où les codecs disponibles et les plafonds mémoire par page imposent une ingénierie audio spécifique.

**Diffuser des fichiers privés sans les exposer.** Les morceaux appartiennent aux artistes: aucun bucket public, mais des écoutes fluides pour les ayants droit et des liens de partage qui expirent d'eux-mêmes.

**Une facturation fiable sans serveur de webhooks.** Le cycle de vie des abonnements (souscription, changement de palier, résiliation, échec de paiement) doit être traité de manière durable et rejouable, sans endpoint webhook à héberger et surveiller.

**Des déploiements reproductibles et sûrs.** Sept stacks CloudFormation interdépendantes et des versions de fonctions à garder cohérentes: la moindre dérive silencieuse devait être détectée et corrigée automatiquement.

## L'architecture

L'architecture suit le trajet du son: un fichier déposé déclenche des événements, des traitements et des dérivés, jamais un serveur qui attend. Chaque frontière (authentification, diffusion, facturation) est confiée au service AWS managé qui la porte le mieux.

Tout y est piloté par les événements. Une action utilisateur traverse le CDN et une API managée, et n'exécute que des fonctions éphémères, facturées à la milliseconde. Le dépôt d'un fichier audio publie un événement qui réveille la chaîne de traitement et produit les dérivés d'écoute. Les cycles de vie longs, la facturation comme les comptes, sont portés par des machines d'états durables qui consomment les événements au fil de l'eau et reprennent après un échec. Aucune brique n'attend: chacune se réveille sur un événement, fait sa part et s'éteint.

### Un plan de contrôle serverless en stacks composées

Le backend est décrit en AWS SAM sous forme de sept stacks CloudFormation imbriquées (bases de données, stockage, fonctions, authentification, API, événements, machines d'états), déployées en un seul geste dans un ordre de dépendances garanti. Vingt-huit fonctions Lambda Python se partagent un socle de code commun, et l'authentification est déléguée à Cognito, connexion Google comprise. Le découpage en stacks a exigé de résoudre de vraies dépendances circulaires entre ces briques.

### Un pipeline audio piloté par événements

Le dépôt d'un fichier dans le bucket audio publie un événement SNS qui déclenche le traitement: une Lambda conteneurisée arm64 embarque une chaîne de transcodage dont la version et l'empreinte sont épinglées au build, pour qu'elle ne change jamais silencieusement. Elle analyse le fichier, produit les dérivés de diffusion lossless et les données de forme d'onde. Une fonction de réconciliation détecte et régénère les dérivés manquants, et les cycles de vie S3 alimentent le nettoyage automatique (corbeille, fichiers publics expirés).

```text
 Upload WAV ──▶ S3 (événement SNS)
    └─▶ Lambda de transcodage (conteneur arm64)
         └─▶ dérivés FLAC par tranches + formes d'onde ──▶ S3
              └─▶ écoute via URL signée CloudFront (durée limitée)
```

### Une diffusion privée et un lecteur multipiste exigeant

Aucun fichier audio n'est public: la diffusion passe par des URLs CloudFront signées, adossées à un key group dédié, avec des liens de partage à expiration automatique. La mise en cache CDN fait le reste: les dérivés d'écoute sont servis au plus près de l'auditeur, la lecture démarre immédiatement et les écoutes répétées d'un même morceau ne re-sollicitent ni le stockage ni le traitement. Côté lecture, le choix des formats (abandon du mp3, WAV comme source de vérité, FLAC comme format de diffusion) et l'architecture du lecteur ont fait l'objet d'un travail de décision documenté: un moteur de lecture à horloge unique pour garantir une synchronisation inter-pistes à l'échantillon près, un streaming par tranches pour borner l'empreinte mémoire, et la prise en compte des limites réelles d'iOS (codecs, plafonds mémoire par page) vérifiées navigateur par navigateur.

### Facturation et cycles de vie orchestrés par machines d'états

Les événements Stripe arrivent nativement dans AWS via un bus partenaire EventBridge: aucun serveur de webhooks à exposer, signer ou superviser. Deux machines d'états Step Functions consomment ces événements pour tenir à jour clients et abonnements, appliquer les quotas de chaque palier et absorber les échecs avec reprise. Deux autres orchestrent la création de compte et la suppression complète des données d'un utilisateur (résiliation des abonnements, purge DynamoDB, purge S3), une exigence de conformité traitée comme un workflow durable plutôt que comme un script.

### Industrialisation et qualité continue

Un environnement complet se recrée depuis zéro en quelques commandes documentées. Le déploiement détecte et corrige lui-même les dérives connues de SAM, comme un alias qui continuerait de pointer vers du code obsolète après une mise à jour du socle commun. La qualité s'appuie sur trois étages: tests unitaires, tests bout en bout automatisés dans le navigateur, et un plan de non-régression de 21 sections documentées avec leurs résultats versionnés.

## Ce que ce projet démontre

**Traitement média serverless.** Un pipeline de transcodage lossless complet (analyse, dérivés, formes d'onde, réconciliation) exécuté entièrement en Lambda, avec un coût qui suit strictement le volume traité.

**Architecture événementielle AWS.** S3, SNS, EventBridge (dont bus partenaire Stripe) et Step Functions composés en workflows durables et rejouables, de l'upload d'un fichier à la résiliation d'un compte.

**Diffusion sécurisée de contenus.** URLs signées CloudFront, liens de partage expirants avec statistiques d'écoute et de téléchargement, mise en cache CDN et quotas par abonnement: la propriété intellectuelle des artistes protégée sans sacrifier la fluidité d'écoute.

**Ingénierie audio web de pointe.** Décisions de formats argumentées, moteur de lecture multipiste à horloge unique, contraintes iOS mesurées: une expertise front-end rare au service d'une exigence de studio.

---

Un projet de plateforme SaaS, de traitement média ou d'architecture serverless ? [Parlons-en](/contact).
