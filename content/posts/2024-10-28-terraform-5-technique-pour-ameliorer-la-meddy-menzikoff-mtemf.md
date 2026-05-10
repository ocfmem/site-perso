+++
title = 'Terraform: 5 technique pour améliorer la maintenabilité'
description = 'Depuis quelques années, Terraform est un de mes outils principaux. Outil d’Infra as Code capable de supporter différents services Cloud il propose aussi de nombreuses fonctionnalités rendant l’outil extrêmement puissant.'
date = 2024-10-28T09:00:00+02:00
draft = false
author = 'Meddy Menzikoff'
tags = ['Cloud', 'DevOps']
[params]
  source = 'LinkedIn'
  source_url = 'https://fr.linkedin.com/pulse/terraform-5-technique-pour-am%C3%A9liorer-la-meddy-menzikoff-mtemf'
+++

> Article initialement publié sur LinkedIn le **28 oct. 2024**.  
> Source : [https://fr.linkedin.com/pulse/terraform-5-technique-pour-am%C3%A9liorer-la-meddy-menzikoff-mtemf](https://fr.linkedin.com/pulse/terraform-5-technique-pour-am%C3%A9liorer-la-meddy-menzikoff-mtemf)

Depuis quelques années, Terraform est un de mes outils principaux. Outil d’Infra as Code capable de supporter différents services Cloud il propose aussi de nombreuses fonctionnalités rendant l’outil extrêmement puissant.

Dans cet article, nous couvrirons certaines des astuces et conseils les plus essentiels pour écrire du bon code Terraform, facile à utiliser et évolutif.

### Les variables

Vous pouvez utiliser des variables pour configurer des ressources ou pour maintenir des paramètres spécifiques à un environnement. Les variables peuvent également être utilisées pour créer des configurations par défaut.

La bonne pratique consiste à définir les variables dans un fichier séparé dans un fichier séparé que l’on peut décliner selon les environements. Vous pouvez ensuite référencer ces variables dans la définition de vos ressources en utilisant la syntaxe ${var.nom_variable}. Ces variables peuvent être passées dans des modules, ce qui vous permet de réutiliser facilement votre code avec différentes variations.

### Les modules

Grâce aux modules, vous pouvez regrouper des ressources et des configurations connexes, puis les empaqueter pour les réutiliser dans divers projets ou même au sein d'un seul projet. Ils permettent également de créer des abstractions personnalisées, facilitant ainsi la lisibilité et la maintenance de votre code.

Les modules sont particulièrement avantageux lorsque vous avez besoin de déployer des composants d'infrastructure similaires dans plusieurs environnements. Au lieu de dupliquer votre code, vous pouvez l'extraire dans un module et l'utiliser ensuite dans différents environnements en adaptant les paramètres. Cette méthode permet non seulement de réduire la duplication, mais aussi de simplifier la gestion de votre infrastructure en assurant une cohérence entre les environnements.

Ce qui est ‘Top’, c'est que les modules Terraform sont simples à créer et à utiliser. Il suffit de définir votre module dans un répertoire séparé avec des entrées et sorties spécifiques. Vous pouvez ensuite le référencer dans votre fichier de configuration à l'aide d'un bloc module. Une ressource précieuse pour trouver des modules Terraform prêts à l'emploi est le Terraform Registry, qui propose un large éventail de modules développés par la communauté Terraform, que vous pouvez utiliser et enrichir.

### Le conditionnel

La logique conditionnelle est cruciale pour concevoir des scénarios d'infrastructure plus sophistiqués. Terraform propose diverses instructions conditionnelles permettant d'appliquer des ressources de manière sélective ou de générer des configurations en fonction de certaines conditions. Par exemple, vous pouvez utiliser une condition pour créer une ressource uniquement si une variable d'environnement spécifique est définie, ou encore pour créer une ressource uniquement dans une région donnée.

Pour utiliser les instructions conditionnelles dans Terraform, vous pouvez utiliser les expressions if ou for_each. Vous pouvez également utiliser le paramètre count, qui vous permet de créer plusieurs instances d'une ressource particulière en fonction du nombre spécifié. L'utilisation de la logique conditionnelle vous aidera à affiner la configuration de votre infrastructure et à la rendre plus efficace.

### Validation et de Linting

Terraform propose des outils de validation et de linting qui vous aident à garantir que votre code est à la fois efficace et sans erreurs. Par exemple, l'exécution de terraform plan vous offre un aperçu des modifications que Terraform prévoit de réaliser, tandis que terraform validate vérifie votre fichier de configuration pour repérer d'éventuelles erreurs de syntaxe.

En complément des outils intégrés, vous pouvez également associer votre code Terraform à des outils de linting et de validation tels que Terrascan ou tfsec pour une vérification plus approfondie. Ces outils sont capables de détecter des problèmes fréquents et des erreurs qui pourraient passer inaperçus.

### Stockage de l’état à distance

Terraform utilise des fichiers d'état pour gérer l'état de votre infrastructure. Par défaut, Terraform stocke les fichiers d'état localement. Cependant, cette approche peut être risquée en cas de crash ou de perte de votre machine locale. De plus, il est difficile de partager des fichiers d'état avec d'autres membres de l'équipe lors d'un projet collaboratif.

Il est donc recommandé de stocker votre fichier d'état dans un emplacement centralisé, tel qu'un bucket S3 ou une base de données distante. Cela permet à plusieurs membres de l'équipe de collaborer sur la même infrastructure sans créer de conflits. Pour stocker votre état à distance, vous pouvez configurer Terraform pour utiliser un backend, tel qu'Amazon S3, Azure Blob Storage ou Consul de HashiCorp.
