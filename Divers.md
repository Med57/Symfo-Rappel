DIVERS
=======

## Ressource 

https://whimsical.com/apollo-KHVuDwbMFpkXLgSYN1QCUj

## Message Flash

En 1er argument le type de message, et en deuxieme argument le message.

https://symfony.com/doc/current/controller.html#flash-messages

Dans le Controlleur : 

```php
$this->addFlash('success', 'Article Created! Knowledge is power!');
```

Dans la vue Twig : 

```php
{% for message in app.flashes('notice') %}
    <div class='alert alert-sucess' role='alert'>
    {{message}}
    </div>
{% endfor %}
```

## Bootstrap


Pour rajouter du zoli :p on peut activer bootstrap 5 dans twig

```yaml
# config/packages/twig.yaml
twig:
    form_themes: ['bootstrap_5_layout.html.twig']
```

## Token CSRF

Pour avoir la protection CSRF, il faut installer le composant symfony suivant On ne change rien dans le code, le token va apparaitre tout seul, Merci Symfony !

```yaml
composer require symfony/security-csrf
```
