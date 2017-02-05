Nous avons déjà vu les deux fonctions qui permettent de sélectionner des documents :

- ```find()``` sélectionne plusieurs documents ;
- ```findOne()``` sélectionne un seul document.

Supposons maintenant que je veuille sélectionner seulement certains documents : je peux donner un document en paramètre à ```find()``` ou ```findOne()```, qui ne sélectionneront alors que les documents qui correspondent à celui passé en paramètre.

Par exemple, pour demander les documents dont l'attribut ```title``` vaut ```"Cheesecake"```, on utilise le document suivant :

```
{title : "Cheesecake"}
```

Ce qui donne :

```javascript
//Pour une liste de documents
db.recipe.find({title : "Cheesecake"})
//Ou, si on ne veut qu'un seul document
db.recipe.findOne({title : "Cheesecake"})
```

```bash
{ "_id" : ObjectId("5676b494338e0e9766676a92"), "title" : "Premier article", "text" : "Hello World !" }
```

[[information]]
| Vous aurez remarqué que la méthode ```findOne()```, en plus de ne renvoyer qu'un seul document, permet aussi de formatter l'affichage de celui-ci afin qu'il soit plus lisible. Il est possible d'appliquer le même formattage au résultat de la méthode ```find()``` en utilisant la méthode ```pretty()``` : ```db.recipe.find().pretty()```

Il est bien sûr possible de préciser plusieurs attributs :

```javascript
db.recipe.find({title : "Meringues suisses", category : "Dessert"})
```

#Ordre naturel

Lorsqu'on ne précise pas d'ordre particulier lors de la sélection de documents, MongoDB va utiliser l'*ordre naturel* des documents. C'est particulièrement important pour la fonction ```findOne()```, qui ne renvoie qu'un seul document : le premier document dans l'ordre naturel.

Cet ordre naturel correspond à l'ordre de lecture des documents sur le disque. Lorsque MongoDB enregistre des documents, les enregistrements sont contigus sur le disque. Chaque enregistrement prend la taille du document qu'il contient, plus un espacé réservé, au cas où le document est enrichi dans le futur, et donc prend plus de place. Il arrive cependant qu'un document finisse par dépasser l'espace qui lui est alloué, auquel cas il doit être changé de place sur le disque. De plus, lorsque des documents ont été supprimés, l'espace qu'ils occupaient est libéré, et d'autres documents peuvent venir prendre leur place.

En d'autres termes, l'ordre naturel correspond au départ à l'ordre d'insertion, mais plus on fait d'opérations sur les documents de la base de données, plus l'ordre naturel s'éloigne de l'ordre d'insertion.