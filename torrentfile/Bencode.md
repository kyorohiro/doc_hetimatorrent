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



