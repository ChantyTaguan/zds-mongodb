Petit rappel : une base de données est donc composée d'une ou plusieurs **collections**, dans lesquelles on enregistre des **documents**

Voici enfin venu le moment que vous attendez tous : nous allons enregistrer quelque chose dans notre base de données. Voici le document que nous allons enregistrer :

```json
{ title : "Lemon curd", text: "Idéal pour une tarte au citron !" }
```

Et voici une commande permettant d'enregistrer notre première recette dans la collections *recipe* : ```insertOne()```, qui comme son nom l'indique insère **un** document dans une collection.

```javascript
db.recipe.insertOne({ title : "Lemon curd", text: "Idéal pour une tarte au citron !" })
```

Ce à quoi MongoDB nous répond gentiment qu'il a bien inséré un document en nous renvoyant son identifiant (nous verrons en détail à quoi correspond celui-ci par la suite).

```bash
{
	"acknowledged" : true,
	"insertedId" : ObjectId("568c36ea338e0e9766676a9a")
}
```

Plusieurs choses à retenir de cette commande d'insertion.

- On travaille sur un objet ```db``` ("database"), qui représente en fait la base de données courante, dans notre cas *cookbook*.
- De cet objet ```db```, on utilise l'attribut ```recipe```, qui représente la collection *recipe*.
- On appelle alors la méthode ```insertOne()``` de la collection *recipe* en lui passant l'objet à insérer en paramètre.

La plupart des opérations courantes sur une base de données se feront suivant ce schéma : ```db.collection.methode(parametres)```.

Pour afficher les documents existant dans la collection *recipe*, on utilise la fonction ```find()``` :

```javascript
db.recipe.find()
```

```bash
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
```

On peut également utiliser la fonction ```findOne()``` pour afficher notre recette. Cette fonction affiche un seul document de la collection.

```javascript
db.recipe.findOne()
```

#Collections sans schéma

Ceux qui ont l'habitude de travailler avec des bases de données SQL auront tout de suite noté à quel point il est rapide et simple d'enregistrer un document à partir de rien.

En effet, dans une base de données SQL, avant de pouvoir enregistrer une recette dans la base de données, il aurait fallu :

- créer la base de données ;
- ensuite seulement sélectionner celle-ci ;
- créer la table *recipe*, en précisant les colonnes qu'elle contient et le type de ces colonnes ;
- et enfin enregistrer la recette elle-même.

Avec MongoDB, vous prévenez que vous comptez désormais utiliser une base de données qui s'appelle *cookbook*, qu'elle existe ou non, et vous insérez un document dans une collection *recipe*, qu'elle existe déjà ou non. En fait, MongoDB crée les collections lors de la première insertion d'un document dans celle-ci. Et crée les bases de données lorsqu'une première collection y est créée.

La commande ```use nom_db``` ne crée donc aucune base de données, elle prévient juste Mongo que dorénavant et jusqu'à nouvel ordre, l'objet ```db``` représente une base de données ```nom_db```, mais tant que celle-ci ne contient rien, elle n'existe pas de manière pérenne.

####Démonstration

- On décide d'utiliser la base de données "superbase".

```javascript
use superbase	
```
```bash
switched to db superbase
```
- On vérifie ce que contient l'objet ```db```.

```javascript
db
```
```bash
superbase
```

- On affiche la liste des bases de données existantes

```javascript
show dbs
```
```bash
cookbook   0.078GB
local  0.078GB
```
Aucune trace de la base de données ```superbase``` : elle n'existe pas encore ! Par contre, la base de données ```cookbook``` existe bel et bien : elle a été créée automatiquement lorsque nous avons enregistré notre article.

## Autres méthodes d'insertion

Revenons à nos moutons, et voyons deux autres méthodes permettant d'enregistrer des documents dans une collection :

- ```insertMany()``` : qui prend en paramètre un array de documents et permet donc d'enregistrer plusieurs documents à la fois ;
- ```insert()```: qui peut prendre un document unique ou un array de documents en paramètre[^insert].

```javascript
use cookbook
db.recipe.insertMany([
{title: "Brownie au chocolat", text: "Le chocolat, c'est bon !", category: "Dessert"},
{title: "Cupcake vanille", text: "Les cupcakes, c'est tout joli."}
])
```

[[information]]
| Notez la possibilité d'écrire une commande sur plusieurs lignes. Si vous entrez une commande incomplète et appuyez sur ||Enter||, mongo attendra gentimment que vous complètiez votre commande en affichant ```...```. S'il s'agit d'une erreur et que vous voulez recommencer (ou annuler) et non compléter la commande, il vous suffit d'appuyer deux fois de plus sur ||Enter||.

Cette fois on enregistre donc deux recettes en une fois. De plus, ces deux recettes n'ont pas la même structure : celle du brownie possède un attribut ```category```. Cela ne pose aucun problème puisque mongoDB est un système de BDD **schemaless** ("sans schéma", c'est-à-dire sans structure fixe prédéfinie).

```javascript
db.recipe.find()
```
```bash
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
{ "_id" : ObjectId("568c37da338e0e9766676a9b"), "title" : "Brownie au chocolat", "text" : "Le chocolat, c'est bon !", "category" : "Dessert" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli." }
```

Cette propriété permet une souplesse totale : vous pouvez enregistrer ce que vous voulez dans votre collection.

Bien entendu, cette souplesse a un revers : vous pouvez enregistrer ce que vous voulez dans votre collection, y compris n'importe quoi. Petit exemple utilisant la troisième méthode d'insertion :

```javascript
db.recipe.insert({text: 37, age: 12})
```

"37" est certainement un joli nombre, mais ce n'est pas un très joli *texte*. D'ailleurs, il n'est pas entouré de guillemets dans le document, ce n'est donc pas une chaîne de caractères. Quant à l'attribut ```age```, il n'a pas vraiment de sens pour une recette. Pourtant, si vous tapez cette commande, mongo ne bronche pas et enregistre un document dont l'attribut ```text``` vaut ```37``` et ```age``` vaut ```12```. Il faut donc être prudent avec ce que l'on insère.

```javascript
db.recipe.find()
```
```bash
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
{ "_id" : ObjectId("568c37da338e0e9766676a9b"), "title" : "Brownie au chocolat", "text" : "Le chocolat, c'est bon !", "category" : "Dessert" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli." }
{ "_id" : ObjectId("568c3831338e0e9766676a9d"), "text" : 37, "age" : 12 }
```

Et histoire d'être complet, voici un exemple d'utilisation de ```insert()``` avec un array de documents :

```javascript
db.recipe.insert([
{title : "Cheesecake", text : "A ma façon", category : "Dessert"},
{title : "Soupe aux chicons", text : "Ou aux endives, pour les français.", category : "Entrée"},
{title : "Meringues suisses", text : "Ca fond dans la bouche", category : "Dessert"}
])
```

Nous voici maintenant avec sept recettes dans notre collection, ce qu'on peut vérifier avec la méthode ```count()```.

```
db.recipe.count()
```

[^insert]: La méthode ```insert()``` était la seule à exister avant la version 3.2, qui a introduit les méthodes ```insertOne()``` et ```insertMany()``` avec la (louable) intention d'uniformiser et clarifier les méthodes CRUD. C'est également pour cette raison historique que la valeur de retour de ```insert()``` est différente de ```insertOne()``` et ```insertMany()```.

*[BDD]: Base de données