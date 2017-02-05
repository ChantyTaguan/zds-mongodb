Nous avons vu comment créer des documents dans une collection, comment les lire, et comment les supprimer. Il ne reste donc qu'une opération du CRUD à couvrir : la modification.

Pour modifier un document, on dispose à nouveau de trois méthodes :

- ```updateOne(filter, update, options)``` : modifie un seul objet, le premier qui correspond au filtre donné en paramètre, selon les instructions passées via le paramètre ```update``` ;
- ```updateMany(filter, update, options)``` : modifie un ou plusieurs objet(s), tous ceux qui correspondent au filtre
- ```update(filter, update, options)``` : modifie un ou plusieurs objet(s).

#Les méthodes ```updateOne()``` et ```updateMany()```

On veut par exemple modifier la recette du merveilleux, en lui ajoutant une catégorie. Voici la commande à exécuter pour y arriver.

```javascript
db.recipe.updateOne({title: "Merveilleux"}, {$set : {category : "Dessert"}})
db.recipe.find()
```
```bash
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli." }
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons", "text" : "Ou aux endives, pour les français.", "category" : "Entrée" }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
{ "_id" : 1, "title" : "Merveilleux", "category" : "Dessert" }
```

Les filtres s'utilisent exactement de la même manière que pour les méthodes de sélection ```find()``` et ```findOne()```. Par contre, le paramètre ```update``` est un peu particulier. Il s'agit d'un objet, composé d'un ou plusieurs **opérateurs de modification** qui précisent de quel type de modification il s'agit, ainsi que la valeur à appliquer.
Pour l'instant, je ne m'attarderai pas sur cette notion d'opérateur, nous y reviendrons rapidement. Sachez simplement que l'opérateur ```$set``` permet de définir ou modifier la valeur d'un ou plusieurs champs.

[[information]]
| Il est intéressant de remarquer que la recette du merveilleux est désormais la dernière de la liste. En effet, le champs *category* ayant été rajouté au document, celui-ci prend plus de place qu'avant. Il a désormais une taille supérieure à la partie de la mémoire qui lui a été allouée lors de sa création et a donc été déplacé ailleurs, ce qui a modifié l'ordre naturel des documents de la collection. 

La méthode ```updateMany()``` est exactement la même, si ce n'est qu'elle modifiera tous les documents correspondant au filtre, et non pas seulement le premier. Si l'on veut modifier tous les documents de la collection, il suffit de passer un objet vide comme filtre.

```javascript
db.recipe.updateMany({}, {$set : {author : "Taguan"}})
```

```bash
{ "acknowledged" : true, "matchedCount" : 5, "modifiedCount" : 5 }
```

#La méthode ```update()```

Avant la version 3.2, dans laquelle les opérations du CRUD ont été uniformisée autant que possible, seule cette dernière méthode était disponible. Elle peut s'utiliser exactement comme ```updateOne()``` et ```updateMany()```, à deux différences près.

##1. Modification unique ou multiple

Par défaut, ```update()``` ne modifie qu'un seul document. Si l'on veut pouvoir modifier plusieurs documents en une fois, il est nécessaire de le préciser. Pour cela, on utilise le paramètre ```options```, en lui passant un objet possédant un champ nommé ```multi``` avec pour valeur ```true``` (```{multi : true}```).

####Exemple

On va modifier l'auteur de toutes les recettes pour en faire un objet imbriqué.

```javascript
db.recipe.update({}, {$set : {author : {pseudo : "Taguan"}}}, {multi : true})
```

```bash
WriteResult({ "nMatched" : 5, "nUpserted" : 0, "nModified" : 5 })
```

##2. Paramètre ```update```

Les deux méthodes ```updateOne()``` et ```updateMany()``` n'acceptent pour le paramètre ```update```qu'un objet contenant des opérateurs de modification, comme ```$set```. Lorsque l'on utilise ```update()``` on peut lui passer un simple objet en paramètre. Cet objet remplacera complètement (à l'exception de l'```_id```, qui ne peut pas être modifiée, le (ou les) objets sélectionnées grâce au filtre.

####Exemple

On modifie à nouveau le merveilleux.

```javascript
db.recipe.update({title: "Merveilleux"}, {title : "Merveilleux au café", "category" : "Dessert"})
db.recipe.find({title: "Merveilleux"})
```

```bash
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

{ "_id" : 1, "title" : "Merveilleux au café", "category" : "Dessert" }
```

Le titre a bien été modifié, l'```_id``` est resté la même, ainsi que la catégorie, puisqu'on a pris soin de la re-préciser lors de la modification. Par contre, plus aucune trace de l'auteur, qui lui n'avait pas été donné. L'objet entier passé en paramètre remplace donc bien complètement l'objet sélectionné.

[[erreur]]
| Il est impossible de mélanger de simples attributs "clé-valeur" et des opérateurs de modification. On utilise soit l'un, soit l'autre.

## Upsertion

Les trois commandes de modification permettent également de faire une "upsertion". C'est-à-dire que si aucun document correspondant au filtre n'est trouvé, un nouveau document va être inséré.

Pour cela, il faut utiliser le paramètre ```options```, et lui passer un document contenant l'attribut ```upsert : true```.

Deux possibilités, selon la modification appliquée :

- soit on utilise des opérateurs de modification. Dans ce cas, si upsertion il y a, un objet correspondant au filtre de sélection sera inséré, puis les opérateurs de modification lui seront appliqués ;
- soit on utilise un objet simple (possible uniquement avec ```update()```), et c'est cet objet là qui sera inséré.

#### Exemple avec opérateurs de modification

On veut modifier la recette de la pâte à choux pour lui ajouter une catégorie. N'étant pas sûr que cette recette existe, on ajoute le paramètre d'upsertion.

```javascript
db.recipe.updateOne({title : "Pâte à choux"}, {$set : {category : "Pâtes"}}, {upsert : true})
```

Mongo vous informe qu'aucun document n'a pu être trouvé (*matchedCount*), par conséquent, un document a été upserté, et aucun modifié (*modifiedCount*). Il vous donne même l'identifiant du document nouvellement créé (*upsertedId*) !

```bash
{
	"acknowledged" : true,
	"matchedCount" : 0,
	"modifiedCount" : 0,
	"upsertedId" : ObjectId("5691631f14861b9d33c5cef4")
}
```

Si l'on fait la même chose avec la recette de la pâte brisée, mais sans préciser l'option ```upsert```, rien ne se passe.

```javascript
db.recipe.updateOne({title : "Pâte brisée"}, {$set : {category : "Pâtes"}})
```

```bash
{ "acknowledged" : true, "matchedCount" : 0, "modifiedCount" : 0 }
```

#### Exemple avec un objet simple

On reprend la recette de la pâte brisée, mais cette fois on inclut bien l'option d'upsertion, et on utilise ```update()``` avec un objet pour le paramètre ```update```.

```javascript
db.recipe.update({title : "Pâte brisée"}, {title : "Pâte brisée", category : "Pâtes"}, {upsert : true})
```

Cette fois-ci, le document est bien inséré. Le format de retour est un peu différent puisqu'il s'agit d'une "vieille" méthode.

```bash
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("56916bab14861b9d33c5cef5")
})
```

#Super combo avec save()

Il existe enfin la méthode ```save()```, qui fait soit une insertion, soit une modification, en fonction du document qu'on lui passe en paramètre.

- Soit le document contient un attribut ```_id```, auquel cas une modification (équivalente à l'utilisation de ```update()```) est réalisée en sélectionnant le document sur base de l'```_id```.
- Soit le document ne contient pas d'attribut ```_id```, et il est alors simplement créé dans la collection (comme si l'on utilisait ```insert()```).

Par exemple, le code suivant va simplement créer la recette de la glace au chocolat.

```javascript
var glace_choco = { title : "Glace au chocolat", texte : "A déguster toute l'année"}
db.recipe.save(glace_choco)
```

```bash
WriteResult({ "nInserted" : 1 })
```

Si j'affiche la variable ```glace_choco```, je peux voir qu'elle contient maintenant un attribut ```_id```.

```javascript
glace_choco
```

```bash
{
	"title" : "Glace au chocolat",
	"texte" : "A déguster toute l'année",
	"_id" : ObjectId("56916df0338e0e9766676aa6")
}
```

Je vais alors manipuler ce document, puis le sauver avec la méthode ```save()```, ce qui va modifier le document existant au lieu d'en créer un nouveau.

```javascript
glace_choco.category = "Dessert"
db.recipe.save(glace_choco)
db.recipe.find()
```

[[information]]
| Petit rappel : le shell est un interpréteur Javascript. On peut donc manipuler les objets avec du simple code Javascript !
| 

```bash
#save()
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

#find()
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons", "text" : "Ou aux endives, pour les français.", "category" : "Entrée", "author" : { "pseudo" : "Taguan" } }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat", "author" : { "pseudo" : "Taguan" } }
{ "_id" : 1, "title" : "Merveilleux au café", "category" : "Dessert" }
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd", "text" : "Idéal pour une tarte au citron !", "author" : { "pseudo" : "Taguan" } }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille", "text" : "Les cupcakes, c'est tout joli.", "author" : { "pseudo" : "Taguan" } }
{ "_id" : ObjectId("5691631f14861b9d33c5cef4"), "title" : "Pâte à choux", "category" : "Pâtes" }
{ "_id" : ObjectId("56916bab14861b9d33c5cef5"), "title" : "Pâte brisée", "category" : "Pâtes" }
{ "_id" : ObjectId("56916df0338e0e9766676aa6"), "title" : "Glace au chocolat", "texte" : "A déguster toute l'année", "category" : "Dessert" }
```

*[CRUD]: Create Read Update Delete