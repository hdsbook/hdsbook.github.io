# Markdown editor with vscode keybinding


ace-code-editor, showdown, github-markdown-css

<!--more-->

---

此篇文章分享如何簡單建立一個好用的 markdown editor

將會用到兩個 js 套件：[ace-code-editor](https://ace.c9.io/) 和 [showdown](https://github.com/showdownjs/showdown)

markdown 呈現的 css 則套用 [github-markdown-css](https://github.com/sindresorhus/github-markdown-css)

那就開始吧

## **1. git init & npm init**

```shell
mkdir showdown-practice
cd showdown-practice
git init
npm init
```

## **2. npm install**

```shell
npm install jquery
npm install bootstrap
npm install showdown
npm install github-markdown-css
npm install ace-builds
```

## **3. index.html**

```html
<link rel="stylesheet" href="node_modules/github-markdown-css/github-markdown.css">
<link rel="stylesheet" href="node_modules/bootstrap/dist/css/bootstrap.min.css">
<style>
    body {
        height: 100vh;
    }
</style>

<div class="d-flex justify-content-around align-items-center h-100 p-5">
    <div class="flex-fill h-100 m-3 mb-5">
        <h3>Input markdown</h3>
        <div id="markdownArea" class="border rounded w-100 h-100"></div>
    </div>
    <div class="flex-fill h-100 m-3 mb-5">
        <h3>Output Html</h3>
        <div id="outputArea" class="markdown-body border rounded w-100 h-100 p-3 overflow-auto"></div>
    </div>
</div>

<script src="node_modules/showdown/dist/showdown.min.js"></script>
<script src="node_modules/jquery/dist/jquery.min.js"></script>
<script src="node_modules/ace-builds/src-min/ace.js"></script>
<script>
    $(function() {
        var editor = initEditor("markdownArea", "outputArea");
    });

    function initEditor(inputId, outputId) {
        let editor = ace.edit(inputId);
        // editor.setReadOnly(true);                         // 是否唯讀
        editor.setTheme("ace/theme/dawn");                // 主題樣式
        editor.setShowPrintMargin(false);                 // 不要顯示右方 margin
        editor.setKeyboardHandler("ace/keyboard/vscode"); // 快捷鍵類型 (keybinding)
        editor.session.setMode("ace/mode/markdown");      // 內容語言類型
        editor.session.on('change', function(delta) {
            // delta.start, delta.end, delta.lines, delta.action
            var markdownText = editor.getValue();
            var html = markdownToHtml(markdownText);
            $("#" + outputId).html(html);
        });
        return editor;
    }

    function markdownToHtml(text) {
        var converter = new showdown.Converter();
        return converter.makeHtml(text);
    }
</script>
```

以上為最簡化的程式，詳細可再到我的 [github](https://github.com/hdsbook/md-editor-by-ace-and-showdown) 上查看
