(questions-chapter)=
# Question

## Formulation du problème

Le problème réside dans la gestion des déplacements des cartes en JavaScript. En effet, une animation de carte dure un certain temps et pendant ce laps de temps, le code doit être "bloqué". C'est à dire que si une autre animtaion devait s'exécuter, elle devrait d'abord attendre que l'animation précédente soit terminée.

Or l'animation est gérée avec Phaser dans une fonction "update()" asynchrone, ce qui implique que la suite du code est exécuté en parallèle de l'animation, rien n'empêche donc de lancer plusieurs animations simultanément. De plus, le code principal n'est pas conscient qu'une animation est en cours, il ne peut donc pas attendre que l'animation soit finit avant d'exécuter la suite du programme.

## Solutions envisageables
En écrivant précisément le problème, les solutions envisageables viennent plus facilement à l'esprit.
Il se trouve que l'une d'entre elle m'est venu à l'esprit en écrivant.

En effet, sans se prendre la tête en utilisant les nouvelles normes "async/await" apparues de la version ES7 de JavaScript, l'utilisation d'une "Promise" à l'air envisageable afin d'attendre une "promessse" retournée par l'animation avant d'exécuter la suite du code.

Une autre approche est à prendre en compte, il s'agit de stocker les animations dans une liste. Ainsi, il serait donc possible de jouer les animations les une après les autres, selon l'ordre dans lequel elles ont été ajoutés à la liste.

Enfin, un mélange de ces deux solutions semblerait permettre de résoudre le problème aisément.