+++
title = "Le protocole a renoncé."
description = "Le 28 juillet, MCP a répondu à ses critiques en s'amputant. Ce que la spécification retire, pourquoi, et ce que ça change vraiment au débat MCP contre CLI."
date = 2026-08-30T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Développement"]
+++

Le 28 juillet, au moment où la France était écrasée sous sa troisième canicule de l'année, le Model Context Protocol a connu une montée de version de ses spécifications. Et bien entendu... C'était le cadet de nos soucis.

Mais maintenant que la température est retombée, il était temps pour moi de regarder ce que ça donnait. Et je n'ai pas été déçu.

Cette révision arrive huit mois après la précédente. Rapporté à un protocole né en novembre 2024, c'est plus du tiers de son existence. Ce n'est donc pas une sortie précipitée, c'est le plus long chantier de son histoire et il a une caractéristique que j'aime: ce qui est livré n'est pas de l'ajout, mais de la suppression.

Depuis février, la thèse dominante tenait en une phrase: MCP est lourd inutilement, donnez un terminal à l'agent et laissez-le travailler. Le protocole vient de répondre à ses critiques en s'amputant.

## Une spécification qui retire

Voici ce que la révision supprime:

- **les sessions et l'en-tête `Mcp-Session-Id`**, qui permettaient au serveur de se souvenir de vous d'un appel sur l'autre
- **le handshake `initialize` / `initialized`**, la négociation d'ouverture où client et serveur s'accordaient une bonne fois sur ce dont ils étaient capables
- **`ping`**, qui servait à vérifier que le lien tenait toujours
- **`logging/setLevel`**, par lequel le client réglait la verbosité du serveur, désormais indiquée requête par requête
- **la reprise de flux interrompu**, qui permettait de rattraper une réponse coupée en cours de route

Et voici ce qu'elle déprécie, avec un préavis de douze mois:

- **Roots**, par lequel le serveur apprenait quels répertoires de votre machine il avait le droit de regarder. À passer maintenant en paramètre d'outil
- **Sampling**, par lequel le serveur demandait au modèle du client de réfléchir pour lui. À remplacer par un appel direct à l'API d'un fournisseur
- **Logging**, le canal par lequel le serveur remontait ses journaux dans le client. À remplacer par `stderr` ou par OpenTelemetry

Prise ligne à ligne, la liste ressemble à du ménage. Lue d'un bloc, elle raconte autre chose.

Chacune de ces fonctionnalités supposait un serveur qui a ses entrées chez vous. Un serveur qui se souvient de vous, qui connaît votre système de fichiers, qui pilote vos journaux, et qui peut mobiliser votre modèle pour son propre compte. Mises ensemble, elles dessinaient un protocole qui voulait être l'environnement d'exécution de l'agent. **C'est cette ambition-là qui vient d'être abandonnée.**

Ce qui la remplace est d'une banalité assumée. Chaque requête se décrit désormais elle-même: version du protocole, capacités et identité du client voyagent dans un champ `_meta`, à chaque appel. Et un serveur qui a besoin de mémoire entre deux appels émet un identifiant explicite, que le modèle repasse comme un argument ordinaire. La spécification justifie le choix en une phrase que je trouve honnête: on a constaté que ça marchait mieux que de l'état caché dans le transport.

## Parce que ça ne se déployait pas

La raison n'a rien de théorique. Elle est opérationnelle, et elle est même assez humiliante pour un protocole qui se voulait moderne.

Avant, un serveur MCP distant exigeait des sticky sessions, un magasin de sessions partagé entre les instances, et une passerelle capable d'ouvrir le corps JSON des requêtes pour savoir quoi en faire. Autrement dit, tout ce qu'on a passé quinze ans à apprendre à ne plus faire.

Depuis le 28 juillet, n'importe quelle requête peut atterrir sur n'importe quelle instance. Un répartiteur de charge en tourniquet suffit. Le serveur tourne en serverless, descend à zéro quand il ne sert pas, et le redémarrage d'un conteneur devient invisible pour le client.

Prenons le cas concret, puisque c'est là que ça se joue. Faire tourner un serveur MCP sur une Lambda était déjà possible avant juillet, mais par contournement: soit un drapeau du SDK qui coupait la négociation de session, soit un magasin DynamoDB interrogé à chaque invocation pour simuler une continuité que le protocole exigeait et que Lambda ne sait pas offrir. Depuis le 28 juillet, l'absence d'état n'est plus un contournement, c'est la spécification, et une fonction derrière une Function URL devient un déploiement conforme. Avec une réserve: le nouveau canal de notifications est une réponse maintenue ouverte, et ça, ça ne rentrera jamais dans une Lambda.

Le détail que je trouve le plus révélateur est ailleurs, dans les en-têtes `Mcp-Method` et `Mcp-Name`, désormais obligatoires.

Jusqu'ici, tout transitait par la même URL et le même verbe. Lister les outils, appeler le plus anodin d'entre eux ou déclencher celui qui écrit en base, votre passerelle voyait la même chose: un `POST /mcp`. Pour faire la différence, il fallait ouvrir le corps JSON de chaque requête, ce qui est cher, fragile, et que personne ne faisait.

Désormais l'intention est écrite sur l'enveloppe. Votre limiteur de débit peut traiter `tools/call` autrement que `tools/list`, votre pare-feu peut refuser un outil nommément, votre facturation peut compter par outil. En clair, les équipes qui exploitent récupèrent leurs instruments habituels.

{{< schema-entetes >}}

C'est la première version de MCP écrite pour ceux qui font tourner la chose, pas pour ceux qui la démontrent. Elle a un prix, d'ailleurs: la rupture de compatibilité est assumée, un serveur en 2026-07-28 ne parlera pas à un client d'avant. La contrepartie est une politique de dépréciation formelle, avec douze mois minimum avant tout retrait. C'est peu glorieux et c'est exactement ce qui manquait.

## Le débat n'a pas été gagné, il a été déplacé

La critique adressée à MCP portait d'abord sur le coût en contexte, et elle était fondée.

Le 11 mars, à la conférence Ask 2026, Denis Yarats, cofondateur et directeur technique de Perplexity, annonçait que son entreprise s'en détournait en interne. Le chiffre a circulé partout: jusqu'à 72 % de la fenêtre de contexte consommée avant le premier message de l'utilisateur. Le cas concret derrière le pourcentage est plus parlant. Trois serveurs, GitHub, Playwright et une intégration d'IDE, représentaient 143 000 tokens de définitions d'outils sur un modèle qui en accepte 200 000. L'agent démarrait sa journée avec les trois quarts de sa mémoire déjà occupés par le mode d'emploi.

Anthropic avait mesuré la même chose de son côté, en ramenant 150 000 tokens à 2 000 par une réécriture des appels sous forme de code. Une réduction de 98,7 %.

La spécification de juillet répond à ce grief au niveau du protocole: durée de fraîcheur et portée de cache sur les listes d'outils, ordre de retour déterministe pour que les caches de prompt en amont fassent mouche. C'est la réponse ennuyeuse, et elle est nécessaire.

La réponse intéressante est datée du 31 juillet.

Simon Willison, cocréateur de Django et l'un des observateurs les plus lus du sujet, écrivait le 4 novembre 2025 ne plus utiliser MCP du tout avec ses agents de code, les utilitaires en ligne de commande faisant très bien le travail. Trois jours après la spécification, il publie un billet dont le titre dit l'essentiel: MCP sans état a ranimé son intérêt. Et son motif n'est pas la scalabilité. Il écrit que donner à un agent un shell avec accès à Internet est lourd de risques, là où les outils MCP sont plus faciles à auditer et à contrôler, et assez simples pour que de petits modèles tournant sur un portable arrivent à les piloter.

Celui qui disait « donnez un terminal à l'agent » revient en expliquant que le terminal est précisément la partie dangereuse.

Kenneth Sinder, qui a construit le serveur MCP hébergé de Notion, avait posé la même chose autrement dès juin: un bon CLI, c'est MCP avec des étapes en plus. Reconstruisez à la main la divulgation progressive, l'authentification cohérente, la sortie dense en tokens et la couche de compétences qui explique au modèle comment s'en servir, et vous n'avez pas échappé à MCP. Vous l'avez rebâti, sans le schéma, sans le registre, sans le vocabulaire commun, et vous devrez l'entretenir seul.

La différence qui survit à l'exercice tient en un mot: la **garde du secret**. Avec MCP, c'est le harnais qui détient les identifiants et les attache hors du canal du modèle. Le modèle produit des arguments, lit des résultats, et ne voit jamais la clé. Avec un CLI brut, la clé est posée dans l'environnement que le modèle manipule lui-même.

Et il faut dire tout de suite ce que ça ne règle pas, parce que Sinder a la correction de le préciser lui-même: MCP ne vous protège pas de l'injection de prompt. La surface d'injection, c'est le contenu qui revient de l'outil, pas le transport qui l'achemine. Un modèle autorisé à appeler un outil authentifié peut toujours être manipulé pour en abuser. Ce qui rétrécit, c'est la surface d'exfiltration d'identifiants. Ce n'est pas rien, ce n'est pas tout, et confondre les deux serait exactement le genre de raccourci qui fait vendre des solutions.

## Ce que le protocole ne décidera jamais à votre place

Dans le même billet du 31 juillet, Willison annonce `datasette-mcp`, un serveur qui expose trois outils. Le troisième s'appelle `execute_sql()`.

Le même jour, la même personne explique que MCP est plus auditable qu'un shell, et publie un serveur qui expose du SQL brut.

Ce n'est pas une contradiction, et je ne vais pas la traiter comme telle (surtout que je n'ai pas la prétention de jouer dans la même ligue). C'est la démonstration la plus nette qu'on puisse trouver: **le protocole ne décide de rien.** Il déplace la question du transport vers le périmètre, et le périmètre, personne ne l'écrira à votre place.

La bascule sans état rend d'ailleurs ce travail plus visible, parce qu'elle transforme trois problèmes de protocole en problèmes d'ingénierie ordinaires.

L'idempotence, d'abord. La reprise de flux a disparu: un flux coupé perd la requête en cours, et le client doit la réémettre avec un nouvel identifiant. Le protocole ne définit aucune clé d'idempotence. Si votre outil écrit quelque chose, le dédoublonnage est à vous.

L'état, ensuite. L'identifiant que vous émettez pour vous souvenir d'un appel sur l'autre transite maintenant par le contexte du modèle, donc il est visible par tout ce qui peut influencer ce contexte. Il doit être signé, lié à l'appelant, et daté d'une expiration. C'est de l'hygiène de jeton, un métier que nous connaissons déjà.

Le troisième point est celui qui m'intéresse le plus. Le nouveau motif d'échange en plusieurs tours permet à un serveur de répondre `input_required` plutôt qu'un résultat, en listant ce qu'il lui manque. Une confirmation, un paramètre, un arbitrage. Demander avant d'agir devient un type de réponse de premier rang, pas un bricolage de session.

C'est très exactement ce que je défends depuis des mois. On ne concède pas de l'autonomie à un agent, on lui conçoit un parcours. Le protocole vient de fournir le point d'arrêt normalisé qui manquait pour le faire proprement.

## Ce qu'un protocole gagne à rétrécir

Antoine de Saint-Exupéry écrivait ceci en 1939, dans *Terre des hommes*, à propos de l'avion:

> Il semble que la perfection soit atteinte non quand il n'y a plus rien à ajouter, mais quand il n'y a plus rien à retrancher.

MCP a cessé de vouloir être le système d'exploitation de l'agent. Il est resté son contrat.

Ce qui subsiste après l'amputation n'a rien de spectaculaire: une surface d'outils décrite, typée, découvrable, cachable, journalisable, et identique pour tout le monde. Un commentateur cité par InfoQ a résumé la version sceptique en une question qui mérite d'être posée: à ce compte-là, est-ce que ce n'est pas juste redevenu une API ?

Presque. Et c'est plutôt une bonne nouvelle.

Parce que la question n'a jamais été de savoir par quel tuyau passe l'appel. Elle a toujours été: qui détient la clé, qu'est-ce qu'on a le droit de me demander, et qui peut prouver ce qui a été demandé. Un terminal répond mal aux trois. Une API bien conçue y répond, à condition que quelqu'un ait fait le travail.

La spécification du 28 juillet n'a pas gagné le débat. Elle l'a concédé. Elle a rendu tout ce qu'on lui reprochait, et ce qui reste tient debout.

Un protocole qui accepte de rétrécir est un protocole qui compte durer.

## Sources

- [La spécification MCP 2026-07-28](https://blog.modelcontextprotocol.io/posts/2026-07-28/) et son [journal des changements](https://modelcontextprotocol.io/specification/2026-07-28/changelog) — la liste exacte des suppressions, des dépréciations et de la politique de cycle de vie.
- [Simon Willison, « Stateless MCP has recaptured my interest »](https://simonwillison.net/2026/Jul/31/stateless-mcp/) — le retournement du 31 juillet, et `datasette-mcp`.
- [Kenneth Sinder, « The battle for context: why MCP vs. CLI is the wrong fight »](https://ksinder.medium.com/the-battle-for-context-why-mcp-vs-cli-is-the-wrong-fight-3c0cb63849a4) — le CLI comme MCP avec des étapes en plus, et la distinction entre garde du secret et injection.
- [InfoQ — « MCP Goes Stateless, and Developers Ask Whether That Just Makes it an API Again »](https://www.infoq.com/news/2026/08/mcp-stateless-gateway/) — la lecture sceptique, et le routage par en-têtes vu depuis les passerelles.
- [Repello AI — « MCP vs CLI: What Perplexity's Move Actually Means for AI Security Teams »](https://repello.ai/blog/mcp-vs-cli) — l'annonce de Denis Yarats et le détail des 143 000 tokens.
- [Anthropic — « Code execution with MCP »](https://www.anthropic.com/engineering/code-execution-with-mcp) — les 150 000 tokens ramenés à 2 000.
- [Google Developers Blog — mise à l'échelle avec MCP sans état](https://developers.googleblog.com/scaling-ai-agent-infrastructure-with-the-mcp-stateless-updates/) — le versant exploitation, répartition de charge et serverless.
- [Equixly — « Stateless MCP: what the 2026-07-28 specification changes for security »](https://equixly.com/blog/2026/08/05/stateless-mcp/) — l'idempotence absente, et l'état devenu visible dans le contexte du modèle.
