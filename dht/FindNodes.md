# FindNodeでネットワークを構築
<hr>
* **FindNodeクエリでネットワークを構築する**
<br>
* **リスポンスを受けたらRootingTableを更新する**
<br>
<hr>

前章で、kBucketとRootingTableについては説明しました。このRootinhTableを更新しながら、実際にDHTのネットワークを組んでみましょう。


## FindNodeクエリでネットワークを構築する
Mainline DHT こと、KademliaではUDPを利用してPeerどうしが通信を行います。

DHRのネットワークを構築は、FindNodeクエリとFindNodeレスポンスのみで実現しています。

本章では、FindNodeクエリについて解説していきます。


### 指定したKIDに近い距離にあるPeerを紹介してもらう事ができる



-------
Kyorohiro work

http://kyorohiro.strikingly.com
