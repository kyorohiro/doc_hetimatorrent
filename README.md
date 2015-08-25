![](cover.jpg)
* [イントロ](intro/Intro.md)
   * [はじめに](intro/Introduction.md)
   * [Torrentとは](intro/About.md)
   * [ゴール](intro/Goal.md)
* おまかな仕組み
* [Torrentファイルを読み込む](torrentfile/Torrentfile.md)
   * [TorrentFile](torrentfile/About.md)
   * [Bencode](torrentfile/Bencode.md)
   * [Bencodeの実装](torrentfile/Implementation.md)
   * [TorrentFileの中身](torrentfile/Content.md)
* Httpサーバーを作成してみる
* [UPnpによるポートマップ](upnp/Upnp.md)
   * [UPnPによるポートマップ](upnp/About.md)
   * [UPnPの実装](upnp/Implementation.md)
* [Trackerへアクセスしてみる](tracker/Tracker.md)
   * [Trackerへアクセスしてみる](tracker/About.md)
   * [TrackerはHttpサーバ](tracker/Http.md)
   * [リクエストの中身](tracker/Request.md)
   * [レスポンスの中身](tracker/Response.md)
   * [テスト](tracker/Test.md)
* リダイレクトに対応
   * [リダイレクト](tracker/Redirect.md)
* [ダウンロードゲームへ参加してみる](client/Client.md)
   * [ダウンロードゲーム](client/Readme.md)
* [DHTに対応してみる](dht/Dht.md)
   * [Tracker無しでPeerを探す](dht/About.md)
   * [KademuliaのkBucketを利用している](dht/kBucket.md)
   * [RootingTableを実装してみよう](dht/kBucketImpl.md)
   * [FindNodeでネットワークの構築](dht/FindNodes.md)
   * [FindNodeを実装](dht/FindNodesImpl.md)
   * [GetPeersでInfoHashに対応するPeerを探す](dht/GetPeers.md)
   * [テスト](dht/Test.md)
* 囚人のジレンマ
* スモールワールド
* PortMapテクニック
