[[information]]
| Notez que tous les critères de sélection peuvent aussi être utilisés dans les méthodes de modification et suppression de documents.

### Exemple 1 : trouver les recettes de dessert

Ici, pas besoin de sélection particulière, même si l'attribut *categories* est un array. MongoDB cherchera parmi les valeurs de l'array et récupèrera les documents avec la valeur demandée.

```javascript
db.recipe.find({categories : "Dessert"})
```

### Exemple 2 : trouver les recettes dont l'auteur a pour pseudo "Taguan"

Pour faire des sélections sur des attributs d'un objet imbriqué, on utilise la notation avec un point ```.```. Attention que dans ce cas, **les guillemets autour de la clé sont indispensables**.

```javascript
db.recipe.find({"author.pseudo" : "Taguan"})
```

### Exemple 3 : trouver les personnages qui utilisent du lait comme ingrédient

Même principe ici pour sélectionner sur base de l'attribut d'un objet imbriqué contenu dans un array.

```javascript
db.recipe.find({"ingredients.name" : "Lait"})
```