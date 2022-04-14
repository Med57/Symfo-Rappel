Relation Doctrine 
=================

## Relations

- Le plus simple pour construire une relation est d'utilisé le wizard avec la commande : 

```bin/console make:entity```

et choisir l'entité a créer ou modifier et au moment de choisir le type de colonne a créer choisir ```Relation```


Je veux créer une relation entre deux entités suivant mon MCD.

Je vais donc détailler à Doctrine ce que je veux.

Pour les relations, la seule chose qui nous intéresse dans le MCD, c'est la cardinalité MAX, le 0 ou 1 de la cardinalité MIN est là pour l'option de nullité.

Je pars de mon MCD et je note :

N de mon coté, et 1 de l'autre
ManyToOne
1 de mon coté, et N de l'autre
OneToMany
N de mon coté, et N de l'autre
ManyToMany

```
What type of relationship is this?
 ------------ ------------------------------------------------------------------ 
  Type         Description                                                       
 ------------ ------------------------------------------------------------------ 
  ManyToOne    Each Movie relates to (has) one Genre.                            
               Each Genre can relate to (can have) many Movie objects            
                                                                                 
  OneToMany    Each Movie can relate to (can have) many Genre objects.           
               Each Genre relates to (has) one Movie                             
                                                                                 
  ManyToMany   Each Movie can relate to (can have) many Genre objects.           
               Each Genre can also relate to (can also have) many Movie objects  
                                                                                 
  OneToOne     Each Movie relates to (has) exactly one Genre.                    
               Each Genre also relates to (has) exactly one Movie.               
 ------------ ------------------------------------------------------------------ 
```
Ce qui est important pour Doctrine c'est qui porte la relation : mappedBy OU inversedBy

ManyToOne
Je suis le porteur de la relation, c'est moi qui dans la base contient la FK. Dans le code, je doit avoir :
```
/* dans la classe Post
* @ORM\ManyToOne(targetEntity=Author::class, inversedBy="posts")
*/
 private $author;
```
J'ai donc une propriété dans ma classe porteuse avec un objet de la classe correspondante (dans l'exemple Author) Je doit trouver un inversedBy

OneToMany
Je NE suis PAS le porteur de la relation, c'est l'autre qui dans la base contient la FK. Dans le code, je doit avoir :
```
/** dans la classe Author
 * @ORM\OneToMany(targetEntity=Post::class, mappedBy="author")
 */
private $posts;
```
J'ai donc une propriété dans ma classe avec un ArrayCollection qui contient toutes les instances des objets liés (dans l'exemple Post) Je doit trouver un mappedBy

ManyToMany
Aucune des deux tables ne porte de FK, il y a une table pivot. Dans le code je doit avoir :
```
/** dans la classe Tag
 * @ORM\ManyToMany(targetEntity=Post::class, mappedBy="tags")
 */
private $posts;
/** dans la classe Post
* @ORM\ManyToMany(targetEntity=Tag::class, inversedBy="posts")
*/
private $tags;
```
Mais ?? pourquoi on a quand même mappedBy OU inversedBy ?

Il faut quand même donner à Doctrine qui des deux entités est l'entité porteuse, celle qui est la plus logique, à vous de décider suivant le cas. L'idée est que l'on veux avoir la collection d'entité depuis l'une plutôt que depuis l'autre.

Dans notre exemple, un Post est notre objet porteur car on affichera les tags dans la page du post, et pas l'inverse. Donc on doit avoir inversedBy dans notre classe Post