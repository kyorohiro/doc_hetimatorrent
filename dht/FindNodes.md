# FindNodeでネットワークを構築する
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

FindNodeクエリの機能はとても単純です。 指定したKIDのを指定して
```
arguments:  {"id" : "<querying nodes id>", "target" : "<id of target node>"}

response: {"id" : "<queried nodes id>", "nodes" : "<compact node info>"}
```
ref http://www.bittorrent.org/beps/bep_0005.html

-------
Kyorohiro work

http://kyorohiro.strikingly.com
