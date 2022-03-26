# Scénarios d'utilisation
Ce chapitre vise à apporter des idées concrètes quant à l'utilisation du projet par un professeur pour ses élèves.

## Introduction aux listes
Un premier scénario, et pas des moindres, est de faire expérimenter aux élèves toutes les opérations qu'il est possible de réaliser avec des listes : ajouter des éléments, les supprimer, les déplacer, lire leur valeur, les comparer, etc.

Les expérimentations se font directement en écrivant du code dans un langage quelconque, tant qu'il permet d'appeler les fonctions de l'API au moment d'une opération.

## Introduction aux algorithmes
Si les notions de bases de la gestion des listes sont acquises, alors, le programme est tout à fait adapté à démontrer visuellement les opérations effectuées par un algorithme reposant sur l'utilisation d'une liste ; par exemple, un algorithme de tri ou de recherche :

### Trouver le minimum
Cet algorithme simple à mettre en place, permet d'avoir un avant-goût du potentiel de l'outil. Sur une liste évidemment non-triée, voici à quoi ressemblerait un algorithme qui recherche la plus petite valeur dans une liste :

```js
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

[^pseudo-code]: 