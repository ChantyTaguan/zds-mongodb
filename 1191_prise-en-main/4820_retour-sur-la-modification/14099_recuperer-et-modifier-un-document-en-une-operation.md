La méthode ```findAndModify()``` modifie un document (suppresion, upsertion ou modification), et renvoie ce même document.

Quel intérêt me direz-vous ? Deux choses :

1. Si vous modifiez un document avec la méthode ```update()``` pour ensuite récupérer le document que vous venez de modifier avec ```find()```, il est possible que pendant le lapse de temps entre la modification et la récupération du document, quelqu'un d'autre ait modifié le document. Vous vous retrouvez donc potentiellement avec un document qui n'est pas cohérent avec l'action que vous venez d'effectuer.
2. Par défaut, la méthode ```update()``` ne modifie qu'un seul document, même si plusieurs correspondent au critère de recherche donné. Il est impossible de savoir quel document parmi tous ceux possibles va être modifié. Par conséquent, il est compliqué de récupérer par la suite le document qui a été modifié. ```findAndModify()``` permet d'agir sur le document qui sera modifié si plusieurs documents correspondent au critère, et renvoie directement le document modifié.

Cette méthode prend un paramètre un objet qui peut contenir les attributs suivants.

|Attribut|Type de valeur|Description
|---|---|---
|```query```|Document|Facultatif. Critère de sélection classique, identique au premier paramètre de la méthode ```find()```|
|```fields```|Document|Facultatif. Critère de projection à utiliser pour choisir les attributs du document qu'il faut récupérer, identique au second paramètre de la méthode ```find()```|
|```sort```|Document|Facultatif. Permet de trier les documents correspondant au critère de sélection. Seul le premier document sera affecté et récupéré par ```findAndModify()```, il est impossible de modifier plusieurs documents avec cette méthode.|
|```update```|Document|Obligatoire, sauf en cas de suppression (voir paramètre ```remove```). Modification à effectuer sur le document, identique au second paramètre de la méthode ```update()```|
|```remove```|Booléen|Obligatoire, sauf si le paramètre ```update``` est donné. Supprime le premier document correspondant au critère ```query``` (éventuellement influencé par ```sort```). Par défaut, ```remove``` vaut ```false```.|
|```upsert```|Booléen|Facultatif, ne s'utilise pas en cas de suppression. Défini si la méthode doit créer un document si aucun document existant ne correspond à la requête. Par défaut, on ne fait pas d'upsertion.|
|```new```|Booléen|Facultatif, ignoré en cas de suppression. Définit si le document récupéré par ```findAndModify()``` est le document *après* modification ou *avant* modification. Par défaut, il vaut ```false``` et renvoie donc le document **non modifié**.|

### Exemple : modification

Modifions Harry en lui ajoutant l'escalade comme sport.

```javascript
db.character.findAndModify({
  query : { name : "Harry" },
  fields : { name : 1, _id : 0, sports : 1 },
  update : { $push : { sports : "Escalade" } }
})
```
On récupère bien Harry, mais il ne fait toujours pas d'escalade. Normal : on n'a pas précisé le paramètre ```new``` qui vaut false par défaut. C'est donc le document **avant** modification qui est récupéré.

```bash
{ "name" : "Harry", "sports" : [ "Basket", "Rugby", "Tir à l'arc" ] }
```

Vérifions donc qu'avec ```new : true```, on récupère bien le document après modification en lui rajoutant un cinquième sport.

```javascript
db.character.findAndModify({
  query : { name : "Harry" },
  fields : { name : 1, _id : 0, sports : 1 },
  update : { $push : { sports : "Vélo" } },
  new : true
})
```

```javascript
{
	"name" : "Harry",
	"sports" : [
		"Basket",
		"Rugby",
		"Tir à l'arc",
		"Escalade",
		"Vélo"
	]
}
```

### Exemple : critère correspondant à plusieurs documents

Nous allons retirer 7 à la *maxLife* du sorcier ayant la *maxLife* la plus haute. Le critère de sélection correspondra donc à tous les sorciers, et on utilisera un tri pour que le document modifié soit bien celui qu'on veut.

```javascript
db.character.find({ type : "Sorcier" }, { name : 1, _id : 0, maxLife : 1 })

db.character.findAndModify({
  query : { type : "Sorcier" },
  sort : {maxLife : -1},
  fields : { name : 1, _id : 0, maxLife : 1 },
  update : { $inc : { maxLife : -7 } },
  new : true
})
```

Hermione avait 62, Harry 50. Désormais, Hermione n'a plus que 55 points de vie au maximum, tandis qu'Harry n'a pas été modifié.

### Exemple : upsertion

Si Rincevent n'existait pas, il faudrait l'inventer ! Allons-y donc pour *upserter* ce fameux mage.

```javascript
db.character.findAndModify({
  query : { name : "Rincevent", type : "Sorcier" },
  update : { $set : { maxLife : 45, maxMana : 2 } },
  upsert : true,
  new : true
})
```

Ne pas oublier de préciser ```new : true```, sinon, dans le cas d'une upsertion, on récupère ```null``` puisque la version avant modification (ou plutôt insertion) n'existe pas.

```javascript
{
	"_id" : ObjectId("5581cfef8698579ee3634631"),
	"name" : "Rincevent",
	"type" : "Sorcier",
	"maxLife" : 45,
	"maxMana" : 2
}
```

### Exemple : suppression

Terminons enfin avec une suppression : finalement Rincevent ne sert à rien puisqu'il est incapable de faire de la magie !

```javascript
db.character.findAndModify({
  query : { name : "Rincevent" },
  remove : true
})
```

Cette fois par contre, ça n'a pas de sens de mettre ```new : true```. Cela provoquerait une erreur puisqu'après modification (c'est-à-dire suppression dans ce cas), le document n'existe plus.