---

layout: post
title: "Setup Laravel to use sqlite"
date: 2022-12-11 00:00:00 -0000
categories: laravel
draft: false
summary: "How to use sqlite in laravel"

---

# Setup Laravel to use sqlite

Setup .env

Open the .env file and change the following settings:

<!--more-->

```
DB_CONNECTION=sqlite
DB_DATABASE=database/database.sqlite
```

and set

```
SESSION_DRIVER=file
```

to file.

After this run `php artisan migrate and php artisan serve`
