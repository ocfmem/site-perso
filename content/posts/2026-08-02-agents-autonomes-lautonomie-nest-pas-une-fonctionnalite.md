+++
title = "Agents autonomes: l'autonomie n'est pas une fonctionnalité"
description = "Hermes Agent, OpenClaw: les agents qui décident seuls de leur parcours explosent. Pourquoi je fais l'inverse, et pourquoi l'autonomie est aussi la surface d'attaque."
date = 2026-08-02T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Développement"]
+++

En février 2026, Nous Research publiait Hermes Agent. Trois mois plus tard, le projet dépassait les 140 000 « stars » sur GitHub, la croissance la plus rapide qu'ait connue l'écosystème des agents. Le principe: on donne un objectif en langage naturel, l'agent le découpe lui-même en étapes, choisit dans une bibliothèque de plus de quarante outils, et itère jusqu'à estimer qu'il a terminé.

Moi aussi j'aime bien les belles histoires. Mais j'en construis, des agents. Ça prend de plus en plus de place dans mon activité, et plus j'en construis, moins je leur laisse d'autonomie.

## Ce que « autonome » veut dire, techniquement

Derrière le mot, il y a un mécanisme très simple, et c'est précisément le problème. On donne un objectif au modèle. Le modèle décide des étapes. Il exécute, observe, décide de la suivante, recommence, jusqu'à juger qu'il a fini. Aucun chemin n'est écrit nulle part. Le chemin est produit à l'exécution, une fois, et il ne sera pas le même la prochaine fois.

Ce qu'on y gagne: ne pas avoir à prévoir les cas. Ce qu'on cède en échange, et dont on parle beaucoup moins: savoir ce que le système va faire.

Des chercheurs de Carnegie Mellon ont monté un banc d'essai qui mérite le détour, TheAgentCompany. C'est une fausse entreprise logicielle complète: son GitLab, son OwnCloud, son outil de gestion de projet, sa messagerie interne, et jusqu'à des collègues simulés auxquels l'agent doit arracher des informations pour avancer. 175 tâches professionnelles longues, du développement à la finance en passant par les RH.

Le meilleur modèle testé en termine **30,3 %** en autonomie. Le deuxième, 26,3 %. Un GPT-4o tombe à 8,6 %.

On peut lire ce résultat comme un « pas encore mûr ». Je le lis autrement: c'est ce que produit une décomposition d'objectif non bornée quand elle rencontre le désordre du réel.

## L'autonomie n'est pas une fonctionnalité qui a des failles

Du taux d'échec, on pourrait s'accommoder: on relance. Le vrai coût est ailleurs, et il est double.

D'abord la non-reproductibilité. Quand un agent autonome se plante, il ne se plante pas deux fois de la même façon. On ne rejoue pas la trajectoire, on ne bissecte pas, on ne pose pas un point d'arrêt à l'étape 7, parce qu'il n'y a pas d'étape 7: il y avait une étape 7 cette fois-là. Déboguer devient de l'archéologie.

Ensuite la sécurité, et c'est là que le sujet cesse d'être théorique. OpenClaw, l'agent local qui tourne en permanence sur la machine de son utilisateur et se réveille de lui-même, est devenu en quelques mois le cas d'étude du genre, disséqué par IBM X-Force comme par plusieurs équipes de recherche. La vulnérabilité la plus parlante s'appelle ClawJacked: une simple page web malveillante suffisait à détourner une instance locale et à en exfiltrer des données silencieusement. Pas via un bug de parsing exotique: en se servant de l'autonomie de l'agent, qui faisait exactement son travail.

Le problème n'est pas qu'OpenClaw serait mal écrit. Il est structurel: chaque degré de liberté accordé à l'agent est aussi accordé à celui qui saura lui parler. Un agent autonome avec accès aux fichiers, aux identifiants et au réseau, c'est une surface d'attaque qui prend ses propres décisions.

## Des logs, ce n'est pas de la traçabilité

La non-reproductibilité est un problème de développeur. La traçabilité, elle, est un problème d'entreprise, et c'est souvent celui qui fait basculer la décision.

Un agent autonome produit des logs en quantité: chaque appel d'outil, chaque réponse du modèle, chaque token consommé. Ce qu'il ne produit pas, c'est une chaîne de décisions rattachée à un processus connu. Savoir que l'agent a appelé telle API à 14 h 03 ne dit pas pourquoi il l'a appelée, ni ce qu'il a écarté au passage.

Les outils, pourtant, existent et sont bons. LangSmith, la plateforme d'observabilité de LangChain, trace chaque appel de modèle, chaque outil, chaque étape d'une exécution, et c'est devenu le backend de tracing par défaut de LangGraph. Mais un outil de traçage ne restitue que ce qu'on lui donne. Sur un graphe conçu, la trace se lit: chaque nœud porte un nom, chaque transition correspond à une étape que j'ai voulue, et relire un incident revient à suivre le processus. Sur une boucle autonome, le même outil produit une suite plate d'appels dont il faut deviner l'intention après coup. Même outillage, deux lisibilités qui n'ont rien à voir.

Et la différence n'a rien de cosmétique. Quand une dépense part, quand une donnée est modifiée, quand un message est envoyé, il faut pouvoir dire ce qui l'a déclenché, sur quelle base, et à quel endroit du processus. Pour l'audit, pour la conformité, et tout bêtement pour rendre des comptes. Un chemin écrit à l'avance répond à ça par construction.

## Concevoir le parcours de réflexion

C'est pour ça que je fais l'inverse de la tendance. Je ne cherche pas à rendre mes agents plus autonomes: je cherche à concevoir leur parcours de réflexion.

Ça fait au moins trois ans que je travaille avec des IA sur les tâches satellites du développement d'un SaaS: le SEO, les campagnes d'ads, les rapports, l'analyse des parcours utilisateurs, la veille concurrentielle, le tri des retours clients, le suivi des coûts d'infrastructure. Tout ce qui n'est pas le produit, et qui occupe pourtant la moitié du temps quand on édite du logiciel. Sur ce trajet, j'ai à peu près tout essayé: des prompts de plus en plus longs et de plus en plus alambiqués (au fait, vous en connaissez encore, vous, des prompt engineers ?), des graphes LangGraph, les outils intégrés de Claude, et bien sûr des agents du type Hermes, à qui l'on confie un objectif et qu'on regarde partir.

Un exemple que je peux raconter, parce qu'il est chez moi. Sur HollowHost, un de mes produits, j'ai automatisé le suivi, l'analyse et l'ajustement des campagnes Google Ads. Vu de loin, c'est le profil de tâche qu'on confierait aujourd'hui à un agent autonome sans y réfléchir: un objectif qui s'énonce en une phrase, une API à disposition, et une boucle qui doit tourner sans moi. J'aurais pu lâcher un agent dessus avec les clés du compte.

Je ne l'ai pas fait, pour une raison qui n'a rien de théorique: chaque tour de cette boucle dépense de l'argent réel. Un agent qui décide seul de ses étapes décide aussi seul de son budget.

Alors le chemin est écrit. La récupération des données n'est pas confiée au modèle: c'est du code déterministe, qui va chercher exactement les métriques dont j'ai décidé qu'elles comptaient, au moment où j'ai décidé qu'il fallait les regarder. L'analyse, en revanche, est un vrai travail d'interprétation, et c'est là que le modèle est à sa place: voici ces chiffres, voici l'historique, qu'est-ce qui bouge et pourquoi. Périmètre serré, résultat de forme connue. L'ajustement, enfin, est la seule étape qui touche au portefeuille, et la seule que j'ai bornée chiffres en main: une enchère sur un mot clé ne peut varier qu'entre un minimum et un maximum que j'ai fixés, et les ajouts comme les suppressions de mots clés sont plafonnés en nombre sur une période donnée. L'agent peut corriger la trajectoire. Il ne peut pas refaire le compte.

Ce découpage n'a rien d'original, et c'est justement le point: il se généralise. J'applique la même grille aux autres automatisations sur lesquelles je travaille depuis plusieurs années, autour du métier d'éditeur de logiciel. De mes réussites et de mes échecs, j'ai tiré quelques enseignements.

Écrire le chemin plutôt que le laisser émerger, ça veut dire des transitions d'état explicites: depuis cet état, avec ce résultat, on ne peut aller que là. Des étapes bornées, où le modèle traite l'ambiguïté d'un problème précis. Des points de contrôle, dont certains humains, placés là où une erreur coûte cher et pas ailleurs. Et de la persistance d'état, pour qu'une tâche interrompue reprenne où elle en était au lieu de tout rejouer. C'est très exactement ce qu'un LangGraph outille, avec ses arêtes et son routage conditionnel: des frontières que le modèle ne peut pas franchir. Ce n'est pas un détail d'implémentation, c'est un choix d'architecture.

Le plus intéressant, c'est que la recommandation vient de ceux qui vendent les modèles. Anthropic, dans son propre guide d'ingénierie, conseille de chercher la solution la plus simple possible, quitte à ne construire aucun système agentique du tout. Quand un fournisseur vous explique comment consommer moins de son produit, ça vaut la peine de l'écouter.

## L'autonomie vous rend interchangeable

Reste l'argument qui compte le plus à mes yeux. Ce chemin déterministe que je viens de décrire n'est pas de la plomberie qu'on installe autour de l'intelligence: c'est l'endroit où je mets mon expertise métier. Décider quelles métriques comptent et lesquelles ne sont que du bruit, à quel moment il est pertinent de les regarder, ce qui constitue une dérive et ce qui n'est qu'une variation saisonnière: rien de tout cela ne se déduit d'un objectif énoncé en une phrase. Ça vient de ce que je sais du métier, et de ce que mes erreurs passées m'ont appris.

Un agent autonome, lui, produira ce que produisent tous les agents autonomes: la moyenne de ce que le modèle a lu. Le parcours que j'écris, c'est exactement ce que personne d'autre n'a. C'est là que se loge le petit quelque chose de différenciant.

## Alors quand est-ce qu'on lâche la bride ?

Je ne dis pas que l'autonomie ne sert à rien. Elle a un domaine de validité, simplement plus étroit qu'on ne le raconte: quand les entrées varient trop pour qu'on écrive un chemin à l'avance, quand la tâche est exploratoire par nature, quand le coût d'une erreur est faible et qu'un humain regarde le résultat avant qu'il produise un effet. Chercher, trier, proposer: oui. Décider, engager, dépenser: non.

La règle que j'applique tient en une phrase: le workflow est la réponse par défaut, et c'est à l'agent de prouver qu'il apporte quelque chose que la logique déterministe ne sait pas produire.

## Le plan de vol

Il y a une industrie qui pratique l'autonomie à grande échelle depuis cinquante ans, et qui a réglé la question bien avant nous.

Un pilote automatique est un des systèmes autonomes les plus aboutis que l'on ait construits. Il tient un cap, une altitude, exécute une approche, corrige en permanence des perturbations que personne n'avait prévues, mais suit un plan de vol déposé à l'avance, avec ses points de passage, ses altitudes imposées et ses minima.

L'autonomie du pilote automatique s'exerce dans l'exécution, jamais dans le choix de la route. Quelqu'un reste responsable, et peut reprendre la main à tout instant.

L'aéronautique n'a jamais confondu autonomie et liberté.

Un agent autonome, lui, ne reçoit qu'un objectif. La route, il l'invente à l'exécution, et il en inventera une autre la fois suivante.

Concevoir le parcours de réflexion d'un agent, ce n'est pas le brider. C'est ce qui le rend livrable, et c'est la seule chose qui le distingue de celui du voisin.

## Sources

- [TheAgentCompany: Benchmarking LLM Agents on Consequential Real World Tasks](https://arxiv.org/abs/2412.14161) — le banc d'essai de Carnegie Mellon et ses taux de complétion autonome.
- [Anthropic — « Building Effective Agents »](https://www.anthropic.com/engineering/building-effective-agents) — la distinction workflow / agent et la recommandation de commencer par le plus simple.
- [IBM X-Force — ce qu'OpenClaw révèle des risques de sécurité de l'IA agentique](https://www.ibm.com/think/x-force/what-openclaw-reveals-about-agentic-ai-security-risks).
- [The Hacker News — les failles d'OpenClaw permettant prompt injection et exfiltration de données](https://thehackernews.com/2026/03/openclaw-ai-agent-flaws-could-enable.html).
- [Barracuda — ce que les équipes sécurité doivent savoir de l'IA agentique](https://blog.barracuda.com/2026/04/09/openclaw-security-risks-agentic-ai).
- [« Uncovering Security Threats and Architecting Defenses in Autonomous Agents: A Case Study of OpenClaw »](https://arxiv.org/html/2603.12644v1) — l'étude de cas académique.
- [NVIDIA — sur Hermes Agent et les agents auto-améliorants](https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/).
- [LangChain — panorama des frameworks d'agents en 2026](https://www.langchain.com/resources/ai-agent-frameworks) — sur la persistance d'état et le routage conditionnel.
- [LangSmith](https://www.langchain.com/langsmith) — la plateforme d'observabilité et de tracing utilisée en exemple.
