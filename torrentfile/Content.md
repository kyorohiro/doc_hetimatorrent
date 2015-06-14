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


## ファイルが複数の場合

ひとつのファイルを配信するデータは説明した通りです。複数
のファイルをパッケージする場合は、もう少し構造が複雑にな
ります。

```
{
 "announce":http://example.com/tracker,
 "created by":torrent generator,
 "creation date":1364723642,
 "encoding":utf-8,
 "info":{
 “files”: [
 {
 length:512,
 path:[aaa, bbb.mp3]
 },
 {
 length:1024,
 path:[ccc, ddd.mp3]
 },
 ]
 "name":xxx
 "piece length":16384,
 "pieces":<......20バイト単位のバイナリデータ>
 }
```

さきほどのと比較すると、info辞書の中に、files リストが増え
ています。今回の場合だと、「xxx/aaa/bbb.mp3」、「xxx/ccc/
ddd.mp3」という2つのデータが含まれている事が読み取れま
す。


## Torrentファイルを作成してみよう

これらの理解できた事を整理して、理解出来ていない事を発見
しながら、Torrentファイルを作成するツールを作成してみまし
ょう。無事作成できたならば、だいたいは理解できたという事
になります。既に、Bencoding のパーサーはありますから、ゴ
ールは目の前です。

```
{
 Map file = {};
 Map info = {};
 file[TorrentFile.KEY_ANNOUNCE] = announce;
 file[TorrentFile.KEY_INFO] = info;
 info[TorrentFile.KEY_NAME] = name;
 info[TorrentFile.KEY_PIECE_LENGTH] = piececSize;
 info[TorrentFile.KEY_LENGTH] = targetLength;
 info[TorrentFile.KEY_PIECE] = pieceBuffer;
 Bencode.encode(file);
}
```

といった感じで、必要な情報をMapに配置して、Bencode にエ
ンコードすれば完成です。

実際に作成してみると、明らかでなかった点が現れてきます。
例えば、ファイルが複数ある場合は、pieceデータをどのよう
に算出するのでしょうか？ まだ、明らかになっていませんでし
た。2つ生成する方法を思いつきました。どちらかが、Torrent
で採用されている方法だと良いのですが..。


1. ファイル単位で、pieceデータを作成する。
2. 複数ファイルは結合してひとつのファイルと見なしてから、pieceデータを作成する。


実際に試してみたところ、 2の方法が採用されているようで
す。具体的には、

```
{
 Blob fileA =...
 Blob fileB =...
 Blob image = new Blob([fileA, fileB]);
 FileReader reader = new FileReader();
 reader.readAsArrayBuffer(image).then((e){
 int start = 0;
 do {
 int end = start+pieceLength;
 if(end<image.size()){ end = image.size()}
 crypto.SHA1 sha1 = new crypto.SHA1();
 sha1.add(reader.sublist(start,pieceLength));
 print(sha1.close().toList().toString());
 start = end;
 } while(end < image.size()):
 });
}
```

といったコードで生成できます。

