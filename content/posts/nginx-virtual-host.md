+++
date = '2025-05-11T18:52:32+09:00'
title = 'Nginxでバーチャルホストしてみる'
+++

Nginx でバーチャルホストしてみたときのメモ。

## Nginx の設定ファイル

server_name に追加するドメインをかく

```conf
server {
        listen 80;
        server_name hoge.example.com;

        root /var/www/hoge;
        index index.html index.htm;

        access_log /var/log/nginx/hoge.access.log;
        error_log /var/log/nginx/hoge.error.log;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

シンボリックリンクをおいてリロード

```bash
ln -s /etc/nginx/sites-available/hoge.example.com /etc/nginx/sites-enabled/
sudo systemctl reload nginx
```

## ほか

- DNS の設定しておく
- FW で弾いているなら許可しておく（これ忘れてめっちゃ沼った
- 割り当て先の root ディレクトリを`/home/hoge/www/`みたいにしたときに`setfacl`で www-data にも権限与えられるかと思いきやうまくいかなかった
  - usermod でグループに追加してしまったほうが良い？わからん
