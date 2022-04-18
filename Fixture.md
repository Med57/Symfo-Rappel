Fixture 
=========

- commande pour installer les fixtures : 

```bash composer require orm-fixtures```

- construire une fixture :

```bash bin/console make:fixtures```

- charger les fixtures :

```bash bin/console doctrine:fixtures:load```

Cette commande purgera la base de données pour la recharger avec les donné dans la fonction load.

- Exemple de code avec l'utilisisation de faker.

```php
$faker = Factory::create('fr_FR');
$faker->addProvider(new MovieProvider($faker));

for ($i=0; $i < 1500; $i++) { 
            $actor = new Actor();
            $actor->setFirstName($faker->firstname());
            $actor->setLastName($faker->lastname());

            $manager->persist($actor);
            // on garde trace de tout les actor dans un tableau
            // pour une utilisation ultérieure
            $actorList[] = $actor;
        }
```

- Pour charger les données avant des autre donnée il faut utiliser la fonction : 

```php
public function getDependencies()
    {
        // On veux absolument que les fixture de Genre soit éxecuté AVANT
        return [
            GenreFixture::class,
        ];
    }
```
