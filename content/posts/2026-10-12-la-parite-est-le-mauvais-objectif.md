+++
title = "La parité est le mauvais objectif."
description = "Un développeur a cloné sept applications Adobe avec une IA, et ça ne marche pas. Pourquoi son échec est le meilleur argument pour fabriquer son propre logiciel."
date = 2026-10-12T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Développement", "Management"]
+++

Je me suis mis au piano. Ne me demandez pas pourquoi, je ne le sais pas moi-même. J'ai récupéré un petit clavier MIDI et j'ai commencé à jouer avec.

Il me fallait un outil. Quelque chose pour mémoriser la position des touches, travailler les accords, reconnaître les notes à l'oreille, et m'enregistrer vite fait pour me réécouter.

Ces applications existent. Elles sont bonnes, elles sont connues, elles coûtent le prix d'un café par mois.

Je ne les ai pas essayées.

Je me suis vu parcourir le store, installer, tester, désinstaller, réinstaller autre chose, finir par en adopter une, et découvrir trois semaines plus tard que la fonction dont j'avais besoin est derrière le paiement. J'ai déroulé tout ça dans ma tête, et j'ai ouvert mon éditeur de code.

Une heure plus tard, j'avais mon outil. Et encore, une heure, je suis généreux.

Ce n'est pas une lubie. Depuis que je sais coder, je me fabrique mes outils. Des crons, de petites applications mobiles, des bricoles qui ne quittent jamais mon matériel et qui répondent exactement au besoin que j'avais ce jour-là. Les modèles n'ont rien inventé chez moi, ils ont seulement allongé la liste de ce que je peux me permettre de fabriquer.

Je n'ai donc pas refusé d'acheter. J'ai su, tout de suite, que je pouvais me faire du sur-mesure.

Ça pose une question que je trouve de plus en plus difficile à écarter: à partir de quand fabriquer revient-il moins cher qu'acheter ? Et je ne parle pas du prix de la licence.

## « Software is over »

Le 7 octobre, un développeur nommé Brandon Thomas publiait Artcraft: sept applications open source qui clonent Photoshop, Illustrator, Premiere, Lightroom, After Effects, InDesign et Acrobat Pro. Écrites en Rust, produites avec Claude Opus 5.5, gratuites, sans abonnement. Le déclencheur est assumé, ce sont les frais de résiliation d'Adobe, qui ont valu à l'éditeur une plainte du gouvernement américain et un règlement de 150 millions de dollars en mars dernier.

Sa conclusion tient en deux mots: *software is over*. Il annonce dans la foulée qu'on remplacera Google Search et qu'on fabriquera nos propres smartphones.

Un homme. Quelques semaines. Sept applications professionnelles. Il y a dix-huit mois, cette phrase n'avait aucun sens.

Sauf que ça ne marche pas.

Ses propres mots: *super early alpha*. Le fil Hacker News consacré au projet a été signalé par la communauté, ce qui n'est pas rien venant du public le plus favorable qui soit à un projet Rust open source. Les gens qui ont installé le logiciel rapportent que les fonctions de base ne répondent pas, l'un d'eux notant qu'un fichier Photoshop avec objet dynamique ressort complètement cassé, et qu'une application web vieille de treize ans fait mieux. La promesse initiale, 100 % de parité fonctionnelle sous un mois, a été ramenée quelques jours plus tard à 99 % en des mois plutôt qu'en des années.

Et il faut être juste: quatre jours après les premiers essais catastrophiques, un autre testeur trouve la version du jour globalement utilisable. Ça itère vite.

Je dois vous dire d'où je parle. Je n'ai jamais remplacé le logiciel d'un éditeur chez un client. Ce que j'ai, c'est un outil d'entraînement au piano fabriqué en une heure, et une histoire plus ancienne sur laquelle je reviens plus bas. Ce qui suit est donc un pari, pas un retour d'expérience.

## Personne n'a besoin de 100 %

L'échec d'Artcraft n'est pas un échec de compétence. Thomas a quinze ans de métier dont une dizaine sur de gros projets en Rust, et nous reviendrons sur ce détail, qui n'en est pas un.

C'est un échec de cible.

Il s'est donné la chose la plus difficile qui soit: atteindre la parité fonctionnelle d'un produit généraliste utilisé par des millions de gens. Un commentateur du fil l'a formulé mieux que je ne saurais le faire. Viser la parité, c'est se tromper sur la nature même du logiciel qu'on copie.

Parce qu'une liste de fonctionnalités n'a jamais été écrite pour un utilisateur. Elle est l'addition de vingt ans de demandes de tous les clients à la fois. Photoshop est gros parce qu'il doit servir le photographe, le graphiste, l'illustrateur, l'imprimeur et le monteur, dans trente pays et sur deux systèmes d'exploitation.

Vous, vous n'êtes qu'un seul de ces gens.

Retour au piano une seconde. Je n'avais pas besoin de 4 % de ces applications. J'avais besoin d'une chose qu'elles ne font pas et ne feront jamais: un outil d'entraînement, sans cours, sans catalogue de morceaux, sans progression et sans badge. Personne ne me le vend, parce qu'on ne bâtit pas un abonnement là-dessus.

Ce qui manque n'est donc pas un bout du produit. C'est ce que le produit ne peut pas être.

Dans votre entreprise, cette chose-là a un nom. Ce sont les fichiers Excel qui gravitent autour du logiciel. Ils ne sont pas le symptôme d'un désordre à ranger. Ils sont le cahier des charges, écrit par l'usage, de tout ce que l'éditeur n'a pas couvert.

## Ça marchait déjà sans l'IA

Il y a quelques années, bien avant que les modèles n'arrivent dans nos éditeurs de code, j'étais l'un des acteurs d'une histoire que je raconte rarement.

Un projet s'enlisait chez une très grande compagnie d'assurance. Un salarié, expert de la donnée et chef de produit, a convaincu sa direction d'arrêter les frais et de reprendre le sujet depuis zéro. À trois: lui, et deux développeurs seniors dont j'étais.

Ça a marché. Complètement.

Et il faut dire tout de suite ce que cette histoire ne dit pas. Les gens sur le projet initial n'étaient pas mauvais, et nous n'étions pas meilleurs qu'eux. Nous avions simplement quelque chose qu'ils ne pouvaient plus avoir.

Sur le moment, on s'est raconté que nos atouts étaient une stack claire et du code propre. C'est faux, ou plutôt c'est l'inverse. La stack était claire et le code propre parce qu'on repartait de zéro. C'étaient des conséquences, pas des causes.

Le seul vrai atout tient en trois mots: **pas d'inertie**.

Voilà ce qu'on oublie quand on compare un éditeur à ce qu'on pourrait construire soi-même. Son produit n'est pas lourd parce que ses développeurs seraient mauvais. Il est lourd parce qu'il sert tous ses clients à la fois, qu'il doit rester compatible avec lui-même, et qu'il traîne vingt ans de cas particuliers dont pas un seul ne vous concerne.

Vous ne serez jamais meilleur que lui à son métier. Vous serez seulement plus léger, parce que vous ne servez qu'un client et que vous ne vous souvenez de rien.

Et la partie difficile de cette histoire n'était pas technique. Elle a consisté à convaincre une direction d'arrêter quelque chose qui était déjà payé.

## Ce que l'éditeur portait pour vous

Maintenant la contrepartie, parce qu'un article qui n'en aurait pas serait une plaquette commerciale, et que j'en suis le premier bénéficiaire.

Un éditeur ne vous vend pas que des fonctionnalités. Il absorbe à votre place une conformité réglementaire qui ne dort jamais: facturation électronique, paie, évolutions fiscales, obligations qui changent par décret. Il porte la maintenance sur dix ans. Il est responsable contractuellement, ce qui signifie qu'en cas de problème il existe quelqu'un à mettre en face d'un juge.

Et il faut être honnête sur ce qu'on propose à la place. Quitter un éditeur, ce n'est pas sortir de la dépendance. C'est en changer. Vous passez d'un fournisseur que vous pouvez poursuivre à un prestataire dont vous espérez qu'il décrochera encore son téléphone dans trois ans.

Il y a enfin un risque dont personne ne parle. Thomas s'est imposé une règle stricte, inscrite noir sur blanc dans les instructions qu'il donne à son modèle: ne jamais ouvrir ni désassembler le logiciel d'Adobe, n'en observer que le comportement. C'est la méthode dite de la salle blanche, et elle est juridiquement sérieuse. Sauf qu'un commentateur a pointé ce que cette discipline ne couvre pas. Le modèle qui écrit le code, lui, a été entraîné sur à peu près tout le code libre existant. La propreté s'applique à l'humain, pas à la machine. Pour une entreprise qui ferait réécrire son outil métier, la question n'a rien de théorique.

Un mot, enfin, pour les éditeurs eux-mêmes, qu'on traite un peu vite en adversaires. Ils ont résolu un vrai problème, et pendant trente ans ils l'ont résolu pour des gens qui n'avaient aucune autre option. Personne ne pouvait se payer du sur-mesure. Ce qui a changé n'est pas leur qualité. C'est le coût de construire.

## Ce que je ferais, et ce que je ne promets pas

Voilà le pari, et j'y crois assez pour l'écrire avant de savoir si j'ai raison.

Le métier de prestataire a longtemps consisté à paramétrer l'outil d'un autre, à l'intégrer, à le faire tenir debout chez vous. Je pense qu'il consistera de plus en plus à fabriquer celui qui n'existe pas. Pas le clone de votre ERP: la chose que votre ERP ne fera jamais, et que vous tenez aujourd'hui dans un classeur.

Ce qui bascule n'est d'ailleurs pas le coût du développement, qui a toujours été l'argument de la discussion. C'est le coût de l'évaluation. Le cahier des charges, les trois éditeurs en lice, les démonstrations, le pilote, la négociation, la reprise de données: additionnez ce que vous dépensez avant même d'avoir signé. C'est contre ce montant-là qu'il faut comparer, et non contre le prix d'une licence.

Et puisque j'ai reconnu que j'en étais le bénéficiaire, voici les conditions auxquelles j'accepterais de le faire.

Le code vous appartient. La documentation aussi. Vous devez pouvoir me remplacer, et je dois vous en avoir donné les moyens sans moi. **Un prestataire qui ne prépare pas son propre remplacement ne vous vend pas un logiciel, il vous vend une dépendance.** C'est très exactement ce que vous cherchiez à quitter.

Je n'ai pas encore remplacé l'outil d'un éditeur chez un client. Je pense que ce sera l'un des grands sujets des années qui viennent, et j'ai écrit cet article pour le dire avant d'avoir eu raison ou tort.

En attendant, j'ai un outil d'entraînement au piano qui ne sait faire que quatre choses. Il ne ressemble à rien, personne d'autre ne s'en servira jamais, et il fait exactement ce que je lui demande. Maintenant je regarde mes MK2 de travers. Je suis sûr qu'il y a quelque chose à faire, quelque chose qui colle parfaitement à mon envie. Sans refaire Serato.

La parité n'a jamais été l'objectif.

## Sources

- [Ars Technica, « "Software is over": Bold AI developer takes aim at Adobe with open source clones »](https://arstechnica.com/ai/2026/10/software-is-over-bold-ai-developer-takes-aim-at-adobe-with-open-source-clones/) — la couverture d'origine, le pivot d'Artcraft et le risque juridique de l'imitation d'interface.
- [Le fil Hacker News consacré au projet](https://news.ycombinator.com/item?id=49958850) — les essais de terrain, la révision de la promesse de parité, et la critique de la salle blanche.
- [L'annonce de Brandon Thomas sur Reddit](https://www.reddit.com/r/SaaS/comments/1wwnhbt/ive_created_open_source_100_rust_cleanroom/) — la promesse initiale, dans ses termes.
- [Les applications Artcraft](https://getartcraft.com/apps) — pour se faire son propre avis.
- [TechSpot, sur les frais de résiliation d'Adobe](https://www.techspot.com/news/114136-software-over-developer-uses-ai-build-free-adobe.html) — la plainte du gouvernement américain et le règlement de 150 millions de dollars.
