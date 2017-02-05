# Existence d'un attribut

MongoDB permet d'enregistrer dans une même collection dans documents ayant des structures différentes. Il est parfois intéressant de sélectionner les documents sur base de la structure des documents, notamment sur base de l'existence ou non d'un attribut. Pour celà, on peut utiliser l'opérateur ```$exists```.

#### Exemple 1 : Les personnages pour lesquels l'attribut *portions* existe

```javascript
db.recipe.find({portions : {$exists : true}}, {title : 1})
// Ou
db.recipe.find({portions : {$exists : 1}}, {title : 1})
```
```
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons" }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille" }
{ "_id" : ObjectId("5691631f14861b9d33c5cef4"), "title" : "Pâte à choux" }
{ "_id" : ObjectId("56916df0338e0e9766676aa6"), "title" : "Glace au chocolat" }
```

#### Exemple 2 : Les recettes pour lesquels l'attribut *portions* n'existe pas

```javascript
db.recipe.find({portions : {$exists : false}}, {title : 1})
// Ou
db.recipe.find({portions : {$exists : 0}}, {title : 1})
```
```
{ "_id" : ObjectId("56916bab14861b9d33c5cef5"), "title" : "Pâte brisée" }
```

# Expression régulière

Lorsqu'un attribut comprend du texte, il est souvent intéressant de faire des recherches un peu plus complexes qu'un simple égalité. On peut pas exemple rechercher les recettes dont le titre comprend "chocolat", ou qui ont un auteur dont le pseudo comprend la lettre "l".

Pour ce faire, MongoDB permet de faire des recherches avec des expressions régulières, grâce à l'opérateur ```$regex```.

```
{ clé : { $regex : /regex/, $options : 'options' } }
//Ou
{ clé : { $regex : /regex/options } }
```

MongoDB utilise les [expressions régulières PCRE](http://www.pcre.org/). Les options sont facultatives.

#### Exemple 1 : Recette dont le nom contient par "chocolat"

```javascript
db.recipe.find({ title : { $regex : /chocolat/} }, {title : true})
```

```bash
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
{ "_id" : ObjectId("56916df0338e0e9766676aa6"), "title" : "Glace au chocolat" }
```

#### Exemple 2 : Recette dont le pseudo de l'auteur contient un "t"

```javascript
db.recipe.find({ "author.pseudo" : { $regex : /t/i} }, {title : true, "author.pseudo" : true, _id : false})
//Ou
db.recipe.find({ "author.pseudo" : { $regex : /t/, $options : 'i'} }, {title : true, "author.pseudo" : true, _id : false})
```

```
{ "title" : "Soupe aux chicons", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Cupcake vanille", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Pâte à choux", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Pâte brisée", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Glace au chocolat", "author" : { "pseudo" : "Taguan" } }
```

On a ici utilisé l'option ```i``` pour que la recherche soit insensible à la casse.

Il est également possible d'utiliser une regex avec un opérateur ```$in```. Dans ce cas, on n'utilise pas l'opérateur ```$regex```, on précise simplement l'expression régulière.

Exemple 3 : Recettes appartenant à une catégorie comprenant les combinaisons "ou" ou "au"

```javascript
db.recipe.find({categories : {$in : [/ou/, /au/]}}, {_id : 0, title : 1, categories : 1})
```
```bash
{ "title" : "Soupe aux chicons", "categories" : [ "Entrée", "Soupe" ] }
{ "title" : "Moelleux au chocolat", "categories" : [ "Dessert", "Gateau" ] }
```

# Javascript

Malgré la richesse des opérateurs disponibles (tous n'ont pas été présentés ici), il arrive que certains critères ne soient pas exprimables avec ceux-ci. Il est alors possible d'utiliser une expression javascript comme critère. Cela se fait grâce à l'opérateur ```$where```.

L'expression doit retourner un booléen (donc *vrai* ou *faux*), et on peut utiliser les mots clé ```this``` ou ```obj``` pour représenter le document.

#### Exemple : Comparaison entre attributs

On veut trouver les recettes pour lesquelles il faut exactement autant d'ingrédients différents que le nombre de portions prévues.

L'expression peut être une fonction, éventuellement passée via une variable, ou une simple expression entourée de guillemets.

```javascript
//Fonction
db.recipe.find({
  $where : function() {
    return this.ingredients.length == this.portions;
  }
}, {title : 1, portions : 1, ingredients : 1, _id : 0})

//Via une variable
var fct = function() {
    return this.ingredients.length == this.portions;
  }
db.recipe.find({$where : fct}, {title : 1, _id : 0})

//Expression (entourée de guillemets)
db.recipe.find({
  $where : "this.ingredients.length == this.portions"
}, {title : 1, _id : 0})
```
