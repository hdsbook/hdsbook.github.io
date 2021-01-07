# Laravel 5.6 CKeditor 部署


<!--more-->

我在Laravel 5.6 部署 CKeditor，此篇僅做最基本快速部署的筆記

### **Step1: Download CKeditor**

到 [官方網站](https://ckeditor.com/ckeditor-4/download/) 下載 Fullpage 的 CKeditor (with easy Image)

---

20181029 更新:

後來我發現easy Image是付費的功能，不付費會有時間和上傳圖片數量的限制

所以建議還是一開始就不要勾選那個easy Image

之後會有介紹透過CKFinder讓CKEditor有上傳圖片的功能

---

### **Step2: Put to asset folder**

放到 laravel project 的 public 資料夾下 ( ex: public/plugins/ckeditor )

### **Step3: codesnippet, markdown**

感覺未來我會希望我的 CKeditor 能有程式碼片段及支援 markdown 的功能

於是我到官網另外下載了兩個plugins

1. For codesnippet ( [連結](https://ckeditor.com/cke4/addon/codesnippet) )
2. For markdown ( [連結](https://ckeditor.com/cke4/addon/markdown) )

這兩個plugins下載完放到原 ckeditor 底下的 plugins 資料夾底下即可

( i.e. public/plugins/ckeditor/plugins/ )

### **Step4: Edit config file as follow**

```javascript
CKEDITOR.editorConfig = function( config ) {
    config.tabSpaces = 4;
    config.extraPlugins= 'easyimage,codesnippet,markdown';
    config.codeSnippet_theme = "monokai";
};
```

第一行是使 CKeditor 可以吃 tab spaces，並且指定為四個空格，才不會想寫一些程式碼片段時按下 tab時，cursor focus 到下一個欄位或是button

第二行是使 CKeditor 載入一些我們額外下載的套件: codesnippet, markdown

最後第三行只是要設定我的 css style of codesnippet 為 monokai

### **Step5: Link css js to layouts**

```html
<script src="{{ asset('plugins/ckeditor/ckeditor.js') }}"></script>
<script src="{{ asset('plugins/ckeditor/config.js') }}"></script>
<script src="{{ asset('plugins/ckeditor/adapters/jquery.js') }}"></script> {{-- ckeditor jquery adapter  --}}
<script src="{{ asset('plugins/ckeditor/plugins/codesnippet/lib/highlight/highlight.pack.js') }}"></script>

{{-- CKeditor codesnippet monokai css --}}
<link rel="stylesheet" href="{{ asset('plugins/ckeditor/plugins/codesnippet/lib/highlight/styles/monokai.css') }}">
```

### **Step6: 應用**

把 input 變成 ckeditor 的方法大概有以下兩種

1. 基本款: 此方法的參數只能為 id

    ```javascript
    CKEDITOR.replace('editor_id');
    // 對應的input需要設定對應的 id <input id="editor_id" />

    ```

2. CKeditor jquery adapter: 若想用jquery的方式抓取特定input，那麼上一步驟須確實引入 ckeditor jquery adapter

    ```javascript
    $('.editor_class').ckeditor();
    // <input class='editor_class' />

    ```

然後欲存取ckeditor的value，用 jquery 的 .val() 即可

至少我這樣取好像還沒有出過問題，不像另一款editor tinymce 還要用getContent()

### **Step 7: Apply codesnippet css style to front page**

這最後也是我卡了一陣子的問題

就是當我把存在DB的內容顯示到前端時

```
// laravel 顯示HTML內容要用 {!! !!}
{!! $blog->content !!}

```

我的程式碼片段並沒有monokai的黑底style，但是我的css明明也都link進來了呀

後來才找到這個解決方法

```javascript
// CKeditor css apply to front page
hljs.initHighlightingOnLoad();

// Or you can write in page ready function as follow
$(function(){
    $('pre code').each(function (i, block) {
      hljs.highlightBlock(block);
    });
});
```

參考來源 : [https://stackoverflow.com/questions/51325778/hljs-inithighlightingonload-does-not-render-the-page-directly](https://stackoverflow.com/questions/51325778/hljs-inithighlightingonload-does-not-render-the-page-directly)
