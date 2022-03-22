# Laravel Vue Js Search Example with Lodash (Debounce)
Hello artisan, coming with a brand new tutorial that is Laravel vue js search example with debounce. In this laravel vuejs ajax search example you will learn how to search data from database in Laravel and vue js using lodash.

To make a ajax request request to fetch vue js search data i will use axios. You know that axios is a javascript promise based http popular request. So i will use this axios request to fetch our vue js laravel search data with pagination.

laravel vuejs search with pagination example i will use a controller and fetch data from this controller. From this laravel vue search tutorial you will also learn lodash and how to import debounce from lodash vuejs.

 

# Step 1: Install Laravel 

In this vue lodash debounce tutorial you have to first download a fresh laravel project to learn laravel vue js instant search data with pagination.
```
composer create-project --prefer-dist laravel/laravel blog
```
 

# Step 2:  Create Model

We need units table. So paste this below code to units table
```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUnitsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('units', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->tinyInteger('is_active')->default(1);
            $table->integer('created_by')->nullable();
            $table->integer('updated_by')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('units');
    }
}
```
 

Now run php artisan migrate command to migrate units table.
```php
php artisan migrate
```
 

We need to setup our Unit model. so paste this below code to avoid mass assignment error before inserting data to units table.

app/Unit.php
```php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Unit extends Model
{
    protected $guarded = [];
}
```
 

# Step 3:  Setup Route

We have to first fetch the data then we have to create search data option for vue js. So we need two routes like below. So update your web.php file as like below.

routes/web.php
```php
Route::get('/unit', 'UnitController@view_unit')->name('view');
Route::get('/search_unit', 'UnitController@search_unit_by_key');
```
 

And update api.php 

routes/api.php
```php
Route::apiResources(
	[

		'unit' => 'API\UnitController'
	]
);
```
 

# Step 4:  Create Controller

Now we have to create controller to create those avobe method. so create contoller and paste this below code.

php artisan make:controller API/UnitController --api
PHP
 

Now paste below code in UnitController.php

app/Https/Controller/UnitController.php
```php
namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Unit;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class UnitController extends Controller
{
    
    public function view_unit()
    {  
        return view('unit');
    }

    public function search_unit_by_key()
    {
    	$key = \Request::get('q');
        $unit = Unit::withCount('products')
                                    ->where('name','LIKE',"%{$key}%")
                                    ->orWhere('is_active','LIKE',"%{$key}%")
                                    ->get();

    	return response()->json([ 'unit' => $unit ],Response::HTTP_OK);
    }

}
```
 

And we have to also update our API Unit controller to create search with vue js in laravel.

app/Https/Controller/API/UnitController.php
```php
namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Unit;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class UnitController extends Controller
{   

    public function index( Request $request )
    {
        return $request->ajax() ? Unit::paginate(5) : abort(404);
    }
}
```
 

# Step 5:   Configure Vue

If you want to install vue in laravel 8 application then install following laravel ui composer package to get it. So run below command
```php
composer require laravel/ui
```
 

Install Vue
```php
php artisan ui vue
```
 

Now run this command to install laravel vue pagination package
```php
npm install laravel-vue-pagination
```

 

Now our setup is completed. Now we just need to change our app.js file and our component file.

resources/assets/js/components/UnitComponent.vue


```php
<template>

<div class="card">

  <div class="card-header" id="hide">
    <div class="row">
       <div class="col-md-2">
          <input type="text" @keyup="searchUnit" placeholder="Search" v-model="search" class="form-control form-control-sm">
        </div>
    </div>
  </div>


  <div class="card-body" id="hide_again">
    <table id="" class="table table-bordered table-striped">
      <thead>
      <tr>
         <th>Name</th>
         <th>Status</th>
       </tr>
      </thead>
     <tbody>
        <tr v-for="unit in units.data" :key="unit.id">
          <td>{{ unit.name }}</td>
          <td>{{ unit.is_active == 1 ? 'Active' : 'InActive' }}</td>
        </tr>
     </tbody>
     <pagination :data="units" @pagination-change-page="getResults"></pagination>
    </table>

  </div>
</div>
</template>

<script>

    import _ from "lodash";

    export default {

    data() {
        return {
            units: {},
            search: '',
        }
    },
    methods: {

        fetchingAllUnit() {
          axios.get("api/unit").then( data => (this.units = data.data));
        },
        getResults(page = 1) {
              axios.get('api/unit?page=' + page)
                .then(response => {
                  this.customers = response.data;
              });
        },

        searchUnit:_.debounce(function(){
          axios.get('/search_unit?q='+this.search)
                .then((response)=>{
                this.units.data = response.data.unit
          })
        }),
    },
    created(){

        this.fetchingAllUnit();

    },

}
</script>
  
```
 

now update the app.js file like belew

resources/js/app.js

```php
require('./bootstrap');

window.Vue = require('vue');

Vue.component('pagination', require('laravel-vue-pagination'));

Vue.component('unit-component', require('./components/UnitComponent.vue').default);

const app = new Vue({
    el: '#app',
});
JavaScript
 ```

# Step 6:   Configure blade file

 

Now almost all are set to go. open resources/layouts/app.php and paste this following code.

resources/views/layouts/app.blade.php
```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>{{ config('app.name', 'Laravel') }}</title>
    <link href="{{ mix('css/app.css') }}" rel="stylesheet">
    <title>Document</title>
</head>
<body>
    
    <div id="app">

    <div class="py-4">
        @yield('content')
    </div>
    
    </div>
    <script src="{{ mix('js/app.js') }}" defer></script>
</body>
</html>    
```
 

Now open resources/views/post/create.blade.php file and paste this code. 
```php
resources/views/unit.blade.php


@extends('layouts.app')

@section('content')

    <unit-component></unit-component>
           
@endsection   
```
 

Now everything is set to go. Now need to compile our all javascript file. so run below command.
```php
npm run dev 
```
 

 

Now visit the following url to see Laravel vue js instant search example without page refresh with pagination using lodash debouce.

 

VISIT
```php
http://localhost:8000/unit
 ```

# Hope it can help you.

 

#vue-js #laravel #lodash #debounce

A web enthusiastic, a self-motivated full-stack software engineer from Kenya, Nairobi with experience in developing applications using Laravel , React and Vue js
