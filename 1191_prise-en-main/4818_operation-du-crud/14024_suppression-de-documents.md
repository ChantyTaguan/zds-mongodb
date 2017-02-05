#Supprimer plusieurs document

Les méthodes ```deleteMany(filter)``` et ```remove(filter)``` permettent de supprimer tous les documents correspondant au filtre passée en paramètre.

```javascript
db.recipe.deleteMany({category: "Dessert"})

//Ou 
db.recipe.remove({category: "Dessert"})
```

```bash
{ "acknowledged" : true, "deletedCount" : 3 }
```

#Supprimer un seul document

Pour supprimer un seul document, on peut utiliser ```deleteOne(filter)``` ou ```remove(filter, {justOne : true})```.

Ce qui donne, si l'on veut supprimer la recette ayant pour texte ```37```, tout en étant sûr qu'on ne supprime qu'une seule recette :

```javascript
db.recipe.deleteOne({text : 37})
//Ou
db.recipe.remove({text : 37}, {justOne : true})
```

```bash
{ "acknowledged" : true, "deletedCount" : 1 }
```

Si l'on sélectionne les recettes, elle a bien disparu.

```javacript
db.recipe.find()
```

```bash
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli." }
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons", "text" : "Ou aux endives, pour les français.", "category" : "Entrée" }
{ "_id" : 1, "title" : "Merveilleux" }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
```

[[attention]]
| Contrairement aux méthodes ```find()``` et ```findOne()```, qui récupèrent respectivement tous les documents et le premier document si on ne leur passe pas de filtre en paramètre, ```remove()```, ```deleteMany()``` et ```deleteOne()``` ne fonctionneront pas sans filtre :
|
|```javascript
|db.recipe.remove()
|```
|
|```bash
|016-01-05T23:08:43.305+0100 E QUERY    [thread1] Error: remove needs a query :
|...
|```
|
|Cela évite de supprimer tous les documents par inadvertance. Si l'on veut supprimer tous les documents d'une collection avec la fonction ```remove()``` ou ```deleteMany()```, il faut explicitement lui passer un document vide ```{}``` en paramètre.