Il existe trois méthodes permettant de jouer avec l'ordre des résultats d'une requêtes, et le nombre de documents récupérés.

- ```sort(sortDoc)``` : trie les résultats en fonction du document passé en paramètre.
- ```limit(nb)``` : limite le nombre de documents récupérés.
- ```skip(nb)``` : indique un nombre de résultats à ne pas récupérer.

# Tri

Le paramètre de tri est un document, ayant pour clés les attributs sur lesquels les résultats doivent être triés, et pour valeur ```1``` si le tri est ascendant, ```-1``` s'il est descendant.

### Exemples

```javascript
//Trier par ordre alphabetique du nom
db.character.find({}, {_id:0, name:1, maxMana:1}).sort({name:1})

//Trier par maxMana décroissant, puis par ordre alphabétique pour les maxMana égaux
db.character.find({}, {_id:0, name:1, maxMana:1}).sort({maxMana : -1, name : 1})
```

Il est tout à fait possible de trier sur base de l'attribut d'un document imbriqué.

### Exemple ordre alphabétique du nom des armes

```javascript
db.character.find({}, {_id:0, name:1, weapon:1}).sort({"weapon.name":1})
```

# Restriction des résultats

Les méthodes ```limit(nb)``` et ```skip(nb)``` prennent un nombre entier en paramètre, représentant respectivement le nombre de résultats à récupérer et le nombre de résultat à passer.

### Exemples

```
// On récupère seulement 2 résultats
db.character.find().limit(2)

// On passe 2 résultats (du coup, on n'en récupère que 3 puisqu'il y en a 5 au total)
db.character.find().skip(2)
```

# Curseurs et combinaisons de méthodes

Si ```limit()``` peut parfaitement s'utiliser seule, ça n'a pas beaucoup de sens d'utiliser ```skip()``` si les résultats ne sont pas également triés. En effet, ```skip(nb)``` va passer les *nb* premiers résultats, mais sans tri explicite, on ne peut pas être sûr de l'ordre des résultats. 

Les trois méthodes (```sort()```, ```limit()``` et ```skip()```) s'appliquent à un curseur de résultats et renvoient un curseur de résultats, de sorte qu'il est possible de chaîner les appels à ces trois fonctions.

Par ailleurs, l'ordre de chaînage n'a pas d'importance. En effet, un curseur de résultats ne va pas chercher les résultats dans la base de données tant qu'ils ne sont pas utilisés, c'est-à-dire affichés ou manipulés. Appliquer une méthode ```sort()``` ou ```skip``` à un curseur modifie celui-ci mais ne le parcourt pas.

```sort()```, ```limit()``` et ```skip()``` n'agissent donc pas sur les résultats, mais bien sur le curseur. On peut utiliser les méthodes dans l'ordre qu'on veut, le curseur final sera le même. 

```javascript
//Bien préciser "var" à chaque assignation sinon les résultats associés au curseur seront affichés
var cursor = db.character.find({}, {name : 1, maxLife : 1, _id : 0})
var cursor = cursor.sort({maxLife : -1})
var cursor = cursor.limit(3)
var cursor = cursor.skip(1)
cursor
```

La série d'opération ci-dessus est équivalente à la requête suivante.

```
db.character.find({}, {name : 1, maxLife : 1, _id : 0}).skip(1).sort({maxLife : -1}).limit(3)
```