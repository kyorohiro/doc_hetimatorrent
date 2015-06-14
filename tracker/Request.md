# リクエストの中身
<hr>
<br>

* **peer id を生成するバ**

<br>

* **info 辞書 の Hashを生成する**

<br>

* **portを用意する**

<br>

* **アドレスを生成する**

<br>

* **Get リクエストでデータ**

<br>

<hr>

実際に、Trackerサーバー とTrackerクライアント の間でやり取
りされるデータの形式について解析していきます。
Tracker はデータを配信している Peer の一覧を管理していま
す。以前解説した通り、Torrentではデータをダウンロードする
側もデータの配信にまわるのでした。
Torrent ではこの仕組みを実現するために、Trackerへ自身を登
録する対価として、Peerの一覧を取得できるような仕組みにな
っています。

<hr>


## データ配信に必要なものが入っている

データ配信に必要なものは、配信するデータ、配信するサーバ
ーのIPアドレス、配信するサーバーのポート番号、配信する端
末のID、どのデータを配信かるかの情報です。
まず一つ目、配信するデータは、Trackerに初めてアクセスす
る時には持っていないものなので、ここでは除外しましょう。

次に必要と思われるのが、IPアドレスです。このアドレスも用
意する必要ありません。TrackerがあなたのIPアドレスを知って
いるからです。具体的には、Trackerにアクセスする際に、自身
のIPアドレスをTrackerは知る事ができます。
例えば、Httpサーバーで通信相手のIPアドレスを返すようなプ
ログラムは以下のような感じで書けます。

```
{
  HttpServer.bind(address, port).then((io.HttpServer server) {
    server.listen((HttpRequest request) {
      request.response.write(request.connectionInfo.remoteAddress.toString(););
      request.response.close();
    });
  });
}
```

このように、Httpサーバーは、相手のIPアドレスを知っていま
す。なので、リクエストに含める必要はなさそうです。
その次に必要と思われるのはポート番号です。他のPeerからの
接続を待ち受けるには、IPアドレスとPort番号が必要です。
Tracker は待ち受けしている Port 番号を IPアドレス のように
知るすべはありません。リクエストとして通知してあげる必要
がありそうです。

最後に、IDが必要です。Peerを識別する情報としてIPアドレス
を利用する方法が考えられます。しかし、IPアドレスは複数の
端末で共有している場合や、通信環境によっては同一のIPアド
レスを長時間維持するができなかったりします。
そこで、Peerごとに識別子を自身で生成して、それを利用しま
す。

もう一つ、必要なものがありました。どのデータをダウンロー
ドしたいかといった情報です。Trackerは同一のアドレスで多数
のデータを配信するPeerを管理することがあります。どのデー
タを配信してほしいのかを Tracker に通知する必要がありま
す。

だいたい、リクエストら必要な情報が見えてきました。具体的
にGetリクエストで使用する値について見ていきましょう。


## Info辞書からIDを生成

まずは、どのデータをダウンロードしたいかを指定するための
IDを用意しましょう。 Torrent が扱う全てのデータは衝突しな
い固有の識別子を持っています。これは、ひとつの配信データ
を複数の Tracker で管理したい場合に有効な方法です。唯一の
IDで有るならば、このTrackerで管理しても、関係のないデー
タが混ざる事がないからです。このような方法を用いる事で、
Tracker への負荷を分散する事が可能になります。
では、どのように唯一IDを生成するのでしょうか。特定の管理
団体などにIDをもらう必要があるのでしょうか？Torrentでは
SHA1 が用いられています。
SHA1 は、全てのデシタルデータに 160bit(20byte) の ID をふ
るアルゴリズムです。2の160乗のIDの振る事ができますから、
異なるデータなのに、同じIDを振られる事はありません。

Torrentでは、Info辞書のSHA1をとりIDとして利用します。
Dartでは以下のように書けます。

```
{
  data.Uint8List list = Bencode.encode(file.mMetadata[TorrentFile.KEY_INFO]);
  crypto.SHA1 sha1 = new crypto.SHA1();
  sha1.add(list.toList());
  sha1.close(); //return value is id
}

見ての通り Torrentファイルのなかの Info 辞書の部分のバイナ
リでデータのSHA1をとるだけです。

## 自身のIDを用意する

Info辞書の識別子と同様に、自身のIDも20バイトのバイナリデ
ータを利用します。そして、IDの生成は各Torrentクライアント
行います。IDの生成は、Info辞書のSHA1をとった時のように明
確に生成するルールは存在していません。
他の Torrent クライアントが生成したIDと衝突しない用に注意
し生成する必要があります。
ここでは、「乱数を使う方法」を紹介します。

```
class PeerIdCreator {
  static math.Random _random = new math.Random(new DateTime.now().millisecond);
  static List<int> createPeerid(String id) {
  List<int> output = new List<int>(20);
  for (int i = 0; i < 20; i++) {
    output[i] = _random.nextInt(0xFF);
  }
  List<int> idAsCode = id.codeUnits;
  for(int i=0;i<5&&i<idAsCode.length;i++) {
    output[i+1] = idAsCode[i];
  }
  return output;
}
```

1byte づつ乱数を計算して代入しています。追加機能として、
どのクライアントで生成されたを判別できるように、idの一部
に文字列を追加できるようにしました。

## Portを用意する
Torrent クライアントは、サーバーの機能とクライアントの機
能をもっています。他のTorrentクライアントからの接続を待ち
受けるには、サーバーの機能を立ち上げる必要があります。こ
の時のサーバーのポート番号を用意します。

```
{
  HttpServer.bind(address, port).then((io.HttpServer server) {
    server.listen((HttpRequest request) {
      request.response.write(request.connectionInfo.remoteAddress.toString(););
      request.response.close();
    });
  });
}
```

以前、書いたHttpサーバーのコードで指定したPort番号で良い
です。
実際には、ご家庭ネットワーク環境では、ここで指定したポー
ト番号と、実際に外部から見えているポート番号が異なる場合
があります。このような場合に対処するには、もうひと工夫必
要です。
付録の「なぜなにNat Travarsal」を参照してください。


## アドレスを生成する

だいたい必要な情報は整いました。さっそくアドレスを生成し
てみましょう。
実際には、以下のような情報も追加してアドレスを作成する事
になります。

##### "info_hash"
TorrentファイルのInfo辞書のSHA1

##### "peer_id"
生成した20byteのバイナリデータ

##### "event"
Torrentクライアントり状態を表します。“started”, “stopped”,”completed”
の3つ。それぞれ、データーのダウンロード中である場合は、”started”、
データのダウンロードと配信から抜ける場合は、”stopped”、データのダ
ウンロードを完了した場合は、”completed”が指定されます。

##### "downloaded"
今までダウンロードが完了したバイト数

##### "uploaded"
今までに配信したバイト数

##### "left"
ダウンロードが済んでいないデータのバイト数
これらのデータを結合して、URLを生成するば完成です。
```

String toString() {
return scheme + "://" + trackerHost + ":"
+ trackerPort.toString() + ""
+ path + toHeader();
}
String toHeader() {
return "?" + KEY_INFO_HASH + "=" + infoHashValue
+ "&" + KEY_PORT + "=" + port.toString()
+ "&" + KEY_PEER_ID + "=" + peerID
+ "&" + KEY_EVENT + "=" + event
+ "&" + KEY_UPLOADED + "=" + uploaded.toString()
+ "&" + KEY_DOWNLOADED + "=" + downloaded.toString()
+ "&" + KEY_LEFT + "=" + left.toString();
}
```

といった感じで書けます。無事URLを生成する事ができました
ね。