---

layout: post
title: "Setup Laravel to use mssql"
date: 2022-12-12 00:00:00 -0000
categories: laravel
draft: false
summary: "How to use mssql in laravel"

---

# Setup Laravel to use mssql

## Install mssql driver for php

Driver: https://websolutionstogo.de/blog/mssql-microsoft-sql-treiber-in-xampp-einbinden/

## Setup .env

Open the .env file and change the following settings:

<!--more-->

```
DB_CONNECTION=sqlsrv
DB_HOST=localhost
#DB_PORT=3306
DB_DATABASE=LARAVEL_TEST
DB_USERNAME=laravel
DB_PASSWORD=***
```

After this run `php artisan migrate and php artisan serve`

## Autoincrement

To use the mysql autoincremnt in mssql:

see 
```php
$query = "CREATE TABLE [table].[myTable](id%20int%20IDENTITY(1,1), project varchar(10), name varchar(50), path varchar(500), categorie varchar(50))"; 
```
