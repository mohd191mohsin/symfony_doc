1.create app in symfony

#symfony new ecom --webapp

2.update .env
DATABASE_URL="mysql://root@127.0.0.1:3306/symfony_ecom_app"

3. create database
php bin/console doctrine:database:create

4.Create User Entity
php bin/console make:user

5.Add more fields in users table 

php bin/console make:entity user

add 
    1.mobile
    2.otp
    3.otp_count
    4.two_way_auth
    5.created_at
    6.updated_at

6. create migration
php bin/console make:migration

7. run migration
php bin/console doctrine:migrations:migrate


8. Create Login Form
php bin/console make:auth

9. Configure Security
    open security.yaml and update it
    security:
    password_hashers:
        Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface: 'auto'

    providers:
        app_user_provider:
            entity:
                class: App\Entity\User
                property: email

    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false

        main:
            lazy: true
            provider: app_user_provider
            custom_authenticator: App\Security\LoginFormAuthenticator
            logout:
                path: app_logout
                target: app_login

    access_control:
        - { path: ^/login, roles: PUBLIC_ACCESS }
        - { path: ^/register, roles: PUBLIC_ACCESS }
        - { path: ^/, roles: ROLE_USER }


10. strart dev server
symfony server:start

11. restrict auth user to access login and register 

    if ($this->getUser()) {
        return $this->redirectToRoute('app_home');
    }


12. create a home controller 

    php bin/console make:controller HomeController

13. update HomeController 
    add 
    #[IsGranted('USER_ROLE')]

14. update AppCustomAuthenticator

    add urn new RedirectResponse($this->urlGenerator->generate('app_home'));

15. update home/index.html.twig template

{% extends 'base.html.twig' %}

{% block title %}Hello HomeController!{% endblock %}

{% block body %}
<style>
    .example-wrapper { margin: 1em auto; max-width: 800px; width: 95%; font: 18px/1.5 sans-serif; }
    .example-wrapper code { background: #F5F5F5; padding: 2px 6px; }
</style>

<div class="example-wrapper">
    {% if app.user %}
        <div class="mb-3">
            You are logged in as {{ app.user.userIdentifier }}, <a href="{{ path('app_logout') }}">Logout</a>
        </div>
    {% endif %}
</div>
{% endblock %}
