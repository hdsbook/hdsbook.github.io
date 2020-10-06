# ES6 語法學習筆記


ace-code-editor, showdown, github-markdown-css

<!--more-->

---

最近透過兩門線上課程學習現代前端的知識，在此紀錄一下對於 es6 語法的觀念及細節



## let 與 const

------

- var 的作用範圍是 scope(作用域)

  - function scope
  - global scope (不是在函式中，就是 global scope)

- let, const 的作用範圍是 block (用大括號來介定它的範圍)

  - 大括孤，中間有程式碼，稱為 block

- const 是不能被更改(reassign) 的變數

  - 常用來宣告**元素實體**。**物件、陣列、函式**也常以 const 宣告
  - 只有在 object、array 的情況下可以塞東西，但不可以改變型別，所以在嚴謹的情況下會使用 const 來宣告物件或陣列
  - const 可視為不能 reassign 的 let

- let 與 const 可以完全取代 var

- 範例

  ```javascript
  var num = 1;
  if (true) {
    // 用 var 宣告，變數會跑到global
    var name = "tony";

    // let 是用大括號來介定它的範圍
    // 暫時性的變數用 let 宣告 (local 變數)
    let num = 2;

    // const 是不能被更改的變數，且作用範圍是local，常用在宣告 document.getElementById()
    const selector = "a";
    // selector = "b";
    console.log(selector);
  }
  console.log(name);
  console.log(num); // 1

  // const 只有在 object 和 array 的情況下可以塞東西，但不可以改變型別
  // 所以在嚴謹的情況下會使用 const 來宣告物件或陣列
  const a = {};
  a.name = "Mike";
  a.name = "Judy";
  // a = [];   // 不被允許
  console.log(a);

  const b = [];
  b.push(1);
  // b = 2;   // 不被允許
  console.log(b);
  ```

## 變數解構

------

```javascript
const user = {
  name: "Mike",
  age: 12,
  address: "Taiwan",
};
const { name, age, address } = user;
console.log(name, age, address);

// 直接將物件放到物件中，物件會在物件中自動進行解構(以變數名稱為其key值)
const userData = {
  user, // 會自動解構成 => user: {name: "Mike", ... }
};
console.log(userData);
```

## 箭頭函式運算式

------

```javascript
const Add = function (a, b) {
    return a + b;
}

// 省略 function 關鍵字，加上鍵頭，變成：
const Add = (a, b) => {
    return a + b;
}

// 當回傳只有一行時，可進一步簡化成：
const Add = (a, b) => a + b;

// 當函數參數只有一個的時候，可以省略小括孤
const Double = a => a * 2;
```

箭頭函式中沒有this，this會往上一層找

若要取得動元素實體本身，則要透過 e.target 取得

```javascript
document.getElementById("aLink1").addEventListener("click", function () {
  console.log(this); // 指向 #aLink1
});
document.getElementById("aLink2").addEventListener("click", (e) => {
  // 箭頭函式中沒有this，this會往上一層找
  console.log(this); // 指向 window

  // 若要取得動元素實體本身，則要透過 e.target 取得
  console.log(e.target); // 指向 #aLink2
});
```

箭頭函數的參數可以指定預設值

```javascript
const ArrayToStr = (arr = [1, 1, 1, 1]) => {
  return arr.map((item) => item + "");
}
```

## 函數的 this 指向誰，取決於如何執行函式

```javascript
function jump() {
    console.log(this);
}

// 1. 直接執行      (this 為 gloabal 或 window)
jump(); // this 指向 window

// 2. 放到物件中執行 (this 為 該物件)
var a = {};
a.jump = jump;
a.jump(); // this 指向 a

// 3. 綁定 dom 事件 (this 為 發出事件的DOM)
btn.addEventListener('click', jump); // this 指向 #btn dom 物件

// 當用箭頭函式宣告，則不管怎麼執行，this都是上層的this (箭頭函數沒有自己的 this)
const jump = () => {
    console.log(this)
}
```

## 字串模版

------

```javascript
// 透過字串模版，可以更方面的進行字串與變數的串接、換行
const add = (name, age) => {
  return `
    Hi
    我叫${name}，
    我今年${age}歲
  `;
};
console.log(add("Mike", 12));
```

## ES module

------

./js/Tools.js

```javascript
const Add = (a, b) => {
    return a + b;
};

// 可以 export 很多東西出去，但是default只能有一個
export default Add;

// 要用 export 丟出去的只能用 const 變數丟出去 (named export)
export const Name = "mike";
export const age = 12;
export const Minus = (a, b) => {
    return a - b;
}
```

index.html

```html
<!-- 用 type="module" 告訴瀏覽器這邊是屬於 es6 module 的用法，讓它認得 import 指令 -->
<script type="module">
  // 除了 default 的東西，其它要用解構的方式 import 進來
  import Add, { Name as userName, age, Minus } from "./js/Tools.js";

  console.log(Add(1, 2));
  console.log(userName);
  console.log(age);
  console.log(Minus(5, 3));
</script>
```

## Class

------

```javascript
class Dog extends Animal {
    constructor() {
        this.age = 0;
    }
    bark() {
        console.log('woof');
    }
}

const dog = new Dog();
class Dog extends Animal {
    this.age = 0;
    bark() => console.log('woof');
}

const dog = new Dog();
```



參考資料：

[2020 Vue3 專業職人 | 入門篇 (預購中) - HiSKIO 跨領域學程式 | 專業程式線上課程平台 | 嗨 程式技能](https://hiskio.com/courses/286/about)

[React 16 完全精通 - 從原理到大量實戰應用 - HiSKIO 跨領域學程式 | 專業程式線上課程平台 | 嗨 程式技能](https://hiskio.com/courses/215/about)
