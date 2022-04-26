#Event

[doc](https://symfony.com/doc/current/components/http_kernel.html#the-workflow-of-a-request)

## Listener VS Subscriber

[doc](https://symfony.com/doc/current/event_dispatcher.html#listeners-or-subscribers)

## make:subscriber


```bash
bin/console make:subscriber

 Choose a class name for your event subscriber (e.g. ExceptionSubscriber):
 > RandomMovie

 Suggested Events:
 * Symfony\Component\Security\Http\Event\CheckPassportEvent (Symfony\Component\Security\Http\Event\CheckPassportEvent)
 * Symfony\Component\Security\Http\Event\LoginSuccessEvent (Symfony\Component\Security\Http\Event\LoginSuccessEvent)
 * Symfony\Component\Security\Http\Event\LogoutEvent (Symfony\Component\Security\Http\Event\LogoutEvent)
 * console.command (Symfony\Component\Console\Event\ConsoleCommandEvent)
 * console.error (Symfony\Component\Console\Event\ConsoleErrorEvent)
 * console.terminate (Symfony\Component\Console\Event\ConsoleTerminateEvent)
 * debug.security.authorization.vote (Symfony\Component\Security\Core\Event\VoteEvent)
 * kernel.controller (Symfony\Component\HttpKernel\Event\ControllerEvent)
 * kernel.controller_arguments (Symfony\Component\HttpKernel\Event\ControllerArgumentsEvent)
 * kernel.exception (Symfony\Component\HttpKernel\Event\ExceptionEvent)
 * kernel.finish_request (Symfony\Component\HttpKernel\Event\FinishRequestEvent)
 * kernel.request (Symfony\Component\HttpKernel\Event\RequestEvent)
 * kernel.response (Symfony\Component\HttpKernel\Event\ResponseEvent)
 * kernel.terminate (Symfony\Component\HttpKernel\Event\TerminateEvent)
 * kernel.view (Symfony\Component\HttpKernel\Event\ViewEvent)
 * security.authentication.failure (Symfony\Component\Security\Core\Event\AuthenticationFailureEvent)
 * security.authentication.success (Symfony\Component\Security\Core\Event\AuthenticationSuccessEvent)
 * security.interactive_login (Symfony\Component\Security\Http\Event\InteractiveLoginEvent)
 * security.switch_user (Symfony\Component\Security\Http\Event\SwitchUserEvent)

  What event do you want to subscribe to?:
 > kernel.controller
 
 ## Exercice 
 
 ### Event Kernel

```bash
bin/console make:subscriber

Choose a class name for your event subscriber (e.g. ExceptionSubscriber):
 > Maintenance

  What event do you want to subscribe to?:
 > kernel.response

 created: src/EventSubscriber/MaintenanceSubscriber.php

           
  Success! 
           
```

### Event Doctrine

```bash
bin/console make:ent
 Class name of the entity to create or update (e.g. OrangePizza):
 > Movie
 Your entity already exists! So let's add some new fields!
 New property name (press <return> to stop adding fields):
 > updatedAt
 Field type (enter ? to see all types) [datetime_immutable]:
 > datetime
 Can this field be null in the database (nullable) (yes/no) [no]:
 > y
 updated: src/Entity/Movie.php
 Add another property? Enter the property name (or press <return> to stop adding fields):
 > 
           
  Success! 
           
```

```bash
bin/console ma:mi
bin/console d:m:m
```

#### Avec EntityListener

[doc](https://symfony.com/doc/current/doctrine/events.html#doctrine-entity-listeners)


 created: src/EventSubscriber/RandomMovieSubscriber.php

           
  Success! 
           
```
