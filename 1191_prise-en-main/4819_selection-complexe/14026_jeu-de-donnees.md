Rajoutons quelques informations à nos recettes, afin d'avoir des données plus intéressantes, avec des arrays et des objets imbriqués.

```javascript
var taguan = {pseudo : "Taguan", inscriptionDate : new Date("07-31-2015"), email : "taguan@email.com"}
var leeloo = {pseudo : "Leeloo", inscriptionDate : new Date("06-21-2015"), email : "leeloo@email.com"}

var soupeChicons = db.recipe.findOne({title : "Soupe aux chicons"})
soupeChicons.author = taguan
delete soupeChicons.category
soupeChicons.categories = ["Entrée", "Soupe"]
soupeChicons.portions = 6
soupeChicons.ingredients = []
soupeChicons.ingredients.push({name : "Chicons", quantity : 6}, {name : "Bouillon", quantity : 1, unity : "litres"}, {name : "Courgettes", quantity : 1}, {name : "Sel et poivre"})
db.recipe.save(soupeChicons)

var moelleux = db.recipe.findOne({title : "Moelleux au chocolat"})
moelleux.author = leeloo
delete moelleux.category
moelleux.categories = ["Dessert", "Gateau"]
moelleux.portions = 8
moelleux.ingredients = []
moelleux.ingredients.push({name : "Chocolat", quantity : 200, unity : "grammes"}, {name : "Beurre", quantity : 120, unity : "grammes"}, {name : "Oeufs", quantity : 4}, {name : "Sucre", quantity : 150, unity : "grammes"}, {name : "Farine", quantity : 80, unity : "grammes"})
db.recipe.save(moelleux)

var lemonCurd = db.recipe.findOne({title : "Lemon curd"})
lemonCurd.author = leeloo
delete lemonCurd.category
lemonCurd.categories = ["Dessert"]
lemonCurd.portions = 8
lemonCurd.ingredients = []
lemonCurd.ingredients.push({name : "Citron", quantity : 4}, {name : "Beurre", quantity : 240, unity : "grammes"}, {name : "Oeufs", quantity : 4}, {name : "Sucre", quantity : 220, unity : "grammes"})
db.recipe.save(lemonCurd)

var cupcake = db.recipe.findOne({title : "Cupcake vanille"})
cupcake.author = taguan
delete cupcake.category
cupcake.categories = ["Dessert", "Cupcakes"]
cupcake.portions = 10
cupcake.ingredients = []
cupcake.ingredients.push({name : "Sucre vanillé", quantity : 20, unity : "grammes"}, {name : "Beurre", quantity : 100, unity : "grammes"}, {name : "Oeufs", quantity : 2}, {name : "Sucre", quantity : 100, unity : "grammes"}, {name : "Farine", quantity : 120, unity : "grammes"}, {name : "Vanille", quantity : 1, unity : "gousses"}, {name : "Levure", quantity : 4, unity : "grammes"}, {name : "Mascarpone", quantity : 200, unity : "grammes"}, {name : "Crème fraîche", quantity : 60, unity : "ml"}, {name : "Chocolat blanc", quantity : "200", unity : "grammes"})
db.recipe.save(cupcake)

var pateAChoux = db.recipe.findOne({title : "Pâte à choux"})
pateAChoux.author = taguan
delete pateAChoux.category
pateAChoux.categories = ["Pâte"]
pateAChoux.portions = 4
pateAChoux.ingredients = []
pateAChoux.ingredients.push({name : "Eau", quantity : 125, unity : "ml"}, {name : "Beurre", quantity : 30, unity : "grammes"}, {name : "Oeufs", quantity : 2}, {name : "Farine", quantity : 62, unity : "grammes"})
db.recipe.save(pateAChoux)

var pateBrisee = db.recipe.findOne({title : "Pâte brisée"})
pateBrisee.author = taguan
delete pateBrisee.category
pateBrisee.categories = ["Pâte"]
pateBrisee.ingredients = []
pateBrisee.ingredients.push({name : "Eau", quantity : 30, unity : "ml"}, {name : "Beurre", quantity : 125, unity : "grammes"}, {name : "Oeufs", quantity : 1}, {name : "Farine", quantity : 250, unity : "grammes"}, {name : "Sel"})
db.recipe.save(pateBrisee)

var glaceChoco = db.recipe.findOne({title : "Glace au chocolat"})
glaceChoco.author = taguan
delete glaceChoco.category
glaceChoco.categories = ["Dessert", "Glace"]
glaceChoco.portions = 6
glaceChoco.ingredients = []
glaceChoco.ingredients.push({name : "Lait", quantity : 300, unity : "ml"}, {name : "Crème fraîche", quantity : 300, unity : "ml"}, {name : "Sucre", quantity : 75, unity : "grammes"}, {name : "Jaunes d'oeuf", quantity : 4}, {name : "Chocolat noir", quantity : 200, unity : "grammes"})
db.recipe.save(glaceChoco)

db.recipe.remove({_id : 1})
```

Vous pouvez vérifier ce que ça donne en sélectionnant toutes vos recettes. Pensez à appliquer la méthode pretty() aux résultats pour avoir un joli formattage bien pratique !

```javascript
db.recipe.find().pretty()
```

L'attribut simple *category* a été remplacée par un array *categories*, *author* est un objet imbriqué, et *ingredients* est un array contenant des objets. MongoDB permet de faire des requêtes concernant ces attributs très facilement.