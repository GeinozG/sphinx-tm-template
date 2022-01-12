# Fonctionnement du projet
Le projet repose sur plusieurs grands aspects, qui permettent une répartition du code en différents systèmes.

## L'utilisation de Phaser

### Système de gestion des cartes

### Système d'évènements

### Gestion de la scène et de la boucle principale

## Système d'animation
Le système d'animation permet au développeur de créer des schémas d'animation. C'est à dire que, par exemple, le développeur peut aisément créer une animation qui engendre le déplacement simultané ou séquentiel d'une ou plusieurs cartes.

### Principe fondamental
Naïvement, on pourrait penser qu'il suffit que chaque carte possède un attribut "animation" qui possède les informations nécessaire à décrire une animation, par exemple, de déplacement :

``` js
animation = {};
animation.toAnimate = true;
animation.type = "movement";
animation.x = ...;
animation.y = ...;

card.animation = animation;
```

Et qu'ainsi, dans la fonction "update" gérée par Phaser, une "boucle for" parcours toutes les cartes et effectue l'animation qui lui est attachée, si celle-ci est a animée :

``` js
update()
{
    // Parcours toutes les cartes.
    for (let i = 0; i < nbCards; i++)
    {
        // Stocke la carte courante dans une variable.
        const currentCard = lstCards[i];

        if (currentCard.toAnimate)
        {
            // Gérer l'animation.
        }
    }
}
```

Or, cette manière de procédé comporte un gros désavantage. En effet, elle ne laise au programme que la possibilité de gérer toutes les animations en même temps, ce qui signifie que si le développeur souhaite jouer des animations dans un certain ordre, il doit attendre que l'animation précédente soit terminer avant de configurer l'animation suivante dans une ou plusieurs cartes.

### Déplacements des cartes