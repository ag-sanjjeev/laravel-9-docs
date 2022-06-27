# **Project Setup:**

## Laravel Installation:

  `composer global require laravel/installer`

## Creating new laravel project

  `laravel new example-app`

## Create project with git repository

  `laravel new example-app --git`

  `laravel new example-app --git --branch="main"`

## Serving / Running project

  `cd example-app`

  `php artisan serve`

## Maintenance mode

  `php artisan down --refresh=15` 
  
  *refresh the page every 15 seconds*

  `php artisan down --retry=60`

## Bypassing Maintenance mode

  `php artisan down --secret="1234542a-246b-4b66-afa1-dd72a4c43515"`

  *via following URL with secret key :* <https://example.com/1234542a-246b-4b66-afa1-dd72a4c43515>

## Pre-rendering Maintenance mode

  `php artisan down --render="errors::503"`

## Redirect Maintenance mode

  `php artisan down --redirect=/`

## Disabling Maintenance mode

  `php artisan up`

## Storage directory mapping or symbolic link

  `php artisan storage:link`

  *this will generate symbolic link for the publicly available directory that storage/app/public into public/storage.*

## Get available make artisan commands

  `php artisan list make`

  *That commands originated from console directory inside app directory.*

---

# **Important Steps to follow when deploying:**

  This will have all essential technique, performance optimization and security features implementation with it.

  Here are some as follows:
    1. Server Requirement to run application,
    2. Server configuration (example: pointing to public/index.php),
    3. Autoloader optimization,
    4. configuration cache (env file will never used after that),
    5. route cache,
    6. view cache,

  all before process set debug mode false.

  Follow the bellow link or check for any other versions.

  <https://laravel.com/docs/9.x/deployment>


---

# **Routings:**

## Route structures:

  The first parameter is referred to the request and second to the callback in different method as given further below methods.

  ```php  
  Route::get($uri, $callback);
  Route::post($uri, $callback);
  Route::put($uri, $callback);
  Route::patch($uri, $callback);
  Route::delete($uri, $callback);
  Route::options($uri, $callback);
  ```

## Method 1:

  Returning text for the request.

  ```php
  use Illuminate\Support\Facades\Route;

  Route::get('/greeting', function () {
    return 'Hello World';
  });
  ```

## Method 2:

  Returning to controller method for the request.

  use App\Http\Controllers\UserController;

  Route::get('/user', [UserController::class, 'index']);

## Method 3:

  This will check request method as mention in the first parameter array.

  ```php
  Route::match(['get', 'post'], '/', function () {
    //
  });
  ```

## Method 4:

  This will take request with any of the request method.

  ```php
  Route::any('/', function () {
    //
  });
  ```

## Redirecting Route:

  Simple redirection for the request URL.

  ```php
  Route::redirect('/here', '/there');
  ```

  Redirection with status code for the requested URL.

  ```php
  Route::redirect('/here', '/there', 301);
  ```

  Permanent Redirection with status code 301 for the requested URL.

  ```php
  Route::permanentRedirect('/here', '/there');
  ```

## Method 5:

  Returning the view page / file for the requested URL.

  ```php
  Route::view('/welcome', 'welcome');
  ```

  This will additionally pass some data to it.

  ```php
  Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
  ```

## Method 6:

  Routes with required URL parameter.

  ```php
  Route::get('/user/{id}', function ($id) {
      return 'User '.$id;
  });
  ```

  Routes with many parameter required and that parameter should be positional.

  ```php
  Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
  });
  ```

## Method 7:

  Route parameter with dependency injection. Here, Request is dependency. That is injected with URL parameter.

  ```php
  use Illuminate\Http\Request;

  Route::get('/user/{id}', function (Request $request, $id) {
    return 'User '.$id;
  });
  ```

## Method 8:

  Route with optional URL parameters.

  ```php
  Route::get('/user/{name?}', function ($name = 'Guest') {
      return 'User name : ' . $name;
  });
  ```

## Method 9:

  Route URL parameters with regular expression constrains. That accepts request only if valid.

  ```php
  Route::get('/user/{name}', function ($name) {
      //
  })->where('name', '[A-Za-z]+');

  Route::get('/user/{id}', function ($id) {
      //
  })->where('id', '[0-9]+');

  Route::get('/user/{id}/{name}', function ($id, $name) {
      //
  })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

  Route::get('/user/{id}/{name}', function ($id, $name) {
      //
  })->whereNumber('id')->whereAlpha('name');

  Route::get('/user/{name}', function ($name) {
      //
  })->whereAlphaNumeric('name');

  Route::get('/user/{id}', function ($id) {
      //
  })->whereUuid('id');

  Route::get('/category/{category}', function ($category) {
      //
  })->whereIn('category', ['movie', 'song', 'painting']);
  ```

## Route URL Parameter Global Constrains:

  It need to define inside boot method in the App\Providers\RouteServiceProvider Class.
  and this will recognize id URL parameter wherever is defined also need not to call where constrain for that parameters in routes\web.php file.  

  ```php
  Route::pattern('id', '[0-9]+');
  ```

## Explicit allowing forward slashes as URL parameter:

  This will take {search} parameter for the link example.com/search/food/rise as food/rise.

  ```php
  Route::get('/search/{search}', function ($search) {
    return $search;
  })->where('search', '.*');
  ```

## Creating named routes:

  ```php
  Route::get('/user/profile', function () {
    //
  })->name('profile');
  ```

  This URL will be named as profile and Wherever Profile name used that URL again generated.

  ```php
  $url = route('profile');
  $url = route('profile', ['id' => 1]); # for URL parameter.
  return redirect()->route('profile');

  Inspecting / Checking current route:

  if ($request->route()->named('profile')) {
      //
  }
  ```

## Route Group with middleware:

  This will apply middleware for all other routes inside this group.

  ```php
  Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second middleware...
    });

    Route::get('/user/profile', function () {
        // Uses first & second middleware...
    });
  });
  ```

## Route Group with controller:

  This will target specific controller for all other routes inside this group.    

  ```php
  Route::controller(OrderController::class)->group(function () {
      Route::get('/orders/{id}', 'show');
      Route::post('/orders', 'store');
  });
  ```

## Sub-domain routing with Route Group:

  The route group can target and apply all other additional information to all routes with it for sub-domain purpose.

  ```php
  Route::domain('{account}.example.com')->group(function () {
      Route::get('user/{id}', function ($account, $id) {
          //
      });
  });
  ```

## Route Prefix:

  The route group can be used for creating prefix route for all other routes inside it.
  Here prefixes URL with admin.

  ```php
  Route::prefix('admin')->group(function () {
      Route::get('/users', function () {
          // Matches The "/admin/users" URL
      });
  });
  ```

## Route Name Prefix:

  The route group can be used for creating prefix named route for all other routes inside it.
  Here Prefixes name for named routes with admin.

  ```php
  Route::name('admin.')->group(function () {
      Route::get('/users', function () {
          // Route assigned name "admin.users"...
      })->name('users');
  });

  $url = route('admin.users');
  ```

## Custom 404 Route:

  This will handle 404 not found by this method.

  ```php
  Route::fallback(function () {
    //
  });
  ```

## Route Rate Limiting:

  This will limit traffic to specific route / URL. and this can be defined inside App\Providers\RouteServiceProvider class.

  ```php
  use Illuminate\Cache\RateLimiting\Limit;
  use Illuminate\Http\Request;
  use Illuminate\Support\Facades\RateLimiter;

  protected function configureRateLimiting()
  {
      RateLimiter::for('global', function (Request $request) {
          return Limit::perMinute(1000);
      });
  }

  RateLimiter::for('global', function (Request $request) {
      return Limit::perMinute(1000)->response(function (Request $request, array $headers) {
          return response('Custom response...', 429, $headers);
      });
  });
  ```

## Route Details:

  Get current route, name and action.

  ```php
  $route = Route::current();
  $name = Route::currentRouteName();
  $action = Route::currentRouteAction();
  ```

## Route related artisan commands:

  Get all routes available.

  `php artisan route:list`

  Get all routes available with middleware.

  `php artisan route:list -v`

  Get all routes created by developer.

  `php artisan route:list --except-vendor`

  Get all routes available by application itself.

  `php artisan route:list --only-vendor`

  Route Caching:

  `php artisan route:cache`

  Route Cache Clearing:

  `php artisan route:clear`

---

# **Middlewares:**

  Middlewares provide convenient way to filters requests and perform some actions based on conditions.
  example: Authentication middleware can check requested user is logined or not. if the user is not loggined then redirected to login page.

## Creating Middlewares:

  Middlewares can be created via following artisan command.
  EnsureTokenIsValid class created inside app/Http/Middleware.

  `php artisan make:middleware EnsureTokenIsValid`

## Registering Middlewares:

  If middleware need to be used then need to registered inside app/Http/Kernel.php in the routeMiddleware.

## Applying Middleware:

  Apply single middleware for specific request URL.

  ```php
  Route::get('/profile', function () {
    //
  })->middleware('auth');
  ```

  Apply multiple middlewares for specific request URL.

  ```php
  Route::get('/', function () {
      //
  })->middleware(['first', 'second']);
  ```

  Apply middleware without specifying the name but fully qualified class.

  ```php
  use App\Http\Middleware\EnsureTokenIsValid;

  Route::get('/profile', function () {
      //
  })->middleware(EnsureTokenIsValid::class);
  ```

## Excluding middleware from middleware route group.

  ```php
  Route::middleware([EnsureTokenIsValid::class])->group(function () {
      Route::get('/', function () {
          //
      });

      Route::get('/profile', function () {
          //
      })->withoutMiddleware([EnsureTokenIsValid::class]);
  });
  ```

## Exclude the route from global middleware group.

  ```php
  Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
      Route::get('/profile', function () {
          //
      });
  });
  ```

## Global Middlewares:

  Group of global middleware group can be registered inside App\Providers\RouteServiceProvider for corresponding web.php and or api.php route files.
  That route group can be applied as following ways.

  ```php
  Route::get('/', function () {
      //
  })->middleware('web');

  Route::middleware(['web'])->group(function () {
      //
  });
  ```

## Sorting Middleware Proceedings:

  The middlewares can be sorted / priority assigned when it is falls under group of middlewares.
  That can be implemented inside app/Http/Kernel.php file with property name called $middlewarePriority.

  Middleware can accept parameters when implementing.

  ```php
  Route::put('/post/{id}', function ($id) {
      //
  })->middleware('role:editor');
  ```

## Terminable Middleware:

  After response if need to do something with middleware then terminate method will used.

---

# **CSRF Token:**

  CSRF token needed if any form has following method POST, PUT, PATCH and DELETE request methods.

## Creating CSRF Protection:

  Blade directive to generate hidden csrf field using @csrf representation. or equivalent with
  ```html
  <input type="hidden" name="_token" value="{{ csrf_token() }}" />
  ```

## Get CSRF Token as following ways:

  ```php
  $token = $request->session()->token();
  $token = csrf_token();
  ```

## Excluding CSRF Protection:

  Adding their URLs to the $except property of the VerifyCsrfToken middleware implementation inside App\Providers\RouteServiceProvider class.

  ```php
  protected $except = [
      'stripe/*',
      'http://example.com/foo/bar',
      'http://example.com/foo/*',
  ];
  ```

## X-CSRF-TOKEN:

  The App\Http\Middleware\VerifyCsrfToken middleware will also check for X-CSRF-TOKEN in requests.
  So, this can be implemented as follows.

  ```html
  <meta name="csrf-token" content="{{ csrf_token() }}">
  ```
  ```js
  $.ajaxSetup({
      headers: {
          'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
      }
  });
  ```

  Laravel stores the current CSRF token in the form of an encrypted XSRF-TOKEN as cookie.
  
---

# **Controllers:**

  Controllers are logical block of code that can be control the flow of application request.
  Controllers can have mulitple methods and some controller may have single method. This controllers are used in the Routes file.

## Creating Controllers:

  `php artisan make:controller ProvisionServer`

## Creating Single Action Controllers:

  `php artisan make:controller ProvisionServer --invokable`

## Creating Resource Controllers:

  This will create default methods for Create, Read, Edit, Update and Delete actions.

  `php artisan make:controller CrudController --resource`

## Creating Controller with resource model:

  This will bind specific model class to the controller with CRUD resources.

  `php artisan make:controller PhotoController --model=Photo --resource`

## Creating Controller with Form Requests:

  This will generate Form Request classes for that controller with CRUD resources.

  `php artisan make:controller PhotoController --model=Photo --resource --requests`

## Single Action Controllers:

  Define and implement __invoke method inside controller class. This will not require to specify method name in Route files.

  `Route::post('/server', ProvisionServer::class);`

## Controller Middleware:

  Middlewares can be implemented inside constructor method or In the Route itself.

## Registering Many Resource Controllers:

  This will redirect to specific method by default for crud operation.

  ```php
  Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
  ]);
  ```

## Missing Model Controller:

  To avoid 404 page not found error and redirect to specific action can be done by adding missing method in Route resources.

  ```php
  Route::resource('photos', PhotoController::class)->missing(function (Request $request) {
      return Redirect::route('photos.index');
  });
  ```

## Partial Resource Routes:

  This will redirect to specific method of action that are mentioned here

  ```php
  Route::resource('photos', PhotoController::class)->only([
    'index', 'show'
  ]);
  ```

  This will redirect to specific method except following method of action that are mentioned here

  ```php
  Route::resource('photos', PhotoController::class)->except([
    'create', 'store', 'update', 'destroy'
  ]);
  ```

## API Resource Routes:

  This will consumed by API requests.

  ```php
  Route::apiResource('photos', PhotoController::class);

  Route::apiResources([
      'photos' => PhotoController::class,
      'posts' => PostController::class,
  ]);
  ```

## Nested Resources:

  The nested route resources for the requests /photos/{photo}/comments/{comment} will be declared as follows.

  ```php
  Route::resource('photos.comments', PhotoCommentController::class);
  ```

## Naming Resources Routes:

  Assigning name for specific method of action.

  ```php
  Route::resource('photos', PhotoController::class)->names([
    'create' => 'photos.build'
  ]);
  ```

## Naming Parameters in Resource Routes:

  To take up named parameter for requested URL /users/{admin_user} defined as follows.

  ```php
  Route::resource('users', AdminUserController::class)->parameters([
    'users' => 'admin_user'
  ]);
  ```

---

# **Requests:**

## Accessing Requests:

  Get request parameters as follows.

  ```php
  $name = $request->input('name');
  ```

## Retrieving requested path:

  ```php
  $uri = $request->path();
  ```

## Inspecting / Checking Request Path:

  To check the requested path is starting with admin/ or not.

  ```php
  if ($request->is('admin/*')) {
    //
  }
  ```

## Checking Named Request Routes:

  To check the route name is starting with admin or not.

  ```php
  if ($request->routeIs('admin.*')) {
      //
  }
  ```

---
