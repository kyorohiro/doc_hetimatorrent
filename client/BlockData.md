# ブロックデータ
<hr>
* **Bitfieldを実装**
* **BlockDataを実装**
<hr>


## Bitfield実装

Bitfieldを実装していきす。 Bitfield Bool値(0, 1)を持つ任意の長さの配列です。

```:dart
class Bitfield {
  bool List<bool> _data = [];
  Bitfield(int length) {
    _data = new List.filled(length, 0);
  }
  bool operator [](int idx) => _data[idx];
  void operator []=(int idx, bool value) => data[idx] = _value;
  int get length => _data.length;
}
```

Torrentはこの値をバイト配列として利用するので、変換するメソッドを用意しておきましょう。
```
class Bitfield {
  bool List<bool> _data = [];
  Bitfield(int length) {
    _data = new List.filled(length, 0);
  }
  bool operator [](int idx) => _data[idx];
  void operator []=(int idx, bool value) => data[idx] = _value;
  int get length => _data.length;
  
  toBytes() {
  }
}
```
