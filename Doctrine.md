Doctrine 
==========

## Schema

![Vu d'ensemble fonctionnement Doctrine](/images/doctrine-orm.png "Vu d'ensemble fonctionnement Doctrine").


## Configuration 

- Crée une copie de .env et le renommé en .env.local et completer les infos.

```php DATABASE_URL="mysql://db_user:db_password@127.0.0.1:3306/db_name?serverVersion=mariadb-10.3.34"```

## Utilisation

### BDD

- Création d'une base de donnée 
```bash bin/console doctrine:database:create```

### Entité

- Création d'une Entitée
```bash bin/console make:entity```

- Les infos de mappings sont dans les commentaires, elles permettent de faire le liens vers les differentes tables/colonnes ainsi les regles qui les regissent .

```php
namespace App\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity(repositoryClass="App\Repository\ProductRepository")
 */
class Product
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    private $name;

    /**
     * @ORM\Column(type="integer")
     */
    private $price;

    public function getId()
    {
        return $this->id;
    }

    // ... getter and setter methods
}
```

- Valider les regles avec la commande :</br>
```bash bin/console doctrine:schema:validate ```

- Pour generer les getter/setter ou regenerer les getter/setter apres une eventuel modification d'une Entité utilisé la commande :</br>
```bash bin/console make:entity --regenerate ```

### Migration 

- pour generer une migration suite a la creation d'une Entité utilisé la commande :</br>
```bash bin/console make:migration```</br>
```bash bin/console ma:mi```

- pour executer une migration : </br>
```bash bin/console doctrine:migrations:migrate```</br>
```bash bin/console d:m:m```

### Entity Manager

- Permet de persister les objets vers la base de données

```php
namespace App\Controller;

use App\Entity\Product;

class ProductController extends Controller
{
    /**
     * @Route("/product", name="product")
     */ 
    public function index(EntityManagerInterface $entityManagerInterface) // On fais une injection de dépendance sur EntityManagerInterface.
    {
        $product = new Product();
        $product->setName('Keyboard');
        $product->setPrice(1999);
        $product->setDescription('Ergonomic and stylish!');

        // On sauvegarde le nouveau produit mais aucune requete SQL n'a été effectué.
        $entityManager->persist($product);

        // On execute la requete SQL.
        $entityManager->flush();

        return $this->redirectToRoute('nom_de_route_voulu')
    }
}
```

### Repository

- Permet de recuperer des objets depuis la base de données.
- Possibilité de créer des methodes personnalisées dans le Repository.

```php
src/Controller/ProductController.php

/**
 * @Route("/product/{id}", name="product_show")
 */
public function show($id, ProductRepository $productRepository) // On fais une injection de dépendance sur le Repository de l'Entité.
{
    $product = $productRepository->find($id);

    if (!$product) {
        throw $this->createNotFoundException(
            'No product found for id '.$id
        );
    }

    return $this->redirectToRoute('nom_de_route_voulu')
}
```

- Select : 
```php
// query for a single product by its primary key (usually "id")
$product = $repository->find($productId);

// dynamic method names to find a single product based on a column value
$product = $repository->findOneById($productId);
$product = $repository->findOneByName('Keyboard');

// dynamic method names to find a group of products based on a column value
$products = $repository->findByPrice(1999);

// find *all* products
$products = $repository->findAll();
```

- Les methodes findBy et findOneBy (Repository): 
```php
// Permet de faire une requete en recuperant 1 seul objet avec plusieurs criteres.
$product = $repository->findOneBy(
    array('name' => 'Keyboard', 'price' => 1999)
);

// Permet de faire une requete en recuperant plusieurs objets avec plusieurs criteres.
$products = $repository->findBy(
    array('name' => 'Keyboard'),
    array('price' => 'ASC')
);
```

- Update ( Repository )

```php
// Après avoir récupéré l'objet
$product->setName('New product name!');
$em->flush();
// A noter : $em->persist() pas utile ici car récupéré via Doctrine
```

- Delete ( EntityManager)

```php
// Après avoir récupéré l'objet
$em->remove($post); // en mémoire (intention)
$em->flush(); // en bdd ! (execute DELETE)
```


### Doctrine Query Language ( DQL )

C'est un langage qui ressemble fortement à SQL. C'est propre à doctrine.

### Query Builder 

- C'est une classe qui va nous proposer de créer des requetes depuis le repository. On ne fait toujours pas de SQL, un construit à partir de nos entités

```php
/**
* Get Casting + Person for a given movie
*/
public function findAllJoinedToPersonByMovieQb(Movie $movie)
{
    // SELECT c.* FROM casting
    return $this->createQueryBuilder('c')
        // JOIN casting ON c.person = person.id
        ->innerJoin('c.actor', 'a')
        // SELECT aussi a.* :)
        ->addSelect('a')
        // WHERE c.movie_id = movie.id
        ->where('c.movie = :movie')
        ->setParameter('movie', $movie)
        // ORDER BY creditOrder ASC
        ->orderBy('c.creditOrder', 'ASC')
        // Récupère la requête
        ->getQuery()
        // Récupère les résultats
        ->getResult();
}
```

### Schema recapitulatif 

![Resumé Doctrine](/images/doctrine-symfony.png "Resumé Doctrine").
