# Laravel Jetstream


<!--more-->

{{< admonition info "Info" >}}
[Laravel Jetstream](https://jetstream.laravel.com/1.x/introduction.html) 是隨著 Laravel 8.x 一同釋出的新套件

它提供了登入、註冊、信箱驗證、雙重驗證、Session管理等功能

可以快速為新專案提供一個好的開始

本篇簡述部署安裝 Jetstream 的過程
{{< /admonition >}}


---
## 建立專案

```shell
laravel new jetstream-demo
npm install
```

記得養成建立版控的好習慣

```shell
git init
git add .
git commit -m "first commit"
```

---
## 安裝 Jetstream

[Jetstream](https://jetstream.laravel.com/1.x/introduction.html) 提供 `livewire` 與 `inertia` 兩種 frontend stacks (我不知道怎麼翻，就翻"特殊前端框架"吧)

安裝 Jetstream 時必須 2 擇 1 ，這裡選擇安裝 `livewire`

而不管選擇哪一個，事實上在開發的時候也都可以選擇是否使用或是局部使用

且我之後應該還是會用 VueJS 或 React 來配合開發

所以對這兩個工具目前暫時沒有深入研究，只知道這兩個工具是 for 對前端框架不那麼熟悉的後端開發者所做的工具

詳細教學可以查看官方文件([livewire](https://laravel-livewire.com/) / [inertia](https://inertiajs.com/)) 或是 [這篇文章](https://jaceju.net/laravel-livewire/)

安裝方式參考自 [Laravel 8 官方文件](https://laravel.com/docs/8.x/authentication#included-routing)，指令如下：

```shell
composer require laravel/jetstream
php artisan jetstream:install livewire
```

因為 jetstream 它包含了上述框架與 [tailwindcss](https://tailwindcss.com/)(比 [Bootstrap](https://getbootstrap.com/) 更有彈性的css架構)，所以安裝好它會提示你要執行以下指令

來將上述框架的 js, css 一起打包到 `public/css/app.css` 與 `public/css/app.js`

```shell
npm install && npm run dev
```

---
## 設定 public disk link

laravel 的 public disk 預設使用 local driver(設定在 `config/filesystems.php`，詳見[官方說明](https://laravel.com/docs/8.x/filesystem#the-public-disk))

會把檔案存到 `storage/app/public`，但是這個位置對於訪客來說是存取不到的(整個網站公開對外的資料夾為 public)

故執行以下指令，會在網站根目錄 public 產生一個 storage 的捷徑，以確保 jetstream 上傳照片的功能夠正常運作

```shell
php artisan storage:link
```

這個指令的詳細說明可以再參考此文章：[Laravel 之有關 storage:link 路徑設置](https://ithelp.ithome.com.tw/articles/10231319)


---
## 設定資料庫

在本機端建一個名為 jetstream 的資料庫，並設定 `.env`

```shell
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=jetstream
DB_USERNAME=root
DB_PASSWORD=
```

---
## 執行 migration

```shell
php artisan migrate
```

到目前為止就算部署完成了，相當簡單，可以執行 `php artisan serve`

就會看到首頁右上角有多了 Login 與 Register 的按扭

並且也真的可以正常運作

---
## 參考資料

- [Jetstream 官方文件](https://jetstream.laravel.com/1.x/introduction.html)
- [Laravel 8 官方文件](https://laravel.com/docs/8.x/authentication#included-routing)
- [Laravel Installer 4.0 更新小記](https://learnku.com/articles/49247)
- [什麼是 Inertia.js](https://ithelp.ithome.com.tw/articles/10233926?sc=hot)
- [什麼是 Laravel Jetstream](https://tw511.com/a/01/13382.html)
