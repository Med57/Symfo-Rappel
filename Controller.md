Controller 
==========

Une méthode de controller (sauf celles qui ne sont jamais appelées directement par une route) renvoie toujours un objet Response.

<strong>AbstractController</strong> est un controlleur de base qui propose des méthodes prete a l'emploi.
Quelque exemple de methode :
Ces méthodes permettent notamment de :

- render, renderView , renderForm : appeler un template
- createNotFoundException, createAccessDeniedException: renvoyer un statut HTTP particulier (erreurs 404, 403, 500…)
- generateUrl: générer une url pour une route donnée
- rediriger le visiteur
- récupérer les services disponibles dans l’application (router, session…)
- Déclencher une redirection interne vers une autre action (forward)
- Pour récupérer un paramètre depuis parameters.yaml ou config.yaml > parameters:, la méthode $this->getParameter('secret'); est celle qu’il vous faut

[Tout les methodes de AbstractController](https://github.com/symfony/framework-bundle/blob/5.4/Controller/AbstractController.php)

## Création d'un Controller

- commande :</br>
```php bin/console make:controller ControllerName```
