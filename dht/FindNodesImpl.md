# FindNodesを実装してみよう
<hr>
* **FindNodeクエリでネットワークを構築する**
<br>
* **リスポンスを受けたらRootingTableを更新する**
<br>
<hr>


通信ライブラリとして、hetimanetを使用しています。APIのインターフェイスは他の通信ライブラリと大きな違いはありません。なので、お使いのものと読み替えて読み進めてください。


## (1) KNodeはUDPサーバー機能を持つ。

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

## (2) Krpc Messageをパースする

MainLine DHT では、PeerとPeerの通信には、Bencodeが利用されます。
すでにBencodeのパーサーは作成ずみなので、難しいことはないはずです。

```
class KrpcMessage {
  KrpcMessage.fromMap(Map map) {
    _messageAsMap = map;
  }

  static Future<KrpcMessage> decode(List<int> data) async {
    Map<String, Object> messageAsMap = null;
    try {
      Object v = Bencode.decode(data);
      messageAsMap = v;
    } catch (e) {
      throw {};
    }
    return  new KrpcMessage.fromMap(messageAsMap);
  }
}
```
こんな感じです。あとは、必要に応じて、パースした結果を読み取るだけです。

````
class KrpcMessage {
...
...
  List<int> get transactionId => _messageAsMap["t"]);
  String get transactionIdAsString => UTF8.decode(transactionId);

  //
  List<int> get messageType => _messageAsMap["y"];
  String get messageTypeAsString => UTF8.decode(messageType);

  //
  List<int> get query => _messageAsMap["q"];
...
...
}
````
こんな感じです。BEP5のスペックをみると結構ありますがも気長にコーディングしていけば、半日くらいで終わると思います。


## (3) メッセージを送信する


```
class KNode {
 ..
 ..
  sendMessage(KrpcMessage message, String ip, int port) {
      return _udpSocket.send(message.messageAsBencode, ip, port);
  }
 ..
}

class FindNode {

  static int queryID = 0;

  static KrpcMessage createQuery(List<int> queryingNodesId, List<int> targetNodeId) {
    List<int> transactionId = UTF8.encode("fi${queryID++}");
    return new KrpcMessage.fromMap({"a": {"id": queryingNodesId, "target": targetNodeId}, "q": "find_node", "t": transactionId, "y": "q"});
  }

  static KrpcMessage createResponse(List<int> compactNodeInfo, List<int> queryingNodesId, List<int> transactionId) {
    return new KrpcMessage.fromMap({"r": {"id": queryingNodesId, "nodes": compactNodeInfo}, "t": transactionId, "y": "r"});
  }
}
```

メッセージの送信は、受信用に作成したUDPSocketを利用します。こうすることで、受信相手に、ポート番号を伝えることができます。

## (4) ネットワークへの参加用のコードを書く

RootingTableに所持しているデータの中から、自分自信ともっとも近いKIDをもつPeerへFindNodeクエリを送信する

```
class KNodeWorkFindNode {
  ...
  ...
  updateP2PNetworkWithoutClear(KNode node) {
    node.rootingtable.findNode(node.nodeId).then((List<KPeerInfo> infos) {
      for (KPeerInfo info in infos) {
        if (!_findNodesInfo.rawsequential.contains(info)) {
          _findNodesInfo.addLast(info);
          node.sendFindNodeQuery(info.ipAsString, info.port, node.nodeId.value).catchError((_) {});
        }
      }
    });
  }
  ...
  ...
}
```

レスポンスを受けとったら、もう一度繰り返す
```
class KNodeWorkFindNode {
  ...
  ...
  onReceiveQuery(KNode node, HetiReceiveUdpInfo info, KrpcMessage query) {
    if (query.queryAsString == KrpcMessage.QUERY_FIND_NODE) {
      KrpcFindNode findNode = query.toFindNode();
      return node.rootingtable.findNode(findNode.targetAsKId).then((List<KPeerInfo> infos) {
        return node.sendFindNodeResponse(info.remoteAddress, info.remotePort, query.transactionId, KPeerInfo.toCompactNodeInfos(infos)).catchError((_) {});
      });
    }
    node.rootingtable.update(new KPeerInfo(info.remoteAddress, info.remotePort, query.nodeIdAsKId));
    updateP2PNetworkWithoutClear(node);
  }
  ...
  ..
}
```

一定時間だったら、もう一度アクセスする
```
class KNodeWorkFindNode {
  ...
  ...

  onTicket(KNode node) {
    _findNodesInfo.clear();
    updateP2PNetworkWithoutClear(node);
  }
```

## (5) FindeNodeクエリに対応したレスポンスを返せるようにしよう

```
class KNodeWorkFindNode {
  ...
  ...
  onReceiveResponse(KNode node, HetiReceiveUdpInfo info, KrpcMessage response) {
    if (response.queryFromTransactionId == KrpcMessage.QUERY_FIND_NODE) {
      KrpcFindNode findNode = response.toFindNode();
      for (KPeerInfo info in findNode.compactNodeInfoAsKPeerInfo) {
        node.rootingtable.update(info);
      }
    }
    node.rootingtable.update(new KPeerInfo(info.remoteAddress, info.remotePort, response.nodeIdAsKId));
    updateP2PNetworkWithoutClear(node);
  }
  ..
}
```

