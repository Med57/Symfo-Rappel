Formulaire
===========

- Generer un formulaire.

```bash 
bin/console make:form
```

Cela me crée une classe dans laquelle je défini les champs que je veux générer/utiliser dans le dossier Form.

[Les types de champs possibles](http://symfony.com/doc/current/reference/forms/types.html)

- Validation.

```bash
composer require symfony/validator
```

## Dans le Form

Avec ce composant je peux rajouter des annotations sur les propriétés de mon entity afin de définir des règles de validation.

```php
use Symfony\Component\Validator\Constraints as Assert;
    /**
     * @ORM\Column(type="string", length=255)
     * @link https://symfony.com/doc/current/reference/constraints/Length.html
     * @Assert\NotBlank
     * @Assert\Length(
     *      min = 5,
     *      max = 50,
     *      minMessage = "Your first name must be at least {{ limit }} characters long",
     *      maxMessage = "Your first name cannot be longer than {{ limit }} characters"
     * )
     */
    private $username;
    /**
     * @ORM\Column(type="text")
     * @Assert\NotBlank
     */
    private $body;
```

## Dans Twig 

Mais cela n'empèche pas la validation automatique par le HTML5

Pour désactiver cette validation auto, je rajoute dans twig

```php
{# avant {{ form(formulaire) }} #}
{{ form(formulaire, {attr: {novalidate: 'novalidate'}}) }}
```


[Contraintes de validation](http://symfony.com/doc/current/reference/constraints.html)

## Dans le Controller

```php 
$form = $this->createForm(CommentType::class, $commentForFormulaire);

Je vais donc demander au formulaire, dans le controller, de me dire si toutes les données
renvoyés par request sont valides

// j'associe le formulaire à la request 
// pour qu'il puisse prendre en charge la validation des données
$form->handleRequest($request);

if ($form->isSubmitted() && $form->isValid()){
    // mon code d'insertion dans la BDD
}
// si c'est pas valide, on aura les messages d'alerte dans le HTML
// avec les messages provenant de notre annotation
```


