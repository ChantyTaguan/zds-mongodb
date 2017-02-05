Les opérateurs logiques permettent de combiner plusieurs critères de différentes manières. Ils sont au nombre de quatre.

|Opérateur|Description|Syntaxe
|---|---|---|
|```$and```|Et - toutes clauses doivent être vérifiées|```{ $and : [ {critere1}, {critere2}, ... ]}```|
|```$or```|Ou - Au moins une des clauses doit être vérifiée|```{ $or : [ {critere1}, {critere2}, ... ]}```|
|```$nor```|Ni... ni... - Aucune des clauses ne peut être vérifiée|```{ $nor : [ {critere1}, {critere2}, ... ]}```|
|```$not```|Non - La clause doit être fausse|```{$not : {critere}}```|

Aux opérateurs logiques qui portent sur plusieurs critères (```$or```, ```$and``` et ```$nor```), on passe donc un array, qui contient les critères en questions. Chacun de ces critères est représenté sous forme d'objet, comme d'habitude.

### Exemple 1 : On veut les recettes de dessert pour 6 personnes

```javascript
db.recipe.find({ $and : [{portions : 6}, {categories : "Dessert"}]}, {_id : 0, title : 1, categories : 1, portions : 1})
// Est équivalent à...
db.recipe.find({portions : 6, categories : "Dessert"}, {_id : 0, title : 1, categories : 1, portions : 1})
```

```bash
{ "title" : "Glace au chocolat", "categories" : [ "Dessert", "Glace" ], "portions" : 6 }
```

Si l'on veut les recettes prévues pour 6 personnes OU qui sont des desserts, on peut utiliser l'opérateur ```$or```.

```javascript
db.recipe.find(
{
    $or : [
        {portions : 6},{categories : "Dessert"}
    ]
},{_id : 0, title : 1, categories : 1, portions : 1}) //Projection
```

```bash
{ "title" : "Soupe aux chicons", "categories" : [ "Entrée", "Soupe" ], "portions" : 6 }
{ "title" : "Moelleux au chocolat", "categories" : [ "Dessert", "Gateau" ], "portions" : 8 }
{ "title" : "Lemon curd", "categories" : [ "Dessert" ], "portions" : 8 }
{ "title" : "Cupcake vanille", "categories" : [ "Dessert", "Cupcakes" ], "portions" : 10 }
{ "title" : "Glace au chocolat", "categories" : [ "Dessert", "Glace" ], "portions" : 6 }
```

### Exemple 2 : Combinaison !

On peut combiner ces opérateurs, et les combiner avec d'autres opérateurs pour faire des requêtes plus complexes.

On va récupérer les recettes répondant à au moins un de ces critères :

- Etre une entrée
- Utiliser du chocolat comme ingrédient être prévue pour plus de 6 personnes

Faites bien attention aux ouvertures/fermetures d'accolades et de crochets, et tout devrait bien se passer.

```javascript
db.recipe.find(
{ $or :
  [ 
     {categories : "Entrée"},
     { $and :
       [
         { "ingredients.name" : "Chocolat blanc" },
         {"portions" : {$gt : 6}}
       ]
     }
  ]
}, {title : 1, _id : 0}
)
```

```
{ "title" : "Soupe aux chicons" }
{ "title" : "Cupcake vanille" }
```

### Exemple 3 : Attributs absents

On veut sélectionner les recettes qui n'ont pas un nombre de portions supérieur à 4. A priori, pour trouver ces recettes on aurait tendance à utiliser l'opérateur ```$lte```. Voyons ce que ça donne.

```javascript
db.recipe.find({portions : { $lte : 4 }}, {title : 1, portions : 1, _id : 0})
```

Seule la pâte à choux est sélectionnée par cette requête. Mais la pâte brisée n'a pas non plus un nombre de portions supérieur à 4. Et pour cause, l'attribut *portions* n'est pas défini pour cette recette.

Le critère ```$lte : 4``` ne cherche pas tout à fait ce qui est demandé. On ne veut pas les recette avec *portions* inférieur ou égal à 4, mais les recettes qui n'ont **pas** un nombre de *portions* supérieur à 4. C'est là que l'opérateur ```$not``` entre en jeu !

```
db.recipe.find({portions: {$not : {$gt : 4}}}, {title : 1, portions : 1, _id : 0})
```

```
{ "title" : "Pâte à choux", "portions" : 4 }
{ "title" : "Pâte brisée" }
```

Cette fois-ci, c'est correct : on a exclu les recettes pour lesquelles *portions* vaut plus que 4.