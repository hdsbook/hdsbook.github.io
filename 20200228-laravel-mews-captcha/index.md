# Laravel 驗證碼套件


<!--more-->

此篇紀錄如何在 Laravel 配置驗證碼功能

- 環境：
    - laravel 5.6
    - php 7.1
    - mews/captcha 3.0 ([https://github.com/mewebstudio/captcha](https://github.com/mewebstudio/captcha))

---
## **Step 1: Require mewebstudio/captcha**

```
$ composer require mews/captcha
$ php artisan vendor:publish
```

---
## **Step 2: Set providers and aliases**

```php
<?php
// config/app.php
return [ // ...
    'providers' => [
        // ...
        Mews\Captcha\CaptchaServiceProvider::class,
    ],
    'aliases' => [
        // ...
        'Captcha' => Mews\Captcha\Facades\Captcha::class,
    ]
]
```

---
## **Step 3: Config captcha**

可在 `config/captcha.php` 設定驗證碼的各項設定，預設會取 `default` 之設定

```php
<?php
// config/captcha.php
return [
    // ...
    'default' => [
        'length' => 5, // 驗證碼長度
        'width' => 120,
        'height' => 36,
        'quality' => 90,
        'math' => false, // 是否啟用數字驗證碼
    ],
    // ...
]
```

---
## **Step 4: 前端**

```php
<?php
{!! captcha_img() !!}
<input name="captcha" type="text" />
@if ($errors->hasAny(['captcha', 'email', 'account']))
    @foreach ($errors->all() as $error)
        {{ $error }}
    @endforeach
@endif
```

`captcha_img()` 亦可加入第一參數以選取在 Step3 中不同設定的驗證碼

如： `captcha_img('flat')`

---
## **Step 5: 後端**

```php
<?php
$validator = validator($request->all(), array(
    'captcha' => 'required|captcha',
    // ...
));

if ($validator->fails()) {
    return redirect()->back()->withErrors($validator);
}
```

---
## **Step 6: Set language**

此一步驟要在專案有配置多語系時才需要設定，主要是讓驗證不通過的文字能夠呈現正確的文字

```php
<?php
// resources/lang/zh-TW/validation.php
return [
    // ...
    'attributes' => [
        // ...
        'captcha' => '驗證碼',
    ],
    // ...
    'captcha' => '驗證碼錯誤'
]
```

完成！
