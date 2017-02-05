MongoDB est un SGBD basé sur le modèle "client - serveur" : le serveur est le logiciel qui va gérer les données, les stocker ; le client quant à lui est un logiciel qui permet de communiquer avec le serveur, d'envoyer des requêtes à celui-ci pour récupérer des données, les modifier, etc. Il est possible (et même courant) d'avoir plusieurs clients différents communiquant avec le même serveur.

Lorsque l'on installe « MongoDB », on installe donc en général plusieurs logiciels :

- **mongod** : le serveur (le « d » vient du terme « daemon », qui désigne un processus exécuté en arrière-plan ;
- **mongo** : le shell mongo, un logiciel client pour communiquer avec le serveur. C'est dans ce logiciel que nous allons écrire nos requêtes, créer nos collections et nos documents, les modifier ...
- **mongos** : Logiciel permettant de gérer les serveurs mongod lorsqu'on fragmente les données (le « s » de « mongos » signifie « shard ») sur plusieurs serveurs (voir la partie sur la fragmentation des données) ;
- et d'autres logiciels clients utilitaires, permettant par exemple de faire des sauvegardes de la base de données, d'importer des données en masse, etc.

Notez qu'il est tout à fait possible de n'installer qu'un seul de ces composants. On peut très bien vouloir installer uniquement le serveur mongod sur une machine, et installer le client ailleurs. Si vous êtes dans ce cas, référez-vous à la documentation.