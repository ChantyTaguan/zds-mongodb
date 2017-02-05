Dernier opérateur bien pratique : ```$setOnUpsert```. Cet opérateur s'utilise lorsque l'on fait une upsertion, et permet d'initialiser certains attributs dans le cas où c'est une insertion qui a lieu, et pas une modification.

### Exemple

```javascript
db.character.update({name : "Plop", type : "Lutin"}, {$set : { weapon : {name : "Pioche", twoHanded : false} }, $setOnInsert : {maxMana : 5}}, {upsert : true});
db.character.findOne({name : "Plop"})
```

Le lutin Plop utilise désormais une pioche, mais comme il existait déjà, l'opérateur ```$setOnInsert``` n'a pas eu d'effet. Voyons maintenant ce qu'il se passe si le critère de sélection ne correspond à aucun personnage.

```javascript
db.character.update({name : "Gurki", type : "Orc"}, {$set : { weapon : {name : "Massue", twoHanded : false} }, $setOnInsert : {maxMana : 5}}, {upsert : true});
db.character.findOne({name : "Gurki"})
``` 

Gurki l'orc a été inséré avec sa massue, et grâce à ```$setOnInsert``` son attribut ```maxMana``` a été initialisé à 5.

```javascript
{
	"_id" : ObjectId("551ce1af8c54e5577f24b904"),
	"name" : "Gurki",
	"type" : "Orc",
	"weapon" : {
		"name" : "Massue",
		"twoHanded" : false
	},
	"maxMana" : 5
}
```