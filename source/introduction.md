# Introduction (version Markdown / MyST)

```{admonition} Information
Ce chapitre d'introduction doit être complété avec les informations concernant le chapitre d'introduction au TM. 
```

À compléter ... avec les informations concernant le chapitre d'introduction au TM. 

## Informations à inclure pour un TM de type "outil"

Si le TM est du type "outil d'enseignement", ce chapitre doit contenir les informations suivantes:

* Présentation rapide de l'outil
* Intérêt de l'outil (ce qu'il apporte) et aperçu des solutions similaires existantes s'il y en a
* Aperçu des technologies utilisées
* Configuration matérielle requise pour compiler / utiliser l'outil
* Connaissances requises pour utiliser l'outil
* Connaissances requises pour comprendre le fonctionnement de l'outil

## Informations à inclure pour un TM de type "tutoriel"

Si le TM est du type "tutoriel", ce chapitre doit contenir les informations suivantes:

* Présentation très rapide du sujet / domaine
* Si le tutoriel montre comment développer un projet, présenter très rapidement le projet
* Objectifs pédagogiques du tutoriel
* Aperçu des technologies utilisées
* Configuration matérielle requise pour compiler / utiliser le projet
* Connaissances requises pour comprendre le projet 



Ce chapitre doit présenter rapidement le projet, son intérêt, les technologies utilisées.


# Introduction

## Présentation de l'outil
L'objet de ce travail de maturité consiste en la programmation d'un outil permettant d'accompagner un professeur souhaitant développer une compréhension intuitive de la notion de liste à ses élèves. Il permet d'avoir une vision clair de ce qu'est une liste en programmation et quelles sont les manipulations qu'un ordinateur peut effectuer sur celle-ci.

Concrètement, une liste est représentée comme étant un “jeu de cartes”. Chaque élément de la liste compose une carte avec sa valeur affichée d’un côté de la carte. Ainsi, tous les éléments de la liste forment à eux, le jeu de cartes au complet. Certains algorithmes requièrent des variables externes à la liste, ces variables sont créées via des appels de fonctions sur une API(à expliquer) et sont représentées dans une zone de l’écran spécifique. Cependant, des variables “spéciales”, qui n’ont comme seul rôle, d’être un index qui parcours les éléments d’une liste (dans une “boucle for” par exemple), peuvent également être créées et sont représentées comme des flèches qui pointent sur l’élément de la liste qui correspond à l’index contenu dans la variable.

Le but étant de démontrer les actions opérées par un ordinateur sur une liste, les interactions entre les éléments de la liste entre eux ou avec une variable sont animées. Ainsi, pendant l’exécution d’un programme, il fera discrétement appel à l'API afin qu'une animation se déclenche et montre sur l’écran le comportement de la liste.