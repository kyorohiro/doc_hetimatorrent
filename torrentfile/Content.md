# Torrent ファイルの中身

<hr>
<br>


* **TorrentファイルはBencodingで記載されている**

<br>

* **Trackerのアドレスが記載されている**

<br>

* **ファイル名が記載されている**

<br>

* **ブロックデータごとのSHA1 Hashが記載されている**

<br>

* **Bencodingはパースしやすい構造**

<br>

<hr>

## Torentファイルを読み込んでみよう

無事、Bencodeのパーサーを書く事ができました。これで、
Torrentファイルの中身を解析できます。Torrentファイルの中身
を確認してみましょう。

さっそく、Torrentファイルを読み込んでみましょう。Parserを
作成していない方は、「https://github.com/kyorohiro/
dart_hetimalib_test/tree/master/hetimalib_sample/TorrentFileParser」を利用してください。

読み込んで見ると以下のようなデータ構成である事がわかりま
す。

```
{
 "announce":http://example.com/tracker,
 "created by":torrent generator,
 "creation date":1364723642,
 "encoding":utf-8,
 "info":{
 "length":1024,
 "name":xxx
 "piece length":16384,
 "pieces":<......20バイト単位のバイナリデータ>
 }
}
```

###  announce
Tracker サーバーのアドレスが記載されています。本アドレス
のサーバー
にからデータを配信してくれる端末を紹介してもらえます。

### created by
本ファイル生成したツールを示す名前のようです。

### creation date
本ファイルが生成された日にちを表しているようです。

### info.length
配信されているデータのサイズです。1024byte のデータである
事がわかります。

### info.name
配信されているデータのファイル名です。xxx という名前であ
る事が解ります。

### piece length
データを配信する際に、分割するサイズです。16kb単位で分割
する事がわかります。

### pieces
分割されたデータごとのHash値です。データーの正当性を判定
するのに使用します。

