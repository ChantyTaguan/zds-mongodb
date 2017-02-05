# Orienté documents

Les données sont enregistrées dans des **collections** sous forme de **documents**, au format **JSON** (JavaScript Notation Object). 

Une collection est simplement une liste de documents. C'est l'équivalent d'une table dans les bases de données relationnelles. On aura par exemple une collection ```client```, une collection ```arbre```, etc.

Un document JSON est délimité par des accolades ```{ document }``` et comprend simplement une liste de clés auxquelles sont associés des valeurs. Nous verrons cela en détails par la suite. 

### Exemple 1 : objet JSON simple

```Javascript
{
	"a" : 1,
	"b" : "Texte"
}
```

Cette manière de conserver les données permet de simplifier notamment les allées-venues entre la base de données et l'application qui utilise celle-ci. Beaucoup de langages informatiques utilisent le paradigme de la programmation orientée objets. Ces langages manipulent des objets, qui vont avoir plusieurs propriétés, certaines de ces propriétés étant elles-mêmes des objets ou des listes d'objets. Dans MongoDB, un document est le pendant d'un objet. On peut stocker directement des documents complexes dans Mongo.

### Exemple 2 : objet JSON plus complexe, avec un objet imbriqué

```Javascript
{
	"title" : "Oliver Twist",
	"author" : {
		"firstname" : "Charles",
		"lastname" : "Dickens"
	} 
}
```

Par ailleurs, c'est un SGBD sans schéma défini, c'est-à-dire que les objets stockés n'ont pas de structure fixe et définie à l'avance, et une collection peut contenir des documents n'ayant pas la même structure. Dans la pratique, en général, dans une même collection on retrouvera des objets représentant le même concept. Par exemple, on aura une collection ```article``` et une collection ```commentaire``` pour un blog. Simplement, les documents dans ces collections ne sont pas obligés d'avoir tous la même structure. Certains commentaires peuvent avoir un attribut ```"note"``` par exemple, et d'autres en être complètement exempt.

# Requêtes complexes

MongoDB permet de faire des requêtes complexes, sur n'importe quel attribut d'un document, y compris les attributs des documents imbriqués. On peut faire des recherches par comparaison directe ("tous les livres de Charles Dickens" par exemple), mais aussi en faisant des comparaisons (les livres publiés entre 1890 et 1914, les livres dont le titre commence par "O", ...). On peut même définir des fonctions pour utiliser des critères de sélection complexes.

# Index

Pour permettre une exécution rapide des requêtes, MongoDB permet de créer des index, c'est-à-dire des structures de données spéciales qui stockent une petite portion des documents d'une collection de manière ordonnée. Ainsi, quand la requête se fait sur la portion en question, il est plus facile de retrouver les documents qui correspondent au critère demandé, ce qui accélère la requête.

[[information]]
| Notez que la taille des index peut grandement influer sur les performances de celui-ci. C'est une des raisons pour lesquelles on préfèrera travailler avec plusieurs collections, représentant chacune un concept logique, plutôt que de tout mettre dans une seule collection ---ce qui serait possible, vu la nature « sans schéma » des collections. L'idéal étant que l'ensemble des index puisse tenir dans la *mémoire-vive* du serveur, qui est bien plus rapide à accéder que le disque dur sur lequel il faudrait aller lire dans le cas contraire.
|
| Petit exemple simpliste en reprenant les collections ```article``` et ```commentaire``` d'un blog. Imaginons que nous avons 5 articles et et 10 commentaires dans notre base de données, et que nous désirons un index sur l'attribut ```"category"``` des articles, et un index sur l'attribut ```"articleId"``` (qui référence l'article visé par le commentaire) des commentaires. Ayant séparé les documents en deux collections, on aura deux index : l'un sur la table ```article```, qui contiendra 5 éléments (autant que d'articles) et l'autre sur la table ```commentaire```, qui en contiendra 10. Ce qui fait au total 15 éléments pour les deux index.
|
| Si nous avions mis tous les documents dans une seule collection, l'index sur ```"category"```aurait contenu 15 éléments (dont seulement 5 vraiment concernés par cet index) et l'index sur ```"articleId"``` aurait aussi contenu 15 éléments (dont seulement 10 intéressants). Ce qui nous fait au total 30 éléments pour les deux index.
|
| Travailler avec des plusieurs collections dont les documents auront en général grosso-modo la même structure, mais surtout seront des représentations d'un même concept permet donc de **diminuer la taille des index**, et ainsi **améliorer les performances** de ceux-ci.

# Réplicats

Pour garantir la disponibilité d'une base de données, on peut en entretenir plusieurs copies, placées sur des serveurs différents. On a alors un serveur primaire, vers lequel seront dirigées les requêtes, et un ou plusieurs serveurs secondaires (au moins deux en général). Ainsi, si le serveur primaire tombe, pour une raison ou une autre, les secondaires prennent le relais : l'un d'eux devient le serveur primaire. MongoDB permet facilement de créer et gérer ces ensembles de copies, appelés "réplicats".

# Répartition de charge

Lorsque l'on a beaucoup de données, tout stocker sur un même serveur a de gros impacts sur les performances de la base de données. MongoDB permet de distribuer ses données sur plusieurs serveurs, qui chacun contiendra alors un sous-ensemble des données. Ces sous-ensembles ont appelés « fragments » (en anglais, "*shards*"). Toute la gestion, la répartition des données, l'ajout de nouveaux serveurs quand le besoin se fait sentir est extrêmement simple. Il est possible de combiner fragmentation des données et réplicats, pour être à la fois fiable et performant (chaque fragment est alors répliqué sur plusieurs serveurs).

[[information]]
| Cette répartition des données se fait au niveau des **collections**, et non pas au niveau de la base de données elle-même. On peut donc décider de répartir les données de certaines collections, mais pas d'autres. Nous verrons par la suite comment décider quelles collections sont intéressantes à répartir, et comment les répartir efficacement. Avec les performances potentiellement accrues des index, voilà encore une bonne raison d'utiliser plusieurs collections plutôt qu'une seule grande collection « fourre-tout ».