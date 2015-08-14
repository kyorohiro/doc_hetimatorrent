# GetPeersでInfoHashに対応するPeerを探す
<hr>
* **GetPeersでPeerを探す**
<br>
* **AnnouncePeerでP2Pネットワーク上に値を保存する**
<br>
<hr>

前章でP2Pネットワークを作成する事ができました。本章では、作成したP2Pネットワークにデータを記録する方法について解説していきます。


## GetPeerでネットワークとFindNodeはほとんど同じ。

MainLine DHT ではGetPeersメッセージを利用して、データを所持しているPeerを探します。

GetPeerコマンドは「指定してKIDに対応するデータを所持しているPeerを知っていれば、そのPeerについて教えてもらう」、もしも知っていなければ、もっとも近いPeerを教えてもらう。といった事をします。

この操作を何度も繰り返す事で、KIDともっとも近くにあるNodeを発見する事ができます。

これは、ほとんど、FinddNodesと同じような動作ですね。
* KIDの近いPeerを紹介してもらうのが同じ
* なんども、送信と受信を繰り返すのが同じ

実装もほとんど同じになります。


## AnnouncePeerでデータを記録する

GetPeerを繰り返して、上位K個のNodeが固定されたら、AnnounePeerメッセージを利用してデータを記録してもらいます。

K個のNodeへデータの記録を依頼します。複数のPeerへ依頼することによって、堅牢性が高まります。

* 発見されやすくなる
* 登録したPeerが離脱しても大丈夫
* 登録したPeerの負荷を分散することができる。


## メッセージの構成

```
arguments:  
{
  "t":"aa",
  "y":"q",
  "q":"get_peers", 
  "a": {
    "id" : "<querying nodes id>", 
    "info_hash" : "<20-byte infohash of target torrent>"
  }
}

response: have value
{
  "id" : "<queried nodes id>",
  "token" :"<opaque write token>",
  "values" : 
  ["<peer 1 info string>", "<peer 2 info string>"]
}

response: have node info
{
 "t":"aa",
 "y":"r",
 "r": {
  "id" : "<queried nodes id>",
  "token" :"<opaque write token>",
  "nodes" : "<compact node info>"
  }
}
```

FindNodeとほとんど同じですね。本書では"token"と"values"が初めて出てきました。

"token" は、レスポンスを返す側に自由に決めることができるバイトデータです。AnnouncePeerクエリを送信する時に使います。このバイトデータのサイズもレスポンスを返す側のクライアントによって、異なります。

"value" は、6バイトのデータ[<IP 4bute> ,<Port 2byte>]をList形式で、複数個格納しています。


ref http://www.bittorrent.org/beps/bep_0005.html

-------
Kyorohiro work

http://kyorohiro.strikingly.com
