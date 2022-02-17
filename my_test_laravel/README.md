## M1環境下でDocker+Laravel+MySQL+phpmyadminを起動させる
M1環境でDocker+MySQLを動かすためには少々工夫が必要だったので備忘録。
アプリ名はmy_test_laravelにしているのでプロジェクトごとに変更してください。

## Dockerを起動させる
.docker_my_test_laravelをローカルにクローンする。
```
cd my_test_laravel
```
Dockerを起動させる
```
docker-compose -f .my_test_laravel/docker-compose.yml up -d
```

## laravelプロジェクトを作成する
コンテナに入る
```
docker-compose -f .my_test_laravel/docker-compose.yml exec php /bin/bash
```
composerでlaravelをインストールする
```
composer create-project laravel/laravel=8.1.0 my_test_laravel
```
インストール終了後、http://127.0.0.1:8085 と http://127.0.0.1:8086 が正しく表示されるか確認する。

### phpmyadminにログインできないとき
```
エラーメッセージ
#1130 - Host 'XX.XX.X.XXX' is not allowed to connect to this MySQL server
```
（参考サイトhttps://codeaid.jp/blog/docker-mysql-php/）

dbコンテナに入る
```
docker-compose -f .my_test_laravel/docker-compose.yml exec db /bin/bash
```
mysqlに入る
```
mysql -u root -p
パスワードを求められるのでpasswordと入力
```
ユーザーを作る
```
GRANT ALL ON user_db.* to 'root'@'%' IDENTIFIED BY 'password'；
FLUSH PRIVILEGES;
```
自分の場合はこれで解決しました。

## SQLとの接続を確認する
phpmyadminにて下記SQLを実行
```
CREATE DATABASE my_test_laravel DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci
```

## ユーザーテーブルを作成
.envを編集します
.env.exampleを参考にして編集します
phpコンテナ内でマイグレーションを実行する
```
php artisan migrate
```
エラーが出なければ成功です。

## M1環境下でのDocker+MySQLはバージョンによって対応が異なるらしい
調べた中では
```
platform: linux/x86_64
```
をdocker-compose.ymlに追記するだけで起動している人が多かった。しかし自分の場合phpmyadminとMySQLとの接続でエラーが起きました。知識が浅く、かなり苦戦。
同じエラーで困っている人の参考になれば幸いです。
