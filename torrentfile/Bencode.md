# Bencoding
<hr>
<br>


* **Torrentファイルはbencodingて書かれている。**

<br>

* **Bencodingは、Integer、String、List、Dictionaryを扱える。**


<br>
<hr>

## Torrentファイルは Bencoding 

Torrentファイルは、bencode という形式で書かれています。
Torrentファイルに記載されている事を読み解くためには、
bencode を解釈できるようにならなくてはなりません。まずは、Bencode のパーサーを書いていきましょう。


Bencodingは、文字列、整数、辞書、リストの4つのデータを
扱うことができます。

```
beninteger : “i” [0-9]* “e”
benstring : <string length> “:” <bytes array string>
string length : [0-9]*
bendiction : “d” <dictelements> “e”
benlist : “l” <listelements> “e”
benobject : beninteger | benstring | bendiction | benlist
listelements : benobject ( benobject)*
dictelements : benstring benobject (benstring benobject)*
```
そして、上記のようなフォーマットで書かれています。


<hr style="page-break-before: always;">

#### 文字列(String)を扱える

Bencode で文字列は、「<文字の長さ> “:” <文字>」という形式
で書かれています。例えば、「torrentという文字列は、
bencodeでは、「7:torrent」と書く事ができます。

もうひとつ、bencode の文字列は、バイトデータとして扱われ
る事もあります。IPアドレスのバイト表示や、Hash値などの非
アスキーな範囲のデータなども、本形式で扱うことができます。

例えば、日本語で「アイ」はSJISで表現すると「0x83, 0x41,
0x83, 0x43」の4バイトで表現できます。この場合、Bencode
では、「4:アイ」と表記できます。


###### oden を、Bencodeで表現する
```
4:oden
```

###### おでん(SJIS)を、Bencodeで表現する
```
6:オデン
```

###### SHA1Hashデータを、Bencodeで表現する
```
 20:<SHA1 Hashデータ>
```

#### 整数(Number)を扱える

整数は0より大きな値を表現するデータです。「“i” *[0-9] “e”」
という形式で表すことができます。例えば、1024は「i1024e」
と書くことができます。

ファイルのサイズ、ポート番号、といった、数字で表現できる
ものに利用します。

###### 2 を、Bencodeで表現する。
```
i2e
```
###### 1024を、Bencodeで表現する
```
 i1024e
```

###### 3.14を、Bencodeで表現する
「3.14」BencodeのNumber表見する事ができません。扱えるのは整数値だけです。



#### リストを扱える

リストは複数のデータを順序ありで保持することができます。
Bencodeでは「"l" <listelements> "e"」という形式で表すことができます。

例えば、所持してる本、「てんで性悪キューピッド」「幽☆遊☆白書」「レベルE」「HUNTER×HUNTER」といった本の一覧は、個別に
Bencodeで表現すると、「22:てんで性悪キューピット」、「12:幽☆遊☆白書」、「7:レベルE」、「13:HUNTER×HUNTER」となります。

これをListで「l22:てんで性悪キューピット12:幽☆遊☆白書7:レベルE13:HUNTER×HUNTERe」とまとめる事ができます。


###### 「あいうえお、かきくけこ」をBencodeで表現する
```
 l12:あいうえお12かきくけこe
```
###### 「128、 100、500」を、Bencodeで表現する
```
 li128ei100ei500ee
```

### 辞書
辞書はキーワードとデータを関連づけて保持する事ができます。「 “d” <dictelements> “e”」という形式で表現できます。

例えば、RPGゲームの主人公のパラメータとして、名前、レベル、習得した魔法、といったものが設定されているとしましょう。

名前を勇者、レベルが1、習得した魔法をハリトとカティノとしてみましょう。それぞれBencodeで、「4:名前」「4:勇者」「6:レベル」「i1e」「4:魔法」、「l6:ハリト8:カティノe」と表現できます。
これらをまとめて、「d4:名前4:勇者6:レベルi1e4:魔法l6:ハリト8:カティノee」と辞書で表見できます。

###### levelが13で、nameが山田、Bencodeで表現する
``
di5:leveli13e4:name4山田e
```


##  今後の表記について

今後、データ構造を表す場合は、以下の表記を利用します。

リスト
「li512e4:teste」は、[512,"test"]

辞書
「d5:leveli13e5magic6halitoe」は {level:13,magic:"halito"}



