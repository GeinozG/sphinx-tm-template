# Fonctionnement de l'outil
Le projet repose sur plusieurs grands aspects, qui permettent une répartition du code en différents systèmes.

## L'utilisation de Phaser
Phaser[^phaser] se décrit comme un ***framework***[^framework] de création de jeux vidéo. Il contient donc de nombreux utilitaires facilitant la gestion d'images, les déplacements et des effets en tous genres comme la distorsion d'image ou des effets de transparence.

### Système de scènes
L'un des plus gros avantages qu'offre Phaser est qu'il repose sur un système de scènes, qui possèdent des méthodes spécifiques permettant par exemple de précharger des images par la méthode *preload()* ou une méthode *update()* appelée plusieurs fois par seconde permettant d'actualiser des valeurs (très utile pour le système d'animation).

Afin de définir une scène, il faut créer une classe héritant de la scène de base de Phaser et d'implémenter ses méthodes :

``` js
class MainScene extends Phaser.Scene
{
    // Appelée lors de l'instanciation de la scène.
    constructor()
    {
        super("MainScene");
    }
    
    // Méthodes native aux scènes de Phaser :
    
    // Principalement pour charger les images.
    preload()
    {
        // ...
    }

    // Principalement pour créer des objets de Phaser.
    create()
    {
        // ...
    }

    // Appelée à intervalles réguliers
    // pour actualiser des valeurs.
    update()
    {
        // ...
    }
}
```

Pour instancier la scène, il suffit simplement de créer une variable de configuration, afin de définir la taille du ***canvas***[^canvas] utilisé pour dessiner les images, le nombre d'actualisations par seconde de la fonction *update()*, ainsi que le nom de la classe de la scène principale, et d'instancier une classe *Phaser.Game* :

``` js
const config = {
    // taille du canvas.
    width: 1000,
    height: 600,

    // Nombre d'actualisation par seconde.
    fps: {
        target: 60,
        forceSetTimeOut: true
    },

    // Scène principale.
    scene: [MainScene]
};



// Création du jeu avec les configurations.
const game = new Phaser.Game(config);
```

### Système de gestion des cartes
Les scènes de Phaser permettent la création d'objets divers, comme des images, du texte, ou autre, à afficher à l'écran. Les cartes et les variables sont en fait des objets images dont les coordonnées varient afin de les faire se déplacer :

``` js
class MainScene extends Phaser.Scene
{
    preload()
    {
        // Charge l'image des cartes en mémoire.
        this.load.image("card", "images/card.png");
    }

    create()
    {
        // Crée une carte aux coordonnées (0; 0)
        // ayant comme image : "card".
        const card = this.add.image(0, 0, "card");
    }
}
```

### Système d'événements
```{figure} images/event.jpg
---
---

Fonctionnement du système d'événements de Phaser.
```

Le système d'événements de Phaser joue un rôle crucial dans le projet. En effet, il permet la communication entre l'API et le programme en lui-même. Pour ce faire, il est nécessaire d'instancier un objet *EventEmitter*[^eventEmitter], proposé par Phaser. Cet objet permet d'émettre des événements et de les recevoir. C'est-à-dire qu'il est possible d'établir une connexion entre plusieurs fichiers ou parties de code différentes en émettant un événement contenant des paramètres qui seront transmis à une autre partie du code qui appellera une fonction en lui passant les paramètres spécifiés lors de l'envoi de l'événement. Cela revient, en résumé, à appeler une fonction qui est censée être hors de portée. Concrètement, le code se construit de la manière suivante :

Avant tout, il faut instancier l'objet *EventEmitter* :

``` js
// eventEmitter.js

const eventEmitter = new Phaser.Events.EventEmitter();
```

Ensuite, le code de l'API émet les événements :

``` js
// api.js

class Cards
{
    // Echange la position de deux cartes.
    swap(index1, index2)
    {
        // Emet l'événement.
        eventEmitter.emit(
            "swapCard", // Nom de l'événement.
            index1,     // Premier argument.
            index2      // Deuxième argument.
        );
    }
}
```

Enfin, le code du programme s'occupe d'intercepter l'événement :

``` js
// main.js

class MainScene extends Phaser.Scene
{
    create()
    {
        // Reçoit l'événement.
        eventEmitter.on(
            "swapCard",     // Nom de l'événement.
            this.swapCard,  // Fonction à appeler.
        );
    }

    // La fonction à appeler par l'événement.
    swapCard(index1, index2)
    {
        // ...
    }
}
```

## Système d'animation
Le système d'animation permet au développeur de créer des schémas d'animation. C'est-à-dire que, par exemple, le développeur peut aisément créer une animation qui engendre le déplacement simultané ou séquentiel d'une ou plusieurs cartes.

### Principe fondamental
Naïvement, on pourrait penser qu'il suffit que chaque carte possède une propriété *animation* qui contient les informations nécessaires à décrire une animation, par exemple, de déplacement :

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

Et qu'ainsi, dans la fonction *update()* gérée par Phaser, une *boucle for* parcourt toutes les cartes et effectue l'animation qui lui est attachée, si celle-ci doit être animée :

``` js
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Parcourt toutes les cartes.
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

Or, cette manière de procéder comporte un gros désavantage. En effet, elle ne laisse au programme que la possibilité de gérer toutes les animations en même temps, ce qui signifie que si le développeur souhaite en jouer dans un certain ordre, il doit attendre que l'animation précédente soit terminée avant de configurer la suivante dans une ou plusieurs cartes. Ce n'est pas viable pour gérer une quantité importante d'animations qui s'exécutent à la suite.

C'est pour cela qu'un véritable système d'animation est nécessaire. Concrètement, la scène principale possède une propriété *animationQueue*. Il s'agit d'une liste initialement vide, qui stocke les animations les unes à la suite des autres. Ce principe simple permet de conserver l'ordre dans lequel les animations doivent être jouées ; selon l'ordre d'apparition dans la liste. Ce procédé nécessite donc également la création d'un objet *animation*, qui sera l'objet stocké dans la liste animationQueue :

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

De cette manière, la fonction *update()*, gérée par Phaser, peut accéder à la liste d'animations et les jouer dans l'ordre. Ainsi, une fois que la première animation de la liste est arrivée à son terme, il suffit de la supprimer pour pouvoir traiter la suivante.

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
                // Récupère la référence de la carte concérnée.
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

 Cependant, comme chaque objet *animation* décrit le comportement d'une unique carte, il n'est pas possible d'exécuter plusieurs animations simultanément. Pour contourner ce problème, le système est programmé pour jouer toutes les animations à la suite qui ne sont pas interrompues par un objet *animation* de type *break*[^break]. Lorsque ce type est rencontré, le système s'assure que toutes les animations précédentes sont terminées avant de jouer le bloc d'animations suivant :

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

Bien que l'emploi du *switch* soit pratique pour identifier le type d'animation, il ne peut pas être utilisé dans le cas présent car l'instruction ```break;``` du type *break* doit mettre fin à la *boucle for* et non à l'instruction *switch*.

Toutefois, il est parfois nécessaire d'effectuer des opérations au moment exact où une animation prend fin. Par exemple, lorsque l'animation d'assignation d'une valeur à une variable est terminée, il faut modifier la valeur que contient la variable en question, en accord avec la valeur qui lui a été assignée pendant l'animation. Evidemment, on ne peut pas modifier sa valeur au début ou pendant l'animation, car l'animation perdrait tout son intérêt. C'est pourquoi les animations de type *break* possèdent un attribut ```.callback```[^callback] qui permet de stocker une fonction dans l'attribut et de l'appeler plus tard comme une fonction normale : ```.callback(arguments)```. Ainsi, il suffit de vérifier si une fonction *callback* est stockée dans les animations de type *break* et d'appeler la fonction stockée le cas échéant :

```{code-block} js
---
emphasize-lines: 18 - 22
---
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Parcourt les animations de la liste (animationQueue).
    for (let i = 0; i < animationQueue.length; i++)
    {
        // Stocke l'animation courante de la liste.
        const currentAnimation = animationQueue[i];
        
        // Identifie le type de l'animation.
        if (currentAnimation.type == "break")
        {
            // Si le type break est le premier élément de la liste.
            if (i == 0)
            {
                // Retire le premier élément de la liste.
                animationQueue.shift();

                if (currentAnimation.callback != null)
                {
                    // Exécute la fonction callback.
                    currentAnimation.callback();
                }
            }

            // Met fin à l'exécution de la boucle for.
            break;
        }
    }
}
```


### Déplacement des cartes
Le *framework* Phaser permet de déplacer ses objets par un procédé qui s'appelle le *tweening*[^tweening]. Ce procédé permet en effet de déplacer un objet d'un point A à un point B automatiquement et fluidement. Appliquer du tweening sur une carte exécute un code asynchrone modifiant les propriétés *x* et *y* de la carte concernée afin de la faire se diriger vers le point souhaité :

``` js
    // "this" fait référence à la scène de Phaser.
    this.tweens.add({
        // Objet concerné par l'animation.
        targets: image,

        // Coordonnées de la destination.
        x: ...,
        y: ...,

        // Durée de l'animation.
        duration: ...,

        // Type d'accélération.
        ease: 'Power1',

        // Fonction appelée à la fin de l'animation
        onComplete: function() {...}
    });
```

Cependant, ce principe ne respecte pas le fondement du système d'animation développé précédemment, qui consiste à stocker dans une liste toutes les animations créées, afin de pouvoir les jouer dans un ordre défini, simultanément ou non. En effet, le *tweening* proposé par Phaser déclenche une animation au moment même où le *tweening* est créé, ou éventuellement avec un délai mesuré en microsecondes. Par conséquent, il est préférable que les déplacements des cartes ne relèvent pas de la responsabilité de Phaser.

Il est donc nécessaire que le programme gère ce type d'animation lui-même. Pour cela, l'objet *animation* de type *movement* doit faire appel à des notions de trigonométrie élémentaires afin de calculer l'angle en radians entre le point de départ et le point d'arrivée du déplacement :

``` js
const animation = {
    // Type de l'animation.
    type: "movement",
    
    // Autres propriétés de l'animation.
    // ...

    // Angle entre le point de départ et d'arrivé.
    directionAngle: /* Angle en radian */
};
```

Pour obtenir l'angle de la direction, il suffit de calculer l'arc tangente du quotient de la différence d'ordonnée sur la différence d'abscisse entre le point de départ et d'arrivée :

``` {math}
\alpha = \arctan{\left(\frac{\Delta y}{\Delta x}\right)}
```

Javascript possède nativement un objet *Math*[^math] qui contient une fonction *atan2(y, x)*[^atan2] qui prend en paramètres les coordonnées *x* et *y* du point d'arrivée relativement au point de départ (0; 0) et retourne l'arc tangente formé par le quotient de *y* sur *x*. L'avantage de cette fonction est qu'elle gère elle-même les cas où *x* ou *y* serait égal à 0 :

```{code-block} js
---
emphasize-lines: 9 - 10
---
const animation = {
    // Type de l'animation.
    type: "movement",
    
    // Autres propriétés de l'animation.
    // ...

    // Angle entre le point de départ et d'arrivé.
    directionAngle: Math.atan2((animation.y - card.futureY),
                               (animation.x - card.futureX));
```

Comme l'angle de la direction dans laquelle la carte doit se déplacer n'est calculé qu'une seule fois au moment de la création de l'animation, la fonction *update()* de Phaser n'a plus qu'à actualiser les coordonnées de la carte en tenant compte de l'angle. Les fonctions trigonométriques sinus et cosinus de l'angle permettent d'obtenir le décalage horizontal et vertical approprié :

```{code-block} js
---
emphasize-lines: 16 - 17
---
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Parcours les animations de la liste (animationQueue).
    for (let i = 0; i < this.animationQueue.length; i++)
    {
        // Stocke l'animation courante de la liste.
        const currentAnimation = this.animationQueue[i];

        // Identifie le type de l'animation (mouvement).
        if (currentAnimation.type == "movement")
        {
            // Récupère la référence de la carte concérnée par l'animation.
            const card = currentAnimation.card;

            // Déplacement de la carte.
            card.x += Math.cos(currentAnimation.directionAngle);
            card.y += Math.sin(currentAnimation.directionAngle);
        }
    }
}
```

### Retournement des cartes
Le retournement des cartes permet de montrer ou de cacher à l'utilisateur la valeur d'une carte. Grâce à Phaser, ceci peut être géré aisément. En effet, chaque carte est une instance de la classe *Image*[^image] proposée par Phaser. Cette classe possède des propriétés *scaleX* et *scaleY* qui représentent respectivement l'étirement horizontal et vertical de l'image. Ainsi, modifier l'une ou l'autre de ces valeurs, modifie le rendu visuel de la carte en question. La valeur par défaut de ces propriétés est de 1, signifiant un étirement d'échelle 1:1 ; plus la valeur est grande, plus l'image est étirée sur l'axe correspondant :

```{figure} images/scale.PNG
---
---

Effet d'étirement de la propriété scaleX.
```

Avec cette fonctionnalité, il suffit de rétrécir totalement la carte horizontalement et de l'étirer à nouveau jusqu'à sa taille originelle pour donner l'impression d'un retournement :

```{code-block} js
---
emphasize-lines: 15, 18 - 21
---
update() // Exécutée 60 fois par seconde par Phaser.
{
    // Parcours les animations de la liste (animationQueue).
    for (let i = 0; i < this.animationQueue.length; i++)
    {
        // Stocke l'animation courante de la liste.
        const currentAnimation = this.animationQueue[i];

        // Identifie le type de l'animation (retournement).
        if (currentAnimation.type == "flip")
        {
            // Récupère la référence de la carte concernée par l'animation.
            const card = currentAnimation.card;

            card.scaleX -= 0.05;
            
            // Si la carte a fait un retournement complet.
            if (card.scaleX <= -1)
            {
                // Réinitialise l'étirment à sa valeur d'origine.
                card.scaleX = 1;

                // Animation terminée.
                currentAnimation.isFinished = true;
            }
        }
    }
}
```

[^phaser]: https://phaser.io/
[^framework]: Voir glossaire
[^canvas]: Voir glossaire
[^eventEmitter]: https://photonstorm.github.io/phaser3-docs/Phaser.Events.EventEmitter.html
[^break]: *pause* ou *rupture* en anglais
[^tweening]: Voir glossaire
[^callback]: *Fonction de rappel* en français, voir glossaire
[^math]: https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Math
[^atan2]: https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Math/atan2
[^image]: https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Image.html