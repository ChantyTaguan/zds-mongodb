Avant de passer aux opérateurs de comparaison, nous allons voir comment utiliser la projection pour récupérer uniquement les attributs qui nous intéressent dans les documents sélectionnés.

Pour ce faire, on passe un deuxième paramètre à la méthode find() ou findOne() : un document avec comme clés les attributs et comme valeur un booléen (vrai si l'on veut récupérer l'attribut, false sinon).

### Exemple 1 : Récupérer uniquement le titre des recettes

```javascript
db.recipe.find({}, {title : true})
```

```
{ "_id" : ObjectId("568c38d5338e0e9766676a9f"), "title" : "Soupe aux chicons" }
{ "_id" : ObjectId("568c3af2338e0e9766676aa2"), "title" : "Moelleux au chocolat" }
{ "_id" : ObjectId("568c36ea338e0e9766676a9a"), "title" : "Lemon curd" }
{ "_id" : ObjectId("568c37da338e0e9766676a9c"), "title" : "Cupcake vanille" }
{ "_id" : ObjectId("5691631f14861b9d33c5cef4"), "title" : "Pâte à choux" }
{ "_id" : ObjectId("56916bab14861b9d33c5cef5"), "title" : "Pâte brisée" }
{ "_id" : ObjectId("56916df0338e0e9766676aa6"), "title" : "Glace au chocolat" }
```

On a bien l'attribut *title*, mais on récupère également l'attribut *_id* ! Par défaut, MongoDB renvoie toujours l'*_id*. Si vraiment on veut éviter de récupérer la clé primaire, il faut le dire explicitement.

[[information]]
| On peut aussi remarquer le document vide donné comme paramètre de requête. C'est nécessaire puisque le paramètre de projection est le deuxième. S'il n'y a qu'un seul paramètre, il sera considéré comme le paramètre de requête. Pour sélectionner tous les documents, on précise donc une requête vide, et ensuite seulement on donne le paramètre de projection.

### Exemple 2 : Récupérer le titre, sans l'*_id*

```javascript
db.recipe.find({}, {title : true, _id : false})
```

```
{ "title" : "Soupe aux chicons" }
{ "title" : "Moelleux au chocolat" }
{ "title" : "Lemon curd" }
{ "title" : "Cupcake vanille" }
{ "title" : "Pâte à choux" }
{ "title" : "Pâte brisée" }
{ "title" : "Glace au chocolat" }
```

Si l'on veut récupérer uniquement certains attributs d'un objet imbriqué, on peut utiliser la notation avec le point, comme pour la requête.

### Exemple 3 : le pseudo de l'auteur suffit

[[attention]]
| Ne pas oublier les guillemets lorsqu'on utilise le point !

```javascript
db.recipe.find({}, {title : true, _id : false, "author.pseudo" : true})
```

```
{ "title" : "Soupe aux chicons", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Moelleux au chocolat", "author" : { "pseudo" : "Leeloo" } }
{ "title" : "Lemon curd", "author" : { "pseudo" : "Leeloo" } }
{ "title" : "Cupcake vanille", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Pâte à choux", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Pâte brisée", "author" : { "pseudo" : "Taguan" } }
{ "title" : "Glace au chocolat", "author" : { "pseudo" : "Taguan" } }
```

Si un attribut n'existe pas pour l'un ou l'autre document, aucun problème : il est simplement ignoré.

Notez que ```1``` est l'équivalent de ```true```, ```0``` l'équivalent de ```false```. On peut donc également écrire :

```javascript
db.recipe.find({}, {title : 1, _id : 0, "author.pseudo" : 1})
```