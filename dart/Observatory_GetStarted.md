# Getting Started with Observatory
https://www.dartlang.org/tools/observatory/get-started.html



#### [Contents]
* Get Observatory
* Start Observatory
  * Standalone apps from the command line
  * Web apps
* Observatory UI
* VM screen
* What next?

```
ObservatoryはFreeでGETできます。
https://www.dartlang.org/downloads/ から取得できます。
issueやrequestは、 http://dartbug.com/new で受け付けています。
```

### Get Observatory
Observatory は Dart SDK 中の toolsのひとつです。https://www.dartlang.org/downloads/　からダウンロードできます。

Dartでアプリケーションをつくる場合2つの方法があります。 ひとつは、standalone applications として動作させる方法です。もうひとつは、web 

applications　として動作させる方法です。 standalone appsの場合、 command line から Observatory を使う事ができます。

browser-based apps の場合、command line からDartium 上でアプリを起動させる事で、Observatory を利用できます。

つまり、どちらの場合でもObservatoryを利用する事ができます。


### Start Observatory
standaloneか web appかによって、 Observatory 有効にする方法は異なります。
しかし、UIについてだいだい同じです。

##### Standalone apps from the command line

Observatoryを有効にするには、dartvm を起動する時にオプションを追加します。
例えば、
```
dart --observe <script>.dart
```

次に、お好みのブラウザーで http://localhost:8181 にアクセスしてください。Observatory UI　が表示されます。


デフォルトでは、このサービスは同コンピュータからのみ許可されています。
この制限は、ssdhでport foward するなどして回避することもできます。

```
ssh -L8181:127.0.0.1:8181 user@targetmachine
```

LinuxやMacを使っている場合は、IGQUITメッセージを送る事で、この機能を後から有効にすることもできます。Observatory UI へのアドレスは、コンソール上(STDOUT)に表示されます。

```
$ ps ax | grep dart
<pid> pts/61   Sl+    0:01 dart example.dart
$ kill -s SIGQUIT <pid>
Observatory listening on http://127.0.0.1:<port>
```
