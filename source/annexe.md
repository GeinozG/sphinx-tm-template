# Annexe

## Code source

### phaser.min.js
Le fichier qui initialise *Phaser* dans le projet est beaucoup trop grand pour être inclus ici. Ce lien permet d'accéder au fichier en question depuis GitHub : "https://raw.githubusercontent.com/photonstorm/phaser/master/dist/phaser.min.js"

### eventEmitter.js
```js
const eventEmitter = new Phaser.Events.EventEmitter();
```

### api.js
```js
class Cards
{
	append(value = 0, nbCards = 1)
	{
		for (let i = 0; i < nbCards; i++)
		{		
			eventEmitter.emit("createCard", value);
		}	
	}

	appendList(lstValues)
	{
		eventEmitter.emit("createCards", lstValues);
	}

	insert(value, index)
	{
		eventEmitter.emit("insertCard", value, index);
	}

	pop()
	{
		eventEmitter.emit("popCard");
	}

	swap(index1, index2)
	{
		eventEmitter.emit("swapCard", index1, index2);
	}

	move(indexCard, indexGo)
	{
		eventEmitter.emit("moveCard", indexCard, indexGo);
	}

	read(index, outVar = "")
	{
		eventEmitter.emit("readCard", index, outVar);
	}

	assign(index, value)
	{
		eventEmitter.emit("assignCardValue", index, value);
	}

	add(index1, index2, outVar = "")
	{
		eventEmitter.emit("addCards", index1, index2, outVar)
	}

	multiply(index1, index2, outVar = "")
	{
		eventEmitter.emit("multiplyCards", index1, index2, outVar)
	}

	substract(index1, index2, outVar = "")
	{
		eventEmitter.emit("substractCards", index1, index2, outVar)
	}

	divide(index1, index2, outVar = "")
	{
		eventEmitter.emit("divideCards", index1, index2, outVar)
	}

	showIndex(shouldShow)
	{
		eventEmitter.emit("showIndex", shouldShow);
	}
}

class Variables
{
	create(name, defaultValue = '?')
	{
		eventEmitter.emit("createVariable", name, defaultValue);
	}

	delete(name)
	{
		eventEmitter.emit("deleteVariable", name);
	}

	assign(name, value)
	{
		eventEmitter.emit("assignVarValue", name, value);
	}

	add(varName, cardIndex)
	{
		eventEmitter.emit("addToVar", varName, cardIndex);
	}

	substract(varName, cardIndex)
	{
		eventEmitter.emit("substractToVar", varName, cardIndex);
	}

	multiply(varName, cardIndex)
	{
		eventEmitter.emit("multiplyToVar", varName, cardIndex);
	}

	divide(varName, cardIndex)
	{
		eventEmitter.emit("divideToVar", varName, cardIndex);
	}
}
```

### MainScene.js
```js
class MainScene extends Phaser.Scene
{
	// Membres de la scène.
	lstCards;
	lstVariables;

	canvasWidth;
	canvasHeight;

	nbCardsOnSpot;

	animationID;

	animationQueue;

	indexShown;
	lstIndices;

	camera;

	constructor()
	{
		super("MainScene");

		this.lstCards = [];
		this.lstVariables = [];

		this.canvasWidth = 0;
		this.canvasHeight = 0;

		this.nbCardsOnSpot = 0;

		this.animationID = 0;

		this.animationQueue = [];

		this.indexShown = true;
		this.lstIndices = [];
	}

	// ---- Méthodes spécifiques aux scènes de Phaser ----

	preload()
	{
		this.load.image("card", "images/card.png");
		this.load.image("variable", "images/var.png");

		this.canvasWidth = this.sys.game.canvas.width;
		this.canvasHeight = this.sys.game.canvas.height;

		this.camera = this.cameras.main;
	}

	create()
	{
		eventEmitter.on("createCard", this.createCard, this);
		eventEmitter.on("createCards", this.createCards, this);
		eventEmitter.on("insertCard", this.insertCard, this);
		eventEmitter.on("popCard", this.popCard, this);
		eventEmitter.on("swapCard", this.swapCard, this);
		eventEmitter.on("moveCard", this.moveCard, this);
		eventEmitter.on("readCard", this.readCard, this);
		eventEmitter.on("assignCardValue", this.cardAssignment, this);

		eventEmitter.on("addCards", this.addCards, this);
		eventEmitter.on("multiplyCards", this.multiplyCards, this);
		eventEmitter.on("substractCards", this.substractCards, this);
		eventEmitter.on("divideCards", this.divideCards, this);

		eventEmitter.on("addToVar", this.addVar, this);
		eventEmitter.on("substractToVar", this.substractVar, this);
		eventEmitter.on("multiplyToVar", this.multiplyVar, this);
		eventEmitter.on("divideToVar", this.divideVar, this);

		eventEmitter.on("createVariable", this.createVariable, this);
		eventEmitter.on("deleteVariable", this.deleteVariable, this);
		eventEmitter.on("assignVarValue", this.variableAssignment, this);

		eventEmitter.on("showIndex", this.showIndex, this);
	}

	update()
	{
		for (let i = 0; i < this.animationQueue.length; i++)
		{
			const currentAnimation = this.animationQueue[i];

			if (currentAnimation.type == "move")
			{
				const entity = currentAnimation.entity;

				entity.x += Math.cos(currentAnimation.directionAngle) * 10;
				entity.y += Math.sin(currentAnimation.directionAngle) * 10;
				
				entity.value.x = entity.x;
				entity.value.y = entity.y;

				if (entity.type == "var")
				{
					entity.name.x = entity.x;
					entity.name.y = entity.y + entity.height;
				}

				// Quand l'animation est terminée.
				if (Math.abs(entity.x - currentAnimation.x) < 5 && Math.abs(entity.y - currentAnimation.y) < 5)
				{
					entity.x = currentAnimation.x;
					entity.y = currentAnimation.y;
					entity.value.x = entity.x;
					entity.value.y = entity.y;

					if (entity.type == "var")
					{
						entity.name.x = entity.x;
						entity.name.y = entity.y + entity.height;
					}

					this.animationQueue.splice(i, 1);
				}
			}
			else if (currentAnimation.type == "flip")
			{
				const card = currentAnimation.card;

				card.scaleX -= 0.05;
				// Quand l'animation est terminée.
				if (card.scaleX <= -1)
				{
					if (card.state == "back")
					{
						card.state == "front";
					}
					else if (card.state == "front")
					{
						card.state = "back";
					}

					card.value.visible = !card.value.visible;
					card.scaleX = 1;
					this.animationQueue.splice(i, 1);
				}
			}
			else if (currentAnimation.type == "spawn")
			{
				const card = currentAnimation.card;

				card.visible = true;
				if (card.state == "front")
				{
					card.value.visible = true;
				}

				this.animationQueue.splice(i, 1);
			}
			else if (currentAnimation.type == "value")
			{
				currentAnimation.tempValue.visible = true;
				currentAnimation.tempValue.x += Math.cos(currentAnimation.directionAngle) * 10;
				currentAnimation.tempValue.y += Math.sin(currentAnimation.directionAngle) * 10;
				
				// Quand l'animation est terminée.
				if (Math.abs(currentAnimation.tempValue.x - currentAnimation.x) < 5 && Math.abs(currentAnimation.tempValue.y - currentAnimation.y) < 5)
				{
					this.animationQueue.splice(i, 1);
					currentAnimation.tempValue.destroy();
				}
			}
			else if (currentAnimation.type == "break")
			{
				if (i == 0)
				{
					this.animationQueue.shift();
					if (currentAnimation.callback != null)
					{
						currentAnimation.callback();
					}
				}

				break;
			}
		}
	}

	// ---- Méthodes spécifiques aux animations ----
	
	animateSpawn(card)
	{
		const animation = {};
		animation.type = "spawn";
		animation.card = card;

		this.animationQueue.push(animation);
	}

	animateMove(entity, x, y)
	{
		// Configure l'animation sur la carte.
		if (entity.futureX != x || entity.futureY != y)
		{
			const animation = {};
			animation.type = "move";
			animation.entity = entity;
			animation.x = x;
			animation.y = y;
			animation.directionAngle = Math.atan2((animation.y - entity.futureY), (animation.x - entity.futureX));

			entity.futureX = x;
			entity.futureY = y;

			this.animationQueue.push(animation);
		}
	}

	animateFlip(card)
	{
		const animation = {};
		animation.type = "flip";
		animation.card = card;

		this.animationQueue.push(animation);
	}

	animateValueAffect(startX, startY, value, entityTo)
	{
		const animation = {};
		animation.type = "value";
		animation.entityTo = entityTo;
		animation.x = entityTo.futureX;
		animation.y = entityTo.futureY;
		animation.directionAngle = Math.atan2((entityTo.futureY - startY), (entityTo.futureX - startX));
		animation.tempValue = this.add.text(startX, startY, value).setOrigin(0.5);
		animation.tempValue.visible = false;

		this.animationQueue.push(animation);
	}

	// Poste un message dans la queue d'animation qui les délimite.
	postBreakAnim(callback = null)
	{
		const breakAnim = {};
		breakAnim.type = "break";
		breakAnim.callback = callback;

		this.animationQueue.push(breakAnim);
	}

	// Actualise les coordonnées des cartes par rapport à leur position.
	updateCardsCoord(shouldPostBreak = true)
	{
		for (let i = 0; i < this.lstCards.length; i++)
		{
			const currentCard = this.lstCards[i];

			this.animateMove(currentCard, i * currentCard.width + (currentCard.width / 2), this.canvasHeight - (currentCard.height / 2));
		}

		if (shouldPostBreak)
		{
			this.postBreakAnim();
		}
	}

	// Actualise les coordonnées des variables par rapport à leur position.
	updateVarsCoord(shouldPostBreak = true)
	{
		for (let i = 0; i < this.lstVariables.length; i++)
		{
			const currentVar = this.lstVariables[i];

			this.animateMove(currentVar, i * currentVar.width + i * 60 + (currentVar.width / 2), currentVar.height / 2);
		}

		if (shouldPostBreak)
		{
			this.postBreakAnim();
		}
	}

	getVarByName(varName)
	{
		for (let i = 0; i < this.lstVariables.length; i++)
		{
			const variable = this.lstVariables[i];
			if (variable.name.text === varName)
			{
				return variable;
			}
		}
	}

	// Affecte une valeur à une variable existante.
	assignVarValue(name, value)
	{
		let currentVar = this.getVarByName(name);
		currentVar.value.text = value;
	}

	showIndex(shouldShow)
	{
		this.indexShown = shouldShow;
		for (let i = 0; i < this.lstIndices.length; i++)
		{
			this.lstIndices[i].visible = shouldShow;
		}
	}

	// ---- Méthodes appelées par l'API ----
	
	// Insère une carte à une position spécifique de la liste.
	insertCard(value, index, allInOnePower = true)
	{
		// Crée la carte.
		const card = this.add.image(0, 0, "card").setOrigin(0.5);
		card.type = "card";
		card.value = this.add.text(card.x, card.y, value).setOrigin(0.5);

		card.visible = false;
		card.value.visible = false;

		card.x = this.canvasWidth / 2;
		card.y = this.canvasHeight / 2;

		card.value.type = "value";
		card.value.x = card.x;
		card.value.y = card.y;

		card.state = "front"; // ou "back"
		card.futureX = card.x;
		card.futureY = card.y;
		card.value.futureX = card.value.x;
		card.value.futureY = card.value.y;
		card.endTime = 0;

		this.lstCards.splice(index, 0, card);

		const cardIndex = this.add.text(
			48 * this.lstIndices.length + 24,	// x position
			this.canvasHeight - 60,				// y position
			this.lstIndices.length 				// valeur
		).setOrigin(0.5)
		cardIndex.visible = false;

		this.lstIndices.push(cardIndex);

		this.animateSpawn(card);
		this.updateCardsCoord(allInOnePower);
		
		if (allInOnePower)
		{
			this.animateFlip(card);
			this.postBreakAnim(() =>
			{
				if (this.indexShown)
				{
					this.lstIndices[index].visible = this.indexShown;
				}
			});
		}
	}

	// Ajoute une carte à la fin de la liste.
	createCard(value)
	{
		this.insertCard(value, this.lstCards.length);
	}

	createCards(lstValues)
	{
		for (let i = 0; i < lstValues.length; i++)
		{
			this.insertCard(lstValues[i], this.lstCards.length, false);
		}

		this.postBreakAnim();

		for (let i = 0; i < lstValues.length; i++)
		{
			this.animateFlip(this.lstCards[this.lstCards.length - lstValues.length + i]);
		}

		this.postBreakAnim(() =>
		{
			this.showIndex(this.indexShown);
		});
	}

	// Supprime la dernière carte de la liste.
	popCard()
	{
		this.lstCards[this.lstCards.length - 1].value.destroy();
		this.lstCards[this.lstCards.length - 1].destroy();
		this.lstCards.pop();
	}

	// Echange la position de deux cartes.
	swapCard(index1, index2)
	{
		const card1 = this.lstCards[index1];
		const card2 = this.lstCards[index2];

		[this.lstCards[index1], this.lstCards[index2]] = [this.lstCards[index2], this.lstCards[index1]];

		this.animateMove(card1, card1.futureX, card1.futureY - 100);
		this.animateMove(card2, card2.futureX, card2.futureY - 100);
		this.postBreakAnim();
		this.updateCardsCoord();
	}

	// Change l'emplacement de la carte dans la liste.
	moveCard(indexInit, indexGo)
	{
		const card = this.lstCards[indexInit];

		this.lstCards.splice(indexGo, 0, this.lstCards.splice(indexInit, 1)[0]);

		this.animateMove(card, card.futureX, card.futureY - 100);
		this.postBreakAnim();
		this.updateCardsCoord();
	}

	// Crée une nouvelle variable.
	createVariable(name, value)
	{
		const variable = this.add.image(this.canvasWidth / 2, this.canvasHeight / 2, "variable").setOrigin(0.5);
		variable.type = "var";
		variable.name = this.add.text(variable.x, variable.y + variable.height, name).setOrigin(0.5);
		variable.value = this.add.text(variable.x, variable.y, value).setOrigin(0.5);
		variable.value.type = "value";

		variable.futureX = variable.x;
		variable.futureY = variable.y;

		this.lstVariables.push(variable);

		this.updateVarsCoord();
	}

	// Supprime une variable existante.
	deleteVariable(name)
	{
		for (let i = 0; i < this.lstVariables.length; i++)
		{
			const currentVar = this.lstVariables[i];

			if (currentVar.name.text === name)
			{
				this.lstVariables[i].value.destroy();
				this.lstVariables[i].name.destroy();
				this.lstVariables[i].destroy();
				this.lstVariables.splice(i, 1);

				break;
			}
		}

		this.updateVarsCoord();
	}

	// Assigne une valeur à une variable.
	variableAssignment(varName, value)
	{
		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, value, this.getVarByName(varName));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(varName).name.text, value);
		});
	}

	// Additione 2 cartes et stocke le résultat dans une variable.
	addCards(index1, index2, outVar)
	{
		const card1 = this.lstCards[index1];
		const card2 = this.lstCards[index2];
		const result = parseInt(card1.value.text) + parseInt(card2.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(outVar));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(outVar).name.text, result);
		});
	}

	// Multiplie 2 cartes et stocke le résultat dans une variable.
	multiplyCards(index1, index2, outVar)
	{
		const card1 = this.lstCards[index1];
		const card2 = this.lstCards[index2];
		const result = parseInt(card1.value.text) * parseInt(card2.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(outVar));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(outVar).name.text, result);
		});
	}

	// Soustrait 2 cartes et stocke le résultat dans une variable.
	substractCards(index1, index2, outVar)
	{
		const card1 = this.lstCards[index1];
		const card2 = this.lstCards[index2];
		const result = parseInt(card1.value.text) - parseInt(card2.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(outVar));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(outVar).name.text, result);
		});
	}

	// Divise 2 cartes et stocke le résultat dans une variable.
	divideCards(index1, index2, outVar)
	{
		const card1 = this.lstCards[index1];
		const card2 = this.lstCards[index2];
		const result = parseInt(card1.value.text) / parseInt(card2.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, parseInt(result), this.getVarByName(outVar));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(outVar).name.text, parseInt(result));
		});
	}

	// Additione la valeur d'une carte à la valeur actuelle d'une variable.
	addVar(varName, cardIndex)
	{
		const card = this.lstCards[cardIndex];
		const variable = this.getVarByName(varName);
		const result = parseInt(card.value.text) + parseInt(variable.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(varName));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(varName).name.text, result);
		});
	}

	// Soustrait la valeur d'une carte à la valeur actuelle d'une variable.
	substractVar(varName, cardIndex)
	{
		const card = this.lstCards[cardIndex];
		const variable = this.getVarByName(varName);
		const result = parseInt(variable.value.text) - parseInt(card.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(varName));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(varName).name.text, result);
		});
	}

	// Multiplie la valeur d'une carte à la valeur actuelle d'une variable.
	multiplyVar(varName, cardIndex)
	{
		const card = this.lstCards[cardIndex];
		const variable = this.getVarByName(varName);
		const result = parseInt(variable.value.text) * parseInt(card.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, result, this.getVarByName(varName));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(varName).name.text, result);
		});
	}

	// Divise la valeur actuelle d'une variable par la valeur d'une carte
	divideVar(varName, cardIndex)
	{
		const card = this.lstCards[cardIndex];
		const variable = this.getVarByName(varName);
		const result = parseInt(variable.value.text) / parseInt(card.value.text);

		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, parseInt(result), this.getVarByName(varName));
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(varName).name.text, parseInt(result));
		});
	}

	// Lis la valeur d'une carte et la stocke en mémoire.
	readCard(index, outVarName)
	{
		const card = this.lstCards[index];
		const outVar = this.getVarByName(outVarName);

		this.animateMove(card, card.futureX, card.futureY - 100);
		this.postBreakAnim();

		this.animateFlip(card);
		this.postBreakAnim();

		this.animateValueAffect(card.futureX, card.futureY, card.value.text, outVar);
		this.postBreakAnim(() =>
		{
			this.assignVarValue(this.getVarByName(outVarName).name.text, card.value.text);
		});

		this.animateFlip(card);
		this.postBreakAnim();

		this.updateCardsCoord();
		this.postBreakAnim();
	}

	// Assigne une valeur à une carte.
	cardAssignment(index, value)
	{
		this.animateFlip(this.lstCards[index]);
		this.postBreakAnim();
		this.animateValueAffect(this.canvasWidth / 2, this.canvasHeight / 2, value, this.lstCards[index]);
		this.postBreakAnim(() =>
		{
			this.lstCards[index].value.text = value;
		});
		
		this.animateFlip(this.lstCards[index]);
		this.postBreakAnim();
	}
}
```

### main.js
```js
const config = {
    type: Phaser.AUTO,
    width: 1000,
    height: 600,
    fps: {
        target: 60,
        forceSetTimeOut: true
    },
    scene: [MainScene]
};

window.onload = function()
{
	const game = new Phaser.Game(config);
}
```