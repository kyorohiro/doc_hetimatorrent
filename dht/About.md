# Mainline DHTについて、TrackerなしでPeerを探す。
<hr>
<br>
* **Trackerがなくてもデータを探せる**
<br>
* **六次の隔たりで実現している**
<br>
* **Trackerがなくてもデータを探せる**
<br>
* **Torrent は、 Kademulia を採用している**
<br>
<hr>

本章では、Mainline DHT と呼ばれる機能を解説/実装していきます。


##### Trackerなしで、ネットワーク構築できる
　TorrentクライアントはTrackerサーバーへアクセスして、データを共有する端末を教えてもらうのでした。
　このTrackerの役割を、P2Pネットワーク上で実現したものが、Mainline DHTです。

　今までは、Torrent方式は、Trackerの部分は、P2P方式ではなくて、サーバー・クライアント方式でした。基本的には、Trackerサーバーを、WWW上に公開する必要がありました。
　しかし、DHTを採用することで、WWW上にTrackerサーバーを公開しなくてもデータを共有できるようになります。
　
# MainL















