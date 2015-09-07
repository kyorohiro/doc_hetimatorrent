# Trackerへアクセス
<hr>
<br>

* **Peerの一覧を取得してみよう**

<br>

<hr>

　これまでの成果で、Torrentファイル から必要な情報を取り
出す事が出来るようになりました。Torrentクライアント は、
Torrentファイルを解析が終わると、Trackerサーバーにアクセスし
ます。

　本章では、実際に簡易の Trackerサーバー を作成しながら、Tracker から Peer の一覧を取得する方法について解説し
ます。


#### Peerの一覧をTorrentはサーバーで管理するようにした。
 P2Pアプリケーションは、データを配信してくれるPeerを探すところから始まります。
 
　Googleで検索ワードを指定しても欲しい情報を探すように、P2Pアプリもなんらかの方法で、データを配信してくれるPeerを発見する必要があります。
　
　TorrentはTrackerサーバーを利用する方法でこれを実現しています。Trackerサーバーは「データダウンロードしたいPeer」、「データをアップロードしたいPeer」の一覧を管理しています。

　Torrentクライアントは、このTrackerに登録する事で、データ配信をしているPeerを紹介してもらうことができます。


#### Peerの一覧をP2Pネッワーク上で管理する方法も提供している。
　現在では、Trackerサーバーの機能もP2P化されています。



-------
Kyorohiro work

http://kyorohiro.strikingly.com

