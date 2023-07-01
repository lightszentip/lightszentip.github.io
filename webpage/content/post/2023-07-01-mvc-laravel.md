---

layout: post
title: "Implementing MVC Architecture with Repository Pattern in Laravel"
date: 2023-07-01 00:00:00 -0000
categories: laravel
draft: false
summary: "A Example for laravel MVC"

---

# Implementing MVC Architecture with Repository Pattern in Laravel

Natürlich! Hier ist eine erweiterte Version des Artikels mit Beispielcode für den Controller und das Repository:

Title: "Implementing MVC Architecture with Repository Pattern in Laravel"

Introduction:

Laravel is a popular PHP framework that enables developers to create web applications quickly and efficiently. One of the key design patterns used in Laravel is the MVC (Model-View-Controller) pattern. When combined with the Repository Pattern, the MVC architecture can enhance code organization and maintainability by separating database access and business logic. In this blog post, we will walk through the step-by-step process of creating a simple Laravel project and implementing the MVC architecture with the Repository Pattern.

## Step 1: Creating a Laravel Project and Model

First, we create a new Laravel project using the Composer command:

```
composer create-project --prefer-dist laravel/laravel example-mvc-app
```

Next, we create a model for our blog articles using the Artisan command:

```
php artisan make:model Artikel
```

## Step 2: Migration and Database Setup

We create a migration to generate the table for our blog articles in the database:

```
php artisan make:migration create_artikel_table
```

Open the newly created migration file in the `database/migrations` folder and define the columns for the blog articles table. Then, execute the migration:

```
php artisan migrate
```

## Step 3: Creating Controller and Views

Now, we create a controller to manage actions for our blog articles:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Repositories\ArtikelRepository;

class ArtikelController extends Controller
{
    private $artikelRepository;

    public function __construct(ArtikelRepository $artikelRepository)
    {
        $this->artikelRepository = $artikelRepository;
    }

    public function index()
    {
        $artikel = $this->artikelRepository->getAll();
        return view('artikel.index', ['artikel' => $artikel]);
    }

    public function show($id)
    {
        $artikel = $this->artikelRepository->getById($id);
        return view('artikel.show', ['artikel' => $artikel]);
    }

    public function create()
    {
        return view('artikel.create');
    }

    public function store(Request $request)
    {
        $this->artikelRepository->create($request->all());
        return redirect()->route('artikel.index');
    }

    public function edit($id)
    {
        $artikel = $this->artikelRepository->getById($id);
        return view('artikel.edit', ['artikel' => $artikel]);
    }

    public function update(Request $request, $id)
    {
        $this->artikelRepository->update($id, $request->all());
        return redirect()->route('artikel.index');
    }

    public function destroy($id)
    {
        $this->artikelRepository->delete($id);
        return redirect()->route('artikel.index');
    }
}
```

## Step 4: Creating the Repository Class

Next, we create a repository class to handle database queries for our blog articles:

First, we create an interface for our ArtikelRepository to define the contract that our repository class must implement. Create a new file ArtikelRepositoryInterface.php in the app/Repositories folder:

```php
// app/Repositories/ArtikelRepositoryInterface.php

namespace App\Repositories;

interface ArtikelRepositoryInterface
{
    public function getAll();

    public function getById($id);

    public function create(array $data);

    public function update($id, array $data);

    public function delete($id);
}

```

```php
<?php

namespace App\Repositories;

use App\Models\Artikel;

class ArtikelRepository implements ArtikelRepositoryInterface
{
    public function getAll()
    {
        return Artikel::all();
    }

    public function getById($id)
    {
        return Artikel::find($id);
    }

    public function create(array $data)
    {
        return Artikel::create($data);
    }

    public function update($id, array $data)
    {
        $artikel = Artikel::find($id);
        $artikel->update($data);
    }

    public function delete($id)
    {
        Artikel::destroy($id);
    }
}
```

## Step 5: Configuring Dependency Injection

```
php artisan make:provider RepositoryServiceProvider
```

And add the following code:

```
public function register() 
{
    $this->app->bind(ArtikelRepositoryInterface::class, ArtikelRepository::class);
}
```

## Step 6: Testing the Architecture

Now that our architecture is implemented, we can ensure everything works by testing our application. 

For this we can write rules with https://github.com/smortexa/laravel-arkitect.

## Conclusion:

By implementing the MVC architecture with the Repository Pattern in Laravel, we maintain a clean and maintainable codebase by separating database access and business logic. This improves code reusability and testability, resulting in an overall better development experience. Laravel provides a flexible environment to implement this architecture, enabling us to build powerful and scalable web applications.
