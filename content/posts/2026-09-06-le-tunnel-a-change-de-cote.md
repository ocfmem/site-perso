+++
title = "Le tunnel a changé de côté."
description = "Les clients arrivent avec quarante pages de specs générées par IA. L'intention est excellente, le résultat ignore l'existant. Parce que la spec n'a jamais valu par le document."
date = 2026-09-06T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Développement", "Management"]
+++

Un client m'envoie quarante pages de spécifications. Le document est propre: sommaire, numérotation, critères d'acceptation. Page trente et une, un modèle de données. Onze tables, clés étrangères comprises.

Problème: ce modèle décrit un système qui n'existe pas. Pas un système futur ou une évolution, mais bien un système imaginaire. Plausible et cohérent, mais sans rapport avec celui que nous construisons depuis des mois.

Je précise tout de suite, parce que c'est le cœur du sujet: ce client fait ce qui lui semble évident. Il a passé du temps sur ce document. Il l'a produit parce qu'il pensait que fournir aux développeurs une spécification complète était la meilleure chose qu'il pouvait faire pour le projet.

Et il y a autre chose que je comprends sans effort: l'IA lui a donné les moyens de s'impliquer dans la construction technique comme il n'avait jamais pu le faire. Nous, développeurs, savons à quel point c'est grisant. Je vois mal comment lui reprocher d'y avoir pris goût.

## L'intention est bonne, et c'est ce qui rend le problème intéressant

Pendant quinze ans, on a réclamé des specs. On s'est plaints des demandes en trois lignes dans un ticket, des besoins qui changeaient une fois le développement commencé, des arbitrages métier qu'on n'obtenait qu'en relançant trois fois. Le métier nous a entendus. Il arrive maintenant avec quarante pages.

Personne ne triche. C'est ce qui rend le phénomène intéressant, et c'est aussi pourquoi il ne se corrigera pas tout seul: tous les signaux disent au porteur de projet qu'il a bien travaillé.

Sauf que le document a trois défauts qui reviennent avec une régularité de métronome. Il est verbeux. Il se contredit d'une section à l'autre parce que personne ne relit quarante pages avec l'attention qu'on accorde à quatre.

Et il ignore l'existant.

Les deux premiers défauts coûtent du temps de lecture; le troisième coûte le projet. Ce défaut annonce sa présence: du code, ou un modèle de données. Quand une spécification en contient, elle a cessé de décrire un besoin. Elle prescrit une solution, en s'appuyant sur un système que son auteur n'a jamais lu.

## La spec n'était pas l'entrée du design, c'était sa sortie

La spécification n'a jamais valu par le document. Elle valait par la négociation qui le produisait.

Cette négociation, c'est l'endroit où le métier découvre ce que coûte son idée: celle-ci, trois jours; celle-là, trois mois, parce que la table des commandes est partagée avec la facturation. C'est aussi l'endroit où la tech découvre ce que le métier veut vraiment, qui n'est presque jamais ce qu'il avait demandé. Le document qui en sortait n'était que le procès-verbal de cette découverte.

Rien de neuf là-dedans, et c'est bien le problème: on le sait depuis vingt-cinq ans. En 2001, Ron Jeffries formalise les trois C de la user story (*Card, Conversation, Confirmation*) précisément pour distinguer la story « sociale » des pratiques documentaires d'avant. La carte n'est pas l'exigence: elle est le prétexte à la conversation où l'exigence se construit. En 2014, Jeff Patton résume l'affaire d'une phrase devenue la plus reprise de son livre: des documents partagés ne font pas une compréhension partagée. Il l'illustre par le jeu du téléphone, et par la sonde Mars Climate Orbiter, perdue en 1999 sur une histoire d'unités que chacun croyait évidente.

Alors, où vit la connaissance de l'existant ? Nulle part dans un document. Elle vit dans cette conversation. Générer la spécification en amont et en solo ne raccourcit donc pas le design: ça le supprime. Et on découvre la suppression à l'implémentation, c'est-à-dire à l'endroit le plus cher.

## Le tunnel a changé de côté

L'effet tunnel, dans ce métier, a toujours désigné le même risque: une équipe technique part la tête dans le guidon et ressort trois mois plus tard avec quelque chose qui n'était pas ce qu'il fallait. Tout l'arsenal agile (revues, démos, ateliers de cadrage, PO embarqué dans l'équipe) a été inventé pour percer des fenêtres dans ce tunnel.

L'IA vient de creuser le même tunnel de l'autre côté de la table. Le porteur de projet travaille désormais seul, avec un interlocuteur qui ne le contredit jamais et le félicite de ses idées brillantes, qui ne connaît pas son système à moins qu'on le lui fournisse (accès aux bases, au code, etc.), et qui ne lui demande pas pourquoi. Il en ressort avec quarante pages et la conviction d'avoir avancé.

Personne n'a de rituel pour détecter ça, pour une raison simple: on n'a jamais eu besoin de protéger le métier de lui-même. Nos garde-fous regardent tous dans l'autre sens.

Le coût, lui, ne disparaît pas. Il change de porteur. Le temps gagné à la rédaction est reversé à l'équipe technique sous forme de réfutation: lire, comprendre l'intention, identifier l'infaisable, l'expliquer, le prouver. Avec un effet de bord qui empoisonne la relation: celui qui dit non devient l'obstacle. Le porteur de projet a produit, la tech a objecté. Sur le papier, on sait qui a avancé et qui a freiné.

## Écrire la spec d'abord n'est pas le problème. La faire écrire, si.

Soyons honnêtes sur l'état de l'art: je nage ici à contre-courant. Écrire la spécification d'abord et faire implémenter ensuite est devenu une pratique nommée et défendue, le *spec-driven development*. Thoughtworks en a fait l'une des pratiques d'ingénierie marquantes de 2025, Microsoft lui consacre des publications. Ce n'est pas une lubie.

Je n'ai rien contre. Ce que je conteste, c'est la lecture qu'on en fait. Ceux qui ont regardé la pratique de près disent tous la même chose: sa valeur tient à la réflexion produite **en écrivant** la spécification, pas à l'outillage autour ni au document qui en résulte. Générer la spécification, c'est donc retirer exactement la partie qui avait de la valeur, et garder la mise en page.

L'histoire a d'ailleurs déjà tranché. Le cycle en V n'a pas échoué parce que les spécifications sont mauvaises, mais parce que découvrir qu'une spécification était fausse arrivait trop tard et coûtait trop cher. **En produire dix fois plus vite ne déplace pas cet endroit d'un millimètre: ça augmente seulement le nombre de pages écrites avant d'y arriver.**

Reste le scénario que je redoute, et il n'a rien de théorique: l'équipe technique qui se sert à son tour de l'IA pour interpréter la spécification. L'IA écrit, l'IA interprète, et plus aucun humain n'a confronté l'intention au réel. Ce qui sort de cette boucle n'est pas un document, c'est le produit. Et personne ne pourra désigner le moment où le malentendu est entré. Le marché vend déjà le chaînon manquant, du reste: des outils qui analysent votre spécification générée par IA pour y détecter les zones floues et les hypothèses implicites. Une IA pour deviner ce que voulait dire l'IA.

## Ce que je réponds, désormais

Je ne renvoie pas le document. Je fais trois choses, dans cet ordre.

Je dis clairement au client que des éléments incohérents ne me font pas gagner de temps: ils m'en coûtent. C'est la phrase la plus utile de l'échange, et la plus rarement prononcée, parce qu'elle a l'air ingrate. Elle ne l'est pas: tant qu'il croit m'aider, il continuera.

Ensuite je le guide, sur les bonnes pratiques et les bons formats pour ce type de document. Ce qu'on attend, ce qu'on n'attend pas, jusqu'où va son terrain. Décrire un besoin, un usage, une règle métier, un cas limite: oui, et c'est précieux. Décider d'un modèle de données: non. Ce n'est pas de la précision, c'est une décision d'architecture prise sans les éléments.

Et je lui rappelle une évidence qu'on oublie des deux côtés de la table: moi aussi, j'ai des outils IA. Je comprends l'intention, mais il ferait mieux d'utiliser ses tokens sur la partie business. Produire l'architecture, le code, le modèle de données, c'est mon métier, et je suis équipé pour. Ce que personne ne peut faire à sa place, c'est arbitrer ce que vaut une fonctionnalité pour son entreprise.

Rien de tout cela n'est un problème de format, et aucun prompt ne le réglera. La spécification peut très bien être générée. Je le fais pour les projets internes d'OCF, dans un format un peu particulier, certes, mais je le fais. Je vous raconterai ce format dans un prochain article. Ce qui ne change rien au principe: elle ne vaut que comme compte rendu d'une conversation qui a eu lieu, jamais comme son remplaçant. Toute la différence tient dans une question: le document arrive-t-il avant la discussion, ou après ?

Je propose: discutons-en avant. Parce qu'au temps du papier, une spécification inutile calait au moins une porte. Le PDF n'a même pas cette qualité.

## Sources

- [Agile Alliance — « The Three C's »](https://agilealliance.org/glossary/three-cs/) — le modèle proposé par Ron Jeffries en 2001 pour distinguer la user story « sociale » des pratiques documentaires.
- [Jeff Patton — *User Story Mapping*](https://www.goodreads.com/work/quotes/41559956-user-story-mapping-discover-the-whole-story-build-the-right-product) — d'où vient « shared documents aren't shared understanding », et l'analogie du jeu du téléphone.
- [« Why Documents Fail », présentation de Jeff Patton](https://urbanhogberg.wordpress.com/2015/02/24/why-documents-fail-a-presentation-by-jeff-patton/) — la version courte de l'argument.
- [Gojko Adzic — *Specification by Example*](https://gojko.net/books/specification-by-example/) — la compréhension partagée comme livrable, obtenue par des exemples concrets plutôt que par un document.
- [Thoughtworks — « Spec-driven development »](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices) — l'analyse de la pratique, et pourquoi le code exécutable reste la source de vérité.
- [Microsoft — « Spec-Driven Development: a spec-first approach to AI-native engineering »](https://developer.microsoft.com/blog/spec-driven-development-ai-native-engineering/) — le point de vue favorable, pour se faire son idée.
- [Jama Software — « AI in Requirements Management: What Works in 2026 »](https://www.jamasoftware.com/blog/ai-requirements-management/) — traiter les sorties d'IA comme des entrées de revue humaine, pas comme des artefacts finis.
