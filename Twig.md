Twig
====

## Syntaxe

|Syntaxe|PHP |TWIG|
|:---|:---|:---|
|Boucle If|```<?php if ($myTest) : ?> ... <?php endif; ?>```|```{% if myTest %} ... {% endif %}```|
|Boucle Foreach|```<?php foreach ($items as $item) : ?> ... <?php endforeach; ?> ```|```{% for item in items %} ... {% endfor %}```|
|Echo|```<?php echo $test ?>```|```{{ $test }} ```|
|Commentaires|```<?php //Commentaires ?>``` |```{# Comentaires #}```|
|Concatenation|```<?php $nom . $prenom ?>```|```$nom ~ $prenom```|

## Templates 

### Fragments 

- Pour du code a inclure : code à mettre dans base.html.twig<br/>
``` {{ include('nom_du_fichier.html.twig')}} ```

### Heritage

- code a mettre dans base.html.twig si pas deja fait<br/>
``` {% block body %}{% endblock %} ``` 

- code a mettre dans element_a_generer.html.twig si pas deja fait<br/>
```{% extends "base.html.twig" %}```<br/>
```{% block body %} Contenu du code html ou twig {% endblock %}```

### Assets

- l'image est stockée ici "public/images/logo.png"<br/>
```<img src="{{ asset('images/logo.png') }}" alt="Symfony!"/>```

- le fichier css est stocké ici "public/css/style.css"<br/>
```<link href="{{ asset('css/style.css') }}" rel="stylesheet"/>```

- le fichier js est stocké ici "public/js/app.js"<br/>
```<script src="{{ asset('js/app.js') }}"></script>```

### Redirection 

- Pour les redirections nous avons besoin du ```name``` d'une route.
  
```<a href="{{ path('name_de_la_route') }}">Noubeau lien</a>```


