(questions-chapter)=
# Questions

## Formulation du problème

Le problème réside dans la gestion des déplacements des cartes en JavaScript. En effet, une animation de carte dure un certain temps et pendant ce laps de temps, le code doit être "bloqué". C'est à dire que si une autre animtaion devait s'exécuter, elle devrait d'abord attendre que l'animation précédente soit terminée.

Or l'animation est gérer avec Phaser dans une fonction update asynchrone, ce qui implique que la suite du code est exécuter en parallèle de l'animation, rien n'empêche donc de 

### Titre 2

