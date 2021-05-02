# Show badges in Selectpicker



<!--more-->

這篇文章是來分享如何在 selectpicker 中顯示 bootstrap 的標籤

註: 在Bootstrap4中，badge 和 label被合併到 badge 中，本篇用的是bootstrap4

這個問題是來自於我想要做一個下拉 select 來選擇我的文章標籤要用哪些，

並且我希望在option裡面顯示出標籤，代碼如下 :

```html
<select class='selectpicker'>
    <option><span class='badge badge-pill badge-secondary'>javascript</span></option>
</select>
```

但是在selectpicker初始化後，option中的內容並沒有預期性的套上標籤的class

而是被一個 text 的 class 取代掉

螫騰了許久，終於讓我找到解決的方式

```html
<select class='selectpicker' data-showContent='true'>
    <optiondata-content="<span class='badge badge-pill badge-secondary'>javascript</span>">
    </option>
</select>
```

