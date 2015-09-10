# テストファーストの環境を整える (1)

本書ではテストファーストで開発を進める事を推奨しています。
テストで固めて、コードをリファクタリングできるように維持していきましょう。

本章では、「Observatory」の使い方についての日本語訳を掲載します。

## Observatory: A Profiler for Dart App
引用(https://www.dartlang.org/tools/observatory/)

Observatoryは profiling と debugging 用のツールです。

```
ObservatoryはFreeでGETできます。
https://www.dartlang.org/downloads/ から取得できます。
issueやrequestは、 http://dartbug.com/new で受け付けています。
```

Observatoryは動作中のDart VM の中身を覗くことができます。そして、即座にレポートしてくれます。

* どの部分に時間を費やしていたか
* allocatedした メモリーを調べます。
* コードのどの部分が実行されたわかります
* メモリーリークをデバックできます
* メモリーの断片化をデバックできます


次のビデオは、Dart Developer Summit をレコードしたものです。John McCutchan と Todd Turnidge で　使い方について解説しています。

https://youtu.be/y39pZCExsOs?list=PLOU2XLYxmsIIQorIS8gagUiMau9S84vZV


### Using Observatory
* [get started with Observatory](dart/Observatory_GetStarted.md)
