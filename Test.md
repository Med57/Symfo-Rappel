# Test

```bash
composer req phpunit --dev
bin/console make:test

 Which test type would you like?:
  [TestCase       ] basic PHPUnit tests
  [KernelTestCase ] basic tests that have access to Symfony services
  [WebTestCase    ] to run browser-like scenarios, but that don't execute JavaScript code
  [ApiTestCase    ] to run API-oriented scenarios
  [PantherTestCase] to run e2e scenarios, using a real-browser or HTTP client and a real web server
 > KernelTestCase


Choose a class name for your test, like:
 * UtilTest (to create tests/UtilTest.php)
 * Service\UtilTest (to create tests/Service/UtilTest.php)
 * \App\Tests\Service\UtilTest (to create tests/Service/UtilTest.php)

 The name of the test class (e.g. BlogPostTest):
 > Service\OmdbApi

 created: tests/Service/OmdbApiTest.php

           
  Success! 
           

```

Pour lancer les test:

```bash
bin/phpunit
```

pour désactiver les deprecation, dans le fichier .env.test

```text
# active les notices de deprecation, même celles de syfony
# on les désactive 
# SYMFONY_DEPRECATIONS_HELPER=999999
SYMFONY_DEPRECATIONS_HELPER=disabled
```

## generer un rapport de couverture de code

à vous de changer le nom du dossier dans lequel mettre le rapport

```bash
bin/phpunit --coverage-html ./tests/coverage/2022-04-22
```

## Webtest

```bash
bin/console make:test

 Which test type would you like?:
  [TestCase       ] basic PHPUnit tests
  [KernelTestCase ] basic tests that have access to Symfony services
  [WebTestCase    ] to run browser-like scenarios, but that don't execute JavaScript code
  [ApiTestCase    ] to run API-oriented scenarios
  [PantherTestCase] to run e2e scenarios, using a real-browser or HTTP client and a real web server
 > WebTestCase


Choose a class name for your test, like:
 * UtilTest (to create tests/UtilTest.php)
 * Service\UtilTest (to create tests/Service/UtilTest.php)
 * \App\Tests\Service\UtilTest (to create tests/Service/UtilTest.php)

 The name of the test class (e.g. BlogPostTest):
 > WebTest\Home

 created: tests/WebTest/HomeTest.php

           
  Success! 
           

```

## Je n'arrive pas à me connecter à ma BDD

Qunad j'execute mes tests, Doctrine rajoute en automatique un `_test` au nom de la BDD pour avoir une BDD spécifique pour les tests

```text
ErrorException: An exception occurred in the driver: SQLSTATE[HY000] [1044] Access denied for user 'oflix'@'localhost' to database 'oflix_test'
```

## Les commandes dans le bon contexte

```bash
bin/console --env=test doctrine:database:create
bin/console --env=test d:m:m
bin/console --env=test doctrine:f:l
```

## mon test me donne un 301

Lorque l'on met une route sur le controller `@Route("/back/movie")` ET une route sur la méthode `@Route("/")`, lorsque l'on demande la route `/back/movie` (sans le `/` à la fin) symfony est gentil ?? et nous redirige vers la route `/back/movie/` et donc nous renvoit un 301
