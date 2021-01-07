# php array_merge 與 array + array 的差別


<!--more-->

如題，本篇探討 array_merge 與 array 相加的差異

### **測試**

```php
<?php
$a1 = [1, 2, 3];
$a2 = [4, 5, 6];
$result = array_merge($a1, $a2); // [1, 2, 3, 4, 5, 6]
$result = $a1 + $a2;             // [1, 2, 3]
```

```php
<?php
$a1 = [
  1,              // 0 => 1
  'a' => 7,
  2,              // 1 => 2
  'b' => 8,
];
$a2 = [
    'a' => 9,
    3,            // array_merge: 2 => 3    array相加: 0 => 3
    'b' => 10,
    'c' => 11,
    4,            // array_merge: 3 => 4    array相加: 1 => 4
    5,            // array_merge: 4 => 5    array相加: 2 => 5
];

$result = array_merge($a1, $a2);
/*
    0   => 1
    'a' => 9
    1   => 2
    'b' => 10
    2   => 3
    'c' => 11
    3   => 4
    4   => 5
*/
$result = $a1 + $a2;
/*
    0   => 1
    'a' => 7
    1   => 2
    'b' => 8
    'c' => 11
    2   => 5
*/
```

以下是依據自己測試過後的結果與參考網路資訊整理下來的 pseudocode

### **array_merge($arr1, $arr2, $arr3...)**

```php
<?php
$result = array();
$index = 0;
依序從 $arr1, $arr2, $arr3... 的每個 item ($key => $value) 做拜訪 {
    if (item 沒有指定key值 ) {
        $key = $index;
        $index++;
    }
    $result[$key] = $value;
}
```

---

### **$arr1 + $arr2 + $arr3...**

```php
<?php
$result = array();
依序從 $arr1, $arr2, $arr3... 的每個 item ($key => $value) 做拜訪 {
    if (is first item of each array) {
        $index = 0;
    }

    if (item 沒有指定key值 ) {
        $key = $index;
        $index++;
    }

    if (!isset($result[$key])) {
        $result[$key] = $value;
    }
}
```

`注意，這裡的 $index 是在每次拜訪一個新的陣列就歸零哦`

---

### **結論**

- array_merge
    - 對有指定 key 值的 item，後者的 value 會覆蓋前者 `(以後者為主)`
    - 對沒指定 key 值的 item，則會以一全新的流水號（從0開始跑的 index counter）為其 key 值加到陣列中
    - 適合用於：`絕大部份情況均適用，後者資料較重要的情況`
- array 相加
    - 對有指定 key 值的 item，前者的 value 會覆蓋後者 `(以前者為主)`
    - 對沒指定 key 值的 item，則會以一全新的流水號（從0開始跑的 index counter）為其 key 值加到陣列中，且每拜訪新的陣列，index counter 就會歸零
    - 適合用於：`key 值含有特殊意義，如 user_id，此時用此法相當於合併兩 user list 並以 user_id 做好 unique 處理, 效率較佳`

---

### **參考資料：**

[PHP Array 相加與 Array_merge](http://blog.hsatac.net/2012/11/php-array-plus-array-versus-array-merge/)
