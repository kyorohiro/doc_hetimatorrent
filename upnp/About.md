# UPnpによるポートマップ
<hr>
<br>

* **Natを越えないとP2P通信できなまい**


* **UPnPを使えば、TCPでもNat越えができる**

<br>
<hr>

Socketを使う事で簡単に、インターネットを利用したアプリケーションが作成する事ができます。プラウザーとかメーラーとかです。

しかし、P2Pアプリケーションを作る場合は、もう一つ考慮しなくてはならない事があります。それが、「Nat越え」です。

P2Pアプリとして、必ず満たさなければならない条件は、何でしょうか？ それは、ユーザーが所有する端末同士が直接データ通信をできる事です。P2Pの定義そのものですね。
これができません。

Nat越えをする方法はいくつかあります。本章では、UPnPを用いてNat越えをする方法について解説したいと思います。



<hr style="page-break-before: always;">

## Global IP が必要

P2P通信するするためには、相手に自分の「IPアドレス」と「Port番号」を教える。必要ががあります。しかし、Nat越えの問題を解決しないと、これができません。


ご家庭からインターネットにアクセスする場合、ルーターというデバイスを利用しています。
ルーターは、ルータに接続している端末に 「Private IP」 を割り振ります。

```
>> netstat
```
とコマンドを入力してみましょう。「192.168.100.1」といったアドレスが端末に割り振られている事が確認できます。このアドレスを相手に伝えても、通信ができません。

### ルータから教えてもらおう

しかし、ルーターからインターネットに接続できているという事は、「Global IP」をルーターは持っているから通信できるのです。つまり、ルーターは「Global IP」をルーターは知っている。しかし、ルーターを利用している端末は「Global IP」をしらないという問題があります。


## UPnPを用いてPort Mappingをしよう

UPNPを実現するには、UDPとTCPを用いて通信できる必要があります。
具体的には、
* UDP Multicast を利用して、使用中のルーターに、ポートマッピングを依頼するためのアドレスを教えてもらう。
* TCP を使って、教えてもらったアドレスからポートマッピングの依頼をだす。

といった事をします。実際にTryしてみましょう。


### 依頼先を調べる

UDPで以下のメッセージを通知する
```
M-SEARCH * HTTP/1.1
MX: 3
HOST: 239.255.255.250:1900
MAN: "ssdp:discover"
ST: urn:schemas-upnp-org:service:WANIPConnection:1
```

もしもルーターが存在していれば、以下のような返答があります。
```
HTTP/1.1 200 OK
CACHE-CONTROL: max-age=1800
ST: urn:schemas-upnp-org:service:WANIPConnection:1
USN: uuid:E8088BD3-E808-8BD3-A042-E8088BD3A042::urn:schemas-upnp-org:service:WANIPConnection:1
EXT:
SERVER: E588 UPnP/1.0 MiniUPnPd/1.6
LOCATION: http://192.168.100.1:54616/rootDesc.xml
OPT: "http://schemas.upnp.org/upnp/1/0/"; ns=01
01-NLS: 1
BOOTID.UPNP.ORG: 1
CONFIGID.UPNP.ORG: 1337
DATE: Sun, 14 Sep 2014 00:42:14 GMT
```

LOCATIONで指定されたアドレスのXMLファイルの<controlURL>タグの中に依頼先のアドレスが記載されています。このアドレスへ依頼をだせば、インターネットから、あなたの端末へアクセスできるようになります。

### 外部の端末から見えているアドレスを聞く

```
POST 192.168.100.1 HTTP/1.1
Host: 192.168.100.1
Connection: close
SOAPACTION: "urn:schemas-upnp-org:service:WANIPConnection:1#GetExternalIPAddress"
Content-Length: 324
```

成功すると、以下のような返信を受け取ります。
```
HTTP/1.1 200 OK
Content-Type: text/xml; charset="utf-8"
Connection: close
Content-Length: 360
Server: E588 UPnP/1.0 MiniUPnPd/1.6
EXT:
DATE: Sun, 14 Sep 2014 01:46:07 GMT
```

### ポートマッピングの依頼をだす
```
POST 192.168.100.1 HTTP/1.1
Host: 192.168.100.1
Connection: close
SOAPACTION: "urn:schemas-upnp-org:service:WANIPConnection:1#AddPortMapping"
Content-Length: 629

<?xml version="1.0"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV:="http://schemas.xmlsoap.org/soap/envelope/" SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
<SOAP-ENV:Body><m:AddPortMapping xmlns:m="urn:schemas-upnp-org:service:WANIPConnection:1">
<NewRemoteHost></NewRemoteHost>
<NewExternalPort>48083</NewExternalPort>
<NewProtocol>TCP</NewProtocol>
<NewInternalPort>8083</NewInternalPort>
<NewInternalClient>192.168.100.100</NewInternalClient>
<NewEnabled>1</NewEnabled>
<NewPortMappingDescription>test</NewPortMappingDescription>
<NewLeaseDuration>0</NewLeaseDuration>
</m:AddPortMapping></SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

```
HTTP/1.1 200 OK
Content-Type: text/xml; charset="utf-8"
Connection: close
Content-Length: 263
Server: E588 UPnP/1.0 MiniUPnPd/1.6
EXT:
DATE: Sun, 14 Sep 2014 01:39:27 GMT

<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/" s:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
<s:Body>
<u:AddPortMappingResponse xmlns:u="urn:schemas-upnp-org:service:WANIPConnection:1"/>
</s:Body>
</s:Envelope>
```

-------
Kyorohiro work

http://kyorohiro.strikingly.com



