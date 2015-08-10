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

##### - XOR距離を数値に直す機能はなくても良い
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
大小比較の機能を追加します。この機能を追加する事により、ソートが可能になります。
ソートができるようになると、あるKIDに近い値順に一覧を出すとかできるようになります。
まさに、これから作成しようとしている、InfoHashに近い値を持つPeer一覧を返す機能そのものです。

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

以上でKIDの作成は完了です。事の顛末を知りたい方は、以下を参照してください。
https://github.com/kyorohiro/dart_hetimatorrent/tree/master/lib/src/dht

実装作業は、必ずテストを書きながら、動作確認しながら、進めてください。

## kBucketを実装する。
kBucketは、K個のPeetについての情報を格納する入れ物です。これは、値を追加する時に制限をもたせたListとして表現できますね。

今回の実装ではkBucketが満杯になった場合は古いデータから削除するようにしています。
このあたりは、実際に動作させてみて最適な方法を試行錯誤すべきでしょう。

```dart
class KBucket {
  int _k = 8;
  int get k => _k;
  List<KPeerInfo> peerInfos = null;

  KBucket(int kBucketSize) {
    this._k = kBucketSize;
    this.peerInfos = [];
  }

  add(KPeerInfo peerInfo) {
    if (peerInfos.contains(peerInfo) == true) {
      peerInfos.remove(peerInfo);
    }
    peerInfos.add(peerInfo);
    if (peerInfos.length > k) {
      peerInfos.removeAt(0);
    }
  }

  int get length => peerInfos.length;
  KPeerInfo operator [](int idx) => peerInfos[idx];
  Iterator<KPeerInfo> get iterator => peerInfos.iterator;
}

```

以上でkBucketの作成は完了です。事の顛末を知りたい方は、以下を参照してください。
https://github.com/kyorohiro/dart_hetimatorrent/tree/master/lib/src/dht



## RootingTableを実装する
前章で説明したとおり、RootingTableは、0〜160までの161個のkBucketを保持する事ができるのでした。
まずは、最初の定義、kBucketを161個保持することができる。

```dart
class KRootingTable {
  List<KBucket> _kBuckets = [];
  int _kBucketSize = 0;

  KRootingTable(int k_bucketSize) {
    this._kBucketSize = k_bucketSize;
    for (int i = 0; i < 161; i++) {
      _kBuckets.add(new KBucket(k_bucketSize));
    }
  }
}
```


#### KIdに値に応じて、追加するxBucketを決める機能を追加する

RootingTableを所持しているPeerとのXORを計算してもその値をもとに、どのxBucketに追加するかを決めます。

| 値 |2進|index |
| -- |--|-- |
| 0 | 000 | 0 |
| 1 | 001 | 1 |
| 2,3 | 010,011 | 2 |
| 4,5,6,7 | 100,101,110,111|3|

数式に直すと、log2(XORを数値化した値)でしょうか?




-------
Kyorohiro work

http://kyorohiro.strikingly.com
