DIVERS
=======


## Message Flash

En 1er argument le type de message, et en deuxieme argument le message.
-
https://symfony.com/doc/current/controller.html#flash-messages

Dans le Controlleur : 

```
$this->addFlash('success', 'Article Created! Knowledge is power!');
```

Dans la vue Twig : 

```
{% for message in app.flashes('notice') %}
    <div class='alert alert-sucess' role='alert'>
    {{message}}
    </div>
{% endfor %}
```

