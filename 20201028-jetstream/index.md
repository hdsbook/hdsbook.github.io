# Laravel Jetstream


<!--more-->

{{< admonition info "5 commands save you 5 days in 5 minutes" >}}
[Laravel Jetstream](https://jetstream.laravel.com/1.x/introduction.html) 是隨著 Laravel 8.x 一同釋出的新套件

它提供了登入、註冊、信箱驗證、雙重驗證、Session管理等功能

可以快速為新專案提供一個好的開始

本篇簡述部署安裝 Jetstream 的過程
{{< /admonition >}}


---
## 建立專案

```shell
laravel new jetstream-demo
```

laravel 8.x 支援在建立專案時就選擇載入 jetstream，指令如下

```
laravel new project-name --jet
```

若沒有在建立專案時就選擇載入 jetstream，循下文用 composer 再 require 進來即可

---
## 安裝 Jetstream

Jetstream 提供 `livewire` 與 `inertia` 兩種 frontend stacks (我不知道怎麼翻，就翻"特殊前端框架"吧)

安裝 Jetstream 時必須 2 擇 1 ，這裡選擇安裝 `livewire`

而不管選哪一個，開發時都可以選擇是否使用，或是局部使用

我目前只知道 `livewire` 好像是 for 對前端框架不那麼熟悉的後端開發者所做的工具

而 `inertia` 像是可以在 laravel 這個框架上更舒適的使用 Vue.js

詳細教學可以查看官方文件([livewire](https://laravel-livewire.com/) / [inertia](https://inertiajs.com/))

安裝 Jetstream 的指令如下：

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

就會看到首頁右上角多了 Login 與 Register 的按扭

並且註冊、登入等功能也真的可以正常運作

真的是 5 commands save you 5 days in 5 minutes (而且大部份時間都在等安裝而已) XD

---
## (optional)設定 public disk link

如果有用到 jetstream 上傳圖片的功能，可能會遇到上傳的圖片破圖的問題，以下為相關說明：

`config/filesystems.php` (檔案系統設定) 紀錄所有磁碟（disk）的設定

每個磁碟設定對應一組儲存驅動與儲存位置(預設有 local, public, s3 三個磁碟設定)

其中 public disk 理論上是要放「能被公開存取的檔案」

但 public disk 預設將檔案存到 `storage/app/public`，這個位置是無法被公開存取的 (整個網站公開對外的資料夾為 public)

故需執行以下指令，在 public 產生一個 storage 的捷徑，如此 jetstream 上傳的照片就能夠被存取到了

```shell
php artisan storage:link
```

總之就是執行上行指令就對了，對於這個指令的詳細說明可以再參考 [Laravel 之有關 storage:link 路徑設置](https://ithelp.ithome.com.tw/articles/10231319) 或 [官方說明](https://laravel.com/docs/8.x/filesystem#the-public-disk)

---
## 參考資料

- [Jetstream 官方文件](https://jetstream.laravel.com/1.x/introduction.html)
- [Laravel 8 官方文件](https://laravel.com/docs/8.x/authentication#included-routing)
- [Laravel Installer 4.0 更新小記](https://learnku.com/articles/49247)
- [什麼是 Inertia.js](https://ithelp.ithome.com.tw/articles/10233926?sc=hot)
- [什麼是 Laravel Jetstream](https://tw511.com/a/01/13382.html)
- [用 Livewire 在 Laravel 應用裡實現無痛的前後端溝通](https://jaceju.net/laravel-livewire/)
