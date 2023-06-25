# postgreSQL技術メモ

**★このドキュメントの改造、流用、配布、クローンは禁止です★**  
    **Modification, diversion, distribution, and cloning of this document are prohibited**  
  

<h1 id="aHowto">このドキュメントについて / About this document</h1>  
このドキュメントはpostgreSQLについて記載するものです。  
**postgreSQLは使わなくなったため、この資料の更新はしておらず、内容が古い可能性があります**  
  





<h1 id="aMokuji">目次 / Table of contents</h1>  

* [よく使うコマンド](#aFrequently)

* [データベース操作](#aDatabase)

* [ユーザ操作](#aUser)

* [テーブル操作](#aTable)

* [テクニック操作](#aTecknik)

* [参考（Material）](#aMaterial)
  






<h1 id="aFrequently">よく使うコマンド</h1>  

## dbファイルの場所（どっちか）

```text
# cd /usr/local/pgsql/ 
# /var/lib/pgsql/
```
  

## ログイン

```text
# psql

```
  

## postgre終わり

```text
> \q

```
  


## db一覧と使用量の確認

```text
> SELECT datname, pg_size_pretty(pg_database_size(datname)) FROM pg_database;

postgresの指定接続
# psql -h [hostname] -U [username] -d [databasename]

```
  





<h1 id="aDatabase">データベース操作</h1>  

## 外からDB一覧 (*SP)

```text
$ psql -l
※Windowsの場合、ログインユーザとDBユーザ名が同じ、かつスーパーバイザでないと使えない

```
  

## スーパーバイザでDBにログイン(PostgreSQLの場合)

```text
$ psql -U postgres postgres
※スーパーバイザのパスワードが必要になる

```
  

## DBにログイン

```text
$ psql -U [ユーザ名] [DB名]
※ログインユーザのパスワードが必要になる

```
  

## DB一覧

```text
=> \l

```
  

## DBの作成 (*SP)

```text
=> create database [DB名];

```
  

## DBの削除 (*SP)

```text
=> drop database [DB名];

```
  

## DBの移行

```text
エクスポート
$ pg_dump -h localhost -p 5432 -U [ユーザ名] -d [DB名] > D:\Test1\db1.txt

```
  






<h1 id="aUser">ユーザ操作</h1>  

## ユーザ一覧

```text
=> select usename from pg_user;

```
  

## ROLEの作成

```text
=> create role [ユーザ名] with password '[パスワード]';

```
  

## パスワードの変更

```text
=> alter role [ユーザ名] with password '[パスワード]';

```
  

## LOGIN権限の付与

```text
=> alter role [ユーザ名] with login;

```
  

## ROLEの削除

```text
=> drop user [ユーザ名]

```
  

## 権限の付与

```text
=> grant select on [DB名] to [ユーザ名];

```
  





<h1 id="aTable">テーブル操作</h1>  

## 一覧

```text
=> \dt

alter table [テーブル名] add [カラム名] [型情報];

delete from [テーブル名];
全レコード削除

drop table [テーブル名];

```
  





<h1 id="aTecknik">テクニック操作</h1>  

## python3からpostgresへの接続


* 1.最新のpsycopg2をインストールする

```text
# pip install -U pip
# pip install psycopg2
# pip install psycopg2-binary

```
  

* 2.接続コードサンプル

```text
import psycopq2
connection = psycopg2.connect(host="localhost", database="[DB名]", user="[ユーザー名]",password="[パスワード]")

```
  


## pg_dump時にパスワードなしにする

* 1.ユーザのルートフォルダに移動する。

* 2.pgpassファイルを作成する。

```text
# vi .pgpass
[ホスト名]:[ポート番号]:[DB名]:[ユーザ名]:[パスワード(生)]

```
  

* 3.権限をのみにする。

```text
# chmod 0600 .pgpass
※root以外ならsudoをつける
  $ sudo chmod 0600 .pgpass

```
  


## 手動バキューム

PostgreSQLでデータの更新(update)、削除(delete)を行うと変更前のデータは物理的には消されずに残り続ける。  
deleteはデータに削除マークを付けただけで、データは消去されない。そのためデータの更新を行い続けると  
データベースファイルのサイズがどんどん大きくなっていく。  
PostgreSQLではVACUUMという機能を持っており、これを実行することで消されずに残り続けている不要領域を回収できる。  
デフォルトではVACUUMは自動実行される設定になっているため、いつ実行されるのかはPostgres任せになっている。  
  
これを手動で実行するための手順は以下の通り。  
なお、他の手順を利用すればテーブル単位でも実行可能だが、ここではデータベース単位で実行するものとする。  
  

* 1.DBを使用しているサービス（mastodonと、ねんのためnginx）を止める。

```text
# systemctl stop mastodon*
# systemctl stop nginx*

```
  

* 2.postgresのスーパーバイザでログインする。

```text
# su - [スーパーバイザユーザ名]

```
  

* 3.DBに接続する。

```text
$ psql -U [スーパーバイザユーザ名] -d [DB名]
パスワードはスーパーバイザユーザのものを入力。

各DBのサイズチェック。
=> SELECT datname, pg_size_pretty(pg_database_size(datname)) FROM pg_database;

```
  

* 4.バキュームの実行。

```text
=> vacuum full;

=> SELECT datname, pg_size_pretty(pg_database_size(datname)) FROM pg_database;
要領が小さくなっているはず。

```
  
なおスーパーバイザでログインしない場合、vacuumできないテーブルはエラーになるが、
それ以外の共通領域はvacuumされるので少しは要領が小さくなっているはず。  
  

* 5.サーバを再起動する。

```text
=> \q
$ exit
# reboot

ブートが完了したらサービスの状態を確認すること。
# systemctl status mastodon*
# systemctl status nginx*
# systemctl status postgres*

```
  






<h1 id="aMaterial">参考 / Material</h1>  
**※敬称略**  

* [python3でPostgreSQLに接続する](https://qiita.com/wassyoi06/items/cf89aa29a14f82672648)  
  

* [psycopg2 でよくやる操作まとめ](https://qiita.com/hoto17296/items/0ca1569d6fa54c7c4732)  
  

* [pg_dumpの実行時にパスワード入力を省略する](http://totutotu.hatenablog.com/entry/2016/05/13/161405)  
  

* [PostgreSQLの基本的なコマンド](https://qiita.com/H-A-L/items/fe8cb0e0ee0041ff3ceb)  
  





***
[[トップへ戻る]](/readme.md)  
  
::Admin= Korei (@korei-xlix)  
::github= [https://github.com/korei-xlix/](https://github.com/korei-xlix/)  
::Web= [https://website.koreis-labo.com/](https://website.koreis-labo.com/)  
::Twitter= [https://twitter.com/korei_xlix](https://twitter.com/korei_xlix)  
