# UPnpによるポートマップ
<hr>
<br>

* **Natを越えないとP2P通信できなまい**

<br>

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

## ルータから教えてもらおう

しかし、ルーターからインターネットに接続できているという事は、「Global IP」をルーターは持っているから通信できるのです。つまり、ルーターは「Global IP」をルーターは知っている。しかし、ルーターを利用している端末は「Global IP」をしらないという問題があります。


## UPnPを用いてPort Mappingをしよう

UPNPを実現するには、UDPとTCPを用いて通信できる必要があります。
具体的には、
* UDP Multicast を利用して、使用中のルーターに、ポートマッピングを依頼するためのアドレスを教えてもらう。
* TCP を使って、教えてもらったアドレスからポートマッピングの依頼をだす。

といった事をします。実際にTryしてみましょう。







ルーターに聞くのが良いでしょう。ルーターと会話する方法として、UPnPというプロトコルがあります。本方法を通じて、


用のアドレスですね。このアドレスを相手に教えてもアクセスしてもらう事はできません。



「Nat越え」ができなければ、サーバー