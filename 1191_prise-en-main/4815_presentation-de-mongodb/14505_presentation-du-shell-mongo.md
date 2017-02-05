Ce petit logiciel est donc un client, permettant de se connecter à un serveur *mongod*. C'est grâce à lui que nous communiqueront avec le serveur tout au long du cours, afin de créer et gérer des bases de données, des collections, des documents, ... Par défaut, il se connecte au serveur du port 27017 (port par défaut de mongo), sur l'ordinateur même. Il serait cependant tout à fait possible de se connecter à un serveur distant, ou un serveur utilisant un autre port, en précisant l'adresse et le port du serveur.

```bash
mongo --host=superhost.domain.com --port=27018
```

Mais le shell mongo, c'est aussi un interpréteur JavaScript. Vous pouvez donc écrire des instructions en JS, elles s'exécuteront sans problème.

```javascript
var obj = {"a" : 1, "b" : 2}

obj.c = 4

obj.d = obj.a +5

obj.nom = "Charles"

4+6*3

print("Bonjour " + obj.nom)

for (var key in obj){ print("clé : \"" + key + "\" - valeur : \"" + obj[key] + "\""); }
```

Pour quitter mongo, vous pouvez :

- fermer votre utilitaire de ligne de commande ;
- utiliser la fonction ```quit()``` ;
- ou encore utiliser la combinaison ||CTRL + C|| (ou ||CMD +C|| dans le cas d'OS X).