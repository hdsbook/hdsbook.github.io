# 簡單就能讓網站擁有按讚功能


<!--more-->
Facebook的按讚分享功能配置起來真的很簡單

設定好 og meta，放置要顯示fb like share的div，最後加上js的部分就好了

### Step1 Set og meta tag

```html
<meta property="og:url"           content="{{ url()->current() }}" />
<meta property="og:type"          content="article" />
<meta property="og:title"         content="{{ $blog->title }}" />
<meta property="og:description"   content="{{ $blog->subtitle }}" />
<meta property="og:image"         content="{{ $blog->image }}" />
```

### Step2 add fb-like div

```html
<div class="fb-like"
        data-href="{{ url()->current() }}"
        data-layout="standard"
        data-action="like"
        data-size="small"
        data-show-faces="true"
        data-share="true"></div>
```

### Step3 Javascript

```html
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = 'https://connect.facebook.net/zh_TW/sdk.js#xfbml=1&version=v3.2';
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
```

參考 : https://developers.facebook.com/docs/plugins/like-button?locale=zh_TW#


