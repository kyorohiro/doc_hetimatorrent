# FindNodesを実装してみよう
<hr>
* **FindNodeクエリでネットワークを構築する**
<br>
* **リスポンスを受けたらRootingTableを更新する**
<br>
<hr>

まずは、UDPを用いて、通信部分を書いてみましょう。Torrentの仕様では、DHTとして動作するPeerをNodeと読んでいます。
DHTの通信を行う主体として、KNode class を定義することにします。

通信ライブラリとして、hetimanetを使用しています。APIのインターフェイスは他の通信ライブラリと大きな違いはありません。なので、お使いのものと読み替えて読み進めてください。


### (1) KNodeはUDPサーバー機能を持つ。

```
class KNode {
  Future start({String ip: "0.0.0.0", int port: 28080}) {
    return new Future(() {
      if (_isStart) {
        throw {};
      }
      _udpSocket = this._socketBuilder.createUdpClient();
      return _udpSocket.bind(ip, port, multicast: true).then((int v) {
        _udpSocket.onReceive().listen((HetiReceiveUdpInfo info) {
          if (!buffers.containsKey("${info.remoteAddress}:${info.remotePort}")) {
            buffers["${info.remoteAddress}:${info.remotePort}"] = new EasyParser(new ArrayBuilder());
            _ai.startParseLoop(this, buffers["${info.remoteAddress}:${info.remotePort}"], info, "${info.remoteAddress}:${info.remotePort}");
          }
          EasyParser parser = buffers["${info.remoteAddress}:${info.remotePort}"];
          (parser.buffer as ArrayBuilder).appendIntList(info.data);
        });

        //////
        _isStart = true;
        _ai.start(this);
        ai.startTick(this);
      });
    }).catchError((e) {
      _isStart = false;
      throw e;
    });
  }

}
```

