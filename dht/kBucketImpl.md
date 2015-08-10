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
InfoHash と InfoHash、Peer IDとInfoHash Peer ID とPeer ID のXOR距離を計算する必要があるのでした。これらのIDは、すべて20バイトのデータであり同じものとして定義できます。本文ではKIDと呼ぶことにします。

XORでは160bitの値を扱う必要があります。しかし、Dart言語では160bitに対応する
数値を持っていません。53bitまでしか使えません。これは、Dart言語の問題というわけではなく、ほとんどの言語で、160bitの値を扱うことができません。

本書では160bitの数値を定義しないで実装する方法で進めます。
実際に実装してみるとわかるのですが、XOR距離を求める必要はありません。RootingTable上のどの位置に格納されるのかという情報と、大小比較する機能が必要になります。


作成していきましょう。まずは、最初の定義、KID は、20byteのデータを持つ事とする。


```dart
class KId {
  List<int> _values = null;
  List<int> get value => new List.from(_values);
  KId(List<int> id) {
    if(id == null || id.length != 20) {
      throw {};
    }
    this._values = new Uint8List.fromList(id);
  }
  int get length => _values.length;
  int operator [](int idx) => _values[idx];
  Iterator<int> get iterator => _values.iterator;
}
```

#### XOR の計算機能を追加する

KIdはXOR距離が計算できる必要があります。数値としては表現する事は諦めましたが、計算した結果はKIDとして返す機能は必要です。バイト配列の各値ごとに xorをとる事で実現できます。

```dart
class KId {
  ...
  ...
  ...
  KId xor(KId b, [KId output = null]) {
    if (output == null) {
      output = new KId.zeroClear();
    }
    for (int i = 0; i < b._values.length; i++) {
      output._values[i] = this._values[i] ^ b._values[i];
    }
    return output;
  }
}
```

#### 大小比較の機能を追加する
```dart
class KId {
  ...
  ...
  ...
  bool operator >(KId b) {
    for (int i = 0; i < b._values.length; i++) {
      if (this._values[i] == b._values[i]) {
        continue;
      } else if (this._values[i] > b._values[i]) {
        return true;
      } else {
        return false;
      }
    }
    return false;
  }

  bool operator ==(KId b) {
    for (int i = 0; i < b._values.length; i++) {
      if (this._values[i] != b._values[i]) {
        return false;
      }
    }
    return true;
  }

  bool operator >=(KId b) {
    return (this == b ? true : (this > b ? true : false));
  }

  bool operator <(KId b) {
    return (this == b ? false : !(this > b));
  }

  bool operator <=(KId b) {
    return (this == b ? true : (this > b ? false : true));
  }

```


#### RootingTableのIndex求める機能を追加










-------
Kyorohiro work

http://kyorohiro.strikingly.com
