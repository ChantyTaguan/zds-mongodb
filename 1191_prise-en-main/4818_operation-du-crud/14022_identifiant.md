Parlons maintenant de cet attribut ```"_id"```, qui n'a jusqu'ici jamais été précisé lors de l'enregistrement des documents, mais a visiblement été créé automatiquement. Il s'agit d'un identifiant unique, ou clé primaire, pour le document. Cet attribut ```_id``` est **obligatoire**. Il est soit précisé à la création, soit créé automatiquement.

#ObjectId

Dans le cas où la clé primaire est créée par mongoDB, celle-ci est représentée par un *ObjectId*, qui est un type de données BSON. Un ObjectId est composé de 4 éléments : 

- un timestamp, c'est à dire un nombre de secondes depuis le 1e janvier 1970 à 0h00 ;
- l'identifiant de la machine sur laquelle l'objet a été créé ;
- l'identifiant du process au cours duquel l'objet a été créé ;
- et enfin un compteur.

On peut d'ailleurs voir qu'une partie de la clé primaire est commune aux sept documents créés, ce qui est logique puisqu'ils ont été créé sur la même machine et au cours du même process.

Le fait de construire les *ObjectId* de cette manière présente certains avantages.

- On peut récupérer la date de création d'un document via la méthode ```getTimestamp``` de l'*ObjectId*.

```javascript
var doc = db.recipe.findOne()  //je récupère une des recettes dans la variable doc
doc._id.getTimestamp()
```

```bash
ISODate("2016-01-05T21:34:34Z")
```

- Si l'on trie les résultats d'un requête par leur attribut ```_id```, ça revient *presque* à les trier par ordre d'insertion.

"*Presque*", car ce n'est précis qu'à la seconde. Si plusieurs processus provoquent la création de plusieurs documents en moins d'une seconde, il n'est pas garanti que l'ordre donné par les *ObjectId* de ces documents correspondra à l'ordre d'insertion. Par ailleurs, le timestamp de création correspond au timestamp du **client**, pas du serveur. Si l'horloge interne de la machine sur laquelle tourne un client est décalée, le timestamp des documents créés via ce client sera également décalé.

#Contrainte d'unicité

```_id``` est un identifiant unique au sein de chaque collection. Si on laisse MongoDB générer un ObjectId pour nous, pas la peine de s'en soucier, l'unicité est garantie. Il faut par contre être attentif si l'on utilise notre propre système d'identifiant.

####Exemple

```javascript
db.recipe.insertOne({_id : 1, title : "Merveilleux"})
```
 
```
{ "acknowledged" : true, "insertedId" : 1 }
```

[[information]]
| Notez à nouveau au passage l'extrême permissivité d'un système de base de données *schemaless*. Même les identifiants peuvent être de différents types au sein d'une collection, et il n'est nullement obligatoire (quoique conseillé en général) d'utiliser le type dédié de MongoDB ```ObjectId```

```
db.recipe.insert({_id : 1, title : "Moelleux chocolat"})
```

Cette fois, une erreur nous est renvoyée.

```
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error index: cookbook.recipe.$_id_ dup key: { : 1.0 }"
	}
})
```

#Créer explicitement un ObjetId

Si vous voulez renseigner explicitement l'attribut ```_id``` mais voulez quand même utiliser des ObjectId, on peut facilement en générer avec un simple constructeur ```ObjectId()```.

```javascript
db.recipe.insert({_id : ObjectId(), title : "Moelleux au chocolat"})
db.recipe.find()
```

```bash
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
{ "_id" : ObjectId("568c37da338e0e9766676a9b"), "title" : "Brownie au chocolat", "text" : "Le chocolat, c'est bon !", "category" : "Dessert" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli." }
{ "_id" : ObjectId("568c3831338e0e9766676a9d"), "text" : 37, "age" : 12 }
{ "_id" : ObjectId("568c38d5338e0e9766676a9e"), "title" : "Cheesecake", "text" : "A ma façon", "category" : "Dessert" }
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons", "text" : "Ou aux endives, pour les français.", "category" : "Entrée" }
{ "_id" : ObjectId("568c38d5338e0e9766676aa0"), "title" : "Meringues suisses", "text" : "Ca fond dans la bouche", "category" : "Dessert" }
{ "_id" : 1, "title" : "Merveilleux" }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
```
