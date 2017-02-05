On déja vu qu'il était possible de faire des requêtes sur des arrays en utilisant un critère classique ```{cle : "valeur"}```. Cela donne tous les documents dont l'array concerné contient la valeur donnée.

```javascript
db.recipe.find({categories : "Dessert"}, {title : 1, categories : 1, _id : 0})
``` 

On peut aussi utiliser ```$in``` pour récupérer les documents dont l'array contient au moins une des valeurs parmi celles données.

```javascript
db.recipe.find({categories : { $in : ["Soupe", "Pâte"]}}, {title : 1, categories : 1, _id : 0})
``` 

Si l'on veut que l'attribut corresponde parfaitement à un array donné en critère, on peut utiliser l'opérateur d'égalité ```$eq```.

```javascript
db.recipe.find({categories : {$eq : ["Entrée", "Soupe"]}}, {title : 1, categories : 1, _id : 0})
```

```
{ "title" : "Soupe aux chicons", "categories" : [ "Entrée", "Soupe" ] }
```
 
[[attention]]
| Attention qu'il s'agit bien d'une égalité parfaite, y compris dans l'ordre et le nombre des éléments. Les deux requêtes suivantes ne produiront aucun résultat.
| 
```javascript
db.recipe.find({categories : {$eq : ["Soupe", "Entrée"]}}, {title : 1, categories : 1, _id : 0})

db.character.find({sports : {$eq : ["Soupe"]}}, {title : 1, categories : 1, _id : 0})
```

Il existe également quelques opérateurs qui ne servent qu'aux arrays : ```$all```, ```$size``` et ```$elemMatch```.

# ```$all```

Cet opérateur permet de sélectionner les document pour lesquels un array possèdent toutes les valeurs données.

Par exemple, pour trouver les recettes qui sont à la fois dans les catégories "Soupe" et "Entrée" :

```javascript
db.recipe.find({categories : {$all : ["Soupe", "Entrée"]}}, {title : 1, categories : 1, _id : 0})
```

L'ordre des catégories dans le document n'a ici pas d'importance.

# ```$size```

Comme son nom l'indique, cet opérateur permet de sélectionner les documents sur base de la taille d'un array.

```
// On trouve une recette pour laquelle il faut 5 ingrédients
db.recipe.findOne({ingredients : {$size : 5}})
```

[[erreur]]
| ```$size``` n'accepte qu'un nombre. Impossible d'utiliser par exemple un opérateur, comme ```$lt``` ou ```$gte```. Si l'on veut faire des requêtes plus complexes par rapport au nombre d'éléments d'un array, il est nécessaire d'avoir dans les documents un champs qui donne le nombre d'éléments de l'array considéré.

# ```$elemMatch```

Dans le cas d'arrays contenant des documents imbriqués, on veut parfois avoir des critères complexes. Imaginons qu'on veuille sélectionner les recettes qui nécessitent plus de 300 ml d'un ingrédient. La requête suivante ne convient pas :

```javascript
db.recipe.find({"ingredients.unity" : "ml", "ingredients.quantity" : {$gt : 200}}, {title : true, ingredients : true}).pretty()
```

En effet, elle sélectionne les recettes qui ont un ingrédient (ou plus) se mesurant en ml, **et** qui ont un ingrédient avec une quantité supérieure à 200. La recette de la pâte brisée est ainsi récupérée alors qu'elle ne nécessite que 30 ml d'eau (mais 250 **grammes**) de farines.

Pour ce genre de critères, on utilise donc ```$elemMatch```, qui permet de définir un document auquel doit correspondre au moins un élément de l'array.

```javascript
db.recipe.find({ingredients : {$elemMatch : {unity : "ml", quantity : {$gt : 200}}}}, {title : 1, ingredients : 1}).pretty()
```

Cette fois, seule la glace au chocolat est sélectionnée.

# Opérateurs de projection

Voyons en passant deux opérateurs qui peuvent être utilisés dans le paramètre de projection et qui concernent les arrays :

- l'opérateur positionnel ```$``` ;
- ```$elemMatch```.

L'opérateur positionnel ```$``` peut être utilisé dans le paramètre de projection pour ne récupérer que le premier élément de l'array qui correspond au critère de recherche donné.

### Exemple 1

On récupère les recettes de desserts, mais l'on n'est pas intéressé par les autres catégories de celles-ci. On va donc utiliser l'opérateur positionnel pour ne pas récupérer ces autres catégories.

```javascript
db.recipe.find({categories : "Dessert"}, {title : 1, "categories.$" : 1, _id : 0})
``` 

```bash
{ "title" : "Moelleux au chocolat", "categories" : [ "Dessert" ] } // Et Gateau
{ "title" : "Lemon curd", "categories" : [ "Dessert" ] }
{ "title" : "Cupcake vanille", "categories" : [ "Dessert" ] } // Et Cupcakes
{ "title" : "Glace au chocolat", "categories" : [ "Dessert" ] } // Et Glace
```

[[erreur]]
| Ne pas oublier les guillemets autour de ```"sport.$"```
| 
### Exemple 2

Reprenons l'exemple des ingrédients nécessitant plus de 200ml, mais cette fois on ne récupère que ceux-ci.

```javascript
db.recipe.find({ingredients : {$elemMatch : {unity : "ml", quantity : {$gt : 200}}}}, {title : 1, "ingredients.$" : 1}).pretty()
```

Seul le lait apparaît désormais dans les ingrédients de la glace au chocolat.

Quant à ```$elemMatch```, il permet de également de ne récupérer qu'un seul élément d'un array, mais cette fois il s'agira du premier élément qui correspond à la condition donnée au critère de projection ```$elemMatch``` (et non plus au critère de sélection).

```javascript
db.recipe.findOne({}, 
   {title : 1, 
    ingredients : {
                    $elemMatch : {name : "Sucre"}
                  }
   })

db.recipe.findOne({categories : "Dessert"}, 
   {title : 1, 
    ingredients : {
                    $elemMatch : {name : "Sucre"}
                  }
   })
```

La première requête renverra la soupe, et aucun ingrédient. En effet, on utilise findOne(), sans critère de recherche. On récupère donc simplement le premier document de la collection, qui s'avère être la soupe aux chicons, laquelle ne nécessite pas de sucre.

````bash
{
	"_id" : ObjectId("568c38d5338e0e9766676a9f"),
	"title" : "Soupe aux chicons"
}
```

Dans la seconde requête, on précise qu'on sélectionne un dessert (qui a plus de chance de contenir du sucre). Résultat, on récupère cette fois le moelleux au chocolat, avec l'ingrédient correspondant au sucre.

```bash
{
	"_id" : ObjectId("568c3af2338e0e9766676aa2"),
	"title" : "Moelleux au chocolat",
	"ingredients" : [
		{
			"name" : "Sucre",
			"quantity" : 150,
			"unity" : "grammes"
		}
	]
}
```