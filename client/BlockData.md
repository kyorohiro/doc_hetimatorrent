# ブロックデータ
<hr>
* **Bitfieldを実装**
* **BlockDataを実装**
<hr>


## Bitfield実装

Bitfieldを実装していきす。 Bitfield Bool値(0, 1)を持つ任意の長さの配列です。

```:dart
class Bitfield {
  List<bool> _data = [];
  Bitfield(int length) {
    _data = new List.filled(length, false);
  }

  bool operator [](int idx) => _data[idx];
  void operator []=(int idx, bool value) {
    _data[idx] = value;
  }
  int get length => _data.length;

}
```

Torrentはこの値をバイト配列として利用するので、変換するメソッドを用意しておきましょう。
```
class Bitfield {
....
....

  List<int> toBytes() {
    int bytesLengths = _data.length ~/ 8 + (_data.length % 8 == 0 ? 0 : 1);
    Uint8List ret = new Uint8List(bytesLengths);
    for (int i = 0; i < _data.length; i++) {
      if (this[i] == true) {
        ret[i ~/ 8] |= 0x80 >> (7 - (i % 8));
      }
    }
    return ret;
  }
}
```

こんな感じです。


## BlockDataの実装

```
abstract class HetimaData {
  async.Future<int> getLength();
  async.Future<WriteResult> write(Object buffer, int start);
  async.Future<ReadResult> read(int offset, int length, {List<int> tmp:null});
}
```



　




