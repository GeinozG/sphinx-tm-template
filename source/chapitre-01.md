# Réalisation du projet

## Familiarisation avec les outils de développement
Afin de pouvoir programmer le projet, il était essentiel d'être à l'aise avec les technologies nécessaire à son développement. Pour cela, j'ai réaliser un premier prototype afin de m'assurer que j'étais capable de rassembler toutes mes connaissances récemment acquises dans un seul projet.
```{figure} images/proto.PNG
---
class: with-border
---

Capture d'écran du prototype
```

Les cartes présentes sur la capture d'écran sont des objets Phaser déplaçables avec la souris. A ce stade j'étais capable de développer un environnement dans lequel je pouvais faire évoluer le projet final.

## Problème majeur rencontré lors du développement
Lors du développement du projet, un problème majeur  est intervenu. Le problème réside dans la gestion des déplacements des cartes en JavaScript. En effet, une animation de carte dure un certain temps et pendant ce laps de temps, le code doit être "bloqué". C'est à dire que si une autre animtaion devait s'exécuter, elle devrait d'abord attendre que l'animation précédente soit terminée.

Or l'animation est gérée avec Phaser dans une fonction "update()" asynchrone, ce qui implique que la suite du code est exécuté en parallèle de l'animation, rien n'empêche donc de lancer plusieurs animations simultanément. De plus, le code principal n'est pas conscient qu'une animation est en cours, il ne peut donc pas attendre que l'animation soit finit avant d'exécuter la suite du programme.

Pour remédier à ce problème, après avoir été bloqué pendant plusieurs semaines, il m'a simplement fallut le poser par écrit. En écrivant précisément le problème, les solutions envisageables viennent plus facilement à l'esprit.
Il se trouve que l'une d'entre elle m'est venu en écrivant.

En effet, sans se prendre la tête en utilisant les nouvelles normes "async/await" apparues de la version ES7 de Javascript, l'utilisation d'une "Promise" à l'air envisageable afin d'attendre une "promessse" retournée par l'animation avant d'exécuter la suite du code.

Une autre approche est à prendre en compte, il s'agit de stocker les animations dans une liste. Ainsi, il serait possible de jouer les animations les une après les autres, selon l'ordre dans lequel elles ont été ajoutés à la liste.

C'est sur cette dernière méthode que je me suis tourné.

## Titre 1
{ref}``

```{code-block} js
---
linenos: true
---
const variable = "Var1";
```

```{figure} images/download.jpg
---
width: 200%
---
```
### Titre 2
