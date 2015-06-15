# Bencodingの実装
<hr>
<br>


* **Bencodingはパースしやすい構造**

<br>

* **BNFから機械的にコードを抽出**


<br>
<hr>

## 見慣れたデータ構造に落とす

Benodingを実装してみましょう。Bencodingで扱えるデータは、Number, String, List, Dictionary でした。これらを、Dartから扱えるようにしましょう。

具体的には、Dartでサポートされているデータ構造に変換していきます。Dartのnumber,Uint8Array,List,Mapへ変換していきます。

BencodeのNumberは、Dartのnumで表現できます。BencodeのStringは、UInt8Arrayで表現できます。Stringでないところは少しトリッキーです。Bencodeでは、バイト配列を扱うこともあります。Unicodeでは使えないデータもStringとして扱うことになりまいから、Stringでなく、UInt8Arrayを利用します。Bencodeのリストは、Dart言語のcore.Listで、Bencodeの辞書は、Dart言語ではMap<String,Object>で表現できます。


具体的には、以下のようなAPIを考えます。

```
class Bencode {
  static Uint8List encode(Object obj);
  static Object decode(List<int> buffer);
}

```

もちろん、Bencodeの実装は見慣れた形式に落とす方法だけではありません。BencodeのStringはBenString、ListはBenList、DictionaryはBenDict、といった方法で表現することも可能です。

Dartの Map は、BencodeのMapよりも扱える範囲が広いですから、Dartのデータ構造から、Bencodeのデータ構造へ変換ができないパターンがあります。例えば、以下のような形式はBencodeでは表現できません。

```
int // 0.1 といった少数点を含む場合
    // -1といつたマイナス値を含む場合

Map<int, String> // keyがStringでない場合

```

このような制約がある事を、BenString, BenList といった構造を定義する事でAPIとして表現する事ができます。

今回は「見慣れた構造に落とす」という方針で設計しました。


<hr style="page-break-before: always;">

## Bencodeはパースしやすい構造

Bencode はパースが容易な構造になっています。なぜならば、
どのデータ構造なのかが、最初の一文字目で判別する事ができるか
らです。

“i” ならば、整数。 “0123456789” のどれかならば、文字列、”l” ならばリスト、”d” ならば辞書 といった感じです。

これを、コードに直すと、以下のような感じになります。

```
int index = 0;
Object decodeBenObject(Uint8List buffer) {
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

見ての通り、先頭の値に応じて処理が分岐しているだけで
す。後は、おのおのデータ構造とみなして、変換してあげれば
完成です。


## More Parser

Torrentクライアントを実装するにあたり、さまざまなプロトコルを実装していきます。
プログラム言語や自然言語ほど、複雑ではないので容易に解析可能です。
このタイミングでBNFで定義された文法を解析する方法について解説します。

### BNF から機械的にパーサーを書く

BNFで書かれた構文は機械的にパーサーを書く事ができます。
```
• 規則名をルソッドにする。
• ルールに文字列がでてきた場合、一致するかチェックする
• ルールに規則がでてきた場合、そのメソッドを呼び出す
• ルールに違反かる場合は、Exception をスローする。
```

プログラム言語、自然言語といったものは、もう少し工夫が必
要ですが、今回は、基本的なルールを守るだけで実装できま
す。もう少し詳細な情報が欲しい場合は、「Language Implementation Patterns 」という本を読む事をお勧めです。


具体的に、Bencode 用のパーサーを作成しながら、見ていきま
しょう。
例えば、Dictionary は以下のように書けます。

```
Map decodeDiction(data.Uint8List buffer) {
  Map ret = new Map();
  if(buffer[index++] != 0x64) {
    throw new ParseError("bendiction", buffer, index);
  }
  ret = decodeDictionElements(buffer);
  if(buffer[index++] != 0x65) {
    throw new ParseError("bendiction", buffer, index);
  }
  return ret;
}
```

BNFと一対一の関係がある事が解ると思います。Dictionary は
27
「 “d” bendictionelements “e”」と文法で表現されます。ですか
ら、”d”という文字か確認。　dictionelements のメソッドを呼
び出す。“e”という文字か確認する。　ルール通りです。


## テストを書く

テストを書きながら、パーサーを書いていきましょう。まずは
整数からです。

```
unit.test("bencode: number", () {
  num ret = hetima.Bencode.decode(toBuffer("i1024e"));
  unit.expect(1024, ret);
});
```

このテストを満たすように、パーサーを書きます。bencodingで整数は、
「“i” *(0-9) “e”」と書けます。なので、これもル
ールに従って以下のように書けます。

```
num decodeNumber(data.Uint8List buffer) {
  if(buffer[index++] != 0x69) {
    throw new ParseError("bennumber", buffer, index);
  }
  int returnValue = 0;
  while(index<buffer.length && buffer[index] != 0x65) {
    if(!(0x30 <= buffer[index] && buffer[index]<=0x39)) {
      throw new ParseError("bennumber", buffer, index);
    }
    returnValue = returnValue*10+(buffer[index++]-0x30);
  }
  if(buffer[index++] != 0x65) {
    throw new ParseError("bennumber", buffer, index);
  }
  return returnValue;
}
```

数字を取り出す部分が少し複雑ですが、無事テストが通るコー
ドがかけました。この調子で、文字列、リスト、と同じように
テストしながら、作成すれば完成です。kyorohiroが作成した物
は、以下にあります。「https://github.com/kyorohiro/dart_hetimatorrent」 事の顛末を知りたい方は参照してください。







