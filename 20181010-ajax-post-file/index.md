# Ajax Post file


<!--more-->

其實我很少用ajax的方式來POST檔案

一般真的遇到需要POST檔案的時候

多會用form來達成，如下

```php
<form action="<?= base_url().'/image.upload' ?>" method="POST">
    <input type="file" name="img" />
</form>
```

只是一次在 開發 CI 框架的網站遇到特殊情況

希望能用ajax的方式將檔案POST到後端處理

卻發現 ajax 在 POST 檔案時並不是那麼簡明

於是寫下此篇也為自己做個紀錄

首先，宣告一個全域變數 files 監聽我的 input

```javascript
var files;
$(function(){
    $("input[name='img']").on('change', function(e) {
        files = e.target.files;
    });
});
```

再來是ajax送出的部分

```javascript
function upload_image() {
    var data = new FormData();
    if ( files.length == 0 ) return false;
    data.append('img', files[0]);
    $.ajax({
        url: base_url() + "/image_upload",
        type: "POST",
        data: data,
        processData: false, // Don't process the files
        contentType: false, // Set content type to false
        dataType: "json",
        success: function(){
            ...
        }
    });
}
```

注意! 這邊一定要設定 processData 和 contentType 兩個屬性為false

否則ajax送出時就會出錯

最後php後端直接用 $_FILES['img'] 來取得檔案即可

其他詳細可參考下列文章

[https://abandon.ie/notebook/simple-file-uploads-using-jquery-ajax](https://abandon.ie/notebook/simple-file-uploads-using-jquery-ajax)
