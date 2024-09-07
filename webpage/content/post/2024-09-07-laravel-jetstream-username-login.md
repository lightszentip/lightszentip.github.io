---

layout: post
title: "Jetstream login with username and email instead of email only"
date: 2024-09-07 00:00:00 -0000
categories: laravel
draft: false
summary: "A stept by step how to, to use jetstream login page with username and email instead of login by email only"

---

# Jetstream login with username and email instead email only

## Database

The first step is to add the username to the users table:
```php
/**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('username')->unique()->nullable(false);
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('username');
        });
    }
```

## Seeder

IF you use the default jetstream / laravel user seed, please add the username also

```php
class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        User::factory()->withPersonalTeam()->create([
            'name' => 'Test User',
            'email' => 'test@example.com',
            'username' => 'test',
        ]);
    }
}
```

## Login Fortiy Provider

To handle the login with username and password, you need to change the Forty Provider and config:

### config/fortify.php

```yml
    'username' => 'identity', instead of     'username' => 'email',
```

### FortifyServiceProvider

Add the following function to the end of the boot method:

```php
Fortify::authenticateUsing(callback: function (LoginRequest $request) {
            $user = User::where('email', $request->identity)
                ->orWhere('username', $request->identity)->first();
            if ($user && Hash::check($request->password, $user->password)
            ) {
                return $user;
            }
        });
```

### resources/views/auth/login.blade.php

The login page set in default the parameter as email. So we need to change this to identify:

```html
<div>
                <x-label for="identity" value="{{ __('Username/Email') }}" />
                <x-input id="identity" class="block mt-1 w-full" type="identity" name="identity" :value="old('identity')" required autofocus />
            </div>
```

## Final step

*HINT*: If you have exists user, please set a unique username for each user in the migration script. In other case, the migration script is fail.

```shell
php artisan migrate
php artisan db:seed
```

## Change the existign create user process

### app/Models/User.php

Add 'username' to fillable:

```php
 protected $fillable = [
        'name',
        'email',
        'password',
        'username',
    ];
```

### app/Actions/Fortify/CreateNewUser.php

Update the method 'public function create(array $input): User'

```php
  public function create(array $input): User
    {
        Validator::make($input, [
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
            'username' => ['required', 'string', 'max:255', 'unique:users'],
            'password' => $this->passwordRules(),
            'terms' => Jetstream::hasTermsAndPrivacyPolicyFeature() ? ['accepted', 'required'] : '',
        ])->validate();
        return DB::transaction(function () use ($input) {
            return tap(User::create([
                'name' => $input['name'],
                'email' => $input['email'],
                'username' => $input['username'],
                'password' => Hash::make($input['password']),
            ]), function (User $user) {
                $this->createTeam($user);
            });
        });
    }
```

### tests/Feature/AuthenticationTest.php

Change "'email' => $user->email," to " 'identity' => $user->email,"

### tests/Feature/RegistrationTest.php

add username to the register post

## Example integration in Laravel 

see https://github.com/lightszentip/laravel-starter-base-app/pull/34/files
