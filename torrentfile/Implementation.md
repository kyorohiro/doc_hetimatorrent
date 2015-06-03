# Bencoding
<hr>
<br>


* **Bencodingはパースしやすい構造**

<br>

* **BNFから機械的にコードを抽出**


<br>
<hr>

## 見慣れたデータ構造に落とす

Dart言語には、Bencoding のデータ構造をもっています。今回
は、Bencdoingのデータを読み込み、Dart言語のデータ構造に
落とこみます。

Bencodeの文字列は、Dart言語ではcore.Stringで表せます。
Bencodeの数字は、Dart言語では、core.int で表せます。 Bencodeのリストは、Dart言語のcore.List、Bencodeの辞書は、Dart言語ではMapで表現できます。


## Bencodeはパースしやすい構造

Bencode はパースが容易な構造になっています。なぜならば、
どのデータなのかが、最初の一文字目で判別する事ができるか
らです。


“i” ならば、整数。 “0-9” ならば、文字列、”l” ならばリス
ト、”d” ならば辞書 といった感じです。
これを、コードに直すと、以下のような感じになります。

```
 Object decodeBenObject(data.Uint8List buffer) {
 if( 0x30 <= buffer[index] && buffer[index]<=0x39) {//0-9
 return decodeBytes(buffer);
 } else if(0x69 == buffer[index]) {// i
 return decodeNumber(buffer);
 } else if(0x6c == buffer[index]) {// l
 return decodeList(buffer);
 } else if(0x64 == buffer[index]) {// d
 return decodeDiction(buffer);
 }
 throw new ParseError("benobject", buffer, index);
 }
```

書見ての通り、先頭の値に応じて処理が分岐しているだけで
す。後は、おのおのデータ構造とみなして、変換してあげれば
完成です。


