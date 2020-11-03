# Laravel Model 命名慣例




<!--more-->

命名常常是令人最頭痛的環節，一個專案中沒有統一的命名規則，可能會造成開發上的疑惑，

此篇參考官方文件，簡單整理了在 Laravel 中幾個命名慣例的規則

---
## 命名的方式
- `camelCase` 小駝鋒式命名
- `snake_case` 蛇行命名
- `PascalCase` (pascal 是一門程式語言) 或稱 `大駝鋒式命名`

---
## 約定優於配置(Convention over configuration)

{{< admonition info "定義">}}
約定優於配置(CoC)，或稱「慣例優於設定」

是一種軟體設計範式，旨在減少軟體開發人員需做決定的數量，獲得簡單的好處，而又不失靈活性

開發者只需設定偏離約定的項目
{{< /admonition >}}

例如，當 Ｍodel 名為 `Sale` 時，則對應的資料表默認(約定)為 `sales`

也就是 `Model 的 $table 屬性`預設為 `sales`

只有在偏離這一約定時，例如將資料表命名為 "products_sold"，才需覆寫 Model 的 $table 屬性 為 "products_sold"

```php
<?php

// Sale Model
class Sale extends Model
{
    // 預設值為 sales，覆寫為 products_sold
    protected $table="products_sold"
}
```

而這樣只需對偏離約定的項目進行設定的概念就是所謂的 `約定優於配置`

---
## Laravel Mdel / table / Seeder 命名慣例

下表第一列是命名對象，第二列紅字列代表命名規則，再往下則是範例

 |        Model         |        table         |                Seeder、Factory                 |
 | :------------------: | :------------------: | :--------------------------------------------: |
 | `PascalCase`、`單數` | `snake_case`、`複數` | `PascalCase`、`單數`、`'Seeder/Factory'為結尾` |
 |         Post         |        posts         |            PostSeeder / PostFactory            |
 |        Mybook        |       my_books       |          MybookSeeder / MybookFactory          |

Laravel 底層取得默認 $table 值的邏輯可在 `Illuminate\Database\Eloquent\Model` 的 `getTable()` 找到

若沒有設定 $table 屬性，它會利用 `Illuminate\Support\Pluralizer` 來取得複數的名詞

這個 `Pluralizer` 可能比我們預想的都要來的強大，包含以 y 結尾的單字、常見的不可數名詞都能順利轉換

如：Person => people, Category => categories, Series => series, Information => information...

當然如果為了保守起見，還是可以設定一下 $table 屬性

```php
<?php

// Category Model
class Category extends Model
{
    // 設定 Model 的 $table 屬性
    protected $table="categories"
}
```

---
## Pivot table 的命名

設兩資料表 `products` 與 `features` 是透過一個 pivot (中介資料表) 來進行多對多的關聯

則該 pivot 的默認名稱為 `feature_product`，命名的規則是：`依字母排序的單數名詞、snake_case`

若希望命名 pivot 為 product_feature_maps，則需在兩個對應的 Model 中設定如下 (加入第二個參數，pivot name)

```php
<?php

// in Product model
public function features()
{
    return $this->belongsToMany('App\Feature', 'product_feature_maps');
}

// in Feature model
public function products()
{
    return $this->belongsToMany('App\Product', 'product_feature_maps');
}
```

個人覺得 `product_feature_maps` 這樣的命名可以讓語意更加明顯

不過官方預設命名為 `feature_product` 也不是沒有道理

因為當大部份資料表都依慣例被取名為複數的時候，單數命名的資料表應該就是 pivot table 了

---
## Primary/Foreign Key

|             |          命名方式          |   範例    |
| :---------: | :------------------------: | :-------: |
| Primary key |      一律命名為 `id`       |   `id`    |
| Foreign key | 來源資料表單數名詞 + `_id` | `post_id` |

---
## 結論：

保守為避免出錯可以都設定一下 Model 的 $table 屬性、belongsToMany 的第二參數

當然命名規則最好能盡量符合官方文件的慣例，至少要統一一個規範

---
## 參考資料：
- [維基百科-約定優於配置(CoC)](https://zh.wikipedia.org/wiki/%E7%BA%A6%E5%AE%9A%E4%BC%98%E4%BA%8E%E9%85%8D%E7%BD%AE)
- [Laravel 项目开发规范](https://learnku.com/docs/laravel-specification/7.x/data-model/7600#a3d104)
- [Laravel 的十八個最佳實踐](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/104733/#outline__13)
- [為你自己學 Laravel - Day 12 Model 與資料表](https://www.youtube.com/watch?v=zrsbPqcDFZo&list=PLBd8JGCAcUAFtnWuuqd0tzMwYsVAN4es_&index=12)
- [Laravel 官方文件 - eloquent-model-conventions](https://laravel.com/docs/8.x/eloquent#table-names)
- [stackoverflow-Pivot table 的命名](https://stackoverflow.com/questions/34897444/naming-tables-in-many-to-many-relationships-laravel)
