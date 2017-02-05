On relève principalement quatre grandes tendances qui expliquent ce besoin de bases de données qui n'obéissent pas au modèle relationnel : Big Data, Big Users, « The internet of things », et le Cloud Computing.

#Big Data

Appelées aussi les « mégadonnées » en français, les Big Data sont des **données tellement massives et volumineuses** qu'il devient compliqué de les gérer avec les outils « classiques », comme les bases de données relationnelles.

Voici quelques exemples de secteurs qui génèrent des big data.

- Les réseaux sociaux : avec les relations entre les membres, les publications, les interactions que les membres peuvent avoir entre eux ou les publications, le volume de données d'un réseau social peut très vite exploser. 
- La recherche scientifique : l'ADN est de plus en plus facile à séquencer, les capteurs divers (astronomiques, météorologiques, etc.) enregistrent de plus en plus d'informations. Toutes ces nouvelles et nombreuses données doivent ensuite être croisées, interprétées, décodées.
- Prévisions boursières : des milliers d'opérations de différents types, sur différentes actions, obligations et autres joyeusetés du monde financier. Les programmes qui tentent de prédire les cours de la bourse à partir du passé doivent prendre en compte un nombre incommensurable de données.

Les bases de données relationnelles utilisent des modèles rigides, dans lesquels les données sont enregistrées dans plusieurs tables, liées entre elles pour garantir la cohérence des données. Mais si le prix de la mémoire a fortement baissé, lorsqu'il s'agit de stocker les volumes de données de ce qu'on appelle « Big Data » (on parle alors plutôt en petabytes - 10^15^ bytes - qu'en gigabytes - 10^9^ bytes), cela reste difficilement accessible. Une solution est de stocker les données sur différents disques ; on parle alors de scalabilité horizontale plutôt que verticale (ie. augmenter la capacité du disque). Malheureusement, le modèle relationnel n'est pas adapté pour cette solution, précisément à cause de l'omniprésence de relations entre chaque table, qu'il n'est donc pas possible, ou très peu performant, de stocker en différents endroits.

# Big Users

Autrefois, les applications devaient permettre d'avoir quelques dizaines voire centaines d'utilisateurs simultanés. Aujourd'hui, avec l'utilisation massive et croissante d'Internet, il n'est pas rare d'avoir des applications sur lesquelles des centaines de milliers d'utilisateurs sont connectés en même temps. Il faut donc pouvoir **adapter facilement et rapidement les bases de données à la charge** que représentent ces utilisateurs. Si l'on peut faire évoluer la base de données horizontalement, donc en ajoutant simplement un serveur de plus au cluster de serveurs en charge de la base de données, ça ne pose pas de problème. Par contre, migrer une base de données relationnelle d'un serveur vers un autre serveur plus puissant demande du temps et des précautions, sans compter un certains temps pendant lequel la base de données n'est pas disponible. On le fait une fois, mais pas deux...

# « The Internet of Things »

Dorénavant, un grand nombre d'objets est connecté à Internet : voitures, téléphones, montres, systèmes domotiques pour les maisons, … Tous ces objets enregistrent en permanence toutes sortes de données (le lieu, l'heure, la manière dont l'objet est utilisé, par qui il est utilisé, etc.). Toutes ces données, qui peuvent énormément **varier d'un système à l'autre** et **évoluer rapidement** vont être rassemblées et interprétées par les fabricants des objets connectés afin d'améliorer le produit, ou de mieux cibler les utilisateurs.

Les bases de données relationnelles, avec leur modèle extrêmement rigide et statique (chaque type de données doit être défini à l'avance pour pouvoir être enregistré), ne sont pas adaptées à toutes ces données.

# Cloud computing

Aujourd'hui, de plus en plus d'applications sont hébergées dans le « cloud », et tournent sur des clusters de serveurs, auxquels il suffit de rajouter un noeud pour supporter une charge qui augmente. Or on a vu que ce mode de fonctionnement n'était pas du tout adapté pour les bases de données relationnelles : on ne peut pas simplement **distribuer une base de données** MySQL ou Oracle sur plusieurs serveurs différents.