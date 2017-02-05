Il existe une petite dizaine d'opérateurs de modification de base. Nous allons ici voir les plus utilisés, mais n'hésitez pas à explorer la [documentation](http://docs.mongodb.org/manual/reference/operator/update/) pour découvrir les autres.

# Modifier la valeur de certains champs

L'opérateur ```$set``` permet de ne modifier que les attributs donnés en paramètre. Si l'attribut existait déjà, sa valeur est mise à jour, sinon, l'attribut et sa valeur sont ajoutés au document.

```
db.character.update({name : "Plop"}, {$set : 
  {
    hairColor : "red",
    maxLife : 45
  }
})

db.character.findOne({name : "Plop"})
```

```
{
	"_id" : ObjectId("557c64a85aa209c3b5c4c65d"),
	"name" : "Plop",
	"type" : "Lutin",
	"biography" : {
		"birthCity" : "Lutinville",
		"birthDate" : "04-23-1843"
	},
	"maxLife" : 45,
	"sports" : [
		"Saut à la perche",
		"Saut en hauteur"
	],
	"weapon" : {
		"name" : "Pelle",
		"twoHanded" : true
	},
	"hairColor" : "red"
}
```

# Suppression d'un champ

Pour supprimer un attribut d'un document, on utilise l'opérateur ```$unset```

```javascript
db.character.update({name : "Plop"}, {$unset : {hairColor : true}})
```

# Incrémentation et multiplication

MongoDB implémente des opérateurs permettant d'incrémenter une valeur numérique (c'est-à-dire leur ajouter un nombre) ou de la multiplier.

- ```$inc``` : ```{$inc : {clé : nb}}``` ajoute *nb* à la valeur de *clé*, ou initialise *clé* à *nb* si *clé* n'existe pas.
- ```$mul``` : ```{$mul : {clé : nb}}``` multiplie la valeur de *clé* par *nb*, ou initialise *clé* à 0 si *clé* n'existe pas.

### Exemples

```
//On ajoute 5 à maxLife D'Hermione
db.character.update({name : "Hermione"}, {$inc : { maxLife : 5}})

//On ajoute -3 , toujours chez Hermione (donc on retire 3)
db.character.update({name : "Hermione"}, {$inc : { maxLife : -3}})

//On incrémente xp chez Wolfy, xp n'existe pas encore
db.character.update({name : "Wolfy"}, {$inc : { xp : 12}})

//On multiplie l'xp de Wolfy par 3
db.character.update({name : "Wolfy"}, {$mul : { xp : 3}})

//Idem chez Plop, ce qui revient à initialiser xp à 0 puisque xp n'est pas encore défini
db.character.update({name : "Plop"}, {$mul : { xp : 3}})
```

# Minimum et maximum

Les opérateurs ```$min``` et ```$max``` comparent la valeur de l'attribut visé avec la nouvelle valeur potentielle, et donnent la plus petite (```$min```) ou la plus grande (```$max```) des deux à l'attribut.

### Exemple : jouons un peu avec la vie de Plop

```
db.character.find({name : "Plop"}, {name : 1, _id : 0, maxLife : 1})
```

```
{ "name" : "Plop", "maxLife" : 45 }
```

```
//On donne à maxLife la + petite valeur entre 43 et sa valeur actuelle (45)
db.character.update({name : "Plop"}, { $min : {maxLife : 43 } })
db.character.find({name : "Plop"}, {name : 1, _id : 0, maxLife : 1}) //43

//On donne à maxLife la + petite valeur entre 48 et sa valeur actuelle (43)
db.character.update({name : "Plop"}, { $min : {maxLife : 43 } })
db.character.find({name : "Plop"}, {name : 1, _id : 0, maxLife : 1}) //43

//On donne à maxLife la + grande valeur entre 34 et sa valeur actuelle (43)
db.character.update({name : "Plop"}, { $max : {maxLife : 34 } })
db.character.find({name : "Plop"}, {name : 1, _id : 0, maxLife : 1}) //43

//On donne à maxLife la + grande valeur entre 48 et sa valeur actuelle (43)
db.character.update({name : "Plop"}, { $max : {maxLife : 48 } })
db.character.find({name : "Plop"}, {name : 1, _id : 0, maxLife : 1}) //48
```