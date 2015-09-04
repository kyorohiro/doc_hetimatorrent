# ブロックデータ
<hr>

* **Bitfieldを実装**
* **BlockDataを実装**

<hr>

ブロック単位でデータを管理するためのコードを書いて見ましょう。



## Bitfield実装

Bitfieldを実装していきす。 Bitfield Bool値(0, 1)を持つ任意の長さの配列です。

```:dart
class BitfieldSample {
  List<bool> _data = [];
  BitfieldSample(int length) {
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

BlockDataは、メモリーに治らなまいデータを扱う可能性があるので、メモリーに記録する事ができません。

ここでは、以下のような、インターフェイスを利用することにします。

```
abstract class HetimaData {
  async.Future<int> getLength();
  async.Future<WriteResult> write(Object buffer, int start);
  async.Future<ReadResult> read(int offset, int length, {List<int> tmp:null});
}
```


BlockDataは、Blockごとにデータの状態を管理します。なので、blockごとにデータを所持しているか、所持していないかを判断できるようにします。

```
class BlockDataSample {
  BitfieldSample _info = null;
  HetimaData _data = null;
  int _blockSize = 0;
  int _fileSize = 0;
  BlockDataSample(int fileSize, int blockSize, HetimaData data) {
    _info = new BitfieldSample(fileSize ~/ blockSize + (fileSize % blockSize == 0 ? 0 : 1));
    _data = data;
    _blockSize = blockSize;
    _fileSize = fileSize;
  }

  bool operator [](int idx) => _info[idx];
  int get length => _info.length;
}

```

ブロックごとにデータごとにも書き込み、読み込みの機能を追加します。
```
  Future<WriteResult> writeBlock(int index, List<int> data) async {
    WriteResult ret = await _data.write(data, index * _blockSize);
    _info[index] = true;
    return ret;
  }

  Future<ReadResult> readBlock(int index) async {
    int start = index * _blockSize;
    int end = (start + _blockSize > _fileSize ? _fileSize : start + _blockSize);
    return _data.read(start, end - start);
  }
```


これで完成です。


