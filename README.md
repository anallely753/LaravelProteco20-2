
# Diseño web avanzado con Laravel 2020-2

## Primeros pasos

### Hacer nuevo proyecto
> composer create-project laravel/laravel NombreDelProyecto

### Sistema de autenticación de usuarios
> composer require laravel/ui
php artisan ui vue --auth
npm install
npm run dev

### Base de Datos

 1. Crear manualmente una nueva base de datos en localhost/phpmyadmin
 2. Verificar que en el archivo `.env` el nombre de `DB_DATABASE` coincida con el nombre de la Base de Datos apenas creada
 
## Modelos

Los **modelos** están directamente asociados a una entidad y a su vez a una **tabla** de la base de datos.

Su nombre siempre empezará con mayúscula y deberá ir asociado a un archivo de migración donde se especificarán los atributos (columnas de la tabla).

Crear nuevo modelo junto con la migración correspondiente 
>php artisan make:model NombreModelo -m

[Aquí, en la parte de "Columns", encuentran los diferentes tipos de datos que pueden utilizar para definir los atributos al modelo.](https://laravel.com/docs/7.x/migrations)

Hacer migración
>php artisan migrate

Hacer todas las migraciones desde cero
>php artisan migrate:fresh
>

Si sus modelos se relacionan entre sí, se necesita especificar qué tipo de relación tendrán, esto dentro del archivo `NombreDelModelo.php` que está dentro de la carpeta `/App`

[Aquí encuentran las diferentes relaciones que pueden ocupar](https://laravel.com/docs/7.x/eloquent-relationships)

## Rutas

Todas se definen dentro del archivo `/Routes/web.php` 

Si su ruta responde con un controlador, la sintaxis es la siguiente

```php
Route::get('home', 'HomeController@index')->name('home);
````
El primer parametro es la url y el segundo es el nombre del controlador, seguido de la función. Se recomienda asignar un nombre a este tipo de rutas. 

Para pasar parámetros obligatorios
```php
Route::get('user/{id}', 'HomeController@index')->name('home);
````
Recordando que a la función se le tiene que pasar ese parámetro
```php
public function index($id)
{
 $tareas = Tarea::all();
 return view('home',compact('tareas'));
}
````
Para pasar parámetros opcionales
```php
Route::get('user/{id?}', 'HomeController@index')->name('home);
````
Recordando que a la función se le tiene que pasar el valor por defecto del parámetro
```php
public function index($id=NULL)
{
 $tareas = Tarea::all();
 return view('home',compact('tareas'));
}
````

## Middleware
Se utiliza para filtrar las peticiones HTTP

>php artisan make:middleware NombreMiddleware

para aplicarlo a una única ruta
```php
Route::get('/', function () {
    return view('welcome');
})->middleware('welcome');
````

O para agrupar varias rutas que utilicen el mismo Middleware
```php
Route::middleware(['admin'])->group(function(){
	// Aquí se meten todas las rutas
	Route::resource('admintareas', 'TareaController');
});
````

Necesitamos darle un "alias" a nuestro Middleware en el archivo `Kernel.php`

## Controllers

Nos permiten agrupar la lógica de peticiones HTTP relacionadas y de esta forma organizar mejor nuestro código.

>php artisan make:controller NombreController

Para un controlador de recursos:
>php artisan make:controller NombreController -r

La ruta del controlador de recursos se define así:
>Route::resource('ejemplo, 'NombreController');

 - Index: es la vista principal y normalmente ahí se muestran todos los registros
 - Create: muestra el formulario desde donde se hará el nuevo registro
 - Store: guarda los datos del nuevo registro en la tabla
 - Show: muestra un registro en particular
 - Edit: muestra el formulario para editar el registro
 - Update: guarda los nuevos datos en la tabla
 - Delete: borra el registro

Para mostrar datos en las vistas siempre se tienen que mandar desde el controlador

## Blade

### Plantillas

Las plantillas o layouts nos permiten "reciclar" código. 

Usamos 
```php 
@yield('content')
```

dentro del archivo plantilla para indicar que ahí se anexará el contenido que va  a ir cambiando

Y usamos 

```php
@extends('layouts.app')
@section('content')
@endsection
````

para "copiar y pegar" ese código dentro de la plantilla

### Assets
El helper asset nos permite ubicarnos en la carpeta public para hacer referencia a archivos que se encuentren ahí

```php
href="{{asset('css/styles.css')}}"
````

### Rutas

Si la ruta tiene nombre la pasamos con 
```php
href="{{route('nombredelaruta')}}"
````
	
*Podemos verificar los nombres de nuestras rutas usando el siguiente comando:*
> php artisan route:list

Si la ruta no tiene nombre podemos usar
```php
href="{{url('urldelaruta')}}"
````

## Ciclos y condicionales

[En la sección de 'Control Structures' pueden encontrar todos los tipos de ciclos y condicionales que hay](https://laravel.com/docs/7.x/blade)

El que más usamos fue **@foreach()** y sirve para iterar dentro de un arreglo

## Formularios

En la etiqueta `<form>` debemos agregar los siguientes atributos

 - method="POST": obligatorio para todos los formularios
 - action="" : acción que se ejecuta cuando hacemos click en el botón de submit. Normalmente se pone una ruta. `action="{{route('tareas.store')}}"`
 - enctype="multipart/form-data" : en caso de que el formulario reciba archivos

Adicional tenemos que poner **@csrf** por cuestiones de seguridad (y para que no marque errores)

Y alguno de los siguientes métodos dependiendo el caso

 - @method('PATCH') : en el formulario edit
 - @method('DELETE') : en el formulario de eliminar

*Es importante que a cada uno de sus `<input>` le asignen el atributo `name` ya que de esa manera en el controlador reconocemos a cada uno de ellos.*




