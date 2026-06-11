+++
title = "Classer ses salariés au nombre de tokens : l’aberration managériale du moment"
description = "Le « tokenmaxxing » classe les salariés selon leur consommation de tokens. Pourquoi mesurer le carburant plutôt que la distance est une aberration managériale."
date = 2026-06-11T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["IA", "Management"]
+++

En avril 2026, on découvrait qu'un salarié de Meta avait monté un classement interne, « Claudeonomics », rangeant les 85 000 employés du groupe selon le nombre de tokens qu'ils brûlaient — avec des titres à débloquer comme « Token Legend » ou « Cache Wizard ». Le champion a englouti à lui seul, en un mois, de quoi facturer plus d'1,4 million de dollars ; ni Mark Zuckerberg ni son directeur technique n'apparaissaient dans le top 250. Amazon et OpenAI avaient les leurs.

Non, ce n'est pas une parodie. C'est le « tokenmaxxing », la dernière lubie managériale en date : on classe les salariés selon leur consommation de tokens, comme on placarderait un tableau des meilleures ventes. L'employé qui « consomme » le plus serait, c'est l'évidence, le plus moderne, le plus impliqué, le plus productif. Vous voyez déjà le problème.

C'est, sans exagérer, l'une des idées de management les plus aberrantes que j'aie croisées — loin devant la manie de juger un développeur au nombre de commits qu'il empile (voire au nombre de fois où il a cassé la CI) ou le flicage des mouvements de la souris pour s'assurer que le collaborateur n'est pas à la piscine.

## On confond le carburant avec la distance parcourue

Un token, c'est un coût, pas un résultat. C'est de l'essence, pas des kilomètres. Classer quelqu'un selon sa consommation de tokens, c'est féliciter le conducteur qui vide son réservoir le plus vite sans jamais se demander où il est arrivé — ni même s'il a démarré. Tant qu'à faire, récompensons aussi les salariés au nombre de cafés engloutis, de mails envoyés ou de réunions qui auraient pu être un mail : autant de mesures de l'agitation, jamais de la valeur.

Le problème est structurel, pas anecdotique. Un token mesure une entrée, pas une sortie. Or la productivité, par définition, se joue du côté des sorties — ce qui est produit, résolu, livré, amélioré. Mesurer l'entrée et appeler ça de la productivité, c'est confondre la facture d'électricité avec la lumière.

## La loi de Goodhart vous attend au tournant

« Lorsqu'une mesure devient un objectif, elle cesse d'être une bonne mesure. » Affichez un classement de tokens et vous obtiendrez exactement ce que vous mesurez : des tokens. Les plus malins gonfleront leurs requêtes pour grimper au tableau d'honneur — trois prompts là où un seul suffisait. Les plus prudents, eux, fermeront l'outil de peur de passer pour des dépensiers, et se priveront d'un vrai gain.

Dans les deux cas, le classement ne dit plus rien du réel. Il ne mesure plus qu'une chose : le talent des gens à truquer le classement. Ce n'est pas une crainte théorique : chez Amazon, des salariés se sont mis à multiplier les requêtes inutiles pour gonfler leur score, au point que l'entreprise a fini par débrancher le sien et lui préférer une métrique centrée sur l'utilité réelle du travail produit. Bravo, vous avez inventé un concours dont la seule compétence requise est de gagner le concours.

## Et si on mesurait au moins le bon ratio ?

Admettons qu'on veuille absolument un chiffre, parce qu'un tableau de bord sans chiffre, ça angoisse. Alors regardons non pas la consommation brute, mais le rapport entre ce qui sort et ce qui entre : qui est le plus productif *par* token consommé ? Là, au moins, on récompenserait l'efficacité — la requête bien pensée plutôt que les dix mille tokens gaspillés à tourner en rond.

Sauf que ce ratio non plus ne tient pas dans une jauge. Comparer le « rendement » d'un développeur, d'un juriste et d'un commercial n'a tout simplement aucun sens : leurs tâches, leurs usages, leurs livrables n'ont rien de commun. Et surtout, comment chiffre-t-on le numérateur noble de l'équation — le jugement, la créativité, la relation client, le coup de main donné au collègue paniqué un vendredi à 18 h ? Tout ce qui fait réellement la valeur d'un salarié échappe, comme par hasard, au compteur.

## Le compteur n'est pas le travail

Mesurer la consommation de tokens, c'est prendre le thermomètre pour la fièvre. Au mieux, c'est inutile. Au pire, on installe une surveillance qui grignote la confiance et pousse chacun à optimiser le mauvais chiffre — pendant que le vrai travail, lui, attend. La meilleure preuve ? Le reflux est déjà là : le classement de Meta a disparu après le tollé médiatique, Walmart a plafonné l'usage interne de l'IA, et des dirigeants — d'Uber à plusieurs investisseurs — admettent désormais tout haut que ces tokens dépensés ne se traduisent par aucun gain à l'échelle de l'entreprise. Mi-2026, « tokenmaxxing » ne désigne déjà plus une stratégie à suivre, mais la dérive qu'on cherche à enrayer. Quand une métrique s'effondre aussi vite qu'on l'érige, c'est qu'elle ne mesurait pas grand-chose.

La question n'a jamais été « qui consomme le plus ? », ni même « qui consomme le mieux ? ». Elle reste, exactement comme avant l'IA : qui résout les bons problèmes, et bien ? Aucun compteur de tokens ne répondra jamais à celle-là. Et le jour où un manager pense le contraire, ce n'est peut-être pas le salarié qu'il faudrait classer.

## Sources

- [Fortune — « Meta killed employee AI token dashboard »](https://fortune.com/2026/04/09/meta-killed-employee-ai-token-dashboard/) — d'après la révélation initiale de *The Information*.
- [Fortune — « Tokenmaxxing is dead »](https://fortune.com/2026/05/28/tokenmaxxing-is-dead-companies-didnt-get-the-roi-from-ai-they-wanted-to-see/) — sur l'absence de ROI, avec les propos du COO d'Uber.
- [CNBC — le suivi généralisé de l'usage de l'IA dans les entreprises du Fortune 500](https://www.cnbc.com/2026/05/05/ai-use-work-employee-monitoring-tech-surveillance.html).
- [CIO — « Tokenmaxxing: When AI adoption metrics go bad »](https://www.cio.com/article/4178320/tokenmaxxing-when-ai-adoption-metrics-go-bad.html) — Amazon, JPMorgan, Disney.
- [IT Brew — « Is the tokenmaxxing era over? »](https://www.itbrew.com/stories/is-the-tokenmaxxing-era-over) — sur le plafonnement de l'usage chez Walmart.
- [The Decoder — le classement interne de Meta et ses limites](https://the-decoder.com/meta-employees-compete-for-token-consumption-on-an-internal-ai-leaderboard/).
