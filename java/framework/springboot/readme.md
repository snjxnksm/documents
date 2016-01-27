# SpringBoot

SpringBootとは、従来から利用され複雑化したSpring frameworkの中から、よく利用されるプロダクトを厳選して組み合わされたオープンソースのフレームワークです。　  


# 最初の一歩

eclipseから利用する場合を想定します。  

## STSをインストールする。

1. eclipseを起動
2. ヘルプ　→　Eclipseマーケットプレイスを開く
3. 検索窓にて「sts」で検索。
4. 「Spring Tool Suite(STS) for Eclipse」をインストール。
5. 「使用条件に同意」を選択して「完了」
6. ecliseを再起動する。

## gradleプラグインをインストールする。

springbootのプロジェクトを管理するために[gradle](../gradele/readme.md)を利用します。  
[gradle](../gradele/readme.md)をeclipseから利用するためプラグインをインストールします。  

1. eclipseを起動します。
2. ヘルプ → マーケットプレイスを開きます。
3. 検索窓に「gradle」と検索します。
4. 「Gradle Instegration for Eclipse」をインストール
5. 「利用条件」を許諾します。
6. eclipseを再起動します。

# プロジェクト作成

1. eclipseを起動します。
2. File → New → Others
3. 「Select a wizard」で、Gradle配下のGradle Projectを選択します。
4. New Gradle Project画面で…  
  41. プロジェクト名を入力
  42. Sample Projectを指定する。  
  「java quickstart」を選択する。  
  43. 「完了」します。  
