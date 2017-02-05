Nous avons vu qu'il est possible de sélectionner des documents particulier avec la méthode ```find()```, en lui passant un document en paramètre. Tous les documents qui correspondent au document donné seront récupérés.

```javascript
db.recipe.find({category: "Dessert"})
```

```
{ "_id" : 1, "title" : "Merveilleux au café", "category" : "Dessert" }
{ "_id" : ObjectId("56916df0338e0e9766676aa6"), "title" : "Glace au chocolat", "texte" : "A déguster toute l'année", "category" : "Dessert" }
```

Nous allons maintenant voir qu'il est possible de faire des requêtes bien plus complexes.