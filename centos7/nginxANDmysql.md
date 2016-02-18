# 実行環境構築手順メモ

Centos7サーバに対して実行環境を構築する手順を以下にメモする。

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
  mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootOptim123+'
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

## jenkins

1. yumの準備  
jenkinsのリポジトリを登録します。
```
# wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
# rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```
2. jenkinsをインストール
```
# sudo yum -y install jenkins
```
3. ポートの変更  
デフォルトだと8080で、他のサービスとバッティングするので、8081へ変更する。  
```
# vi /etc/httpd/conf.d/jenkins.conf
このなかの、JENKINS_PORTを 8080 から 8081 に変更する。
```
4. jenkins起動
```
# sudo systemctl start jenkins
```
6. 管理者ユーザの設定  
まず管理者ユーザを作ったうえでanonymousでは何もできないようにしておく。  
  1. ログイン認証の追加
    11. Jenkinsの管理＞グローバルセキュリティの設定  
  「セキュリティを有効化」にチェック  
    12. アクセス制御＞ユーザー情報＞「Jenkins のユーザーデータベース」、「ユーザーにサインアップを許可」にチェック  
    13. アクセス制御＞管理権限＞「ログイン済みユーザーに許可」にチェック  
    14. 保存 (トップ画面に戻される)  
  2. ユーザ作成
    21. Jenkinsのトップ＞「Jenkinsの管理」＞「ユーザの管理」＞「ユーザ作成」
    22. 「ユーザー名」、「パスワード」、「パスワードの確認」、「フルネーム」、「メールアドレス」を入力して、サインアップ
  3. 権限付与
    31. Jenkinsの管理＞グローバルセキュリティの設定
    32. アクセス制御＞ユーザー情報＞「ユーザーにサインアップを許可」　のチェックオフ
    33. アクセス制御＞ユーザー情報＞管理権限＞「行列による権限設定」 にチェック
    34. 登録済みユーザー名(自分)を入力して、追加
    35. ユーザー名(自分) の行のチェックを全てオン
    36. 匿名ユーザー の行のチェックを全てオフ
7. プラグインの登録
Jenkinsのトップ＞「Jenkinsの管理」＞「プラグインの管理」
  * Gradle plugin 追加
  * scm-api plugin 追加
  * Git plugin 追加


## tomcat

1. インストール
```
yum install tomcat
```
2. サービス開始
```
systemctl start tomcat
```
3. nginxとの連携
  31. commingsoon

## war のデプロイ
