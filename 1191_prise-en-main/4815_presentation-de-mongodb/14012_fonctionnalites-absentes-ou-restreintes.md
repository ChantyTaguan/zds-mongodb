En s'affranchissant de la plupart des contraintes du modèle relationnel, MongoDB gagne en souplesse (pas de schéma fixe, documents imbriqués permettant la représentation d'objets complexes), en disponibilité (réplicats) et en performance (fragmentation des données). Mais tout cela a un prix, et certaines fonctionnalités essentielles des bases de données relationnelles n'existent pas avec une base de données MongoDB.

# Les jointures et clés étrangères

Dans une base de données relationnelle, un objet est souvent éclaté sur plusieurs tables. Par exemple, les informations sur un livre seront réparties dans une table *Livre* pour le titre et l'année de publication, une table *Auteur* pour les informations sur l'auteur, une table *Categorie*, etc. Lorsque l'on veut récupérer toutes les données d'un bouquin, il va donc falloir combiner plusieurs tables. Cela se fait grâce aux jointures. Quant aux clés étrangères, elles permettent de s'assurer que les données de tables liées sont cohérentes. Une clé étrangère vous empêchera par exemple d'insérer un livre de l'auteur X dans la table *livre* s'il n'y a aucune trace de l'auteur X dans la table *auteur*.

MongoDB ne propose un système équivalent aux jointures que depuis la version 3.2, sortie fin 2015. De plus, il s'agit d'une fonctionnalité assez restreinte par rapport aux jointures des bases de données relationnelles, et il n'existe pas de mécanisme équivalent aux clés étrangères. De plus, les créateurs et développeurs de MongoDB insistent régulièrement sur le fait que l'existence de cette nouvelle fonctionnalité ne doit pas servir à utiliser MongoDB comme une base de données relationnelles. Dans la plupart des cas, étant donné qu'on peut stocker des documents complexes, il vaut mieux stocker toutes les informations relatives à un document dans une seule collection, plutôt que de les disperser dans plusieurs.

# Les transactions

Dans une base de données relationnelle, il est possible de regrouper plusieurs opérations dans une seule et même transaction. Ce qui signifie que ces opérations seront toutes exécutées en une fois, ou pas du tout. Les étapes intermédiaires entre chaque opération d'une transaction ne seront jamais visibles à l'extérieur de la transaction. Cela permet de toujours garder une certaine cohérence des données. 

Par exemple, lorsque dans votre jeu vidéo, votre super guerrier Mjolnir achète son marteau de glace au marchand, une transaction constituée de trois opérations est exécutée :

- La bourse de Mjolnir est amputées de 30 pièces d'or (*po*) ;
- La caisse du marchand est augmentée de 30 *po* ;
- Le marteau de glace est mis dans l'inventaire de Mjolnir.

Les étapes intermédiaires n'existent pas. Si votre petite soeur éteint la console en pleine transaction, lorsque vous la rallumerez, Mjolnir n'aura pas perdu 30 *po* sans que le marchand n'ait récupéré cet argent et ne lui ait donné son marteau. C'est tout, ou c'est rien.

MongoDB ne permet pas de telles transactions. Si vous avez besoin d'un système permettant d'exécuter plusieurs opérations de la sorte, deux possibilités s'offrent à vous.

- Gérer les éléments qui nécessitent une transaction avec un SGBD relationnel, et éventuellement le combiner avec MongoDB pour les éléments moins critiques.
- Vous arranger pour que les étapes intermédiaires soient facilement identifiables et réversibles en cas d'interruption en cours d'opération. Nous verrons un exemple de ce système dans la partie sur l'organisation des données.

# ACID

Les bases de données relationnelles garantissent la cohérence et la qualité des données en implémentant quatre propriétés : les propriétés ACID, pour **A**tomicité, **C**ohérence, **I**solation, **D**urabilité. MongoDB ne respecte pas complètement ces quatre propriétés.

## Atomicité

Dans les SGBDR, l'atomicité est garantie grâce aux transactions, qui sont, comme on vient de le voir, un ensemble d'opération qui seront toutes exécutées entièrement, ou pas du tout. Une transaction est une opération atomique, donc indivisible.

MongoDB n'a pas de transaction, par contre la modification d'un document est une opération atomique. Quel que soit le nombre d'attributs modifiés, lorsque l'on enregistre des changements dans un document, on peut être sûr que tous les changements seront effectués, ou qu'aucun ne le sera. 

On a donc bien une garantie d'atomicité avec MongoDB, mais elle est plus restreinte que dans les bases de données relationnelles, et n'est pas faite au même niveau :

- dans une base de données relationnelle, l'atomicité est garantie au niveau de la transaction, et celle-ci est définie au moment de l'opération par l'utilisateur (puisqu'il décide où commence une transaction, et oùelle se finit) ;
- dans une BDD MongoDB, l'atomicité est garantie au niveau des documents. Une opération qui nécessiterait de modifier plusieurs documents -- par exemple, modifier l'inventaire et la bourse du document représentant Mjolnir, et modifier l'inventaire et la bourse du marchand d'armes --- ne pourra donc pas être atomique.

## Cohérence

Pour qu'une base de données soit toujours cohérente, il faut qu'un changement dans celle-ci fasse passer les données d'un état valide à un autre état valide, sans étape intermédiaire dans laquelle les données sont invalides. Une fois le changement effectué, toute opération sur la base de données ne doit avoir accès qu'au nouvel état valide.

Par défaut, MongoDB garantit la cohérence des données. Cependant, dans certaines configurations, MongoDB peut passer momentanément par un état « incohérent », mais garanti toujours une « cohérence au final » (en anglais, on parle d'« *eventual consistency* »). Ce qui signifie qu'on peut passer par un moment où des données qui n'existent plus sont visibles. C'est le cas notamment lorsque l'on utilise des réplicats et qu'on autorise les requêtes de lecture à être traitées par les serveurs secondaires plutôt que de les diriger toutes vers le serveur primaire. On utilisera cette configuration lorsqu'il est plus important d'avoir une réponse rapide que d'avoir des données à jour à la milliseconde près.

## Isolation

Toute opération sur les données doit être isolées des autres opérations potentielles. Imaginons qu'on veuille modifier tous les documents d'une collection pour leur ajouter un champ. Si pendant que cette opération se déroule, on tente de modifier un certain document, il est possible que cette modification soit perdue : le document enregistré par l'opération d'ajout de champ écrasant les modifications qui été faite entre le lancement de celle-ci et la modification du document. Les opérations ne sont alors pas isolées.

Les opérations qui n'affectent qu'un seul document sont *de facto* isolées dans MongoDB. Il est impossible que plusieurs modifications aient lieu en même temps sur un document. Pour les modifications qui affectent plusieurs documents, MongoDB dispose d'une option qu'on peut utiliser pour garantir l'isolation. Cependant, cette option ne fonctionnera pas si la base de donnée est fragmentée sur plusieurs serveurs pour répartir la charge.

## Durabilité

La propriété de durabilité garantit qu'une fois que des données ont été enregistrées dans la base de données, elles le sont de manière permanente, même si le serveur tombe en panne ou s'éteint.

Depuis la version 2.0, MongoDB garantit par défaut que toute opération d'écriture est durable, sauf si elle est survenue dans les dernières 100 millisecondes avant un crash serveur. Chaque modification des données est d'abord enregistrée en mémoire. Toutes les 100 millisecondes, elles sont ajoutées au *journal*, qui est un fichier (ou plusieurs fichiers si les opérations d'écritures sont très nombreuses) qui garde une trace de chaque opération d'écriture réalisée. En cas de crash du serveur, lors du redémarrage, MongoDB va vérifier dans le journal s'il reste des opérations qui n'ont pas été enregistrées sur le disque et les applique au besoin.
Toutes les 60 secondes (par défaut), MongoDB applique les opérations du journal aux données sur le disque.

Il est possible de paramétrer MongoDB afin d'avoir une meilleure durabilité, en écrivant dans le journal en moins de 100 millisecondes par exemple, ou au contraire d'assouplir ces règles pour augmenter les performances.