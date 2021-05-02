# PSR-2 Coding Style


<!--more-->

php 現今已經有非常多不同的 framework

像是早期很多人用的Codeigniter，到現在最受歡迎的Laravel

---

PHP FIG (全名 PHP Framework Interoperability Group)，成立於2009年

目的在於建立各框架的統一規範

包含編碼風格、以及對於各 framework 都會遇到的問題，提供建議的解決方案

各框架遵循其規範以增加彼此的共通性，降低學習成本，提高程式的優美度和維護性

---

PSR (PHP Standard Recommendation) 由 PHP-FIG 所制定

目前 [FIG官方網站](https://www.php-fig.org/psr/) 已經約有 12 條規則發佈

其中 PSR-1 PSR-2 是針對 Coding Style 所作的規範

其規範內容可以到官方網站查看

或是參考點燈坊翻譯的中文版 ([PSR-2 Coding Style](https://oomusou.io/php/php-psr2/#PSR-2_Coding_Style))

此篇文章後面僅針對 php-cs-fixer 在 mac 的 vscode 上應用做介紹

---

往往在不同公司維護網站時，因為編碼的習慣不同

對程式的閱讀體驗很差

現在了解了 PSR 對 Coding Style 的規範

對於編碼風格終於可以有個依據

但是其實我很懶，不太想死記熟悉每一條規則

當然希望有套件(php-cs-fixer)可以幫忙自動完成這項作業

以下紀錄在 mac 環境的 vscode 上配置 php-cs-fixer 過程中可能會遇到的問題

## **Step1：下載 php-cs-fixer**

首先假設你的 mac 已經裝有 composer，在 terminal 下

```
$ composer global require --dev friendsofphp/php-cs-fixe
```

點燈坊的文章在四年前，提供的php-cs-fixer是舊版的(fabpot/php-cs-fixer)，安裝後還是會提示你改用新版的

若你已經載了舊版的，要更新成新版只要remove舊版的再require 新版的就可以，如下：

```
composer remove fabpot/php-cs-fixer
composer global require --dev friendsofphp/php-cs-fixer
```

等載好 php-cs-fixer ，可以在 terminal 下指令確認是否有作用

```
$ php-cs-fixer  --version
```

成功的話應該會有訊息告知 php-cs-fixer 的版本訊息

若出現 command not found 訊息，應該是你的環境變數還沒設定

以下是 mac 上設定環境變數的方式：

```
$ touch ~/.bash_profile;
$ open ~/.bash_profile;
```

在 .bash_profile 的環境變數中加入(:~/.composer/vendor/bin)，若你的.bash_profile為空檔案，編輯如下

```
export PATH=[你的其它PATH]:~/.composer/vendor/bin;
```

關閉編輯器後執行(若你本來就有.bash_profile，可以不用執行)

```
$ source ~/.bash_profile;
```

## **Step2：Vscode 安裝 extension：php cs fixer**

vscode 安裝好 php cs fixer 後 (extension_id：`junstyle.php-cs-fixer`，開發者：junstyle)

做以下基本設定：

```json
{
    "php-cs-fixer.onsave": true,
    "php-cs-fixer.executablePath": "${extensionPath}/php-cs-fixer.phar",
    "php-cs-fixer.rules": "@PSR2",
}
```

php-cs-fixer.onsave 為儲存時自動修正，若不想要可以不加

其預設是打大括號時自動修正("php-cs-fixer.autoFixByBracket": true)

完成以上設定，在編程時 vscode 就會自動幫你修正符合 PSR-2 的 Coding Style 啦

