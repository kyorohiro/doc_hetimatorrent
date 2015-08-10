# RootingTableを実装してみよう。
<hr>
* **KIdを実装する**
<br>
* **kBucketを実装する**
<br>
* **RootingTableを実装する**
<br>
<hr>

RootingTableを実装してみましょう。コードに落とす事で理解も深まります。

## KIDを実装する。

#### InfoHashもPeerIDもKIDとして表せる。
InfoHash と InfoHash、Peer IDとInfoHash Peer ID とPeer ID のXOR距離を計算する必要があるのでした。
これらのIDは、すべて20バイトのデータであり同じものとして定義できます。本文ではKIDと呼ぶことにします。

XORでは160bitの値を扱う必要があります。しかし、Dart言語では160bitに対応する
数字を持っていません。53bitまでしか使えません。
これは、Dart言語の問題というわけではなく、ほとんどの言語で、
160bitの値を扱うことができません。

本書では160bitの値を定義しないで実装する方法で進めます。
実際に実装してみるとわかるのですが、XOR距離を求める必要はありません。RootingTable上の
どの位置に格納されるのかという情報と、大小比較する機能が必要になります。



まずは、最初の定義、KID は、20byteのデータを持つ
```dart
class KId {
  List<int> _values = null;
  KId(List<int> id) {
    if(id == null || id.length != 20) {
      throw {};
    }
    this._values = new Uint8List.fromList(id);
  }
}
```
#### RootingTable 







-------
Kyorohiro work

http://kyorohiro.strikingly.com
