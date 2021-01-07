# Upgrade Linode server from 16.04 LTS to 18.04 LTS


<!--more-->

本篇紀錄如何更新linode主機的版本

小弟在幾年前向 [linode](https://www.linode.com/) 租了一台主機，作業系統為 Ubuntu 16.04 (Xenial Xerus)

經過這些年，Ubuntu已經更新到 Ubuntu 18.04 (Bionic Beaver)

16.04 LTS 的版本支援到 2021年4月

18.04 LTS 支援到 2023年4月

LTS 就是 **Long-term support** 代表長期支援

今年是2020年，再一年就到2021了，所以想說就來升級一下

## **Step1: 備分重要資料**

先將你重要的資料庫、檔案備份到本機端，以下提供可能會用到的指令

### **備份mysql資料庫**

連線到你的主機，dump mysql 資料庫

```
ssh root@你的.IP.位址
mysqldump -u root -p 資料庫名稱 > /var/www/mysql_backups/資料庫名稱_bak.sql
```

dump 出資料庫後，備份到本機

```
scp root@你的.IP.位址:/var/www/mysql_backups/資料庫名稱_bak.sql /Users/你的本機路逕/資料庫名稱_bak.sql
```

### **備份重要設定檔資料夾**

一些你怕更新後可能設定會跑掉的設定檔，也先拉到本機來

```
scp -r root@你的.IP.位址:/etc/你要備份的重要資料夾位置 /Users/你的本機路逕
```

## **Step2: 備份你的遠端主機**

[take a manual snapshot](https://www.linode.com/docs/platform/linode-backup-service#take-a-manual-snapshot)

這個步驟需要啟用Linode的backup，需要每個月多繳一點錢，大概是你的月租費的四分之一

但是備份的好處就是，弄壞了就restore，可以在很短的時間內回到你的上一次備份狀態

## **Step3: 安裝 update-manager-core**

```
apt install update-manager-core
```

## **Step4: 編輯 /etc/update-manager/release-upgrades**

確認此檔案中的Prompt有被設定為lts，等一下的更新就會自動去找最新的LTS版本

```
Prompt=lts
```

## **Step5: 開始更新**

```
do-release-upgrade
```

中斷點1：

```
No valid mirror found

While scanning your repository information no mirror entry for the
upgrade was found. This can happen if you run an internal mirror or
if the mirror information is out of date.

Do you want to rewrite your 'sources.list' file anyway? If you choose
'Yes' here it will update all 'xenial' to 'bionic' entries.
If you select 'No' the upgrade will cancel.

Continue [yN] y
```

輸入：y

中斷點2：

```
3 installed packages are no longer supported by Canonical. You can
still get support from the community.

3 packages are going to be removed. 113 new packages are going to be
installed. 448 packages are going to be upgraded.

You have to download a total of 1,150 M. This download will take
about 3 minutes with your connection.

Installing the upgrade can take several hours. Once the download has
finished, the process cannot be canceled.

 Continue [yN]  Details [d]y
```

輸入：y

中斷點3：**Configuring openssh-server**

直接ENTER 選擇預設的 keep the local version currently installed

中斷點N：詢問你要不要重新配置apache、mysql…如下

```
Configuration file '/etc/apache2/apache2.conf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** apache2.conf (Y/I/N/O/D/Z) [default=N] ? n

...

Configuration file '/etc/mysql/mysql.conf.d/mysqld.cnf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** mysqld.cnf (Y/I/N/O/D/Z) [default=N] ? n
```

諸如此類的問題一律選擇 N (預設)，才不會更新完後你的apache, mysql都要重新配置

最後的中斷點：

```
System upgrade is complete.

Restart required

To finish the upgrade, a restart is required.
If you select 'y' the system will be restarted.

Continue [yN]

```

輸入：y

server會自動重啟，重啟後再度ssh連線，輸入以下指令：

```
lsb_release -a

```

看到

```
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
```

成功！重啟 apache 就完成啦！

參考資料：[https://www.linode.com/docs/security/upgrading/upgrade-to-ubuntu-18-04/](https://www.linode.com/docs/security/upgrading/upgrade-to-ubuntu-18-04/)
