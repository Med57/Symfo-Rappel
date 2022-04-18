Authorization
=============

## Roles

Un utilisateur connecté possède un ou plusieurs roles. Parlons un peu du `role hierarchy`

### ROLE_HIERARCHY

Il s'agit d'un mécanisme d'héritage de roles.

```yaml
security:
    # ...
    role_hierarchy:
        ROLE_ADMIN: ROLE_MANAGER # les utilisateurs avec le role admin héritent du role Manager (et également du role user cf ci dessous)
        ROLE_MANAGER: ROLE_USER # les utilisateurs avec le role manager héritent du role user
        # une autre facon d'utiliser les roles si les décideurs changent souvent d'avis et / ou inventent de nouveaux roles régulièrement par exemple
        # cela permet de tester les roles dans notre code et de les "affecter" à des roles de la société
        ROLE_ADMIN: [ROLE_BACK_ADD, ROLE_BACK_EDIT, ROLE_BACK_DELETE]
        ROLE_MANAGER: [ROLE_BACK_SHOW, ROLE_BACK_LIST]
        ROLE_USER: [ROLE_FRONT_COMMENT]
```

## Autorisations (in French in the text)

Après avoir authentifié notre utilisateur (grâce au formulaire de login par exemple), on va s'intéresser à ce qu'il PEUT faire (grâce à son / ses role(s))

On peut définir les autorisations à plusieurs niveaux :

1. dans security.yaml
2. dans un controller
   1. avec `$this->denyAccessUnlessGranted('ROLE_QUI_A_LE_DROIT')`
   2. avec une annotation `@isGranted("ROLE_QUI_A_LE_DROIT")` :warning: penser à ajouter le `use .../IsGranted` pour pouvoir utiliser l'annotation
3. Dans TWIG avec `is_granted('ROLE_QUI_A_LE_DROIT')`

### SECURITY.YAML

cf [la doc](https://symfony.com/doc/current/security.html#securing-url-patterns-access-control)  
et [encore plus de doc](https://symfony.com/doc/current/security/access_control.html#1-matching-options)

```yaml
    access_control:
        # require ROLE_ADMIN for /admin*
        - { path: '^/admin', roles: ROLE_ADMIN, methods: [POST] }
        # Il est possible de limiter par méthode HTTP, IP, ... pour plus de précisions voir https://symfony.com/doc/current/security/access_control.html#1-matching-options 
```

### Dans un Controller

En utilisant la méthode `denyAccessUnlessGranged`

```php
class anyController extends AbstractController
{
  
    /**
     * 
     */ 
    public function anActionToSecure(): Response
    {
        // pour vérifier les autorisations on peut utiliser denyAccessUnlessGranted 
            // si l'utilisateur n'est pas connecté => redirection vers la page de login
            // si l'utilisateur n'a pas le role demandé (y compris avec le role hierarchy) => 403 non authorisé
            // sinon symfony ne fait rien et le code continue
        $this->denyAccessUnlessGranted('ROLE_ADMIN');
        // à partir d'ici le code ne s'exécute que si l'utilisateur a 
    }
}
```

ou par annotations

```php
// ne pas oublier le use !
use Sensio\Bundle\FrameworkExtraBundle\Configuration\IsGranted;
class anyController extends AbstractController
{
  
    /**
     * @IsGranted("ROLE_ADMIN")
     */ 
    public function anActionToSecure(): Response
    {
      // toute l'action est sécurisé
    }
}
```

### Dans Twig

en utilisant `is_granted("ROLE_QUI_A_LE_DROIT")`

```html
{% if is_granted('ROLE_ADMIN') %}
<button>un bouton réservé aux admin</button>
{% endif %}
```