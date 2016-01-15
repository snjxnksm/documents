# Javaについて

2014年３月にJavaが8になり、いくつか新しい書き方が加わりました。  
インストールなどについては [wiki:java](https://github.com/snjxnksm/practice/wiki/Java) を参照のこと。  

# ラムダ式

関数型インターフェースを実現する書き方です。
記述が簡潔になるメリットがあります。  
ラムダ式の基本形は以下のようになります。
```
( 実装するメソッドの引数 ) -> { 処理 }
```
Java8では、ラムダ式を使うAPIが多数追加されています。

# optionalクラス

Java8でoptionalクラスが追加されました。
処理結果がnullになるようなメソッドを使う場合、nullチェックを行っていないことが原因で例外が発生することはよくある不具合の一つです。
こういう場合に、「値が存在しないかもしれない」ことを表現するクラスがoptionalクラスです。

以下にOptionalクラスを作成する例を示します。
```
// 値を持つOptionalオブジェクト(of()メソッドにはnullを渡すと例外がスローされる)
Optional<String> exist = Optional.of("123");
// 値を持たない空のOptionalオブジェクト
Optional<String> empty = Optional.empty();
// 値がnull以外の場合は値を持つOptional、nullの場合は空のOptionalを生成する
Optional<String> optional = Optional.ofNullable(value);
```
上記の例はStringを対象にしていますが、もちろん型パラメータには何でも利用できます。

作成されたoptionalを利用する場合は以下の通りです。
```
// 値を取得(空の場合はNoSuchElementExceptionがスローされる)
String value1 = optional.get();
// 値を取得(空の場合は空文字列を返す)
String value2 = optional.orElse("");
// 値を取得(空の場合はラムダ式の結果を返す)
String value3 = optional.orElseGet(() -> {
  return new SimpleDateFormat("yyyyMMddHHmmSS").format(new Date());
});
// 値を取得(空の場合は例外をスローする)
String value4 = optional.orElseThrow(() -> new Exception("値がありません"));
```

# optionalとラムダ式

Optionalとラムダ式の書き方を利用して、可読性が下がりがちなnullチェックを簡潔に記述できます。
戻り値の型がStringだが、場合によってはnullを返す可能性があるメソッド getHoge() があるとします。
従来は、以下のように、一度Stringで受けてからnullチェックを行いました。

```
String hoge = getHoge(); // hogeはnullかも

if (hoge != null) { // nullチェック

    // hogeに対する処理

}
```

これをOptionalクラスでラップしてやると、以下のような書き方になります。
```
Optional<String> hogeOpt = Optional.ofNullable(getHoge());
hogeOpt.ifPresent(hoge -> // hogeに対する処理 …　）；
```
hogeがString型の引数となり、getHoge()の戻り値がnullでなかった場合に「hogeに対する処理」が実行されます。
getHoge()の戻り値がnullであった場合は、何も実行されません。

このように複数行あったものが、簡潔に記述できるようになります。
