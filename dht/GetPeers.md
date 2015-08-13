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



-------
Kyorohiro work

http://kyorohiro.strikingly.com
