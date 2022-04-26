# API

un logiciel offre des services à d'autres logiciels

Pas de joli pour nos yeux d'humains, pas de template, juste des données

## par où on commence ?

une API reçoit une requete et renvoit une réponse

Donc on commence par un Controller evec des routes

Notre API sera coté Front-Office, mais on va le ranger dans un dossier à part pour notre confort.

```bash
bin/console make:controller --no-template

 Choose a name for your controller class (e.g. TinyGnomeController):
 > Api\Movie

 created: src/Controller/Api/MovieController.php

           
  Success! 
           

32
A circular reference has been detected when serializing the object of class "App\Entity\Mo
```

## JSON

```text
A circular reference has been detected when serializing the object of class "App\Entity\Movie" (configured limit: 1).
```

A circular reference

Doctrine est hyper sympa, dès que l'on tente d'accèder à une relation, il va chercher tout les objets de cette relation.

Le Hic c'est que nous avons des relations dans les deux sens.

Movies->Seasons->Movies->Seasons ... à l'infinie, on tourne en rond, d'où le nom de référence circulaire

## Serialization/normalize/deserialisation

La sérialisation c'est le fait de passer d'un objet à une chaine de caractères.

C'est ce que l'on fait quand on utilise `$this->json($data)`

[doc](https://symfony.com/doc/5.4/components/serializer.html)

```bash
composer require symfony/serializer
```

## Les groupes de sérialisation

[doc](https://symfony.com/doc/5.4/components/serializer.html#attributes-groups)

L'objectif des groupes de sérialisation est de limiter/parametrer la serialisation

Les groupes s'utilisent en annotations

```php
use Symfony\Component\Serializer\Annotation\Groups;
/**
 * @Groups("group1")
 */
public $maPropriete;
```

### Erreur si on oublie le use

```text
[Semantical Error] The annotation "@Groups" in property App\Entity\Genre::$name was never imported. Did you maybe forget to add a "use" statement for this annotation?
```

# nelmio api-doc

[NelmioApiDocBundle](https://symfony.com/doc/current/NelmioApiDocBundle/index.html)

```bash
composer require nelmio/api-doc-bundle
Info from https://repo.packagist.org: #StandWithUkraine
Using version ^4.8 for nelmio/api-doc-bundle
./composer.json has been updated
Running composer update nelmio/api-doc-bundle
Loading composer repositories with package information
Info from https://repo.packagist.org: #StandWithUkraine
Updating dependencies
Lock file operations: 6 installs, 0 updates, 0 removals
  - Locking nelmio/api-doc-bundle (v4.8.2)
  - Locking phpdocumentor/reflection-common (2.2.0)
  - Locking phpdocumentor/reflection-docblock (5.3.0)
  - Locking phpdocumentor/type-resolver (1.6.1)
  - Locking webmozart/assert (1.10.0)
  - Locking zircote/swagger-php (4.3.0)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 6 installs, 0 updates, 0 removals
  - Downloading zircote/swagger-php (4.3.0)
  - Installing zircote/swagger-php (4.3.0): Extracting archive
  - Installing webmozart/assert (1.10.0): Extracting archive
  - Installing phpdocumentor/reflection-common (2.2.0): Extracting archive
  - Installing phpdocumentor/type-resolver (1.6.1): Extracting archive
  - Installing phpdocumentor/reflection-docblock (5.3.0): Extracting archive
  - Installing nelmio/api-doc-bundle (v4.8.2): Extracting archive
Generating optimized autoload files
70 packages you are using are looking for funding.
Use the `composer fund` command to find out more!

Symfony operations: 1 recipe (830c935803ff93bb4ceda254eca6e50c)
  -  WARNING  nelmio/api-doc-bundle (>=3.0): From github.com/symfony/recipes-contrib:master
    The recipe for this package comes from the "contrib" repository, which is open to community contributions.
    Review the recipe at https://github.com/symfony/recipes-contrib/tree/master/nelmio/api-doc-bundle/3.0

    Do you want to execute this recipe?
    [y] Yes
    [n] No
    [a] Yes for all packages, only for the current installation session
    [p] Yes permanently, never ask again for this project
    (defaults to n): y
  - Configuring nelmio/api-doc-bundle (>=3.0): From github.com/symfony/recipes-contrib:master
Executing script cache:clear [OK]
Executing script assets:install public [OK]
              
 What's next? 
              

```

On va décommenter dans le fichier config/routes/nelmio_api_doc.yaml

```yaml
app.swagger_ui:
    path: /api/doc
    methods: GET
    defaults: { _controller: nelmio_api_doc.controller.swagger_ui }
```

[openAPI](https://swagger.io/specification/)


```php
use OpenApi\Annotations as OA;
use Nelmio\ApiDocBundle\Annotation\Model;
/**
 * @OA\Response(
 *     response=200,
     *     description="Returns the rewards of an user",
     *     @OA\JsonContent(
     *        type="array",
     *        @OA\Items(ref=@Model(type=Reward::class, groups={"full"}))
     *     )
     * )
```

## correction Challenge Event

### Event Kernel

```bash
bin/console make:subscriber

Choose a class name for your event subscriber (e.g. ExceptionSubscriber):
 > Maintenance

  What event do you want to subscribe to?:
 > kernel.response

 created: src/EventSubscriber/MaintenanceSubscriber.php

           
  Success! 
           
```

### Event Doctrine

```bash
bin/console make:ent
 Class name of the entity to create or update (e.g. OrangePizza):
 > Movie
 Your entity already exists! So let's add some new fields!
 New property name (press <return> to stop adding fields):
 > updatedAt
 Field type (enter ? to see all types) [datetime_immutable]:
 > datetime
 Can this field be null in the database (nullable) (yes/no) [no]:
 > y
 updated: src/Entity/Movie.php
 Add another property? Enter the property name (or press <return> to stop adding fields):
 > 
           
  Success! 
           
```

```bash
bin/console ma:mi
bin/console d:m:m
```

#### Avec EntityListener

[doc](https://symfony.com/doc/current/doctrine/events.html#doctrine-entity-listeners)

## Relations

Si besoin d'associer des entités existantes (par ex. genres sur movie), on envoie un tableau d'ids dans la propriété JSON concernée, ex. :

```json
{
  "title": "Avatar",
  "type": "Film",
  "duration": 120,
  "rating": 5,
  "summary": "xxx",
  "synopsis": "xxx",
  "releaseDate": "1984-10-05T02:00:44+01:00",
  "poster": "https://m.media-amazon.com/images/M/MV5BYjg4ZjUzMzMtYzlmYi00YTcwLTlkOWUtYWFmY2RhNjliODQzXkEyXkFqcGdeQXVyNTUyMzE4Mzg@._V1_SX300.jpg",
  "genres": [1, 2]
}
```

Et on doit mettre en place un _Entity Denormalizer_ pour permettre au Serializer de convertir l'id en entité Doctrine.

Exemple de code : https://gist.github.com/benlac/c9efc733ee16ebd0d438119bcccb92b9
