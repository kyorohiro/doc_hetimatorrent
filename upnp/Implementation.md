# UPnPポートマップの実装
<hr>
<br>


* ** UDPでSSDPグループに参加する **

<br>

* ** UDPでSSDPグループからデバイスを検索する**

<br>

* ** TCPでルーターへリクエストを送る**


<br>
<hr>



## UPnPを実装しよう

概要は説明した通りです。実際に実装してみましょう。サンプル実装を以下におきました。

* ライブラリ
  https://github.com/kyorohiro/dart_hetimanet/tree/master/lib/src/upnp

* サンプルアプリ
  https://github.com/kyorohiro/HetimaPortMap


実際に作ってもらうのが、もっとも効果的です。しかし、そんな時間がない方がほとんどでしょう。また、UPnP用のライブラリを利用するので、実装をする必要がないという方もいることでしょう。

なので、本章では実際に実装しながら、処理の流れを解説していきます

##### 注意
Dart を利用するのてすが、Socket API は、hetimanetを使用します。2015/6/1において、Dart用に提供されている socketは、"chrome api ver1 " "chrome api ver2"、"dart:io"等いくつかあります。

利用するAPIに依存しないように、hetimanetでsocketを再定義して対応することにしました。


### SSDPグループに参加する

通常、UDP Socketを生成して、IPが"239.255.255.250"、Portが"1900"のグループに参加します。
参加することで、SSDPグループに追加されたデバイス等が把握できるようになったり、グループに参加していないデバイスを無視したりできるようになります。

しかし、Chrome Socketから、上手く動作できなかったので、今回は、グループに参加していません。


#### **(1) UDPソケットを生成する。**

まずは、UDPソケットを生成しましょう。こんな感じです。
```
HetiSocketBuilder _socketBuilder = new HetiSocketBuilderChrome();

HetiUdpSocket _socket = _socketBuilder.createUdpClient();
_socket.onReceive().listen((HetiReceiveUdpInfo info) {
    print("receive udp info");
});

_socket.bind("0.0.0.0", 0).then((int v){
    if (v >=0) {
      print("bind ok");
    } else {
      print("bind error");
    }
}

```

「bind ok」とコンソールに文字が表示されれば成功です。


#### **(2) SSDPグループからデバイスを検索する**

無事にUDPソケットを生成できる事を確認できたら、次はSSDPグループに、ポートマップに対応できるデバイスがないか依頼を出します。

```
_socket.send(
   convert.UTF8.encode(SSDP_M_SEARCH_WANPPPConnectionV1), 
   SSDP_ADDRESS,
   SSDP_PORT).then((HetiUdpSendInfo iii) {
  print("send ok");
}).catchError((e) {
  completer.completeError(e);
});
```

先ほど生成したUDPSocketを利用して、"239.255.255.250"、Portが"1900"に
以下のメッセージを送っているだけです。
```
M-SEARCH * HTTP/1.1
MX: 3
HOST: 239.255.255.250:1900
MAN: "ssdp:discover"
ST: urn:schemas-upnp-org:service:WANIPConnection:1
```

難しい事はないですね。これで、UPnPに対応したルーターが存在していれば、前の章で説明した。<controlURL>タグを含むxmlファイルを取得できます。


#### **(3) グローバルIPを取得する **
ルーターへ依頼をだせるようになりました。試しに、ルーターにGlobal IPについて問い合わせて見ましょう。

UPnPに対応しているルーターは、TCPサーバーが立ち上がっています。TCPサーバーへリクエストを送ります。

```
HetiHttpClient client = new HetiHttpClient(new HetiSocketBuilderChrome()));
client.connect(host, port).then((int v) {
  return client.post(path, convert.UTF8.encode(body), {
    KEY_SOAPACTION: soapAction,
    "Content-Type": "text/xml"
  });
}).then((HetiHttpClientResponse response) {
  print("receive response");
}).catchError((e){
  print("failed request");
});
```

前章で説明した値を、pathとbodyに設定すれば無事リクエストを送信できます。


## 作成したライブラリは以下の通り

* ルータを発見する
```
UpnpDeviceSearcher
  .createInstance(new hetimacl.HetiSocketBuilderChrome())
  .then((hetima.UpnpDeviceSearcher searcher) {
    searcher.onReceive().listen((hetima.UPnpDeviceInfo info) {
    });
    searcher.searchWanPPPDevice();
  });
```

* ポートマッピングする
```
UpnpPPPDevice#addPortMapping(
  localIP, 
  localPort, 
  remotePort,
  UPnpPPPDevice.VALUE_PORT_MAPPING_PROTOCOL_TCP)
    .then((UpnpPortMappingResult r) {
    });
```

* グローバルアドレスを調べる
```
UpnpPPPDevice#requestGetExternalIPAddress()
  .then((String address){
  });
```


* ポートマッピングする
```
UpnpPPPDevice#addPortMapping(
  localIP, 
  localPort, 
  remotePort,
  UPnpPPPDevice.VALUE_PORT_MAPPING_PROTOCOL_TCP)
    .then((UpnpPortMappingResult r) {
});
```

* グローバルアドレスを調べる
```
UpnpPPPDevice#requestGetExternalIPAddress()
  .then((String address){
});
```
