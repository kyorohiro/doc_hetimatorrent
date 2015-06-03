# Bencoding
<hr>
<br>


* **Torrentファイルはbencodingて書かれている。**

<br>

* **Bencodingは、Integer、String、List、Dictionaryを扱える。**


<br>
<hr>

## Torrentファイルは Bencoding 

Torrentファイルは、bencoding という形式で書かれています。
Torrentファイルに記載されている事を読み解くためには、
bencding/bencode を解釈できるようにならなくてはなりませ
ん。
まずは、Bencosingのパーサーを書いていきましょう。
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



#### 文字列を扱える

Bencode で文字列は、「<文字の長さ> “:” <文字>」という形式
で書かれています。例えば、「torrentという文字列は、
bencodeでは、「7:torrent」と書く事ができます。
もうひとつ、bencode の文字列は、バイトデータとして扱われ
る事もあります。IPアドレスのバイト表示や、Hash値などの非
アスキーな範囲のデータなども、本形式で扱います。
例えば、日本語で「アイ」はSJISで表現すると「0x83, 0x41,
0x83, 0x43」の4バイトで表現できます。この場合、Bencode
では、「4:アイ」と表記できます。

```
oden
　　4:oden
オデン SJIS
　　6:オデン
SHA1Hashデータ
 20:<SHA1 Hashデータ>
```

#### 整数を扱える

整数は0より大きな値を表現するデータです。「“i” *[0-9] “e”」
という形式で表すことができます。例えば、1024は「i1024e」
と書くことができます。
ファイルのサイズ、ポート番号、といった、数字で表現できる
ものに利用します。

```
2
 i2e
1024
 i1024e
 65526
 i65526e
```


#### リストを扱える

リストはデータ構造のひとつです。順序ありのデータを保持す
る事ができます。例えば、IPアドレスの一覧、ファイルの一
覧、といったものを表現するのに使用します。

```
あいうえお、かきくけこ
 l12:あいうえお12かきくけこe
128、 100、500
 li128ei100ei500ee
```
