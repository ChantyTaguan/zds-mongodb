Pour répondre à ces problématiques, de nouvelles manières de concevoir la gestion d'une base de données ont été créées, avec en tête plusieurs objectifs.

- Proposer une architecture qu'il est facile de faire évoluer horizontalement, c'est-à-dire en ajoutant un serveur, au lieu d'augmenter la puissance d'un serveur unique. Cela permet de pouvoir facilement servir un nombre très important d'utilisateurs et gérer des données massives, et surtout d'évoluer facilement dans le temps en fonction des besoins.
- Tirer parti du « cloud computing » également pour dupliquer chaque données sur plusieurs serveurs en parallèles. Ainsi même si un des serveurs plante, les autres peuvent prendre le relais, ce qui garantit une très haute disponibilité de la base de données.
- Avoir un format de données qui puisse facilement évoluer dans le temps, et qui permette la représentation de données semi-structurées (dont la structure n'est pas définie *a priori* : chaque donnée définit sa propre structure). 

Les différents modèles et technologies ayant émergés sont aujourd'hui regroupés sous la bannière « NoSQL », qui signifie « *Not Only SQL* ».

[[information]]
| « *Not Only* » signifie « pas seulement ». On pense souvent, à tort, que NoSQL signifie « Non-SQL », ou « pas SQL ». Or le but des système de gestion de BDD NoSQL n'est pas de rejeter purement et simplement le SQL (d'ailleurs certains concepts du SQL --- les index par exemple --- se retrouvent dans certains systèmes NoSQL). L'idée derrière le « pas seulement » est d'offrir des **alternatives** pour les situations ou le SQL n'est pas (ou est moins) adapté. L'idée est de permettre de ne pas seulement réfléchir en terme de BDD SQL, mais d'avoir la possibilité d'utiliser le modèle le plus approprié, SQL ou pas. Voire même de combiner plusieurs modèles dans une seule et même application si c'est la meilleure chose à faire.

# Types de bases de données NoSQL

Il existe plusieurs types de bases de données NoSQL, qu'on distingue selon leur manière de stocker les données : 

- les bases de données clé-valeur ;
- les bases de données orientées documents ;
- les bases de données orientées colonnes ;
- les bases de données orientées graphes.

Il existe d'autres modèles, mais ce sont les principaux. Avant de nous pencher sur ces types de bases de données NoSQL, un petit rappel de la manière dont cela se passe dans une base de données relationelle.

## Représentation des données dans une base de données relationnelle

Les bases de données relationnelles représentent les données sous forme de tables. Chaque colonne correspond à un attribut, et chaque ligne est une donnée. Les tables sont normalisées de manière à ce qu'on ait le moins de répétition de données possible. Le but étant d'avoir des données qui soient complètement factorisées et agnostiques[^agno] de l'application (ou des applications) dans lesquelles elles seront utilisées.

C'est un modèle de données non seulement solide et puissant, mais aussi très rigide et contraignant. En effet, chaque table doit être définie, chaque colonne de la table contient un certain type de données (nombre, chaîne de caractère, date, etc.). Si le modèle de données change, fût-ce légèrement, il faut modifier, voire réorganiser les tables avant de pouvoir insérer les nouvelles données. De plus, la représentation en table ne correspond que rarement à ce qui sera manipulé par l'application même.

Prenons l'exemple classique de l'article de blog : lorsque l'on affiche l'article, on veut afficher non seulement le contenu même de l'article (date, titre, texte, etc.), mais également toutes les métadonnées qui lui sont associées : les catégories liées à l'article et les informations sur l'auteur. On peut aussi vouloir afficher des commentaires de l'article. Dans une base de données relationnelle, toutes les données seraient séparées dans quatre ou cinq tables : une qui stocke les articles, une pour les informations sur les auteurs, une autre pour les commentaires et une ou deux tables pour référencer les catégories et les lier aux articles. Récupérer toutes les informations nécessaires à la page d'un article va donc demander de joindre toutes ces tables.

Et si au départ il était prévu que chaque article ne soit lié qu'à une seule catégorie, référencée dans la table *article*, changer ce comportement nécessitera une grosse modification de la base de données, ainsi qu'une migration des données déjà existantes.

## Représentation des données dans une base de données NoSQL

### Bases de données clé-valeur

C'est le modèle le plus simple. Chaque élément de la base de données est stocké avec une référence unique à cet élément, la clé. Les valeurs (= données) stockées peuvent être n'importe quoi (un nombre, une chaîne de caractères, un objet complexe, etc.). Les requêtes sur la BDD ne peuvent se faire que *via* les clés, ce qui en fait un système extrêmement performant, mais très peu souple quant à la récupération des données (impossible de sélectionner des éléments sur base de leur valeur).

#### SGBD basés sur ce modèle

- [Voldemort](http://www.project-voldemort.com/voldemort/) : développé initialement par LinkedIn.
- [Riak](http://basho.com/riak/).
- [Redis](http://redis.io/), qui est un peu particulier puisqu'en plus de la clé et de la valeur, le type de la valeur est stocké, ce qui permet d'ajouter quelques fonctionnalités.

### Bases de données orientées documents

Ce modèle étend en quelque sorte le modèle clé-valeur. Au lieu de stocker des valeurs, qui sont des données très brutes sans structure définie (du point de vue de la base de données du moins), on va stocker des *documents*, c'est un dire un ensemble de données --- souvent stockée au format JSON --- représentant ou objet ou un concept. Les document sont semi-structurés et peuvent contenir des attributs qui sont eux-mêmes des documents, ou des listes de documents.

###Exemple de document

```javascript
{
  "key" : "clé/identifiant du document"
  "a" : "Ceci est un attribut du document",
  "b" : "Je suis la valeur de la clé b",
  "c" : 0,
  "liste" : ["banane", "pomme", "orange"],
  "livre" : {
    "titre" : "NoSQL, mode ou révolution ?",
    "auteur" : "Taguan"
  }
}
```

Les performances sont moins bonnes qu'avec le modèle clé-valeur, mais ce modèle très souple permet des fonctionnalités avancées. En effet, il est possible de faire des requêtes soit en utilisant la clé du document, (comme dans le modèle clé-valeur), soit en utilisant des attributs (donc le contenu) du document. 

#### SGBD basés sur ce modèle

- [**MongoDB**](http://www.mongodb.org/).
- [Apache CouchDB](http://couchdb.apache.org/) : CouchDB est un SGBD orienté documents qui se veut particulièrement adapté pour le Web. Il vient notamment avec une API complète, qui permet d'interroger un serveur couchDB directement à partir d'un navigateur Internet.

### Bases de données orientées colonnes

Les données sont stockées dans des tables sous forme de colonnes (au lieu de lignes pour les bases de données relationnelles).

Par exemple, si on a les données suivantes :

|id|nom|sexe|age|
|--|--|--|--|
|01|Simone|F|33|
|02|Jules|M|24|
|03|Franck|M|28|

Dans un système classique de base de données relationnelle, ces données sont stockées lignes par ligne.

```
01;Simone;F;33
02;Jules;M;24
03;Franck;M;28
```

Dans une base de données orientée colonnes, les données seront stockées par colonnes.

```
01:Simone;02:Jules;03:Franck
01:F;02:M;03:M
01:33;02:24;03:28
```

Tous les noms sont donc stockés ensemble, tous les âges ensembles, etc. Ca permet d'accéder uniquement et rapidement aux données dont on a besoin. Pour faire une recherche sur l'âge, il suffit d'accéder à la troisième ligne. Pour ceux qui connaissent le principe des index dans une base de données, chaque ligne ici correspond peu ou prou à ce que serait un index sur la colonne correspondante.

Cette manière de stocker permet également de condenser les données si certaines valeurs reviennent plusieurs fois. Par exemple, Jules et Franck sont tous les deux de sexe masculin, on peut donc condenser les données de la manières suivante :

```
01:Simone;02:Jules;03:Franck
01:F;02,03:M
01:33;02:24;03:28
```

Ce modèle est optimisé pour faire des systèmes avec de grands volumes de données, lorsqu'on fait beaucoup d'opération de lecture, mais peu d'opération d'écriture.

#### SGBD basés sur ce modèle

- BigTable : développé par Google et basé sur le système de fichier distribué Google File System. BigTable est un logiciel propriétaire.
- [Apache HBase](http://hbase.apache.org/) : basé sur BigTable et open source.
- [Apache Cassandra](http://cassandra.apache.org/) : également open source et développé par Apache, Cassandra est un hybride entre le modèle clé-valeur et les bases de données orientées colonnes.

### Bases de données orientées graphes

Ce type de base de données repose sur la théorie des graphes et permet de stocker des données sur des réseaux, c'est-à-dire où les éléments sont connectés entre eux. Il sert typiquement aux réseaux sociaux, et à certains domaines scientifiques, comme par exemple l'étude des interactions des gènes ou des protéines.

#### SGBD basés sur ce modèle

- [Neo4j](http://neo4j.com/) : les données sont représentées sous forme de noeuds, d'arêtes et d'attributs. Les *noeuds* peuvent être liés entre eux par les *arêtes*. Les attributs permettent de définir les propriétés des noeuds et arêtes.
- [HyperGraphDB](http://www.hypergraphdb.org/index) : basé sur les hypergraphes, qui sont une généralisation des graphes. Chaque arête peut lier de 2 à X noeuds.

[^agno]: « agnostique » n'est pas à prendre au sens spirituel du terme, mais bien au sens littéral. Ce mot vient du grec ancien ἄγνωστος *agnôstos*, ignorant. Dire que les bases de données relationnelles se veulent agnostiques des application qui les utilisent veut dire qu'on ne fait aucune hypothèse sur la manière dont les applications vont utiliser la base de données lorsqu'on la met en place. C'est donc aux applications à s'adapter à la modélisation des données dans la base.