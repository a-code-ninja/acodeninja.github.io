---
layout: post
title: laravel pwned passwords validator
---

## a bit about pwned passwords

So, a while ago the pwned passwords database was made available to the public.
It was a great idea, a collection of all the discrete passwords that had been
included in all the data breaches from
[';--have i been pwned?](https://haveibeenpwned.com/)
Annoyingly to check this database with version 1 of the api you could only
specify a username to see if that was included in a breach.

With the release of the pwned passwords list, we can now check individual
passwords to see if they were included in a breach. So why not check this list
when registering new users and maintain the integrity of your user accounts.
This idea isn't new, and I heard it on [Risky Business E488](https://risky.biz/RB488/)


## why a laravel package?

I thought hells yeah, great idea, but where is the easiest place I can make this
module work with the most impact for my own projects? [Laravel](https://laravel.com/).

Almost every large scale project I have worked on (that isn't a legacy code
shitfest), has been written using the laravel framework. So why not just add a
validator to this great framework to allow developers to quickly drop in a check
against this database.

## why should I send passwords to some random api?

Don't worry, this package won't do anything as stupid as this. First it will
make a SHA1 hash of the password, then send the first 5 characters to the api.
The response from the API will contain a full list of hashes that match the
prefix we sent. If the rest of the hash exists in the list, then the validation
fails, and the error is returned to the validator instance.

## including in your project

Using this validator is really easy, just use composer to install it

```bash
composer require acodeninja/laravel-pwned-passwords-validator
```

Then use it either in your request classes

```php
/**
 * Get the validation rules that apply to the request.
 *
 * @return array
 */
public function rules()
{
    return [
        'email' => 'required|email|unique:users,email',
        'password' => 'required|pwned_password_strict',
    ];
}
```

Or your controllers (if you swing that way)

```php
$validator = Validator::make($request->all(), [
    'email' => 'required|email|unique:users,email',
    'password' => 'required|pwned_password_strict',
])->validate();
```

## thanks

Thanks go out to [Patrick Gray](https://twitter.com/riskybusiness) for a great
podcast that I've followed for years, and to [Troy Hunt](https://twitter.com/troyhunt)
for making a great resource that is really simple to leverage and increases the
security of the internet as a whole.
