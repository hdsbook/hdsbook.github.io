# PHP pluck array


<!--more-->

時常在做資料處理的時候，會遇到如下的需求

```php
<?php
// 原陣列
$users= array(
    0 => array('user_id' => 31, 'name' => 'Tom', 'age' => 20),
    1 => array('user_id' => 38, 'name' => 'Tony', 'age' => 28),
    2 => array('user_id' => 45, 'name' => 'Jenny', 'age' => 17)
)

// 欲轉換成以 user_id 為 索引，如下
$uesrs_info = array(
    31 => array('user_id' => 31, 'name' => 'Tom', 'age' => 20),
    38 => array('user_id' => 38, 'name' => 'Tony', 'age' => 28),
    45 => array('user_id' => 45, 'name' => 'Jenny', 'age' => 17)
)
```

如上所示，欲創造一個陣列，將其改為以某一個column(user_id)為索引

以方便後續如果要調用某一個 $user 時

我只要有user_id，便可以透過這個array來取得該user的其他資訊

不用再用迴圈或是 array_keys 來查找搜尋

為了實踐上述的需求，最直觀的作法可能是

```php
<?php
$users_info = array();
foreach( $users as $key => $value) {
    $users_info[$value['user_id']] = $value;
}
```

這樣的方式實在是有點冗長

後來發現 Laravel 的 collection 有一個 pluck method

```php
<?php
$users = User()::all()->pluck('user_id')->toArray();
```

這個pluck真的是太方便了，上列程式碼即是將撈取出來的資料以user_id為索引並轉換為陣列

但並不是每次都是在laravel或是有collection套件的環境下開發

所以其實php有一個非常簡單的函式可以達到一樣的效果，那就是array_column

```php
<?php
// 原陣列
$users= array(
    0 => array('user_id' => 31, 'name' => 'Tom', 'age' => 20),
    1 => array('user_id' => 38, 'name' => 'Tony', 'age' => 28),
    2 => array('user_id' => 45, 'name' => 'Jenny', 'age' => 17)
)

$uesrs_1 = array_column($users, 'user_id');
// [ 0 => 31, 1 => 38, 2 => 45]

$users_2 = array_column($users, 'name', 'user_id');
// [ 31 => 'Tom', 38 => 'Tony', 45 => 'Jenny' ]

$users_3 = array_column($users, null, 'user_id');
/*
    array(
        31 => array('user_id' => 31, 'name' => 'Tom', 'age' => 20),
        38 => array('user_id' => 38, 'name' => 'Tony', 'age' => 28),
        45 => array('user_id' => 45, 'name' => 'Jenny', 'age' => 17)
    )
*/
```

以上，更多php array應用之道可參見以下文章

[https://laravel-china.org/articles/14159/the-way-to-use-the-php-array](https://laravel-china.org/articles/14159/the-way-to-use-the-php-array)

