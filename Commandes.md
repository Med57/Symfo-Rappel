## Commandes

Objectif :
la génération du slug pourrait être faites à la demande, plutot que de relancer les fixture à chaque fois
je veux une commande pour lancer la génération des slug direct en BDD

```bash
bin/console make:com

 Choose a command name (e.g. app:brave-popsicle):
 > app:movie:slug

 created: src/Command/MovieSlugCommand.php

           
  Success! 
           
```

### Erreur Commandes

Après avoir créer le constructeur, j'ai cette erreur

```bash
 bin/console app:movie:slug

                                                                                            
  Command class "App\Command\MovieSlugCommand" is not correctly initialized. You probably   
  forgot to call the parent constructor.                                                    
                                           
```

`is not correctly initialized` => ton constructeur ne fait pas tout ce qu'il faut.

Il doit y avoir des instructions/code obligatoire dans ce constructeur.

Comment je sais qu'est ce qu'il faut faire ???

On ne peut pas le deviner, mais ça marchait avant car dans l'héritage on hérite du contructeur du parent.
Le fait que l'on écrive nous même le constructeur, cela "écrase" le constructeur du parent.
On perd donc les `trucs (correctly initialized)` de la classe parent.

On peut y faire appel directement et pas se prendre la tête.
Il nous propose de le faire : `You probably forgot to call the parent constructor.`

```php
public function __construct(MovieRepository $movieRepository, SluggerInterface $slugger, EntityManagerInterface $em)
    {
        // je fait appel au constructeur parent pour être `correctly initialized`
        parent::__construct();
```
