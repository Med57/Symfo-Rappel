Installation Projet 
====================

Installation pour une application.

```bash
composer create-project symfony/website-skeleton my-project 
```

Installation pour API etc...

```bash
composer create-project symfony/skeleton my-project
```

Execution d'un serveur via le serveur de developpement PHP:

```bash
php -S 0.0.0.0:8000 -t public
```
Configuration BDD:

Creer un fichier .env.local

```php 
DATABASE_URL="mysql://oflix:oflix@127.0.0.1:3306/oflix?serverVersion=mariadb-10.3.25"
```

![Ecosysteme Symfo](/images/symfony-ecosystem-1.png "Ecosysteme Symfo").
