# 実行環境構築手順メモ

Centos7サーバに対して実行環境を構築する手順を以下にメモする。  
想定は、最低限のセットアップはされていて、SSH接続は可能であり、root権限を持っているサーバを対象とする。

## yumの準備

1. yu用ライブラリの追加  
```
※RPMFORGEを追加  
rpm -ivh http://apt.sw.be/redhat/el7/en/x86_64/rpmforge/RPMS/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm
※EPELを追加
rpm -ivh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
※REMIを追加
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
```  
2. update  
```
yum update
```

## OSの設定  

1. firewall を有効化  
```
systemctl enable firewalld
systemctl start firewalld
```
2. ポートを開ける  
```
firewall-cmd --add-port=80/tcp --zone=public --permanent
firewall-cmd --reload
firewall-cmd --list-ports --zone=public
```
3. ゾーンにサービス割り付け
```
firewall-cmd --get-services                     # サービス一覧表示
firewall-cmd --list-service --zone=public       # ゾーンに割りついたサービスの表示
firewall-cmd --add-service=http --zone=public   # httpを割り付け
firewall-cmd --list-service --zone=public       # もう一回表示して確認
```

## mysql

1. エンコードをutf8にする  
/etc/my.cnfの最後尾に以下の２行を追加
```
# diff /etc/my.cnf /etc/my.cnf.bkup
28,29d27
< character_set_server=utf8
< skip-character-set-client-handshake
```
1. サービススタート
```
systemctl start mysqld
```
2. ステータス確認
```
systemctl status mysqld
```
3. ログイン
  31. 初期パスワードをログから回収する  
  インストールした直後のデフォルトパスワードは mysqld.log に出力されているので回収する。
  ```
  # cat /var/log/mysqld.log | grep password
2016-02-17T06:27:14.942291Z 1 [Note] A temporary password is generated for root@localhost: *******
  ```
  32. rootでログインする
  ```
  mysql -uroot -p
  ```
  33. パスワードを変更する  
  8文字以上、英大文字小文字数字記号の4種類を含む必要がある
  ```
  mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootmysql123+'
  ```
  34. 初期DBを作成する
  ```
  mysql> CREATE DATABASE `sample_database` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
  ```


* サービス停止
```
systemctl stop mysqld
```
* サービス再起動
```
systemctl restart mysqld
```

## nginx

1. サービススタート
```
systemctl start nginx
```
2. ステータス確認
```
systemctl status nginx
```


* サービス停止
```
systemctl stop nginx
```

### nginxの設定

/etc/nginx/nginx.conf がおおもとの設定ファイルだが、その中から /etc/nginx/conf.d/以下 をインクルードしている。  
なので、/etc/nginx/conf.d/に様々な条件のファイルを作成すればよい。  
インストール直後は以下の２ファイルがある。

* /etc/nginx/conf.d/default.conf  
基本的なデフォルト設定が入っている。  
変更した後は、以下のコマンドで文法が正しいか確認する。  
```
> nginx -t -c /etc/nginx/nginx.conf
```
* /etc/nginx/conf.d/example_ssl.conf  
httpsでアクセスできるようにするための設定が入っている。  
初期状態は中身がすべてコメントアウトされている。  
証明書を入手したのちにコメントを外してからsystemctl restartをしてやればよい。  

#### 設定の例題
8080で稼働しているサービスを、httpsでつなげたい。
8089で稼働しているサービスを、通常のhttpでつなげたい。

```
[practice@test01 ~]$ cat /etc/nginx/conf.d/default.conf
server {
    listen       80;
    server_name  test.practice-test.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;


    location / {
        proxy_pass http://127.0.0.1:8089;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

httpsでアクセスすると8080サーバが応答する。  
ただし、`https://FQDN/demoapp/` のみ、ローカルにある物理ファイルを対象とする。  

```
[practice@test01 ~]$ cat /etc/nginx/conf.d/ssl.conf
# HTTPS server

server {
    listen       443 ssl;
    server_name  practice-test.com;

    ssl_certificate      /etc/nginx/ssl/practice-test.com_ServerCA_2016.crt;
    ssl_certificate_key  /etc/nginx/ssl/practice-test.com_2016.key;

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;

    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect                        off;
    }

    location /demoapp {
        alias   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```
