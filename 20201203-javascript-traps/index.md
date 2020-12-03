# Javascript 的陷阱


<!--more-->

最近面試了幾間公司，才發現自己在 javascript 一些細節的基礎知識實在不夠扎實，實在汗顏

所以此篇文章應該會陸續加入自己看過或遇到過的 javascript 坑，並大至簡單的說明其原因

## 0.1 + 0.2 != 0.3

```javascript
console.log(0.1 + 0.2); // 0.30000000000000004
```

javascript 進行計算前需要先把浮點數轉化為二進位數

但當 0.1 轉為二進制時，為 0.0001 1001 1001... 的無限循環小數

而 javascript 浮點數計算最多支持到第52位小數

捨去52位後的小數然後計算，計算結果再轉為10進位小數

就得到了最後這個有誤差的結果了

## parseInt

```javascript
parseInt(3, 10); // 3
parseInt(3, 0);  // 3
parseInt(3, 8);  // 3

parseInt(3, 2);  // NAN
parseInt(3, 37); // NAN
```

### parseInt：函式說明

parseInt 的運作方式是先將第一個參數轉為字串

然後以第二個參數為基底解析第一個參數

回傳第一個參數十進位的值，如：

parseInt(101, 2) = parseInt("101", 2) = 1 * 2^2 + 0 * 2^1 + 1 * 2^0 = 4 + 0 + 1 = 5

parseInt('2F', 16) = 2 * 16^1 + 15 * 16^0 = 32 + 15 = 47

### parseInt：問題1: 傳入0為基底

如果第二個參數給的是 0，則其效果同傳入預設值 10

所以 parseInt(3, 0) 會等於 parseInt(3, 10) ，結果自然就是 3 了

但不管這個函式的預設值為何，使用這個函式建議還是加上第二個參數為 10

### parseInt：問題2: 不合法的表示法會回傳 NAN

parseInt(3, 2) 是 NAN 的原因是，在 2 進制表示法中，只會有 0 和 1 才對

如：1011010，意即，2進制的表示法中，不可能出現大於等於2的數字

所以 parseInt(3, 2) 因為出現了3，所以結果就是 NAN 了

同理 parseInt(5, 5) 也會是 NAN，

因為 5進制的表示法應該是如：402314，會出現的最大數字是4

### parseInt：問題3: 基底的上限

第二個參數基底是有上限的，就是不能大於36

所以不管第一個參數是什麼，只要第二個參數大於等於37

那回傳值一定是 NAN

## Math.min() > Math.max()

```javascript
Math.min() < Math.max() // false
Math.min() > 0  // true
Math.max() > 0  // false
```

Math.max() 回傳 -Infinity

Math.min() 回傳 Infinity

## 參考資料：

- https://juejin.cn/post/6847902225872879630
