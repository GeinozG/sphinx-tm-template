# Introduction

## Motivation personnelle
Le choix du sujet de ce travail résulte de mon intérêt inlassable de toujours en apprendre plus sur les sujets qui me passionnent. Parmi ceux-ci, l'informatique ou plus précisément la programmation m'a toujours fasciné. Ainsi, si mon travail permet d'aider à appréhender et à comprendre des concepts qui semblent abstraits ou difficiles au premier abord, c'est naturellement que je m'impliquerai dans le projet. C'est donc avec le souhait de rendre un peu plus accessible le monde de la programmation que j'ai fait le choix de mon sujet.

## Présentation de l'outil
L'objet de ce travail de maturité consiste en la programmation d'un outil permettant d'accompagner un professeur souhaitant développer une compréhension intuitive de la notion de liste à ses élèves. Il permet d'avoir une vision claire de ce qu'est une liste en programmation et quelles sont les manipulations qu'un ordinateur peut effectuer sur celle-ci.

Concrètement, une liste est représentée comme étant un “jeu de cartes”. Chaque élément de la liste est représenté une carte avec sa valeur affichée d’un côté de la carte. Ainsi, tous les éléments de la liste forment le jeu de cartes au complet. Certains algorithmes requièrent des variables externes à la liste. Ces variables sont créées via des appels de fonctions sur une API et sont représentées dans une zone de l’écran spécifique. Cependant, des variables “spéciales”, qui n’ont comme seul rôle d’être un indice qui parcourt les éléments d’une liste (dans une “boucle for” par exemple), auraient également dû pouvoir être créées et auraient dû être représentées comme des flèches qui pointent sur l’élément de la liste qui correspond à l’indice contenu dans la variable.

Le but étant de démontrer les actions opérées par un ordinateur sur une liste, les interactions entre les éléments de la liste entre eux ou avec une variable sont animées. Ainsi, pendant l’exécution d’un programme, ce dernier fera discrètement appel à l'API, afin qu'une animation se déclenche et montre sur l’écran le comportement de la liste.

Malheureusement, toutes les fonctionnalités prévues initialement n'ont pas pu être développées avant la fin du travail. De plus, certaines fonctionnalités ont simplement été soustraites au cahier des charges, par exemple : la possibilité de déplacer les cartes avec la souris, de garder en mémoire un historique de toutes les actions effectuées afin de rejouer la série d'instructions, ou même la possibilité d'écrire du code directement dans un éditeur juxtaposé à la zone de rendu des cartes. Bien qu'intéressantes, ces fonctionnalités ne sont pas essentielles au projet et ne sont donc envisageable que dans un second temps.

## Intérêt de l'outil
Les listes et les algorithmes sont des sujets importants à comprendre et à maîtriser dans le cadre de l’apprentissage de la programmation. Actuellement, ces concepts sont amenés aux étudiants et expliqués de manière bien trop abstraite. Il est nécessaire de comprendre ce que représente une liste pour un ordinateur et comment il peut interagir avec ; ainsi que de comprendre le fonctionnement des algorithmes, étape par étape.

Des plateformes d’enseignement de l’informatique tel que “code.org” ont un concept similaire au projet de ce travail de maturité : développer une compréhension intuitive de concepts par l’expérimentation. Cependant, leur projet cible un public jeune pour leur inculquer des bases de programmations en posant brique par brique des blocs de code afin d’amener un personnage jusqu’à son objectif. Le projet envisagé dans le cadre de ce travail de maturité cible un public plus mature, capable de pousser un raisonnement plus en profondeur. En effet les listes sont une notion plus abstraite car elles sont des conteneurs de valeurs quelconques. De plus, ce projet vise à enseigner des notions très spécifiques de la programmation : les listes.

## Technologies utilisées
Le projet repose sur 2 technologies principales : Javascript et HTML5. Cependant pour alléger la quantité de travail, un framework[^framework] de jeu 2D, Phaser[^phaser], est utilisé pour gérer tout ce qui relève de l’affichage d’images, de la gestion de scènes et d’événements et autres qui impliquent la gestion d’objets (les cartes et les variables). Enfin, le projet étant écrit en Javascript principalement, un interpréteur de code peut être nécessaire si le langage étudié par l'outil est différent.

## Configuration matérielle requise pour utiliser l'outil
Afin que l'utilisation de l'outil soit la plus accessible possible, les langages de programmation du Web ont été utilisés pour le développer. Cela signifie que pour utiliser l'outil, seul un accès à un ordinateur opérationnel étant doté d'un navigateur internet et d'une connexion est nécessaire.

## Connaissances requises pour utiliser l'outil
L'outil fait appel aux notions de variables ; éléments qui constituent la liste, ainsi qu'aux opérations de bases entre variables numérique (calculs, affectations de valeurs).

## Connaissances requises au développement de l'outil
Afin de comprendre le fonctionnement de l'outil et pouvoir continuer son développement, un niveau de base qui couvre tous les fondamentaux du Javascript est nécessaire. Le code étant largement commenté, il n'est pas forcément essentiel de savoir utiliser le framework Phaser pour comprendre le code source.

[^framework]: Voir glossaire
[^phaser]: https://phaser.io/