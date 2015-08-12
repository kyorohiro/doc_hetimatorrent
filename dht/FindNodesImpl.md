# FindNodesを実装してみよう
<hr>
* **FindNodeクエリでネットワークを構築する**
<br>
* **リスポンスを受けたらRootingTableを更新する**
<br>
<hr>


通信ライブラリとして、hetimanetを使用しています。APIのインターフェイスは他の通信ライブラリと大きな違いはありません。なので、お使いのものと読み替えて読み進めてください。


### (1) KNodeはUDPサーバー機能を持つ。

まずは、UDPを用いて、通信部分を書いてみましょう。Torrentの仕様では、DHTとして動作するPeerをNodeと読んでいます。
DHTの通信を行う主体として、KNode class を定義することにします。

UDP serverは、メッセージを受け取るメッセージはbencodeなのでした。パースしてそのMessageを使うclassに渡します。

```dart
class KNode {
  bool _isStart = false;
  bool get isStart => _isStart;
  HetiSocketBuilder _socketBuilder = null;
  HetiUdpSocket _udpSocket = null;
 
  KNode(HetiSocketBuilder socketBuilder) {
    this._socketBuilder = socketBuilder;
  }
  
  Future start({String ip: "0.0.0.0", int port: 28080}) async {
    (_isStart != false ? throw "already started" : 0);
    _udpSocket = this._socketBuilder.createUdpClient();
    return _udpSocket.bind(ip, port, multicast: true).then((int v) {
      _udpSocket.onReceive().listen((HetiReceiveUdpInfo info) {
        KrpcMessage.decode(info.data, this).then((KrpcMessage message) {
          onReceiveMessage(info, message);
        });
      });
      _isStart = true;
    });
  }

  Future stop() async {
    if (_isStart == false || _udpSocket == null) {
      return null;
    }
    return _udpSocket.close().whenComplete(() {
      _isStart = false;
      _ai.stop(this);
    });
  }
}
```

### (2) 
