Ajoutons un attribut ```maxLife``` aux personnage qui n'en ont pas.

```
db.character.find({}, {name : 1, _id : 0, maxLife : 1}) // On voit que wolfy et Harry n'ont pas encore de MaxLife

db.character.update({maxLife : {$exists : false}}, {$set : {maxLife : 50}})
```

Résultat :

```bash
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

Visiblement, on a bien modifié quelque chose, mais on n'a modifié qu'un seul  document. En effet, si l'on refait la requête de sélection, on obtient ceci :

```javascript
{ "name" : "Hermione", "maxLife" : 62 }
{ "name" : "Plop", "maxLife" : 48 }
{ "name" : "Harry" }
{ "name" : "Wolfy", "maxLife" : 50 }
```

Par défaut, ```update()``` ne modifie qu'un seul document : le premier qui correspond au critère de sélection. Si l'on veut que tous les documents correspondant soient modifiés, il faut le préciser grâce au troisième paramètre (facultatif) de la méthode, qui définit 3 options. Pour rappel, on a déjà utilisé ce paramètre précédemment pour l'upsertion.

Revoici les attributs qu'on peut utiliser dans ce paramètre ```options```.

- ```upsert``` : ```true``` si l'on veut une upsertion, ```false``` par défaut.
- ```multi``` : par défaut à ```false```. S'il est à ```true```, ```update()``` modifiera tous les documents correspondant à la requête au lieu d'un seul s'il est à ```false```.
- ```writeConcern``` : définit le niveau de sécurité de la commande.

Pour modifier d'un coup tous les personnages qui n'ont pas d'attribut ```maxLife```défini, il faut faire la requête suivante :

```javascript
db.character.update({maxLife : {$exists : false}}, {$set : {maxLife : 50}}, {multi : true})

//Ou 
db.character.update({maxLife : {$exists : false}}, {$set : {maxLife : 50}}, {multi : 1})
```

### Exemple 2

On veut ajouter un attribut ```maxMana``` à tous les sorciers.

```javascript
db.character.update({type : "Sorcier"}, { $set : { maxMana : 25}}, {multi : true})
```

Cette fois, on a bien modifié plusieurs documents.

```bash
WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })
```