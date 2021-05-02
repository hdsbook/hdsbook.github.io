# Docker Laradock 快速部署教學


<!--more-->

此篇筆記紀錄如何在 mac 上部署 laradock

這篇的部署架構將會長得如下

```
- project
    - app
    - ...
    - laradock
```

也就是 laradock 會在 laravel project 的子資料夾中

這樣的部署有一個好處是，將laradock也導入到版本控制中，這樣要部署到其它機器上時

直接在拉下來已經設定好的 docker image 啟動 container 就可以運行網站

---

### **1. 安裝 docker**

打開終端機，執行以下指令 (需事先有裝 homebrew)

```
brew cask install docker

```

安裝後打開 Docker app，按下一步，過程可能會要求提供存取權限，允許之 結束安裝後下 docker ps，確認是否安裝成功

---

### **2. create project**

先直接建立一個 laravel project，並初始化 git

```
composer create-project laravel/laravel --prefer-dist project
cd project
git init

```

---

### **3. add laradock as submodule**

這裡有兩個做法，如果你希望 laradock 是一個能持續接收官方更新的 submodule，則執行以下指令

```
git submodule add https://github.com/Laradock/laradock.git

```

如果你希望laradock加入到這個專案的版本控制，你可以在其它地方先 clone laradock 下來，再手動複製到專案資料夾中

```
cd ..
git clone https://github.com/Laradock/laradock.git
mv laradock project/laradock

```

---

### **4. 修改 laradock/.env**

```
cd laradock/
cp env-example .env
vi .env

```

修改以下資訊

```
APP_CODE_PATH_HOST=../
NGINX_HOST_HTTP_PORT=8000
NGINX_HOST_HTTPS_PORT=4430
MYSQL_PORT=3307

```

---

### **5. 修改nginx設定**

```
cp -r nginx/sites/laravel.conf.example nginx/sites/laravel.test.conf
vim nginx/sites/laravel.test.conf

```

將 `root /var/www/laravel/public;` 改為 `root /var/www/public;` (消去中間的 laravel)

---

### **6. 建立 container**

執行這一步時，要確保 Docker app 為開啟狀態，否則可能會出現錯誤訊息

```
docker-compose up -d nginx mysql phpmyadmin

```

第一次建立可能會花比較多時間

建立起來後，如果要停止，可以下 `docker-compose down`

如果成功建立，訪問 `http://localhost:8000`，應該就可以看到 laravel 的 welcome page 了

要訪問 phpmyadmin 的話，訪問 `http://localhost:8081`

登入之 `伺服器/使用者名稱/密碼` ，請輸入 `mysql/default/secret`

---

### **7. 設定資料庫連線資訊**

修改 laravel project 的 .env

```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_DATABASE=default
DB_USERNAME=default
DB_PASSWORD=secret

```

因為此時是要連線到 docker container 的 mysql，所以 db host 填localhost是沒用的

填對應的socket即可(mysql資料庫就填mysql就好)

---

### **8. push to github**

```
git remote add origin [git repository]
git add *
git commit -m "first commit"
git push -u origin master

```

完成！

---

### **參考**

[http://jsnwork.kiiuo.com/archives/3151/docker-laradock-快速安裝筆記/](http://jsnwork.kiiuo.com/archives/3151/docker-laradock-%E5%BF%AB%E9%80%9F%E5%AE%89%E8%A3%9D%E7%AD%86%E8%A8%98/)

[https://blog.wu-boy.com/2019/12/setup-laravel-environment-in-15-minutes/](https://blog.wu-boy.com/2019/12/setup-laravel-environment-in-15-minutes/)

[https://stackoverflow.com/questions/44084846/cannot-connect-to-the-docker-daemon-on-macos](https://stackoverflow.com/questions/44084846/cannot-connect-to-the-docker-daemon-on-macos)

[https://github.com/laradock/laradock/issues/869](https://github.com/laradock/laradock/issues/869)

