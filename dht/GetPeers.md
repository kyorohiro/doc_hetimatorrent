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

これは、ほとんど、FinddNodesと同じような動作ですね。
* KIDの近いPeerを紹介してもらうのが同じ
* なんども、送信と受信を繰り返すのが同じ


## GetPeerでネットワークから所持している


-------
Kyorohiro work

http://kyorohiro.strikingly.com
