# TrackerはHttpサーバ
<hr>
<br>

* **TrackerはHttpサーバ**

<br>

* **Get リクエストでデータ**

<br>

<hr>

Tracker は Httpサーバーです。皆さんがいつも利用しているイ
ンターネットのからサイトを表示するのと同じルールで動作し
ています。

例えば、インターネットで調べ物をしたい時に、Googleを利用
すると思います。ChromeなりFirefoxなり、IEなどを利用し
て、「http://www.google.com 」にアクセスします。すると、検索ワードを入力するためのページが表示されます。

Trackerもそれと同様の仕組みで動作しています。異なるのは、
人が見やすいように加工されたHtml形式のページを渡す変わり
に、Bencodingでエンコードされたバイナリーデータが渡すところです。

<hr style="page-break-before: always;">

## Getリクエストで依頼をだす

Tracker では、Getリクエストを利用して、データを配信してい
るPeerの一覧を取得はます。Getリクエストは、URLの末端
に、「?xx=yyy&mm=nnn」といった文字列を付与したもので
す。Googleなどの検索エンジンで検索した後、アドレスを確認してみてください。例えば、androidと検索した場合、「?
q=android&oq=android」といった文字列が追加されていると思
います。Trackerも同様の仕組みで、依頼をだします。

## Httpサーバーを作成しよう

TrackerがHttpサーバーである事がわかったところで、 Http サ
ーバーを作成してみましょう。Dart言語では、簡単にHttpサー
バーを作成する事ができます。
まずは、ブラウザーからGetリクエストを受け取った時にHello
と表示してみます。

```
{
  HttpServer.bind(address, port).then((io.HttpServer server) {
    server.listen((HttpRequest request) {
      request.response.write("hello");
      request.response.close();
    });
  });
}
```

といった感じで書けます。Getリクエストで渡された値を知り
たい場合には、「request.uri.queryParameters」として、確認
できます。例えば、「
test」というキーで渡された値を相手に返すコードは以下のよ
うに書けます。

```
{
  HttpServer.bind(address, port).then((io.HttpServer server) {
    server.listen((HttpRequest request) {
      Map<String, String> parameter = request.uri.queryParameters;
      request.response.write("hello"+ parameter[“test”]);
      request.response.close();
    });
  });
}
```

これで、Getリクエストを扱う事ができるようになりました
た。後は、このリクエストに応じて、Peerの一覧、つまりは、
今までリクエストしてきた端末のアドレス等を渡せば、簡易の
Trackerサーバーが完成します。


