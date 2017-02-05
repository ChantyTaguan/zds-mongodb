Nous avons vu qu'on pouvait modifier les documents d'une collection grâce à deux méthodes :

- ```update(query, newDoc)``` : remplace le document qui correspond au critère ```query``` par le document ```newDoc``` ;
- ```save(newDoc)``` : si ```newDoc``` possède une ```_id``` remplace le document correspondant à cet identifiant par ```newDoc```. Crée le document si ```_id``` n'existe pas ou n'est pas trouvé.

Dans ce chapitre, nous allons revenir sur la méthode ```update()```, qui peut utiliser plusieurs opérateurs pour ne modifier qu'une partie du (ou des) documents correspondant(s) aux critères donnés. Nous verrons également la méthode ```findAndUpdate()```, qui permet de modifier un document et le récupérer en une seule opération.