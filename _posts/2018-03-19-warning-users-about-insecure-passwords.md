---
layout: post
title: warning users about insecure passwords
---

## laravel pwned passwords validator

This small guide expects you to have my pwned passwords validator for laravel
5.6. It's painless to install and set up:

```bash
composer require acodeninja/laravel-pwned-passwords-validator
```

Once installed, Laravel's package discovery will take care of loading the
package for use in your application. You can read more about using the validator
[here](http://acode.ninja/laravel-pwned-passwords-validator).

## checking passwords and warning users on login

To check if a password is secure each time a user logs in you can use the
validator. To do this, you use the validator and redirect with errors if the
login password fails to pass.

Simply put, add the following to your application's login controller, in a default application this is at `app/Http/Controllers/Auth/LoginController.php`

{% raw %}
```php
/**
 * @param Request $request
 * @param User $user
 * @return $this|\Illuminate\Http\RedirectResponse
 */
public function authenticated(Request $request, User $user)
{
    $validator = Validator::make($request->all(), [
        'password' => 'required|pwned_password_strict',
    ]);

    $redirect = redirect()->intended($this->redirectPath());

    return $validator->fails() ?
        $redirect->withErrors($validator->errors()) :
        null;
}
```
{% endraw %}

You can then display the errors as normal on the page your application redirects
to, normally this would be `resources/views/home.blade.php`

{% raw %}
```html
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```
{% endraw %}

Or you can make it look a little nicer and display it above other messages with

{% raw %}
```html
@if ($errors->has('password'))
    <div class="alert alert-danger">
        {{ $errors->first('password') }}
    </div>
@endif
```
{% endraw %}
