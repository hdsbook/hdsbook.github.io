# Laravel CKEditor + CKFinder


<!--more-->

繼上一篇 [CKeditor部署](https://hdsbook.github.io/20180918-laravel56-ckeditor/)

我花了好一些時間研究CKeditor要如何上傳圖片到我的server

此篇是來分享 laravel 5.6 部署 CKEditor + CKFinder 的方式

網路上有很多教學直接從官網下載 [原生 php版本] 的ckfinder下來配置

但是其實配置的過程中會遇到滿多問題的

其實已經有熱心的Contributor發布了laravel專用的ckfinder套件

改寫了原本ckfinder的結構，讓ckfinder更容易在laravel上配置

事實上官方網站的說明文件也有提供這個套件的連結

網址: [https://github.com/ckfinder/ckfinder-laravel-package](https://github.com/ckfinder/ckfinder-laravel-package)

### **Step1 Require ckfinder with composer**

打開終端機，來到專案資料夾底下，下以下指令

```
composer require ckfinder/ckfinder-laravel-package
php artisan ckfinder:download
```

這兩個指令會把ckfinder的套件載到vendor資料夾下

### Step2 Publish ckfinder assets

```
php artisan vendor:publish --tag=ckfinder
```

這個指令會將 ckfinder 的 plugin assets 發布到 public/js/ckfinder 底下，

config.php 則會發佈為 config/ckfinder.php

### Step3 Make directory userfiles

```
mkdir -m 777 public/userfiles
```

建立userfiles資料夾並且賦予777權限

這個資料夾為ckfinder預設上傳檔案的位置

### Step4 Set authentication

```php
<?php
// config/ckfinder.php

$config['authentication'] = function () {
    return true;
    // return Auth::check();
};

$config['csrfProtection'] = false;
```

編輯config/ckfinder.php，暫時將認證的部分改成直接回傳true

但若你的專案已經有會員認證功能，希望只允許登入會員上傳檔案

這邊直接寫 return Auth::check(); 就可以摟 ~

並將 csrfProtection 設為 false

### Step5 Set Route (browse, request)

```php
<?php
// routes\web.php

Route::any('/ckfinder/examples/{example?}', 'CKFinderController@examplesaction')
   ->name('ckfinder_examples');

Route::any('/ckfinder/connector', 'CKFinderController@requestAction')
    ->name('ckfinder_connector');

Route::any('/ckfinder/browser', 'CKFinderController@browserAction')
    ->name('ckfinder_browser');

```

以上路由都是從GitHub範例copy過來稍微改寫的，

配合接下來的 Step6可以把一些範例demo放到自己的網站上

第一個路由是用來瀏覽範例的路由，第二個主要是上傳檔案用，第三個則是打開ckfinder瀏覽器的路由

### Step6 Controller, View

從 [https://github.com/ckfinder/ckfinder-laravel-package](https://github.com/ckfinder/ckfinder-laravel-package) 下載上面的範例，裡面有Controller 和 View

可以將Controller和View如下放到你的專案中

Controller  =>  app\Http\Controllers\CKFinderController.php

resources/views

└- ckfinder

└- samples/...

└- browser.php

└- setup.php

若如上配置，記得要改掉 CKFinderController 的 namespace

```php
<?php
namespace App\Http\Controllers;
```

### Step7 Disable csrf verify on connector route

```php
<?php
// app\Http\Middleware\VerifyCsrfToken.php

protected $except = [
    'ckfinder/*'
];

```

這個設定是為了讓檔案上傳的POST可以不用經過csrf的認證

做到這邊，你應該可以透過 [your site url]/ckfinder/examples/ckeditor 來看到許多範例已經配置到你的網站

如本站的 [http://hdsbook.com/ckfinder/examples/ckeditor](http://hdsbook.com/ckfinder/examples/ckeditor)

並且範例上的ckeditor上傳圖片的功能會正常運作

(上列本站的ckfinder範例網址的上傳功能有加入會員權限認證所以可能會無法正常瀏覽或上傳檔案)

剩下的就是你可以到 views/ckfinder/samples 底下去參考各範例的js是怎麼寫的

這邊也簡單提供把CKeditor與CKfinder結合的寫法

```javascript
// <textarea id='editor'></textarea>

var editor = CKEDITOR.replace('editor');
CKFinder.setupCKEditor(editor);

```

別忘了，在你的master layout中要引入 ckfinder.js 喔

```html
<script src="{{ asset('js/ckfinder/ckfinder.js') }}"></script>
```

