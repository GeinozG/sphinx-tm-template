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
const card = {...};

card.animation = {
    toAnimate: true,
    type: "movement",

    // Coordonnées où la carte doit se déplacer.
    x: ...,
    y: ...
};
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

Or, cette manière de procédé comporte un gros désavantage. En effet, elle ne laise au programme que la possibilité de gérer toutes les animations en même temps, ce qui signifie que si le développeur souhaite jouer des animations dans un certain ordre, il doit attendre que l'animation précédente soit terminer avant de configurer l'animation suivante dans une ou plusieurs cartes. Ce n'est pas viable pour gérer une quantité importante d'animations qui s'exécutent à la suite.

C'est pour cela qu'un véritable système d'animation est nécessaire. Concrètement, la scène principale possède un attribut "animationQueue". Il s'agit d'une liste initialement vide, qui stocke les animations les unes à la suite des autres. Ce principe simple permet de conserver l'ordre dans lequel les animations doivent être jouées ; selon l'ordre d'apparition dans la liste. Ce procédé nécessite donc également la création d'un objet "animation", qui sera l'objet stocké dans la liste animationQueue :

``` js
function moveCard(targetCard)
{
    const animation = {
        // Type de l'animation.
        type: "movement",
        
        // Contient un référence à la carte qui doit subir l'animation.
        card: targetCard,
        
        // Coordonnées où la carte doit se déplacer.
        x: ...,
        y: ...
    }; 
    
    // La liste d'animations relative à la scène principale.
    this.animationQueue.push(animation);
}
```

### Déplacements des cartes