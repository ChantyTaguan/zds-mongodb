La méthode ```find()``` renvoie un **curseur de résultats**, c'est à dire une référence vers un set de résultats, au contraire de ```findOne()``` qui renvoie un document directement. Lorsqu'on utilise ```find()``` dans le shell mongo sans stocker le résultat dans une variable locale, le curseur de résultats est automatiquement parcouru et affiche les 20 premiers documents.

### Exemple

Créons une collection en y insérant un grand nombre de documents.

```javascript 
for (var i = 1 ; i <= 20000 ; i++) { db.test.insert({position : i}) }
```

Si l'on sélectionne tous les documents de la nouvelle collection *test*, seuls les vingt premiers s'affichent.

```javascript
{ "_id" : ObjectId("55478814248ea52a209101e9"), "position" : 1 }
{ "_id" : ObjectId("55478814248ea52a209101ea"), "position" : 2 }
{ "_id" : ObjectId("55478814248ea52a209101eb"), "position" : 3 }
{ "_id" : ObjectId("55478814248ea52a209101ec"), "position" : 4 }
{ "_id" : ObjectId("55478814248ea52a209101ed"), "position" : 5 }
{ "_id" : ObjectId("55478814248ea52a209101ee"), "position" : 6 }
{ "_id" : ObjectId("55478814248ea52a209101ef"), "position" : 7 }
{ "_id" : ObjectId("55478814248ea52a209101f0"), "position" : 8 }
{ "_id" : ObjectId("55478814248ea52a209101f1"), "position" : 9 }
{ "_id" : ObjectId("55478814248ea52a209101f2"), "position" : 10 }
{ "_id" : ObjectId("55478814248ea52a209101f3"), "position" : 11 }
{ "_id" : ObjectId("55478814248ea52a209101f4"), "position" : 12 }
{ "_id" : ObjectId("55478814248ea52a209101f5"), "position" : 13 }
{ "_id" : ObjectId("55478814248ea52a209101f6"), "position" : 14 }
{ "_id" : ObjectId("55478814248ea52a209101f7"), "position" : 15 }
{ "_id" : ObjectId("55478814248ea52a209101f8"), "position" : 16 }
{ "_id" : ObjectId("55478814248ea52a209101f9"), "position" : 17 }
{ "_id" : ObjectId("55478814248ea52a209101fa"), "position" : 18 }
{ "_id" : ObjectId("55478814248ea52a209101fb"), "position" : 19 }
{ "_id" : ObjectId("55478814248ea52a209101fc"), "position" : 20 }
Type "it" for more
```

Remarquez le ```Type "it" for more```. Il vous suffit en effet de taper ```it``` (comme "itérateur") pour afficher les vingt documents suivants. On peut ainsi parcourir le curseur de résultat complet. Vous obtiendrez la même chose si vous stockez le résultat de ```find()``` dans une variable locale.

```javascript
// On stocke le résultat
var curseur = db.test.find()
// On affiche
curseur
```

Pour afficher les vingt suivants, vous pouvez alors utiliser ```it``` ou réutiliser ```curseur```.

# Parcours d'un curseur

En dehors du parcours manuel du curseur que l'on vient de voir, via l'affichage dans le shell et le mot-clé ```it```, il existe deux méthodes permettant de parcourir les documents d'un curseur.

- ```next()```, souvent en combinaison avec ```hasNext()```
- ```forEach()``` 

### Exemple 1 : ```next()``` et ```hasNext()```

```javascript
var curseur = db.test.find()

// hasNext() renvoie True s'il reste des documents à parcourir dans le curseur
while (curseur.hasNext()) {
  // et next() renvoie le document suivant dans le curseur
  doc = curseur.next();
  // on affiche les documents qui ont une position multiple de 1000
  if (doc.position % 1000 == 0) { printjson(doc) }
}
```

### Exemple 2 : ```forEach()```

La méthode ```forEach()``` prend en paramètre une fonction. Celle-ci va s'appliquer à tous les documents successivement.

```
// Avec une fonction anonyme
var curseur = db.test.find()

curseur.forEach(function (doc){ if (doc.position % 1000 == 0) { printjson(doc) } })

// En déclarant d'abord la fonction
var curseur = db.test.find()

var printMultipleMille = function (doc){ if (doc.position % 1000 == 0) { printjson(doc) } }
curseur.forEach(printMultipleMille)
```

# Récupérer un array de résultats

On ne peut pas revenir en arrière une fois le curseur parcouru. Les résultats utilisés (qu'ils soient simplement affichés, manipulé via une fonction ou autre) ne sont plus accessibles via le curseur. Si l'ont veut revenir au début des résultats, il est nécessaire de refaire la requête.

Dans le cas où l'on voudrait pouvoir manipuler les résultats avec plus de souplesse, on peut utiliser la méthode ```toArray()``` pour parcourir le curseur et récupérer un array de résultats.

```javascript
var curseur = db.test.find()
var array = curseur.toArray()

printjson(array[13])
printjson(array[164])
printjson(array[89])
```