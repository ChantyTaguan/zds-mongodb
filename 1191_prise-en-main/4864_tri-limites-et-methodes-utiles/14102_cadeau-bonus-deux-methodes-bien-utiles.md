Terminons cette partie avec deux méthodes que je n'ai pas eu l'occasion de présenter plus tôt, mais qui sont souvent très utiles :

- ```count()```
- ```distinct()```

# Compter les documents

Parfois, lorsque l'on fait une requête, peu importe les résultats eux-mêmes, tout ce qui nous intéresse, c'est le nombre de résultats. Dans ce cas, on peut utiliser la méthode ```count()```, qui donne le nombre de résultats qu'une requête ```find()``` avec le même critère renverrait.

```
// Nombre de documents dans la collection
db.character.count()

//Nombre de personnages ayant au moins 50 en maxLife
db.character.count({maxLife : {$gte : 50}})
```

# Trouver les valeurs distinctes d'un attribut

Quels types de personnages avons-nous dans notre collection en ce moment ? Pour le savoir, on pourrait récupérer tous les personnages de la collection, puis compter les valeurs différentes de l'attribut ```type```. 

Il y a plus simple : ```distinct()``` renvoie (sous forme d'array) les valeurs distinctes d'un attributs parmi les résultat d'une requête avec le critère donné.

```
//Sans critère, donc les valeurs distinctes dans la collection entière
db.character.distinct("type")

//Avec critère
db.character.distinct("weapon.name", {type : "Sorcier"})
```

On peut également l'utiliser sur un attribut contenant un array.

```
db.character.distinct("sports")
```