Routage
=======


- définir une route avec des parametres variables ("wildcards") :
  
```php
/**
 * Correspond à /blog/*
 *
 * @Route("/blog/{slug}", name="read-slug")
 */
public function readBySlug($slug)
{
    // $slug sera égal à la partie dynamique de l'URL
    // si l'URL est `/blog/super-routing`, alors `$slug='super-routing'`

    // ...
}
```

- définir des contraintes ("requirement") : 

```php
/**
 *
 * @Route("/blog/article/{id}", name="read-id", requirements={"id"="\d+"})
 */
public function readById(int $id)
{
    // si l'id fournit n'est pas de type numérique, le router n'exécutera pas cette méthode
    // ...
}
```

- définir des valeurs par défaut pour les parametres : 

```php
/**
 *
 * @Route("/blog/{page}", name="list-paginated")
 */
public function paginatedList(int $page = 1)
{
    // si l'utilisateur accède à /blog/ 
    // alors le paramètre $page contiendra la valeur par défaut 1

    // ...
}
```

- pour précisez une méthode : 
  
```php
/**
 * @Route("/edit/{id}", methods={"GET","POST"})
 */
public function edit(int $id)
{
     // cette méthode de controlleur ne sera accessible que si la requête http est effectuée avec le verbe POST ou GET
}
```