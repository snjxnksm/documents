# gradle

[gradle公式サイト](http://gradle.org/)  
[Gradle日本語マニュアル](http://gradle.monochromeroad.com/docs/)

gradleとは、ビルドシステムです。
同様のプロダクトは、Javaにおいてはant、[maven](https://github.com/snjxnksm/practice/wiki/maven)などがあります。  
gradleの特徴は、antのような自由度とmavenのような強力なライブラリ管理機能を、柔軟かつ簡潔に記述できることです。


# インストール方法

1. [gradle公式サイト](http://gradle.org/)からzipファイルを取得。
2. zipファイルを任意のフォルダに展開。  
3. 環境変数を作成  

|環境変数|設定内容|解説|
|---|--|---|
|GRADLE_HOME|zipファイルを展開したフォルダ|任意のフォルダ|
|path|%GRADLE_HOME%\bin|実行ファイルにパスが通るようにする|

その他、Windowsであれば[chocolatey](https://github.com/snjxnksm/practice/wiki/Chocolatey)でもインストールが可能。  

# 最初の一歩

適当な作業フォルダを作成し、以下のコマンドを実行します。
```
 > gradle init
```
同フォルダに、build.guradleが生成されます。
これをもとに機能を拡張していきます。

# 依存ライブラリの追加

build.guradleに記述を追加することで、ライブラリの管理を行います。
以下は、[SpringBoot](../springboot/readme.md)を指定する場合。
```
dependencies {
    compile('org.springframework.boot:spring-boot-starter-actuator')
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('org.springframework.boot:spring-boot-starter-mail')
    compile('org.springframework.boot:spring-boot-starter-redis')
    compile('org.springframework.boot:spring-boot-starter-security')
    compile('org.springframework.boot:spring-boot-starter-web')
    compile('org.springframework.boot:spring-boot-starter-aop')
    compile('org.springframework.boot:spring-boot-starter-mustache')
    compile('org.projectlombok:lombok:1.16.6')
    compile('org.springframework.security.oauth:spring-security-oauth2:2.0.7.RELEASE')
    compile('org.springframework:spring-jms')
    compile('org.apache.activemq:activemq-broker')
    compile('com.amazonaws:aws-java-sdk:1.10.40')
    compile('org.mariadb.jdbc:mariadb-java-client:1.2.3')
    compile('org.liquibase:liquibase-core')
    testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile('com.jayway.restassured:rest-assured:2.4.1')
    testCompile("junit:junit:4.12")
}
```
依存関係を記述する文字列は、mavenライブラリと共有できます。
http://mvnrepository.com/


# eclipseから利用する場合。

eclipseマーケットプレイスから、gradleプラグインをインストールします。

1. eclipseを起動します。
2. ヘルプ → マーケットプレイスを開きます。
3. 検索窓に「gradle」と検索します。
4. 「Gradle Instegration for Eclipse」をインストール
5. 「利用条件」を許諾します。
6. eclipseを再起動します。
