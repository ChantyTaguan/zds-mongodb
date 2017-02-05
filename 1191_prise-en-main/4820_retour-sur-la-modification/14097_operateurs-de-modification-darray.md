Il existe toute une série d'opérateur permettant de modifier les attributs d'un document qui contiennent un array. En voici quelques uns.

# Ajouter un ou plusieurs éléments à un array

L'opérateur ```$push``` permet d'ajouter un ou plusieurs éléments à un array.

### Exemple : Wolfy le sportif

```
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
//Wolfy pratique le ski et le tir à l'arc

//Ajoutons le basket-ball
db.character.update({name : "Wolfy"}, { $push : {sports : "Basket-ball"} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
```

Si l'on veut rajouter plusieurs éléments d'un coup, il faut combiner ```$push``` avec l'opérateur ```$each```. Cet opérateur permet d'appliquer un même effet à tous les éléments d'un array.

```
//On ajoute le golf et la plongée
db.character.update({name : "Wolfy"}, { $push : {sports : { $each : ["Golf", "Plongée sous-marine"]}} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
```

Il existe aussi l'opérateur ```$addToSet```, qui a le même effet que ```$push``` à ceci près qu'il n'insère que les valeurs qui ne sont pas encore présentes dans l'array.

### Exemple : Wolfy le super-sportif

Essayons d'ajouter un sport déjà présent.

```
//D'abord avec $push
db.character.update({name : "Wolfy"}, { $push : {sports : "Ski"} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
//On a deux éléments "Ski"

//Ensuite avec $addToSet
db.character.update({name : "Wolfy"}, { $addToSet : {sports : "Golf"} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
//On n'a toujours qu'un seul élément "Golf"
```

Par contre, si l'on ajoute un sport qui n'est pas encore présent avec ```$addToSet```, il s'ajoute correctement.

```
db.character.update({name : "Wolfy"}, { $addToSet : {sports : "Rugby"} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
```

Comme avec ```$push```, on peut utiliser ```$each``` pour traiter plusieurs éléments d'un coup (sous forme d'array).

```
var sportsHiver = ["Ski", "Snowboard"]
db.character.update({name : "Wolfy"}, { $addToSet : {sports : { $each : sportsHiver }} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
```

## Modificateurs

Les modificateurs permettent de modifier le comportement de ```$push``` et ```$addToSet```. Nous en avons déjà vu un : ```$each``` est un modificateur. Il permet d'ajouter un array de valeur au lieu d'une seule valeur.

Il existe trois autres modificateurs, qui sont tous valables uniquement pour l'opérateur ```$push```, et qui s'utilisent toujours en conjonction avec ```$each```.

|Modificateur|Effet|
|--|--|
|```$slice```|Limite l'array à un certain nombre d'éléments|
|```$sort```|Trie les valeurs de l'array|
|```$position```|Précise la position à laquelle les nouvelles valeurs doivent être insérées|

Le modificateur ```$each``` a donc deux fonctions différentes.

- Il permet de traiter plusieurs valeurs à la fois.
- Il permet de modifier l'array final du document grâce aux trois autres modificateurs.

### ```$slice```

Ce modificateur prend en paramètre un nombre entier, positif ou négatif (ou zéro).

- Positif : les *x* premiers éléments de l'array seront conservés.
- Négatif : les *x* derniers éléments de l'array seront conservés.
- Zéro : vide l'array complètement.

```javascript
db.character.update({name : "Harry"},
  {
    $push : { 
      sports : { 
        $each : ["Rugby", "Tir à l'arc", "Basket"],
        $slice : 3
      }
    }
  }
)

db.character.find({name : "Harry"}, {_id : false, name : true, sports : true})
```

```
{ "name" : "Harry", "sports" : [ "Quidditch", "Rugby", "Tir à l'arc" ] }
```

Seuls trois sports sont conservés, les trois premiers : c'est-à-dire le Quidditch qui était déjà présent, et les deux nouveaux donnés en premier en paramètre. Si l'on avait donné ```-3``` en paramètre à ```$slice```, le Quidditch n'aurait pas été conservé, contrairement au Basket, puisqu'on aurait conservé les trois derniers.

```javascript
db.character.update({name : "Harry"},
  {
    $push : { 
      sports : { 
        $each : ["Basket"],
        $slice : -3
      }
    }
  }
)

db.character.find({name : "Harry"}, {_id : false, name : true, sports : true})
```

### ```$sort```

Permet de trier les éléments de l'array. Il prend en paramètre le critère de tri, qui est différent selon que l'array contient des valeurs ou des documents imbriqués.

- Array contenant des valeurs : ```$sort```prend ```1``` (tri croissant) ou ```-1``` (tri décroissant) en paramètre.
- Array d'objets : le paramètre de ```$sort``` est un objet contenant les clés selon lesquels les objets de l'array doivent être triés, et ```1``` ou ```-1``` comme valeur selon que le tri doit être croissant ou décroissant.

```
//On trie les sports par ordre croissant (ordre alphabétique puisque ce sont des chaînes de caractères)
//On précise $each (obligatoire) mais avec un array vide pour ne rien ajouter
db.character.update({name : "Harry"},
  {$push : {sports : {$each : [], $sort : 1 } }}
)
db.character.find({name : "Harry"}, {_id : false, name : true, sports : true})

//On trie l'inventaire de Hermione par poids décroissant
db.character.update({name : "Hermione"},
  {$push : {inventory : {$each : [], $sort : {weight : -1} } }}
)
db.character.findOne({name : "Hermione"}, {_id : false, name : true, inventory : true})

//Et celui de Harry, par poids croissant, et par prix décroissant
db.character.update({name : "Harry"},
  {$push : {inventory : {$each : [], $sort : {weight : 1, price : -1} } }}
)
db.character.findOne({name : "Harry"}, {_id : false, name : true, inventory : true})
```

Trier les éléments peut être particulièrement intéressant lorsqu'on utilise également ```$slice``` : on peut alors déterminer quels éléments garder sur un critère moins subjectif que "les premiers" ou "les derniers".

On peut par exemple rajouter quelques éléments dans l'inventaire de Wolfy, mais ne garder que les trois plus chers (c'est-à-dire les trois derniers si c'est trié par prix croissant).

```javascript
db.character.update({name : "Wolfy"},
  {$push : {inventory : {
    $each : [{name : "Pierre de Lune", category : "Talisman", price : 32}, {name : "Lapin", category : "Nourriture", price : 3}, {name : "Collier", category : "Accessoire", price : 7}], 
    $sort : {price : 1},
    $slice : -3
 } }}
)
db.character.findOne({name : "Wolfy"}, {_id : false, name : true, inventory : true})
```

Résultat : pas de lapin dans l'inventaire !

# Supprimer un ou plusieurs éléments d'un array

L'opérateur ```$pop``` supprime le premier ou le dernier élément d'un array. ```{ $pop: { clé: -1 } }``` retire le premier élément de l'array *clé*, ```{ $pop: { clé: 1 } }``` retire le dernier élément.

### Exemple

```javascript
//Suppression du premier sport
db.character.update({name : "Wolfy"}, { $pop : {sports : -1} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
//Plus de tir à l'arc

//Suppression du dernier sport
db.character.update({name : "Wolfy"}, { $pop : {sports : 1} })
db.character.find({name : "Wolfy"}, {_id : false, name : true, sports : true})
//Plus de snowboard
```

Quant à l'opérateur ```$pull```, il retire d'un array tous les éléments qui corresponde à un critère donné.

```
{ $pull: { clé : critère } }
```
 
Le critère de sélection des éléments à supprimer peut être une valeur, ou un critère plus complexe, avec éventuellement des opérateurs de comparaison et/ou de logique.
 
### Exemple

On retire tous les sports qui contiennent la lettre "r", chez tous les personnages.

```
db.character.find({}, {_id : false, name : true, sports : true})

//On n'oublie pas l'options {multi : true} pour toucher tous les personnages
db.character.update({}, {$pull : { sports : { $regex : /r/i } }}, { multi : true })
db.character.find({}, {_id : false, name : true, sports : true})
```
Le saut à la perche, le saut en hauteur, le rugby et la plongée sous-marine ont disparu des sports pratiqués.

# Opérateur positionnel

On a déjà vu l'opérateur positionnel utilisé dans le critère de projection, qui permet de ne récupérer que les éléments de l'array correspondant aux critères de sélection.

```javascript
db.character.find({sports : {$in : [/^S/, /^T/]}}, {name : 1, _id : 0, "sports.$" : 1})
```

Utilisé avec la méthode ```update```, l'opérateur positionnel a à peu près le même rôle : il permet de ne modifier que **le premier élément** de l'array qui correspond aux critères de sélection.

[[attention]]
| Si plusieurs éléments correspondent, seul le premier sera modifié !

Décidons par exemple d'augmenter le prix des potions.

```javascript
db.character.find({}, {_id : 0,name : 1, "inventory.name" : 1, "inventory.category" : 1, "inventory.price" : 1}).pretty()
db.character.update({"inventory.category" : "Potions}, {$inc : {"inventory.$.price" : 2}}, {multi : true})
db.character.find({}, {_id : 0,name : 1, "inventory.name" : 1, "inventory.category" : 1, "inventory.price" : 1}).pretty()
```

On a bien augmenté le prix des potions de vies de Wolfy. Par contre, Harry possède des potions de vie et des potions de mana. Seules ces dernières, qui viennent en premier dans son inventaire, ont été modifié.

## Restrictions de l'opérateur positionnel

- Comme on vient de le voir, il ne modifie qu'un élément de l'array.
- Il **faut** que le critère de sélection porte, au moins en partie, sur l'array qu'on veut modifier.
- Si on l'utilise avec l'opérateur ```$unset```, il ne supprime pas l'attribut visé, mais le met à ```null```.
- Il ne fonctionne pas si des arrays sont imbriqués sur plusieurs niveaux.
- Les critères de sélection de négation ```$ne```, ```$not``` et ```$nin``` appliqués à l'array empêchent l'utilisation de l'opérateur positionnel, à moins d'être utilisé dans un ```$elemMatch```.