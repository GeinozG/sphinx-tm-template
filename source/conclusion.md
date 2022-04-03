# Conclusion

## Evolution du projet
Le projet est passé par une multitude de stades et de prototypes avant d'être clair dans ses objectifs et dans sa direction au niveau du développement.

Au commencement, l'outil se voulait être basé sur l'interaction directe de l'utilisateur avec les cartes qui composent la liste. En effet, l'un des premiers prototypes avait comme fonctionnalité primaire le déplacement de cartes à l'aide de la souris. Les interactions directes avec le jeu de cartes (qui représente une liste d'éléments) étaient censées générer une sorte de pseudo-code qui décrit l'équivalent de l'action qui a été effectuée. Par exemple si l'utilisateur avait retourné la troisième carte de la liste, une instruction du type ```RetourneCarte(3)``` aurait été généré. Cette instruction, stockée dans un historique aurait pu permettre de rejouer les actions effectuées par l'utilisateur. Ainsi, ce dernier aurait pu simuler le comportement d'un algorithme afin de réellement comprendre comment il opère. Réciproquement, l'historique aurait pu contenir à l'avance toutes les actions que performe un algorithme afin de pouvoir rejouer cet algorithme devant l'utilisateur. Le but était de se détacher d'un langage de programmation afin de ne pas être limité par lui dans la compréhension des instructions générées par l'outil.

Finalement, le projet a évolué sur une approche plus personnalisable et plus ouverte au niveau de l'implémentation de l'outil afin de s'adapter aux besoins spécifiques de chaque enseignant. De plus, par son API, dont les fonctions peuvent être appelées directement à partir de code concret (Python, JavaScript ou autres langages), l'outil apporte une vision plus réaliste de la façon dont un algorithme est écrit avec du code réel. Cependant, l'aspect d'interaction entre l'outil et l'utilisateur reste présent, du moment que l'élève a la possibilité d'appeler des fonctions de l'API directement ou indirectement. Ainsi, l'implémentation de l'API dans un programme tiers devrait conserver l'interaction afin de poursuivre la philosophie sur laquelle l'outil a été développé ; il s'agit du constructivisme, une méthode d'apprentissage basé sur la représentation qu'un élève construit avec les interactions qu'il a eues avec un objet.

## Difficultés rencontrées lors du développement
A défaut d'avoir rencontré de nombreuses difficultés techniques, l'accroc principal a été la gestion du temps. En effet, l'estimation du temps pour l'ajout d'une fonctionnalité est très souvent largement inférieure à la réalité car de nombreux petits détails techniques n'apparaissent pas dans l'image globale qu'on s'imagine pour implémenter la fonctionnalité. De plus, programmer exige une grande concentration et d'être totalement impliqué afin d'être productif. Une certaine fatigue mentale survient donc rapidement et des pauses plus ou moins fréquentes sont nécessaires.

Une autre difficulté majeure rencontrée a été la gestion des animations. En effet, il s'agissait de mettre de l'ordre dans la manière dont les animations sont jouées. Par exemple, si une animation est en train d'être jouée et que, par la continuité du code, une autre animation doit être jouée, il faut attendre que la première animation soit finie avant de jouer la deuxième. Mais peut-être que l'on souhaite que les deux animations se jouent simultanément ? Il fallait permettre au développeur d'expliciter parfaitement comment les animations doivent s'enchaîner afin de prévenir tout comportement indéterminé (le code qui joue les animations est asynchrone, une animation qui dépend d'une autre n'a donc aucune connaissance de l'état de cette dernière). Cette difficulté a été surmonté grâce au système d'animation, comme expliqué dans la partie "Fonctionnement de l’outil", notamment grâce au stockage de différents types d'animations dans une liste.

## Scénarios pédagogiques
L'outil est capable de s'adapter à de nombreux cas d'utilisation d'algorithmes sur une liste. Par exemple, des algorithmes simples comme sommer toutes les valeurs contenues dans une liste ou trouver un minimum ou des algorithmes plus complexes comme la recherche dichotomique.

## Pistes d'amélioration
Finalement, l'outil respecte plutôt bien la philosophie du constructivisme et les fonctionnalités de bases sont presque toutes implémentées. Cependant, tout n'a pas pu être implémenté en raison des difficultés évoquées. Il est donc évident que le projet n'a pas atteint son potentiel maximal et diverses pistes d'amélioration sont explorables afin d'étendre les fonctionnalités de l'outil et d'améliorer celles qui sont déjà présentes. Voici par exemple une liste non exhaustive des pistes d'amélioration possibles et les suggestions sur la manière de procéder pour les effectuer :

### Variables spéciales
La création de variables spéciales est un élément qui pourrait apporter une meilleure visualisation des fonctions qui accèdent à un élément de la liste. En effet, ces variables spéciales pourraient désigner une variable "de type indice", c'est-à-dire une variable dont le seul but est de servir d'indice pour accéder à un élément d'une liste. Ce type de variable pourrait être représenté d'une couleur différente ou en forme de flèche et pointerait constamment sur l'élément de la liste dont il est l'indice :

```{figure} images/indice.png
---
---

Exemple de ce à quoi pourrait ressembler une variable "indice".
```

Les "cartes" de la liste sont représentées en bleu, les variables "normales" sont en gris et la variable spéciale, ainsi que la carte qu'elle désigne sont en rouge. Ainsi, lors d'un accès à un élément de la liste par une variable spéciale, cette dernière pourrait se manifester (par surbrillance, ou par un déplacement) afin de montrer sa participation à l'opération en cours.

Concrètement, pour implémenter cette fonctionnalité, il faudrait ajouter une nouvelle fonction dans l'API, par exemple ```vars.createIndex(name, indexValue)```. Le paramètre ```name``` correspond au nom de la variable et ```indexValue``` définit la valeur que contient la variable spéciale (l'indice d'une carte dans la liste). Ensuite, lors de l'appel de la fonction, il suffit de créer une variable similaire aux "normales" mais avec une image différente et leur ajouter un comportement qui modifie la couleur de la carte qu'elles pointent. Idéalement chaque instance de variables spéciales doit être de couleurs différentes afin d'avoir une vision claire de quelle variable spéciale pointe vers quelle carte. Enfin il suffit de créer une animation qui met simplement en évidence la variable spéciale, et d'exécuter l'animation à chaque fois que celle-ci intervient dans une opération.

### Visualisation des conditions
La visualisation des conditions n'est pas implémentée dans l'outil mais permettrait de faciliter la compréhension d'un algorithme.

```{figure} images/condition.png
---
---

Visualisation d'une condition entre la carte ```1``` et ```2```.
```

L'implémentation de cette fonctionnalité nécessiterait de créer une nouvelle animation spécifique qui pourrait être appelée par l'API de cette façon : ```cards.compare(index1, index2, comparisonOperator)```. ```index1``` serait l'indice correspondant à la première carte à comparer, ```index2``` serait l'indice de la deuxième carte à comparer et ```comparisonOperator``` serait l'opérateur de comparaison entre les deux valeurs, par exemple : ```==```, ```!=```, ```<```, ```>```, etc...

Quant au développement de l'animation en elle-même, il faudrait la découper en plusieurs séquences isolées et le enchaîner afin de créer une animation fluide. Par exemple, les deux premières cartes pourraient s'élever simultanément afin de repérer quelles sont les cartes qui vont être comparées, puis les retourner en même temps afin de voir leur valeur, suivi de l'apparition de l'opérateur de comparaison entre les cartes et du résultat de la comparaison en dernier temps. Enfin il suffit de faire disparaître l'opérateur et le résultat, de retourner à nouveau les cartes et de les replacer dans la liste. Chaque étape doit évidemment être isolée dans la liste d'animations afin que tout ne se joue pas en même temps.