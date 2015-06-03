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


