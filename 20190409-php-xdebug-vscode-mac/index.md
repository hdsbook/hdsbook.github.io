# Xdebug + PHP + Vscode on Mac


<!--more-->

## **Step1: Install Homebrew**

官網：[https://brew.sh/index_zh-tw](https://brew.sh/index_zh-tw)

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## **Step2: Install php by homebrew**

```
$ brew install php@7.2
```

裝好後，可以下以下指令，會指示你接下來的動作

```
$ brew info php@7.2
```

```
To enable PHP in Apache add the following to httpd.conf and restart Apache:
    LoadModule php7_module /usr/local/opt/php@7.2/lib/httpd/modules/libphp7.so

    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>

Finally, check DirectoryIndex includes index.php
    DirectoryIndex index.php index.html

The php.ini and php-fpm.ini file can be found in:
    /usr/local/etc/php/7.2/

php@7.2 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have php@7.2 first in your PATH run:
  echo 'export PATH="/usr/local/opt/php@7.2/bin:$PATH"' >> ~/.bash_profile
  echo 'export PATH="/usr/local/opt/php@7.2/sbin:$PATH"' >> ~/.bash_profile

For compilers to find php@7.2 you may need to set:
  export LDFLAGS="-L/usr/local/opt/php@7.2/lib"
  export CPPFLAGS="-I/usr/local/opt/php@7.2/include"

To have launchd start php@7.2 now and restart at login:
  brew services start php@7.2
Or, if you don't want/need a background service you can just run:
  php-fpm

```

依上列提示設定就序後，重開 terminal 可下 php -v 檢查是否有成功裝好 php

若成功，則下：

```
$ sudo apachectl restart
```

重啟 apache 後，應該就可以用新的 php 運行你的本機網站了

## **Step3: Install Pear**

官網：[https://pear.php.net/manual/en/installation.getting.php](https://pear.php.net/manual/en/installation.getting.php)

```
$ curl -O http://pear.php.net/go-pear.phar
$ sudo php -d detect_unicode=0 go-pear.phar
```

接著進入輸入模式，做以下設定：

- 輸入 1
- 輸入 /usr/local/pear
- 輸入 4
- 輸入 /usr/local/bin
- 完成上列兩項設調整後，接著直接按 Enter 繼續安裝即可

完成安裝後，可下 pear version 看是否成功安裝

## **Step4: Install xdebug by pecl**

官網：[https://xdebug.org/docs/install](https://xdebug.org/docs/install)

```
$ pecl install xdebug
```

安裝的最後應該會 log 安裝的位置，請趕快記錄下來，我的位置為

`/usr/local/Cellar/php@7.2/7.2.16/pecl/20170718/xdebug.so`

這個位址要寫到 php.ini, 若忘記或不知道 php.ini 的位置，可再下 brew info php@7.2 查看

我的在 `/usr/local/etc/php/7.2/php.ini`

## **Step5: Config php.ini**

打開 php.ini 後，加上下列：

```
# zend_extension="xdebug.so" ; 可能會預設有這行，但這行並無用，要用絕對位址如下一行所示
zend_extension="/usr/local/Cellar/php@7.2/7.2.16/pecl/20170718/xdebug.so"
# xdebug.remote_port=9001 ; 可自行訂別的 port，預設為 9000
xdebug.profiler_enable = on
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
xdebug.remote_host = "localhost"
```

編輯完下 sudo apachectl restart 重啟 apache

## **Step6: Vscode**

- 安裝 extension: PHP DEBUG (app key: `felixfbecker.php-debug`)
- 打開 settings.json 並加上php executable path：

```json
"php.executablePath": "/usr/local/Cellar/php@7.2/7.2.16/bin/php"
```

最後於要使用 xdebug 的專案下切到 debug 頁籤點左上方的設定圖樣開啟 launch.json

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9000
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9000
        }
    ]
}
```

然後就可以下斷點，f5開始debug啦~

