---
layout: post
title: "laravel testing: database reset with seeding"
---

Testing a [Laravel][laravel-testing] application has a low barrier to entry
compared to [other][symfony-testing] [PHP][codeigniter-testing]
[frameworks][cakephp-testing]. I did come across a small problem while testing
an application's api and thought I'd share a solution here.

## feature testing

One of the great features Laravel provides is the ability to
[refresh][laravel-testing-db-refresh] your database after each test. This has
been really useful, but I needed to test verbs like DELETE and a simple
`php artisan migrate:fresh` isn't all that useful when you have seeds that add
information to your database that your tests rely on.

The class `Illuminate\Foundation\Testing\RefreshDatabase` has a method used to
action the refresh `refreshTestDatabase` which just calls out to `artisan` for
the `migrate:fresh` command. Step in my nice little override:

```php
// tests/ResetsDatabase.php
<?php

namespace Tests;

use Illuminate\Contracts\Console\Kernel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\RefreshDatabaseState;

trait ResetsDatabase
{
    use RefreshDatabase;

    protected function refreshTestDatabase()
    {
        if (! RefreshDatabaseState::$migrated) {
            $this->artisan('migrate:fresh', $this->shouldDropViews() ? [
                '--drop-views' => true,
            ] : []);

            $this->artisan('db:seed', []);
            $this->artisan('db:seed', [
                '--class' => 'TestDatabaseSeeder',
            ]);

            $this->app[Kernel::class]->setArtisan(null);

            RefreshDatabaseState::$migrated = true;
        }

        $this->beginDatabaseTransaction();
    }
}
```

You can use this in your tests the same way you would the `RefreshDatabase`
class. But instead of just refreshing the database, you will get db:seed for
free along with any test seeds you want to run.

```php
// tests/Feature/UserApiTest.php
<?php

namespace Tests\Feature;

use Tests\ResetsDatabase;
use Tests\TestCase;

class UserApiTest extends TestCase
{
    use ResetsDatabase;

    public function testUserApiDestroy()
    {
        $destroyingUser = User::find(3);

        $response = $this->actingAs($this->testUser, 'api')
            ->delete('/api/user/' . $destroyingUser->id);

        $response->assertStatus(204);

        $response = $this->actingAs($this->testUser, 'api')
            ->get('/api/user/' . $destroyingUser->id);

        $response->assertStatus(404);
    }
}
```

## what about dusk?

The same can be achieved in [dusk][laravel-dusk] with a similar change in the
`DatabaseMigrations` trait provided for resetting the database.

```php
// tests/ResetsDatabaseInDusk.php
<?php

namespace Tests;

use Illuminate\Contracts\Console\Kernel;
use Illuminate\Foundation\Testing\DatabaseMigrations;
use Illuminate\Foundation\Testing\RefreshDatabaseState;

trait ResetsDatabaseInDusk
{
    use DatabaseMigrations;

    protected function runDatabaseMigrations()
    {
        $this->artisan('migrate:fresh', [
            '--drop-views' => true,
        ]);

        $this->artisan('db:seed', []);
        $this->artisan('db:seed', [
            '--class' => 'TestDatabaseSeeder',
        ]);

        $this->app[Kernel::class]->setArtisan(null);

        $this->beforeApplicationDestroyed(function () {
            $this->artisan('migrate:rollback');

            RefreshDatabaseState::$migrated = false;
        });
    }
}
```

This will do the same as the previous trait but for your Browser tests.


[laravel-testing]: https://laravel.com/docs/5.7/testing "Laravel Testing"
[symfony-testing]: https://symfony.com/doc/current/testing.html "Symfony Testing"
[codeigniter-testing]: https://www.codeigniter.com/user_guide/libraries/unit_testing.html "CodeIgniter Testing"
[cakephp-testing]: https://book.cakephp.org/3.0/en/development/testing.html "CakePHP Testing"
[laravel-testing-db-refresh]: https://laravel.com/docs/5.7/database-testing#resetting-the-database-after-each-test "Resetting The Database After Each Test"
[laravel-dusk]: https://laravel.com/docs/5.7/dusk "Laravel Dusk"
