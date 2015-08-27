# ダウンロードゲーム

TrackerからPeerの一覧を取得できるようになりました。TorrentのP2Pネットワークから、実際にデーターをダウンロードしてみましょう。


## データーを配信しあう
Torrentでは、Peerどうしが所持してしいるデータを配信しあいます。

例えば、"まぎか.mp4" というデータをPeer A が所持していて、Peer B が このデータを欲しい場合

![](client_a.jpg)

Peer A が Peer Bにデーターを提供します。


同様に、Peer B が Peer A が欲しがるデータを所持していた場合、
![](client_b.jpg)
"まぎか.mp4"をダウンロードしながら、"エスカ.mp4"を配信します。



## プロックで管理している。

Torrent の場合は、このデータを共有する単位は、ファイル単位ではなくて、ブロック単位で管理しています。



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

TorrentFile の中に記載されている、"piece length" の単位でデータを分割しています。

例えば、piece_lengthが8で、[0,1,2,3,4,5,........]というデータを持つ場合、
![](client_c.jpg)
といった感じで、n個に分割します。


## ダウンロード速度が早いところからダウンロードする。

効率よくデータをネットワーク全体に配信するために、Torrent Clientは様々な工夫を凝らします。

* ダウンロード速度が早いところから、データーをダウウンロードする。





# 配信専用のPeerを作成してみよう
## 最初の通信
* TCPでデータのやりとりをする
* 最初にHandshakeする
<br>
* 次にBitfieldする
<hr>






## 配信する側
* Notinterestedメッセージ で欲しいデータがない事を通知する
* Interestedメッセージを受け取る
* Unchokeメッセージ でダウンロードできる事を通知する
* Chokeメッセージ でダウンロードできない事を通知する






# 






