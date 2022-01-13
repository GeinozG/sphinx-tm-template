# Fonctionnement du projet
Le projet repose sur plusieurs grands aspects, qui permettent une répartition du code en différents systèmes.

## L'utilisation de Phaser

### Système de gestion des cartes

### Système d'évènements

### Gestion de la scène et de la boucle principale

## Système d'animation
Le système d'animation permet au développeur de créer des schémas d'animation. C'est à dire que, par exemple, le développeur peut aisément créer une animation qui engendre le déplacement simultané ou séquentiel d'une ou plusieurs cartes.

### Principe fondamental
Naïvement, on pourrait penser qu'il suffit que chaque carte possède un attribut "animation" qui contient les informations nécessaires à décrire une animation, par exemple, de déplacement :

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

Et qu'ainsi, dans la fonction "update" gérée par Phaser, une "boucle for" parcours toutes les cartes et effectue l'animation qui lui est attachée, si celle-ci doit être animée :

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
function moveCard(targetCard, ...)
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

De cette manière, la fonction "update", gérée par Phaser, peut accéder à la liste d'animations et les jouer dans l'ordre. Ainsi, une fois que la première animation de la liste est arrivée à son terme, il suffit de la supprimer et de traiter l'animation suivante.

``` js
code qui montre ce qui vient d etre explique.
```

 Cependant, comme chaque objet "animation" décrit le comportement d'une unique carte, il n'est donc pas possible d'exécuter plusieurs animations simultanément. Pour contourner ce problème, le système est programmé pour jouer toutes les animations à la suite qui ne sont pas interrompues par un objet "animation" de type "break" ("pause" ou "rupture" en anglais). Lorsque ce type est rencontré, le système s'assure que toutes les animations précédentes soient terminées avant de jouer le bloque d'animations suivant :

 ``` js
 ```

### Déplacements des cartes