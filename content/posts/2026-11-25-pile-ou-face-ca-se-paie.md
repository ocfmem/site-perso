+++
title = "Pile ou face, ça se paie."
description = "J'ai retiré une fonctionnalité de mon propre produit: héberger des agents autonomes donnait des résultats imprévisibles. Plus on confie de travail aux agents, plus un mauvais résultat coûte cher. Pourquoi un swarm borné a remplacé le harness."
date = 2026-11-25T09:00:00+01:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Développement"]
ogImage = "/img/swarm-agents-ia-hollowhost-og.jpg"
ogImageAlt = "Tableau de bord HollowHost d'un système de huit agents IA planifié chaque jour, avec huit exécutions quotidiennes réussies"
+++

J'ai retiré une fonctionnalité de mon propre produit.

HollowHost, la plateforme que je développe, transforme un dépôt GitHub en agent IA hébergé. Elle proposait deux façons de faire tourner un agent: des jobs, exécutés à la demande ou sur planification, et un mode daemon, qui gardait l'agent en vie en permanence. C'est dans ce second mode que tournaient les harnesses, ces agents généralistes comme Hermes, à qui l'on confie un objectif et qui se débrouillent pour l'atteindre.

On l'a supprimé.

Les résultats étaient mauvais, la consommation de tokens élevée dans cet usage, et surtout il y avait ce côté pile ou face: le même agent, sur la même tâche, réussissait un jour et échouait le lendemain, sans qu'on sache pourquoi. Pour une plateforme dont le métier est de faire tourner des agents, c'était une mauvaise publicité. Le mode daemon entachait l'image du produit.

Je connaissais déjà ce verdict. Avant de construire le système qui publie aujourd'hui un contenu optimisant le SEO par jour ouvré sans humain dans la boucle, j'avais confié ce même travail à Hermes. Même constat.

Ce que j'en ai retenu tient en une phrase: **le problème n'était pas l'outil. C'était l'endroit où vivait le contrôle.**

## La boîte noire

Un harness fait une chose remarquablement bien. Vous lui donnez un objectif, il raisonne, explore, appelle ses outils, se corrige. Quand vous êtes devant, que vous lisez ce qu'il fait et que vous le reprenez au bon moment, c'est un collaborateur impressionnant.

Cette même propriété devient un problème dès que personne ne regarde.

À six heures du matin, quand l'article ne sort pas, l'échec n'est pas une étape. C'est un paragraphe de raisonnement perdu quelque part dans un long contexte. Vous ne pouvez l'accrocher à rien, donc vous ne pouvez rien corriger: vous relancez, et vous espérez. C'est la définition exacte du pile ou face.

Et ce n'est pas qu'un problème de lisibilité. Une équipe de chercheurs, notamment de Stanford et de l'université du Michigan, a mesuré la consommation d'agents sur des tâches de code. Les modèles sous-estiment systématiquement ce qu'ils vont dépenser, et leurs prévisions ne sont au mieux que modérément corrélées à la réalité. L'agent ne sait pas ce qu'il va coûter. Vous non plus, jusqu'à ce qu'il ait fini.

Ce n'est pas un défaut de Hermes. C'est un usage qui n'est pas le sien. J'écrivais en août, à propos du protocole MCP, qu'une architecture se juge à une question simple: qui peut prouver ce qui a été demandé. Un harness laissé seul n'y répond pas.

## Plus de tokens ne fait pas un meilleur texte

Pour rattraper la qualité, on ajoute des boucles de validation. L'agent relit son texte, le juge, le réécrit, le relit. À chaque passe, c'est le même modèle qui se note lui-même, et à chaque passe la facture grimpe.

La même étude apporte une observation qui m'a frappé: consommer davantage de tokens ne rend pas l'agent plus précis. La qualité culmine à un coût intermédiaire, puis plafonne. Ce sont des tâches de code, pas de rédaction, mais la courbe ressemble trait pour trait à ce que j'ai vu.

J'en ai tiré la décision la plus structurante du système qui a remplacé Hermes: la qualité ne se rattrape pas en aval, elle se construit en amont.

Je me suis appuyé pour ça sur STORM, une méthode publiée par une équipe de Stanford, qui place l'essentiel du travail dans la préparation d'un texte, avant toute rédaction. La recherche et l'écriture tournent dans deux jobs séparés. Le premier produit un dossier de faits sourcés. Le second n'a pas le droit d'aller chercher une information ailleurs que dans ce dossier.

Ensuite, trois rôles distincts. Un rédacteur. Un critique, qui tourne sur un autre modèle avec ses propres consignes, pour casser l'auto-complaisance d'un modèle qui se relit lui-même. Et un vérificateur, qui confronte chaque chiffre au dossier de recherche.

La boucle entre rédacteur et critique est bornée à deux révisions. Le contrôle final, lui, n'accorde qu'une seconde chance: au deuxième échec, l'article est écarté et je reçois une alerte.

## Le contrôle change de place

Le système qui publie aujourd'hui les contenus SEO de deux produits compte onze agents, et aucun chef.

Il n'y a pas d'orchestrateur. Chaque agent est réveillé par sa propre planification, lit un état partagé, décide s'il a quelque chose à faire, écrit son résultat et s'arrête. La chaîne de production n'est commandée par personne: elle émerge de l'état.

Quatre des onze agents n'utilisent aucun modèle de langage. Ils surveillent, publient, auditent, de façon parfaitement déterministe. Le modèle n'intervient que là où il apporte du jugement.

C'est toute la différence avec le harness. Le contrôle ne vit plus dans une trace de raisonnement que personne ne relira. Il vit dans un état qu'un humain peut ouvrir et lire, et quand quelque chose échoue, ça échoue à une étape qui porte un nom.

L'objection habituelle: un harness s'installe en une heure, un système comme celui-là est un chantier. C'était vrai. Ça ne l'est plus. Avec les modèles actuels, écrire une dizaine d'agents destinés à tourner dans le cloud sur une tâche récurrente prend quelques jours.

Mais ce que le modèle écrit, c'est le code. Il ne décide pas que le critique doit tourner sur un autre modèle, ni où placer la limite de ce que le système a le droit de toucher. **Le code est devenu bon marché. Les décisions, non.** C'est là que va l'expertise, et c'est ce qui sépare un système fiable d'un tirage au sort.

Le détail de l'architecture est dans [l'étude de cas](/projets/seo-growth-swarm/).

## Un garde-fou se code, il ne se demande pas

Dans un harness, un garde-fou est une consigne. « Ne publie jamais sans avoir vérifié les liens. » « Ne touche à aucun fichier hors du dossier du blog. » Une consigne s'écrit dans un prompt, et un modèle peut toujours raisonner pour la contourner, surtout à la douzième itération.

Dans le système qui a remplacé Hermes, aucun garde-fou n'est une consigne. **Ce sont des fusibles.**

Le premier contrôle qualité ne consomme pas un seul token: métadonnées complètes, longueur dans les bornes, illustration présente, liens internes vérifiés contre l'index réel du site, aucune URL inventée. Un défaut mécanique est détecté sans jamais appeler un modèle.

Un chiffre absent du dossier de recherche est traité comme une hallucination, et l'article est rejeté.

Tout fichier publié doit se trouver dans le dossier du blog, sinon l'exécution échoue et m'alerte. Le système est structurellement incapable de toucher au code de l'application.

Rien n'est fusionné tant que l'article ne répond pas réellement en ligne.

Chaque boucle a un plafond d'itérations et un budget de temps. Jamais de boucle jusqu'à satisfaction.

Et il y a un garde-fou que je n'ai même pas écrit. Ce système tourne sur HollowHost sous forme de jobs, et un job s'arrête au bout de quinze minutes, quoi qu'il arrive. Aucune boucle ne peut s'emballer plus longtemps. Le mode daemon, lui, n'avait pas cette limite. C'est précisément celui que nous avons retiré.

<figure style="width:100%">
<img src="/img/swarm-agents-ia-hollowhost.webp" alt="Tableau de bord HollowHost d'un système de huit agents IA planifié chaque jour à 7h: graphe d'exécution des jobs et historique de huit exécutions quotidiennes réussies" width="1223" height="719" loading="lazy" decoding="async" style="height:auto">
<figcaption>Un autre système d'agents que je fais tourner sur HollowHost: un briefing quotidien en huit jobs bornés. Huit jours d'affilée, huit réussites sur huit, en cinq minutes environ.</figcaption>
</figure>

## L'atelier et l'usine

Je ne vous dirai pas de renoncer aux harnesses, et pour cause: j'en utilise un en écrivant ces lignes. Un assistant de code comme Claude Code appartient à la même famille que Hermes, un agent généraliste à qui l'on confie un objectif et qui raisonne pour l'atteindre.

La différence, c'est que je suis devant. C'est avec ce type d'outil qu'on écrit aujourd'hui les agents d'un swarm, et c'est depuis lui que je déploie et administre le mien: les interfaces en ligne de commande d'AWS ou de HollowHost permettent de piloter des agents dans le cloud sans quitter son terminal.

Le harness n'est donc pas le rival du système borné. C'est l'atelier. Il excelle quand quelqu'un regarde, alors on s'en sert au moment où quelqu'un regarde: à la conception. Puis l'usine tourne seule, dans des limites qu'on a écrites.

Ce n'est pas un choix entre deux outils, c'est une répartition dans le temps.

Et plus on confie de travail aux agents, plus cette répartition compte. Dans l'atelier, un mauvais tirage coûte une relance. Dans l'usine, il coûte un article publié sous votre nom, un client qui voit l'agent échouer, ou l'image d'un produit.

Pile ou face, ça se paie.

*PS: Ce blog, lui, ne sort pas d'un growth swarm. J'y écris avec l'aide d'un modèle, mais rien n'y est automatisé: l'irrégularité de mes publications suffit à le prouver.*

## Sources

- [Bai et al., « How Do AI Agents Spend Your Money? Analyzing and Predicting Token Consumption in Agentic Coding Tasks »](https://arxiv.org/abs/2604.22750) — prépublication, avril 2026: les modèles sous-estiment leur propre consommation, et plus de tokens ne donne pas plus de précision. Mesuré sur des tâches de code.
- [Shao et al., « Assisting in Writing Wikipedia-like Articles From Scratch with Large Language Models »](https://arxiv.org/abs/2402.14207) — STORM, Stanford, NAACL 2024: la méthode qui place le travail dans la préparation du texte.
- [Étude de cas SEO Growth Swarm](/projets/seo-growth-swarm/) — l'architecture complète du système décrit ici.
- [Étude de cas HollowHost](/projets/hollowhost/) — la plateforme sur laquelle il tourne.
