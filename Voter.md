Voter
=====

[doc](https://symfony.com/doc/current/security/voters.html)

```bash
bin/console make:voter

 The name of the security voter class (e.g. BlogPostVoter):
 > MovieVoter

 created: src/Security/Voter/MovieVoter.php

           
  Success! 
           

 Next: Open your voter and add your logic.
 Find the documentation at https://symfony.com/doc/current/security/voters.html
```
## Dans src/Security/Voter


```php
<?php

namespace App\Security\Voter;

use App\Entity\Movie;
use DateTime;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use Symfony\Component\Security\Core\Authorization\Voter\Voter;
use Symfony\Component\Security\Core\Security;
use Symfony\Component\Security\Core\User\UserInterface;

class MovieVoter extends Voter
{
    public const EDIT = 'POST_EDIT';
    public const VIEW = 'POST_VIEW';

    private $security;

    /**
     * J'implémente le constructeur pour pouvoir faire de l'injection de dépendance
     * @link https://symfony.com/doc/current/security/voters.html#checking-for-roles-inside-a-voter
     */
    public function __construct(Security $security)
    {
        $this->security = $security;
    }


    /**
     * Est ce que je participe au vote ?
     *
     * @param string $attribute la question : POST_EDIT 
     * @param [type] $subject sur quelle entité : Movie
     * @return boolean Oui ou non je participe
     */
    protected function supports(string $attribute, $subject): bool
    {
        // replace with your own logic
        // https://symfony.com/doc/current/security/voters.html
        return in_array($attribute, [self::EDIT, self::VIEW])
            && $subject instanceof \App\Entity\Movie;
    }

    /**
     * Je participe au vote
     *
     * @param string $attribute la question à laquelle je dois répondre : POST_EDIT 
     * @param [type] $subject sur quelle entité : Movie
     * @param TokenInterface $token les informations du contexte, l'utilisateur
     * @return boolean
     */
    protected function voteOnAttribute(string $attribute, $subject, TokenInterface $token): bool
    {
        
        // ROLE_SUPER_ADMIN can do anything! The power!
        if ($this->security->isGranted('ROLE_SUPER_ADMIN')) {
            return true;
        }

        $user = $token->getUser();
        // dump($user);
        // if the user is anonymous, do not grant access
        if (!$user instanceof UserInterface) {
            return false;
        }

        // ... (check conditions and return true to grant permission) ...
        switch ($attribute) {
            case self::EDIT:
                // logic to determine if the user can EDIT
                // return true or false
                    // si il est plus de 14h on n'a pas le droit de modifier
                    // https://www.php.net/manual/fr/datetime.format.php
                    if (date_format(new DateTime(), 'Hi') > 1800)
                    {
                        return false;
                    }
                    return true;
                break;
            case self::VIEW:
                // logic to determine if the user can VIEW
                // return true or false
                return true;
                break;
        }



        return false;
    }
}
```