# 20170405
## TodoList
* Install Laravel
  ```shell
  laravel new shoopo-core
  ```
* Install Valet
* npm install
* composer install
* Install Laravel Basic Auth
  ```shell
  php artisan make:auth
  php artisan migrate
  ```
* Install and config Laravel API Passprot
    * composer require laravel/passport
    * register the Passport service provider in the providers array of your config/app.php configuration files:
      ```shell
      Laravel\Passport\PassportServiceProvider::class,
      ```
    * The Passport service provider registers its own database migration directory with the framework, so you should migrate your database after registering the provider. The Passport migrations will create the tables your application needs to store clients and access tokens:
      ```shell
      php artisan migrate
      ```
    * Next, you should run the passport:install command. This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens:
      ```shell
      php artisan passport:install
      ```
    * After running this command, add the Laravel\Passport\HasApiTokens trait to your App\User model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes:
      ```php
      <?php

      namespace App;

      use Laravel\Passport\HasApiTokens;
      use Illuminate\Notifications\Notifiable;
      use Illuminate\Foundation\Auth\User as Authenticatable;

      class User extends Authenticatable
      {
          use HasApiTokens, Notifiable;
      }
      ```
    * Next, you should call the Passport::routes method within the boot method of your AuthServiceProvider. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens:
      ```php
      <?php

      namespace App\Providers;

      use Laravel\Passport\Passport;
      use Illuminate\Support\Facades\Gate;
      use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

      class AuthServiceProvider extends ServiceProvider
      {
          /**
           * The policy mappings for the application.
           *
           * @var array
           */
          protected $policies = [
              'App\Model' => 'App\Policies\ModelPolicy',
          ];

          /**
           * Register any authentication / authorization services.
           *
           * @return void
           */
          public function boot()
          {
              $this->registerPolicies();

              Passport::routes();
          }
      }
      ```
    * Finally, in your config/auth.php configuration file, you should set the driver option of the api authentication guard to passport. This will instruct your application to use Passport's TokenGuard when authenticating incoming API requests:
      ```php
      'guards' => [
          'web' => [
              'driver' => 'session',
              'provider' => 'users',
          ],

          'api' => [
              'driver' => 'passport',
              'provider' => 'users',
          ],
      ],
      ```
