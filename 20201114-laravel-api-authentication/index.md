# Laravel api authentication


<!--more-->

之前一直有遇到一個問題是經由 routes/api.php 的路由怎麼樣也無法取得 Auth::user() 的資料

也無法做出正確的使用者驗證，此篇針對此問題紀錄解決方案與問題原因

---
## 了解 Middleware groups

middleware 中介層，為 HTTP request 進入 Controller 之前的中間過濾/處理器

middleware groups 也就是一組中介層，套用該 middleware groups 的路由需依序進行各中介層的檢查

Laravel 的 middleware groups 預設有兩組，`web` 與 `api`

```php
<?php // app/Http/Kernel.php

protected $middlewareGroups = [
    // web middleware group
    'web' => [
        // ...
        \Illuminate\Session\Middleware\StartSession::class,
        // ...
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

    // api middleware group
    'api' => [
        'throttle:api',  // 對照下面的 $routeMiddleware 可知為執行 ThrottleRequests 並以 api 為參數
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];

protected $routeMiddleware = [
    // ...
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
];
```

套用 `web` 的 route 會依序執行：`StartSession`, `VerifyCsrfToken`, `SubstituteBindings` 等 middleware

套用 `api` 的 route 會依序執行:`ThrottleRequests`(以 'api' 為參數) 和 `SubstituteBindings` 兩個 middleware

---

並且由 `RouteServiceProvider` 可知：

`routes/api.php` 預設使用 `api middleware group`

`routes/web.php` 預設使用 `web middleware group`

```php
<?php // app/Providers/RouteServiceProvider.php

/**
 * Define your route model bindings, pattern filters, etc.
 *
 * @return void
 */
public function boot()
{
    $this->configureRateLimiting();

    $this->routes(function () {
        Route::prefix('api')
            ->middleware('api')
            ->namespace($this->namespace)
            ->group(base_path('routes/api.php'));

        Route::middleware('web')
            ->namespace($this->namespace)
            ->group(base_path('routes/web.php'));
    });
}
```

---
## 了解 Authenticate guards

Laravel 預設的 authenticate guards 也有兩組，用來決定以什麼樣的方式進行使用者驗證

取了同樣的名稱，一個是 `web`，一個是 `api`

為了必免與 middleware group 混淆，以下稱為 `web guard`、`api guard`

`web guard` 的 driver 是 session， `api guard` 的 driver 是 token

```php
<?php // config/auth.php

'guards' => [
    // web guard
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    // api guard
    'api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => false,
    ],
],
```

---
## 分析為何無法在 api routes 驗證使用者

在 `routes/api.php` 預設的內容如下

```php
<?php
Route::middleware('auth:api')->get('/user', function (Request $request) {
  return $request->user();
});
```

嘗試 fetch 這個 api，或是改成 `Route::middleware('auth:web')`，卻都返回 `{message: 'Unauthenticated'}`

有兩層原因，一個是 session 問題，一個是 token 設定問題

---

如果要通過使用者認證，一定要通過任一個 authenticate guard

`web guard` 是透過 session 認證，`api guard` 是透過 token 驗證

---
#### session 問題 => 不能使用 web guard

無法使用`web guard`，意即不能採取`Route::middleware('auth:web')`的方式

原因是 `routes/api.php` 預設使用 `api middleware group`，其中的中介層中沒有經過 `StartSession`

所以就算選擇使用 web guard，也會因為沒有 session 可以取得而認證失敗

事實上 laravel 官方也不建議讓 api 路由再藉由 session 的方式做使用者認證，應該採取較嚴謹的 token 做法

所以在 `routes/api.php` 的預設程式碼才會看到預設傳給 auth middleware 的認證護衛是 `api guard`

#### token 設定問題 => 需額外設定

但如果使用 `api guard`，就必須做好 token 相對應的設定

而 token 驗證的方式太多元，laravel 並沒有預先配置好任一種方式

而很多開發者，可能都把 api routes 寫到 web.php，走 `web middleware`、`web guard`，一樣以 session 做認證

所以官方就將這部份作為一個進階彈性的設定選項，沒有預設載入這部分的配置

---

簡言之，官方預設：

`routes/web.php` 走 `web middleware group`，有經過 `StartSession`，可以使用 `web guard` 以 session 驗證

`routes/api.php` 走 `api middleware group`，沒經過 `StartSession`，只能使用 `api guard` 以 token 驗證

但是 token 驗證的方式請自行搞定

---
## 配置 token authentication

### 核心邏輯

其實 token 驗證的核心邏輯很簡單，就是給 users 資料表加上一個 `api_token` 的欄位，

並且在 request 的時候，也要傳入該 token 的值

比方說，現在已登入的使用者，其紀錄在資料表中對應的 `api_token` 是 `a9lkjw7eoruosdvlaskjerioudoj...`

那發出的 api request 就要傳入一樣的 token

```javascript
axios.get('api/...', {
  params: {
    api_token: 'a9lkjw7eoruosdvlaskjerioudoj...'
  }
})
```

至於為什麼是要加上 `api_token` 這個欄位，是參考自官方文件的說明 (https://laravel.com/docs/5.8/api-authentication#introduction)

### 步驟 0： 確認預設設定

確認 api guard 使用 token driver

```php
<?php // config/auth.php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => false,
    ],
],
```

### 步驟 1： 新增 api_token 欄位

`php artisan make:migration add_api_token_to_users`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddApiTokenToUsers extends Migration
{
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('api_token', 60)
                ->unique()
                ->nullable()
                ->default(null)
                ->after('password');
        });
    }

    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('api_token');
        });
    }
}
```

`php artisan migrate`

### 步驟 2：於登入時產生 api token

註冊 login listener

`php artisan make:listener SuccessLogin`

```php
<?php // app/Listeners/SuccessLogin.php

namespace App\Listeners;

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Str;

class SuccessLogin
{
    public function handle($event)
    {
        $user = Auth::user();
        $user->api_token = Str::random(60);
        $user->save();
    }
}
```

於 `EventServiceProvider` 加入 listener

```php
<?php // app/Providers/EventServiceProvider.php

namespace App\Providers;

// ...
use App\Listeners\SuccessLogin;
use Illuminate\Auth\Events\Login;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        // ...
        Login::class => [
            SuccessLogin::class,
        ]
    ];
}

```

### 步驟 3：使 axios 預設帶入 token

將 api token 與 csrf token 放到 meta tag 上

```php
<meta name="csrf-token" content="{{ csrf_token() }}">
@auth
    <meta name="api-token" content="{{ Auth::user()->api_token }}">
@endauth
```

將 api token 與 csrf token 設定到 axios 的 header

```javascript
// bootstrap.js

window.axios = require('axios');

window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';

const csrfMetaTag = document.querySelector('meta[name="csrf-token"]');
csrfMetaTag
  ? window.axios.defaults.headers.common['X-CSRF-TOKEN'] = csrfMetaTag.getAttribute('content')
  : console.log('csrf token not found');

const apiMetaTag = document.querySelector('meta[name="api-token"]');
apiMetaTag
  ? window.axios.defaults.headers.common['Authorization'] = "Bearer " + apiMetaTag.getAttribute('content')
  : console.log('api token not found');
```

### 步驟 4：完成

確認 routes/api.php 預設內容

```php
<?php // routes/api.php
Route::middleware('auth:api')->get('/user', function (Request $request) {
    return $request->user();
});
```

進行測試：

```javascript
// app.js
axios.get('http://127.0.0.1:8000/api/user').then(res => {
  console.log(res);
});
```

重新登入應該就可以看到 log 出 axios 回傳的 user data 了解


---
## 結語

上述的步驟 2 和 3 的方式是參考自 [這篇文章](https://caloskao.org/laravel-api-token-usage/) 的做法

雖然這樣的做法還是將 api token 放到 meta tag 上了，但每次登入都會進行 token 的代換

已經可以算是最簡單的方式

如果要再更嚴謹的話，應該是要建立一個 controller 更新 token

再將 token 傳回給前端框架使用，或是存到 window.localStorage

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Str;
use Illuminate\Http\Request;

class ApiTokenController extends Controller
{
    /**
     * Update the authenticated user's API token.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function update(Request $request)
    {
        $token = Str::random(60);

        $request->user()->forceFill([
            'api_token' => hash('sha256', $token), // 這邊用 hash 的話，config/auth.php 的 hash 設定就要改成 true
        ])->save();

        return ['token' => $token];
    }
}
```

但就如前面所說，token驗證的方式實在有很多種方式，

只要了解其核心邏輯，開發者其實可以自行決定要怎麼做，

希望這篇文章有幫助到和我遇到一樣問題的人


---
## 參考資料

- https://learnku.com/articles/11006/detailed-explanation-of-laravels-own-api-guard-drive-token

- https://learnku.com/docs/laravel/5.8/api-authentication/3952

- https://laravel.com/docs/5.8/api-authentication#introduction

- https://caloskao.org/laravel-api-token-usage/
