# Scénarios d'utilisation
Ce chapitre vise à apporter des idées concrètes quant à l'utilisation du projet par un professeur pour ses élèves.

## Introduction aux listes
Un premier scénario, et pas des moindres, est de faire expérimenter aux élèves toutes les opérations qu'il est possible de réaliser avec des listes : ajouter des éléments, les supprimer, les déplacer, lire leur valeur, les comparer, etc.

Les expérimentations se font directement en écrivant du code dans un langage quelconque, tant qu'il permet d'appeler les fonctions de l'API au moment d'une opération.

## Introduction aux algorithmes
Si les notions de bases de la gestion des listes sont acquises, alors, le programme est tout à fait adapté à démontrer visuellement les opérations effectuées par un algorithme reposant sur l'utilisation d'une liste ; par exemple, un algorithme de tri, de recherche ou autre :

### Sommer tous les éléments d'une liste

```{code-block} js
---
emphasize-lines: 2, 5, 10
linenos: true
---
// Liste quelconque.
const liste = [1, 2, 3, 4, 5, 6, 7, 8, 9];

// La somme est nulle au départ.
let somme = 0;

// Parcourt la liste.
for (let i = 0; i < liste.length; i++)
{
    somme += liste[i];
}

// Affiche la somme dans la console.
console.log("La somme est : " + somme);
```

Dans cet algorithme certaines lignes peuvent faire appel à l'API afin d'animer l'exécution du programme et de mieux comprendre les opérations efféctuées : 
* Ligne ```2``` : ```cards.appendList(liste)``` qui animerait la création de la liste initiale.
* Ligne ```5``` : ```vars.create("somme", 0)``` qui animerait la création de la variable ```somme``` avec sa valeur de ```0```.
* Ligne ```10``` : ```cards.add(i, "somme")``` qui ajouterait la valeur de la carte à la position ```i``` à la valeur de la variable ```somme```.

### Trouver le minimum
Cet algorithme simple à mettre en place, permet d'avoir un avant-goût du potentiel de l'outil. Sur une liste non-triée, voici à quoi ressemblerait un algorithme qui recherche la plus petite valeur dans une liste :

```{code-block} js
---
emphasize-lines: 2, 5, 10, 12
linenos: true
---
// List non-triée.
const liste = [3, 7, 2, 9, 1, 8, 4, 6, 5];

// Le minimum est un nombre de la liste.
let minimum = liste[0];

// Parcourt la liste.
for (let i = 0; i < liste.length; i++)
{
    if (liste[i] < minimum)
    {
        minimum = liste[i];
    }
}

// Affiche le minimum dans la console.
console.log("Le minimum est : " + minimum);
```

* Ligne ```2``` : ```cards.appendList(liste)```
* Ligne ```5``` : ```vars.create("minimum")``` et ```cards.read(0, "minimum")```
* Ligne ```10``` : ```mince alors```
* Ligne ```12``` : ```cards.read(i, "minimum")```