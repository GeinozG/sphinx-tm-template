# Scénarios d'utilisation
Ce chapitre vise à apporter des idées concrètes quant à l'utilisation du projet par un professeur pour ses élèves.

## Introduction aux listes
Un premier scénario, et pas des moindres, est de faire expérimenter aux élèves toutes les opérations qu'il est possible de réaliser avec des listes : ajouter des éléments, les supprimer, les déplacer, lire leur valeur, les comparer, etc.

Les expérimentations se font directement en écrivant du code dans un langage quelconque, tant qu'il permet d'appeler les fonctions de l'API au moment d'une opération.

## Introduction aux algorithmes
Si les notions de bases de la gestion des listes sont acquises, alors l'outil devrait être en tout cas partiellement adapté à démontrer visuellement les opérations effectuées par un algorithme reposant sur l'utilisation d'une liste :

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

Dans cet algorithme certaines lignes peuvent faire appel à l'API afin d'animer l'exécution du programme et permettre à l'apprenant de mieux comprendre les opérations effectuées : 
* Ligne ```2``` : ```cards.appendList(liste)``` anime la création de la liste initiale.
* Ligne ```5``` : ```vars.create("somme", 0)``` anime la création de la variable ```somme``` avec sa valeur de ```0```.
* Ligne ```10``` : ```vars.add("somme", i)``` ajoute la valeur de la carte à la position ```i``` à la valeur de la variable ```somme```.

### Trouver le minimum
Cet algorithme simple à mettre en place, permet d'avoir un avant-goût du potentiel de l'outil. Sur une liste non triée, voici à quoi ressemblerait un algorithme qui recherche la plus petite valeur dans une liste :

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

* Ligne ```2``` : ```cards.appendList(liste)``` anime la création de la liste initiale.
* Ligne ```5``` : ```vars.create("minimum")``` et ```cards.read(0, "minimum")``` anime la création de la variable ```minimum``` et lui assigne la valeur de la carte à l'indice ```0```.
* Ligne ```10``` : Il aurait été intéressant de pouvoir montrer visuellement la condition mais cette fonctionnalité ne figure pas dans la version actuelle de l'outil.
* Ligne ```12``` : ```cards.read(i, "minimum")``` assigne la valeur de la carte à l'indice ```i``` à la variable ```minimum```.

### Recherche dichotomique
La recherche dichotomique fait partie des algorithmes intéressants auxquels l'outil n'apporte pas encore de réels soutiens visuels qui pourraient aider à la compréhension, les fonctionnalités cruciales qu'il manque à l'outil sont la possibilité de créer des variables spéciales dont la valeur est un indice qui désigne une carte de la liste, et la visualisation des conditions :

```{code-block} js
---
emphasize-lines: 5, 8, 11, 12, 14, 17, 20, 22, 27, 30, 35
linenos: true
---
// La liste passée en paramètre doit être triée.
function recherche(liste, valeur)
{
    // Borne inférieure prise en compte dans la recherche.
    let premier = 0;

    // Borne supérieure prise en compte dans la recherche.
    let dernier = liste.length - 1;
    
    // Déclarations de variables utiles.
    let milieu;
    let element;

    while (premier <= dernier)
    {
        // Milieu des bornes inférieure et supérieure.
        milieu = Math.floor((premier + dernier) / 2);

        // Element courant de la liste.
        element = liste[milieu];

        if (element == valeur)
        {
            // Retourne l'indice de l'élément trouvé.
            return milieu;
        }
        else if (element < valeur)
        {
            // Décale la borne inférieure après le milieu.
            premier = milieu + 1;
        }
        else
        {
            // Décale la borne supérieure avant le milieu.
            dernier = milieu - 1;
        }
    }

    // Retourne -1 si la valeur n'a pas été trouvée.
    return -1;
}

// Affiche les résultats de recherches.
console.log(recherche([1, 2, 3, 4, 5, 6, 7, 8, 9], 5));  // 4
console.log(recherche([1, 2, 3, 4, 5, 6, 7, 8, 9], 10)); // -1
```

Aux lignes ```5```, ```8``` et ```11```, le mieux aurait été de pouvoir créer des variables spéciales de type "indice" qui montrent visuellement la carte à laquelle elles font référence dans la liste. De plus, aux lignes ```14```, ```22``` et ```27```, l'animation des conditions ne figurent pas encore comme fonctionnalités de l'outil :

* Ligne ```5``` : ```vars.create("premier", 0)``` anime la création de la variable ```premier```.
* Ligne ```8``` : ```vars.create("dernier", liste.length - 1)``` anime la création de la variable ```dernier```.
* Ligne ```11``` : ```vars.create("milieu")``` anime la création de la variable ```milieu``` avec une valeur par défaut.
* Ligne ```12``` : ```vars.create("element")``` anime la création de la variable ```element``` avec une valeur par défaut.
* Ligne ```17``` : ```vars.assign("milieu", Math.floor((premier + dernier) / 2))``` anime l'assignation de la valeur à la variable ```milieu```.
* Ligne ```20``` : ```vars.assign("element", liste[milieu])``` anime l'assignation de la valeur à la variable ```element```.
* Ligne ```30``` : ```vars.assign("premier", milieu + 1)``` anime l'assignation de la valeur à la variable ```premier```.
* Ligne ```35``` : ```vars.assign("dernier", milieu - 1)``` anime l'assignation de la valeur à la variable ```dernier```.