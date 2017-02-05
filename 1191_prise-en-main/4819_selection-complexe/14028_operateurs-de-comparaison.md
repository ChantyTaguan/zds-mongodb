Revenons à nos critères de sélection. On sait jusqu'ici demander à ce qu'un attribut (de l'objet même ou d'un objet imbriqué) soit égal à une certaine valeur. Mais comment trouver les documents pour lesquels un attribut est plus grand qu'une valeur donné ? Plus petit ? Différent ?

Pour cela, on utilise les opérateurs de comparaisons. Dans MongoDB, les opérateurs commencent par le caractère ```$```, suivi du nom de l'opérateur. On a déjà croisé un opérateur lorsqu'on a vu la modification des documents : ```$set```.

Il existe 8 opérateurs de comparaisons :

|Opérateur|Description| |
|---|---|--|
|```$gt```|*"Greater than"* - Plus grand que| > |
|```$gte```|*"Greater than or equal"* - Plus grand ou égal| >= |
|```$lt```|*"Lower than"* - Plus petit que| < |
|```$lte```|*"Lower than or equal"* - Plus petit ou égal| <=|
|```$eq```|*"Equal"* - Egal| =
|```$ne```|*"Not equal"* - Différent| =/=
|```$in```|*"In"* - Littéralement : *dedans*, vérifie l'existence d'une valeur dans un array donné|  |
|```$nin```|*"Not in"* - Littéralement : *pas dedans*, vérifie qu'une valeur n'existe pas dans un array donné|  |

# Utilisation

Ces opérateurs sont utilisés dans le document servant de critère de sélection, avec la syntaxe suivante :

```
{ clé : {$opérateur : valeur} }
```

On peut, pour le même attribut, utiliser plusieurs opérateurs :

```
{ clé : {$opérateur1 : valeur1, $opérateur2 : valeur2} }
```

### Exemple 1 : Recettes prévues pour plus de 6 personnes

```javascript
db.recipe.find({portions : {$gt : 6}}, {title : 1, portions : 1, _id : 0})
```

```bash
{ "title" : "Moelleux au chocolat", "portions" : 8 }
{ "title" : "Lemon curd", "portions" : 8 }
{ "title" : "Cupcake vanille", "portions" : 10 }
```

### Exemple 2 : Recettes prévues pour au moins 4 personnes, mais moins de 8.

```javascript
db.recipe.find({portions : {$gte : 4, $lt : 8}}, {title : 1, portions : 1, _id : 0})
```

```bash
{ "title" : "Soupe aux chicons", "portions" : 6 }
{ "title" : "Pâte à choux", "portions" : 4 }
{ "title" : "Glace au chocolat", "portions" : 6 }
```

### Exemple 3 : Toutes les recettes, sauf les cupcakes vanille

```javascript
db.recipe.find({title : {$ne : "Cupcake vanille"}}, {title : 1, _id : 0})
```

```
{ "title" : "Soupe aux chicons" }
{ "title" : "Moelleux au chocolat" }
{ "title" : "Lemon curd" }
{ "title" : "Pâte à choux" }
{ "title" : "Pâte brisée" }
{ "title" : "Glace au chocolat" }
```

### Exemple 4 : Les recettes appartenant à une des catégories parmi celles données

```javascript
db.recipe.find({categories : {$in : ["Pâte", "Cupcakes"]}}, {title : 1, categories : 1, _id : 0})
```

```bash
{ "title" : "Cupcake vanille", "categories" : [ "Dessert", "Cupcakes" ] }
{ "title" : "Pâte à choux", "categories" : [ "Pâte" ] }
{ "title" : "Pâte brisée", "categories" : [ "Pâte" ] }
```

### Exemple 5 : Les recettes qui ne sont ni des entrées, ni des desserts

```javascript
db.recipe.find({categories : {$nin : ["Glace", "Entrée"]}}, {title : 1, categories : 1, _id : 0})
```

```bash
{ "title" : "Moelleux au chocolat", "categories" : [ "Dessert", "Gateau" ] }
{ "title" : "Lemon curd", "categories" : [ "Dessert" ] }
{ "title" : "Cupcake vanille", "categories" : [ "Dessert", "Cupcakes" ] }
{ "title" : "Pâte à choux", "categories" : [ "Pâte" ] }
{ "title" : "Pâte brisée", "categories" : [ "Pâte" ] }
```