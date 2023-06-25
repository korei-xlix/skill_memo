# SQL技術メモ

<h1 id="aHowto">このドキュメントについて / About this document</h1>  
このドキュメントはSQLについて記載するものです。  
**主にMySQLベースで記載します。**  
  





<h1 id="aMokuji">目次 / Table of contents</h1>  

* [MySQLのセットアップ](#aSetup)

* [MySQLの起動](#aStart)

* [MySQLの終了](#aEnd)

* [MySQLのアンインストール](#aUninstall)

* [データベース操作](#aDatabase)

* [テーブル操作](#aTable)

* [ユーザ操作](#aUser)

* [参考（Material）](#aMaterial)

  






<h1 id="aSetup">MySQLのセットアップ</h1>  
MySQLの開発ソースコードをクローン化してソースを取得、ビルドします。  
  
* 1.gitをインストールします。  

```text
Linuxの場合
$ sudo yum install git-all

ubuntuの場合
$ sudo apt-get install git-all
```text
Windowsの場合  
    [git for Windows](https://gitforwindows.org/)  
  

* 2.MySQLリポジトリのクローンを設置します。  

```text
# git clone https://github.com/mysql/mysql-server.git

カレントに設置する場合、最後に . をつけること
```
  

* 3.設置するバージョンのブランチに切り替えます。  

```text
# git branch -r
リポジトリのブランチ一覧取得

# git branch
現在のブランチ

# git checkout [切り替えるバージョン]
ブランチ切り替え

# git pull
ソースを取得する

```
  

* 4.ソースビルド+インストールします。  

```text
# cmake ..
かなり時間がかかります...

# make install

```
  
**以上でインストールは終了です。**
  

* 5.MySQLを起動します。  

```text
mysql > mysqld_safe --datadir='/var/lib/mysql' &

mysql > ps -ef

```
  

* 6.テストログインします。  
  初期はパスワードなし  

```text
$ mysql -u root

.....

MariaDB [(none)]> exit
Bye

```
  

* 7.rootのパスワードを変更します。  

```text
$ mysqladmin -u root password '[パスワード]'
$ mysql -u root -p
Enter password:

MariaDB [(none)]> exit
Bye
```
  

* 8. 一旦MySQLを終了します。  
  
  [[MySQLの終了]](#aEnd)  
  

* 9.設定ファイルのバックアップとエンコードの変更をおこないます。  
  まず、エンコードの確認をします。  

```text
$ mysql -u root -p
Enter password:

$ show variables like 'char%';


MariaDB [(none)]> status
--------------
mysql  Ver 15.1 Distrib 10.3.14-MariaDB, for CYGWIN (x86_64) using  EditLine wrapper

Connection id:          8
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server:                 MariaDB
Server version:         10.3.14-MariaDB Source distribution
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    latin1
Db     characterset:    latin1

※Server、Dbが latin1 なので変更します。
　exitで抜けてください。

```
  

* 10.設定ファイルのテンプレートをバックアップしたあと、設定ファイルを編集します。  

```text
バックアップ
$ cp -p /etc/my.cnf.d/server.cnf /etc/my.cnf.d/server.cnf.org

viで設定ファイルを編集
$ sudo vi /etc/my.cnf.d/server.cnf

以下を[mysqld]に以下を追加する。
[mysqld]
character-set-server = utf8

viを抜けます

```
  

* 11.エンコードが変更されたことを確認します。  

```text
まずサービス起動
$ mysqld_safe --datadir='/var/lib/mysql' &

$ show variables like 'char%';

MariaDB [(none)]> status

--------------
mysql  Ver 15.1 Distrib 10.3.14-MariaDB, for CYGWIN (x86_64) using  EditLine wrapper

Server characterset:    utf8
Db     characterset:    utf8

```
  

> 以上でMySQLを使う準備ができました**  
> データベースとユーザは、以降の操作で作成してください。**  
  





<h1 id="aStart">MySQLの起動</h1>  

```text
まずサービスを起動します
$ mysqld_safe --datadir='/var/lib/mysql' &

ユーザログイン
$ mysql -h localhost -u samafealdbot -p samafealdbot

```
  





<h1 id="aEnd">MySQLの終了</h1>  

```text
$ kill 1772

-bash: kill: (1772) - No such process
[1]+  終了                  mysqld_safe --datadir='/var/lib/mysql'

※killコマンドは何度か実行する必要がある

```
  





<h1 id="aUninstall">MySQLのアンインストール</h1>  
MySQLのアンインストールは以下のコマンドでおこないます。  

```text
# make uninstall

```
  





<h1 id="aDatabase">データベース操作</h1>  

## ユーザで使用するデータベース、ユーザ作成

botで使うユーザ、データベースを作成します。  

* 1.rootでログインします。  

```text
mysql > mysql -u root -p

```
  

* 2.データベースを作成します。  

```text
mysql > CREATE DATABASE [データベース名] CHARACTER SET utf8;

例：データベース samafealdbot を作成
mysql > CREATE DATABASE samafealdbot CHARACTER SET utf8;

```
  

* 3.ユーザを作成し、権限を与えます。  

```text
まずユーザを作成します
mysql > CREATE USER '[ユーザ名]'@'[ホスト名]' IDENTIFIED BY '[パスワード]';

一般的な権限を付与します
mysql > GRANT CREATE, DROP, DELETE, INSERT, SELECT, UPDATE ON * . * TO '[ユーザ名]'@'[ホスト名]';

フル権限を付与する例（セキュアではないので注意）
mysql > GRANT ALL PRIVILEGES ON * . * TO '[ユーザ名]'@'[ホスト名]';
```
  
## **例：ユーザ samafeald を作成する**  

```text
mysql > CREATE USER 'samafealdbot'@'localhost' IDENTIFIED BY '8YALkVbloDOp';

mysql > GRANT CREATE, DROP, DELETE, INSERT, SELECT, UPDATE ON * . * TO 'samafealdbot'@'localhost';

```
  
* 3.作成したデータベース、ユーザを確認します。  

```text
データベース一覧を表示
mysql >  show databases;

ユーザ一覧を表示
mysql >  select host, user from mysql.user;

テーブル一覧を表示
mysql >  show tables;

```
  


## データベース一覧を表示

```text

mysql >  show databases;

```
  





<h1 id="aTable">テーブル操作</h1>  
## テーブル一覧を表示

```text
mysql >  show tables;

```
  



<h1 id="aUser">ユーザ操作</h1>  

## ユーザ一覧を表示

```text
mysql > select host, user from mysql.user;

```
  







<h1 id="aMaterial">参考 / Material</h1>  
**※敬称略**  

* [MySQL Download](https://dev.mysql.com/doc/refman/8.0/ja/installing-development-tree.html)  
  MySQLの場所。  
  





***
[[トップへ戻る]](/readme.md)  
  
::Admin= Korei (@korei-xlix)  
::github= [https://github.com/korei-xlix/](https://github.com/korei-xlix/)  
::Web= [https://website.koreis-labo.com/](https://website.koreis-labo.com/)  
::Twitter= [https://twitter.com/korei_xlix](https://twitter.com/korei_xlix)  
