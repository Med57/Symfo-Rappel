Authentification 
=================

## make:user

<strong> En premier lieu je dois créer un système d'utilisateur, avant d'y connecter un sytème d'authentification </strong>

- ```bash composer require security``` => composant necessaire pour la mise en place d'un systeme d'authentification.

- ```bash bin/console make:user``` => commande pour la mise en place du systeme d'utilisateur.


```php
The name of the security user class (e.g. User) [User]:
 > 

 Do you want to store user data in the database (via Doctrine)? (yes/no) [yes]:
 > 

 Enter a property name that will be the unique "display" name for the user (e.g. email, username, uuid) [email]:
 > 

 Will this app need to hash/check user passwords? Choose No if passwords are not needed or will be checked/hashed by some other system (e.g. a single sign-on server).

 Does this app need to hash/check user passwords? (yes/no) [yes]:
 > 

 created: src/Entity/User.php
 created: src/Repository/UserRepository.php
 updated: src/Entity/User.php
 updated: config/packages/security.yaml

           
  Success! 
           

 Next Steps:
   - Review your new App\Entity\User class.
   - Use make:entity to add more fields to your User entity and then run make:migration.
   - Create a way to authenticate! See https://symfony.com/doc/current/security.html
```

- Generer des users via les fixtures ou via des requetes.
  
Lorsque l'on genere des users via des fixtures les mots de passe ne sont pas hashé dans la bdd, ce qui pose un probleme de sécurité.
On doit le faire manuuellement 

```bash bin/console security:hash-password```

```php
Symfony Password Hash Utility
=============================


 For which user class would you like to hash a password? [App\Entity\User]:
  [0] App\Entity\User
  [1] Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface
 > 0

 Type in your password to be hashed:
 > 

 --------------- ----------------------------------------------------------------- 
  Key             Value                                                            
 --------------- ----------------------------------------------------------------- 
  Hasher used     Symfony\Component\PasswordHasher\Hasher\MigratingPasswordHasher  
  Password hash   $2y$13$nPUl2gNIJ0f9YfmVCojnKeuShRbfK4YDTMjFOpJtOe0D7qUrczD1u     
 --------------- ----------------------------------------------------------------- 

 ! [NOTE] Self-salting hasher used: the hasher generated its own        
 !        built-in salt.                                                

                                                                        
 [OK] Password hashing succeeded                                        
```

Pour la fixture, on devrat échapper les $

```php $user->setPassword("\$2y\$13\$nPUl2gNIJ0f9YfmVCojnKeuShRbfK4YDTMjFOpJtOe0D7qUrczD1u");```
        
Ce n'est toujours pas optimal, on voudrait que symfony fasse le hash en auto

Le problème c'est que notre fixture n'accepte pas d'injection de dépendance.

on va donc faire l'injection dans le constructeur, et le stocker dans une propriété

## make:aut

```bash bin/console make:authenticate```

```php
bin/console make:auth

 What style of authentication do you want? [Empty authenticator]:
  [0] Empty authenticator
  [1] Login form authenticator
 > 1

 The class name of the authenticator to create (e.g. AppCustomAuthenticator):
 > OflixAuthentificator

 Choose a name for the controller class (e.g. SecurityController) [SecurityController]:
 > 

 Do you want to generate a '/logout' URL? (yes/no) [yes]:
 > 

 created: src/Security/OflixAuthentificatorAuthenticator.php
 updated: config/packages/security.yaml
 created: src/Controller/SecurityController.php
 created: templates/security/login.html.twig

           
  Success! 
           

 Next:
 - Customize your new authenticator.
 - Finish the redirect "TODO" in the App\Security\OflixAuthentificatorAuthenticator::onAuthenticationSuccess() method.
 - Review & adapt the login template: templates/security/login.html.twig.
