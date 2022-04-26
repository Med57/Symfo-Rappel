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

## Mon Api me donne pas la bonne route

je vérifie que j'arrive dans la bonne méthode en rajoutant sur la première ligne :

```php
die("Tu es passé par ici");
```

Si je n'arrive pas dans cette méthode, je débug mes routes

```bash
bin/console debug:router
```

Si je n'ai pas de conflit de route
Est ce que il n'y a pas une route qui serait au dessus dans la liste de debug qui prendrais la main avant ma route.

Il faut ABSOLUMENT penser à mettre des requirements sur les routes qui ont des paramètres

```php
// requirements={"id":"\d+"}
// requirements={"slug": "\w+(-\w*)*"}
```

En dernier recours, on peut utiliser le profiler
Sauf que l'on a pas le lien direct depuis notre page API
il faut y aller manuellement : http://localhost:8080/_profiler/
Ensuite on peut cliquer sur un des appels en erreur, et on retrouve notre profiler > routing

## POST et deserialize

Quand on est en mode "API", si on permet la création avec la route `POST`, on doit s'attendre à recevoir du JSON.

Simple en PHP, on `deserialize` le json que l'on reçoit et 💥 on a un objet PHP.

On injecte la requète HTTP dans notre fonction pour en récupérer le contenu

```php
use Symfony\Component\HttpFoundation\Request;
public function createItem(Request $request)
{
    // Récupérer le contenu JSON
    $jsonContent = $request->getContent();
```

Comme prévu on `deserialize`, c'est à dire que l'on transforme le JSON en Objet en précisant l'entité que l'on veux.

On n'oublie pas d'injecter le Serializer de Symfony

```php
use Symfony\Component\Serializer\SerializerInterface;
public function createItem(Request $request, SerializerInterface $serializer)
{
    // Récupérer le contenu JSON
    $jsonContent = $request->getContent();
    // Désérialiser (convertir) le JSON en entité Doctrine Movie
    $movie = $serializer->deserialize($jsonContent, Movie::class, 'json');
```

🎉 trop facile, on donnes ça à Doctrine pour qu'il le mettes en BDD et c'est bon 💪

```php
use Doctrine\ORM\EntityManagerInterface;
public function createItem(Request $request, SerializerInterface $serializer, EntityManagerInterface $doctrine)
{
    // Récupérer le contenu JSON
    $jsonContent = $request->getContent();
    // Désérialiser (convertir) le JSON en entité Doctrine Movie
    $movie = $serializer->deserialize($jsonContent, Movie::class, 'json');
    // On sauvegarde l'entité
    $doctrine->persist($movie);
    $doctrine->flush();
```

😅 `SQLSTATE[xxxx] xxxx cannot be null`

Comment ça MySQL n'est pas content ? 👿

Ben oui, il manque des données, on va demander à Symfony de nous valider tout ça 💪 et surtout de nous dire ce qui coince.
Comme ça on prévient notre utilisateur en front et on lui décrit les problèmes pour qu'il s'adapte et qu'il nous envoie les bonnes données.

```php
use Symfony\Component\Validator\Validator\ValidatorInterface;
public function createItem(Request $request, SerializerInterface $serializer, EntityManagerInterface $doctrine, ValidatorInterface $validator)
{
    // Récupérer le contenu JSON
    $jsonContent = $request->getContent();
    // Désérialiser (convertir) le JSON en entité Doctrine Movie
    $movie = $serializer->deserialize($jsonContent, Movie::class, 'json');
    // Valider l'entité
    // @link : https://symfony.com/doc/current/validation.html#using-the-validator-service
    $errors = $validator->validate($movie);
    // Y'a-t-il des erreurs ?
    if (count($errors) > 0) {
        // @todo Retourner des erreurs de validation propres
        return $this->json($errors, Response::HTTP_UNPROCESSABLE_ENTITY);
    }
    // On sauvegarde l'entité
    $doctrine->persist($movie);
    $doctrine->flush();

    // on renvoit un code 201 et l'objet crée
    return $this->json($movie, Response::HTTP_CREATED);
```

## Securiser notre API

[LexikJWTAuthenticationBundle](https://github.com/lexik/LexikJWTAuthenticationBundle)

```bash
composer require "lexik/jwt-authentication-bundle"
```

On génère les clé de chiffrement des Tokens

```bash
bin/console lexik:jwt:generate-keypair
```

on modifie la durée de validité du token : lexik_jwt_authentication.yaml

```yaml
    token_ttl: 64800 # 18h
```

On modifie le fichier security.yaml pour "activer" la sécurité

```yaml
security:
       enable_authenticator_manager: true
       # ...
       
       firewalls:
           login:
               pattern: ^/api/login
               stateless: true
               json_login:
                   check_path: /api/login_check
                   success_handler: lexik_jwt_authentication.handler.authentication_success
                   failure_handler: lexik_jwt_authentication.handler.authentication_failure
           api:
               pattern:   ^/api
               stateless: true
               jwt: ~
       access_control:
           - { path: ^/api/login, roles: PUBLIC_ACCESS }
           - { path: ^/api,       roles: IS_AUTHENTICATED_FULLY }

```

la dernière chose, rajouter la route pour check le token : route.yaml

```yaml
   api_login_check:
       path: /api/login_check
```

si on teste une de nos routes on doit recevoir

```json
{
	"code": 401,
	"message": "JWT Token not found"
}
```

### Authentification API

on doit utiliser la route `api/login_check` en fournissant un json avec le login et le mot de passe
Ce sont les mêmes logins/mdp que nous avons créer pour notre authentification back

```json
{
	"username":"user@user.com",
	"password":"user"
}
```

Si je fournit les bon couple login/mdp je reçoit mon token

```json
{
	"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpYXQiOjE2NTA1NDQzODYsImV4cCI6MTY1MDYwOTE4Niwicm9sZXMiOlsiUk9MRV9VU0VSIl0sInVzZXJuYW1lIjoidXNlckB1c2VyLmNvbSJ9.KZ_GK4o6JaX7yHuVbWeq_1VGhZ6unqPIRgxrtsvfoN9VMKfS0mDk5y9ZMTotvvTv1QR8uVnVp3qOTtDx8iYtndRL7M8iFPGkoKssjJgCMsvGOlnuQqHySLsqeQtgCJX3MEltEC6_716c3k5O9YBbyxbNO8MtovctqtbyMZfEtU38Gl6Y-i_gY6kYjwS_-NGk3P7C1g0JNwq19kMt4Hk0YC_gxYbtduBouLPIGwGW2RFWbZYMW3NwelHI-RF5C0xBN0yhq8ukp0-Syw-MehCZGVTs0u465iUXQ6FLFCgh0X5Yq78XmQW6SpkdChs7eoDIGKUupR9UTMsynH_-y5icng"
}
```

### si on utilise nelmio api doc

On doit parametrer la doc pour pouvoir s'authentifier

`config/packages/security.yaml`

```YAML
firewalls:
    # bundle openAPI
    nelmio_api_doc:
        pattern: ^/api/doc
        stateless: true
access_control:            
    - { path: ^/api/secure, roles: IS_AUTHENTICATED_FULLY }
```

`config/packages/nelmio_api_doc.yaml`

```YAML
nelmio_api_doc:
    documentation:
        # https://swagger.io/docs/specification/authentication/bearer-authentication/
        components:
            securitySchemes:
                bearerAuth:            # arbitrary name for the security scheme this will be use in annotations @Security(name="bearerAuth")
                    type: http
                    scheme: bearer
                    bearerFormat: JWT 
        security:
            - bearerAuth: []
```

Cela active le bouton d'authentification, ce qui permet de s'authentifier en fournissant le token.
Toutes les requetes qui seront faites à l'API seront envoyés avec le header `Authorization: Bearer`, on verras plus tard comment on désactive l'auth sur certaine routes.

Donc on est bon ici, mais comme la route de Lexkit n'est pas trouvé par Nelmio et nous n'y avons pas accès, donc ça perd de son interêt si on doit ouvrir insomnia pour avoir le token.

J'ai donc fabriqué de tout pièces une description de route pour l'afficher

`config/packages/nelmio_api_doc.yaml`

```YAML
nelmio_api_doc:
    documentation:
        # https://swagger.io/docs/specification/paths-and-operations/
        paths: # documentation de la route pour obtenir le token lexkit
            /api/login_check:
                post:
                    summary: Auth method
                    description: authenticate method
                    # https://swagger.io/docs/specification/grouping-operations-with-tags/
                    tags:
                        - O'Flix API Auth
                    # https://swagger.io/docs/specification/describing-parameters/
                    requestBody:
                        description: JSON Object
                        required: true
                        content: 
                            application/json:
                                schema:
                                    type: object
                                    properties:
                                        username:
                                            type: string
                                        password:
                                            type: string
                    responses:
                        '200':
                            description: JWT Token
                            content:
                                application/json:
                                    schema: 
                                        type: object
                                        properties:
                                            token:
                                                type: string
```

On a donc une nouvelle route qui apparait et qui permet de donner son username/password et d'obtenir le token.
On pourra ensuite le donner via le bouton `Authorize` et le tour est joué.

Pour désactiver l'envoi du token, disons pour une partie open de notre API, il y a des annotations à placer sur la route/controller

```php
use Nelmio\ApiDocBundle\Annotation\Security;
/**
* @Security(name=null) pas de sécurité
* @Security(name="bearerAuth") le nom de la sécurité à appliquer
*/
```

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
