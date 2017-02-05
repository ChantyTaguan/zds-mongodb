Rentrons maintenant dans le vif du sujet : nous allons voir comment faire les opérations de base dans MongoDB : créer, lire, modifier et supprimer des documents. Ces quatre opérations sont couramment appelées CRUD (**C**reate - **R**ead - **U**pdate - **D**elete).

Reprenez votre client. Au démarrage, celui-ci vous donne deux informations : la version de mongoDB utilisée, et la base de données à laquelle vous êtes connecté.

```javascript
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27017/test
```

Par défaut, au démarrage, mongo utilise la base de données *test*. Pour utiliser une autre base de données, il suffit d'utiliser la commande suivante :

```javascript
use cookbook;
```

```shell
switched to db cookbook
```

Nous voilà donc dans une base de données nommée *cookbook*. Cette base de données nous servira tout au long du cours et consistera en un recueil de recettes de cuisine.