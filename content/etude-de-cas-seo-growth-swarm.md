+++
title = "Étude de cas SEO Growth Swarm"
description = "Comment j'ai conçu un système de 11 AI agents autonomes qui produit et publie un article de blog par jour ouvré, sans humain dans la boucle. Architecture multi-agents sans orchestrateur, boucles LLM bornées et publication automatique avec fusibles."
date = 2026-09-09T09:00:00+02:00
draft = false
url = '/projets/seo-growth-swarm/'
Toc = true
+++

Le SEO Growth Swarm est un système de **11 AI agents autonomes** que j'ai conçu et développé de bout en bout. Il prend en charge le growth SEO complet d'un produit, de la veille concurrentielle à l'article déployé en production: analyse de performance, découverte de sujets, planification éditoriale, recherche, rédaction, illustration, contrôle qualité, publication et surveillance. Le rythme est d'**un article par jour ouvré, sans humain dans la boucle**.

Il n'y a pas d'orchestrateur, pas de chef d'orchestre, pas de file d'attente entre les agents. Chaque AI agent est réveillé par son propre cron, constate l'état partagé, décide s'il a quelque chose à faire, écrit son résultat et se termine. Le pipeline émerge de l'état, pas d'un contrôleur central.

Le système tourne aujourd'hui en production sur deux produits distincts, avec le même code déployé deux fois et zéro ligne de code spécifique à l'un ou à l'autre.

**Le swarm en chiffres**

- **11** AI agents autonomes, déployés en **14** jobs planifiés
- **3** lignes de production parallèles: préparation, contenu, surveillance
- **1** article publié par jour ouvré, quota garanti par le système
- **2** produits servis par le même code, différenciés par une seule variable d'environnement
- **0** humain dans la boucle d'exécution: le contrôle est en amont, dans la configuration

## Le problème

Faire tourner une chaîne éditoriale entièrement automatisée n'est pas un problème de prompt. C'est un problème de fiabilité, et il tient en quatre contraintes structurantes.

**Un LLM laissé seul produit du contenu médiocre, et personne ne le voit.** En full-auto, il n'existe aucun relecteur pour sanctionner la paresse rédactionnelle, l'hallucination chiffrée ou l'article qui répète le précédent. La qualité doit donc être une propriété *mécanique* du système, pas une espérance placée dans le modèle.

**Une boucle d'agents non bornée est une bombe à retardement.** Itérer jusqu'à satisfaction signifie, en production, un run qui ne finit jamais et une facture qui ne s'arrête pas. Chaque boucle devait avoir un plafond d'itérations explicite et un budget temps, dans une plateforme qui tue brutalement tout job au bout de 900 secondes.

**Une publication automatique touche un dépôt de production.** L'agent qui publie écrit dans le monorepo d'une application réelle. Une erreur de périmètre, un fichier committé hors du dossier du blog, n'est pas un article raté: c'est un incident.

**Un système de 14 jobs indépendants ne peut présumer d'aucun ordre.** Un cron peut être skippé, un run peut échouer, un retry peut se produire n'importe quand. Sans idempotence stricte, le système produit des doublons ou se bloque.

## L'architecture

Le principe fondateur est le **pattern blackboard**: les AI agents ne se parlent jamais directement. Ils lisent et écrivent un état partagé structuré, ici Notion, qui sert à la fois de bus de coordination, de base de configuration et d'interface humaine.

```text
 CONFIGURATION (éditée par l'humain, en amont)
 règles · garde-fous · champ sémantique · charte · seuils · backlog
        │
        ▼
 PRÉPARATION (dimanche)
 competitor-watch ────▶ reporter + explorer ────▶ strategist ────▶ planner
        │
        │ planning éditorial figé, un sujet par jour ouvré
        ▼
 PRODUCTION (lundi au vendredi)
 writer (research ────▶ write) ────▶ illustrator ────▶ strategist (gate unique)
        │
        │ draft approuvé
        ▼
 PUBLICATION
 executor ────▶ branche git ────▶ CI ────▶ article en ligne ────▶ Slack
        │
        ▼
 SURVEILLANCE (continue, sans LLM)
 watcher · backlink-scout · tech-auditor ────▶ Slack (alertes)
```

### Proposer, valider, exécuter: trois familles d'AI agents

La séparation des rôles est ce qui rend le système sûr.

Les agents qui **proposent** (reporter, explorer, competitor-watch) analysent des données réelles: Search Console, Plausible, positions, SERP concurrentes. Ils émettent des propositions typées, et ne décident jamais.

Le **strategist est le gate unique** du swarm. Lui seul valide, pour les trois lignes de production, les actions d'optimisation, les sujets d'articles et les drafts complets. Il applique les règles écrites par l'humain dans la configuration.

Les agents qui **exécutent** n'ont aucun LLM. Ils appliquent mécaniquement des décisions déjà validées, avec des fusibles. Quatre des onze agents sont d'ailleurs entièrement déterministes (surveillance, exécution, veille backlinks, audit technique): le LLM n'est utilisé que là où il apporte du jugement.

### Un gate de qualité en deux étages

C'est la réponse à la première contrainte. Avant qu'un article puisse être publié, il passe un contrôle en deux temps.

Le premier étage ne consomme **aucun token**: frontmatter complet, longueur dans les bornes, illustration présente, liens internes vérifiés contre l'index réel du corpus, aucune URL inventée. Un défaut mécanique est détecté sans jamais appeler un modèle.

Le second étage est une **grille LLM à six critères** (intention de recherche, exactitude, originalité, absence de *slop*, ton, maillage) avec un seuil de 0,7 sur chacun. L'évaluation se fait avec en contexte le **référentiel d'exactitude**: les faits sourcés collectés pendant la phase de recherche. Un chiffre absent du dossier de recherche est une hallucination, et il est traité comme telle.

En cas de rejet, le gate produit des remarques actionnables, déclenche **une** révision par le module d'écriture, puis re-valide. Un seul cycle, jamais deux: au second échec, l'article est écarté et l'anomalie escalade sur Slack.

### Rédacteur et critique ne sont pas le même modèle

L'agent rédacteur suit le pattern **STORM** (Stanford): la qualité vient de la profondeur du *pre-writing*, pas de la boucle de rédaction. Il travaille en deux phases séparées, portées par deux jobs distincts: une phase de recherche agentique qui produit un dossier de faits sourcés, puis une phase de rédaction qui n'a plus le droit d'aller chercher d'information ailleurs que dans ce dossier.

La rédaction elle-même oppose trois rôles LLM distincts: un **rédacteur**, un **critique** instancié sur un modèle différent avec son propre prompt (pour casser l'auto-complaisance d'un modèle qui se relit lui-même) et un **fact-checker** qui confronte chaque affirmation chiffrée au référentiel. La boucle rédaction et critique est bornée à deux révisions, la boucle de recherche à cinq itérations avec budget temps surveillé. Jamais de boucle jusqu'à satisfaction.

Le choix des modèles est **du routage par rôle**, pas une constante de code: chaque rôle (analyse, validation, tâches mécaniques, rédaction) lit son identifiant de modèle dans une variable d'environnement. Monter la rédaction sur un modèle frontier ou redescendre la validation sur un modèle économique se fait sans redéploiement.

### Une publication automatique avec fusibles

L'agent de publication clone le dépôt du produit en *shallow* et *sparse-checkout* du seul sous-dossier du blog, crée une branche `release_blog/<timestamp>`, y committe l'article et ses assets, puis déclenche le workflow de déploiement par l'API GitHub.

Deux fusibles encadrent l'opération. Un **fusible de périmètre**: tout fichier committé doit être sous le sous-dossier du blog, sinon le run échoue et alerte. Le swarm est structurellement incapable de toucher au code de l'application. Et un **fusible d'intégration**: la CI. L'agent surveille le run par polling, vérifie que l'URL de l'article répond réellement en ligne, et ne merge la branche dans `main` qu'après cette confirmation. `main` reste la référence exacte de ce qui est en production, et une branche en échec reste en place pour le debug sans jamais l'avoir touché.

### Idempotence et dégradation propre

Aucun agent ne présume de l'ordre d'exécution. Les pages d'état sont créées en *get or create*, l'agent de publication cherche les drafts approuvés et non publiés via une machine à états persistée, le backlog de sujets est un registre à statuts. Un retry est toujours sûr, un job rejoué ne duplique rien.

Quand une entrée manque (semaine jamais préparée, jour sans sujet planifié, source de données indisponible), l'agent fait un *no-op* propre ou dégrade en notant la source manquante, mais produit toujours son artefact. Chaque escalade Slack contient une ligne « Défaut »: ce qui se passera si personne n'agit. Le système ne se bloque jamais en attente d'un humain.

### Multi-tenant: zéro code sur mesure

L'identité du produit servi (domaine, dépôt du blog, landing pages, concurrents, mots-clés suivis, langue, ton, charte visuelle, quotas) n'existe **nulle part dans le code**. Elle vient d'une unique variable d'environnement qui pointe vers l'espace de configuration du produit.

Déployer le swarm sur un nouveau produit, c'est un fichier d'environnement et une arborescence de configuration générée par script. Une demi-journée, dont l'essentiel en attentes externes, et aucune ligne de code modifiée. C'est ce qui a permis de passer d'un produit à deux sans toucher au dépôt.

## La stack

Python 3.13 et **LangGraph** (`StateGraph` avec état Pydantic), sorties structurées Pydantic sur **tous** les appels LLM avec repli déterministe si le modèle échoue, API OpenAI avec routage de modèles par rôle. Notion pour la coordination et la configuration. Google Search Console, Plausible, DataForSEO et BrightData pour l'observation et la recherche. API GitHub pour la publication, Slack pour la réception humaine. Génération d'images pour les visuels et graphiques en SVG pur généré en code. Le tout déployé en jobs planifiés sur [HollowHost](/projets/hollowhost/).

## Ce que ce projet démontre

**Ingénierie de systèmes multi-agents.** Onze AI agents autonomes coordonnés par un état partagé selon le pattern blackboard, sans orchestrateur ni communication directe, avec une séparation stricte entre proposer, valider et exécuter, et un gate de validation unique.

**Fiabilité des systèmes à base de LLM.** Boucles bornées avec plafond d'itérations et budget temps, sorties structurées avec repli déterministe, séparation du rédacteur, du critique et du fact-checker sur des modèles distincts, contrôle qualité à deux étages dont un entièrement sans token, et routage de modèles par rôle piloté par configuration.

**Automatisation de bout en bout avec fusibles.** Une chaîne qui va de la donnée d'observation à l'article déployé en production sans intervention humaine, protégée par un fusible de périmètre au niveau du dépôt, la CI comme fusible d'intégration, et une idempotence stricte qui rend tout retry sûr.

**Architecture produit multi-tenant.** Un seul dépôt, zéro code spécifique par client, toute l'identité du service portée par la configuration. Le tout validé par un second déploiement en production sans modification de code.

---

Un projet d'AI agents, d'automatisation LLM ou d'architecture multi-agents ? [Parlons-en](/contact).
