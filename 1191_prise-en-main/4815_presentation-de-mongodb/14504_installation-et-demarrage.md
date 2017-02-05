# Installation

Pour des instructions détaillées sur l'installation de MongoDB, je vous renvoie à la [documentation officielle de MongoDB](http://docs.mongodb.org/manual/installation/), qui sera toujours plus complète et plus à jour que ce cours.

## Linux

MongoDB s'installe facilement avec les gestionnaires de paquets classiques. Il vous faudra simplement ajouter le repository correspondant avant de lancer l'installation.

## Mac OS

Le plus simple pour Mac OS est d'utiliser le gestionnaire de paquets [Homebrew](http://brew.sh/). 

Si vous ne pouvez pas (ou ne voulez pas) utiliser Homebrew, téléchargez directement les fichiers binaires [sur le site de MongoDB](https://www.mongodb.org/downloads), désarchivez-les et copiez les fichiers obtenus là où vous voulez installer MongoDB. Ajoutez ensuite le chemin vers le dossier ```bin``` de votre répertoire MongoDB à votre PATH pour ne pas devoir préciser le chemin vers les logiciels chaque fois que vous voulez en exécuter un.

## Windows

MongoDB est disponible avec le gestionnaire de paquets Windows [Chocolatey](https://chocolatey.org/packages/mongodb).

Si vous ne pouvez pas (ou ne voulez pas) utiliser Chocolatey, vous pouvez [télécharger un fichier .msi](http://www.mongodb.org/downloads), l'exécuter et suivre les instructions.

# Démarrage

Avant de démarrer le serveur MongoDB, il est nécessaire de créer le dossier dans lequel les données vont être stockées. Par défaut, il s'agira du dossier ```/data/db``` pour Linux et Mac OS, et du dossier ```\data\db``` pour Windows.

```bash
#Linux et Mac OS
mkdir -p /data/db

#Windows
md \data\db
```

Si vous désirez créer et utiliser un dossier qui ne correspond pas au dossier par défaut, pas de problème. Il suffira de le préciser au démarrage du serveur. 

[[attention]]
| Si vous avez utilisé un gestionnaire de paquets, vérifiez où MongoDB a été installé, et ouvrez le fichier ```mongod.conf``` pour voir dans quel dossier (```dbpath```) les données sont stockées par défaut. Il aura sans doute été créé automatiquement par le gestionnaire de paquets également.

## Serveur

Nous allons maintenant démarrer le serveur qui répond au doux nom de *mongod*. Pour cela, rien de plus simple, il suffit d'utiliser la ligne de commande (le Terminal  ou équivalent sur Mac OS, le Command Prompt ou équivalent sur Windows).

```bash
mongod
```

Ou, pour Windows (adaptez le chemin si nécessaire) :

```bash
C:\mongodb\bin\mongod.exe
```

Pour préciser dans quel dossier les données devront être stockées, on peut utiliser l'option ```--dbpath```.

```bash
mongod --dbpath /Users/Taguan/mongo/data/
```

Nous avons maintenant un serveur MongoDB qui tourne.

[[information]]
| Il est utile (et très courant) de créer un service permettant de démarrer et arrêter facilement le serveur *mongod*. La plupart des gestionnaires de paquets Linux créent ce service par défaut. On peut alors démarrer le serveur avec la commande ```sudo service mongod start``` et l'arrêter avec ```sudo service mongod stop```. Pour créer un service *mongod* sous Windows, référez-vous à la [documentation](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/#manually-create-windows-service).

## Client

Ouvrez une deuxième fenêtre de votre outil de ligne de commande (en laissant ouvert celui dans lequel vous avez démarré le serveur, histoire de ne pas l'éteindre), et exécutez la commande suivante pour ouvrir le client, fourni par défaut avec MongoDB.

```bash
mongo
```

Pour Windows :

```bash
C:\mongodb\bin\mongo.exe
```

Bienvenue dans le shell mongo !