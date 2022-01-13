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
update() // Exécutée 60 fois par seconde par Phaser.
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
        y: ...,

        // Devient vrai quand l'animation est arrivée à son terme.
        isFinished: false
    }; 
    
    // La liste d'animations relative à la scène principale.
    animationQueue.push(animation);
}
```

De cette manière, la fonction "update", gérée par Phaser, peut accéder à la liste d'animations et les jouer dans l'ordre. Ainsi, une fois que la première animation de la liste est arrivée à son terme, il suffit de la supprimer pour pouvoir traiter l'animation suivante.

``` js
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Si la liste d'animations n'est pas vide.
    if (animationQueue.length > 0)
    {
        // Stocke la première animation de la liste.
        const currentAnimation = animationQueue[0];

        // Identifie la type de l'animation.
        switch (currentAnimation.type)
        {
            // Type: Déplacement
            case "movement":
                // Récupère la référence de la carte concérnée par l'animation.
                const card = currentAnimation.card;
                
                // Exécution de l'animation...
                // ...
                // ...

                if (/* Animation terminée */)
                {
                    currentAnimation.isFinished = true;
                }
                break;
            // ...
        }

        // Si l'animation courante est terminée.
        if (currentAnimation.isFinished)
        {
            // Retire la première animation de la liste.
            animationQueue.shift();
        }
    }
}
```

 Cependant, comme chaque objet "animation" décrit le comportement d'une unique carte, il n'est pas possible d'exécuter plusieurs animations simultanément. Pour contourner ce problème, le système est programmé pour jouer toutes les animations à la suite qui ne sont pas interrompues par un objet "animation" de type "break" ("pause" ou "rupture" en anglais). Lorsque ce type est rencontré, le système s'assure que toutes les animations précédentes soient terminées avant de jouer le bloque d'animations suivant :

```{code-block} js
---
emphasize-lines: 25 - 36
---
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Parcours les animations de la liste (animationQueue).
    for (let i = 0; i < animationQueue.length; i++)
    {
        // Stocke l'animation courante de la liste.
        const currentAnimation = animationQueue[i];
        
        // Identifie le type de l'animation.
        if (currentAnimation.type == "movement")
        {
            // Récupère la référence de la carte concérnée par l'animation.
            const card = currentAnimation.card;
                            
            // Exécution de l'animation...
            // ...
            // ...

            if (/* Animation terminée */)
            {
                currentAnimation.isFinished = true;
            }
        }
        // ...
        else if (currentAnimation.type == "break")
        {
            // Si le type break est le premier élément de la liste.
            if (i == 0)
            {
                // Retire le premier élément de la liste.
                animationQueue.shift();
            }

            // Met fin à l'exécution de la boucle for.
            break;
        }

        // Si l'animation courante est terminée.
        if (currentAnimation.isFinished)
        {
            // Supprime l'élément courant de la liste.
            animationQueue.splice(i, 1);
        }
    }
}
```

Bien que l'emploie du "switch" soit pratique pour identifier le type d'animation, il ne peut pas être utiliser dans le cas présent car l'instruction ```break;``` du type "break" doit mettre fin à la "boucle for" et non à l'instruction "switch".

### Déplacement des cartes


### Retournement des cartes