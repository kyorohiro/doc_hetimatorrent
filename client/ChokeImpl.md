# Chokeを実装
* アルゴリズムとして定義することで検証可能にする
* PeerInfoクラスを定義する
* UnchokeしたPeerからChokeするPeerを選択する
* ChokeしたPeerから、UnchokeするPeerを選択する

<hr>

Chokeを実装してみましょう。Torrentクライアントの通信部分やアプリケーションの部分と、アルゴリズムの部分は分離する事が望ましいです。

このアルゴリズムじたいをテスト可能にするためです。アプリケーション層とまぜると、アプリケーションの操作としてテストしなくては検証できません。
通信部分の一部としてしまっては、通信機能としてテストする事になります。通信部分を利用しなくてはテスト出来ない状態になります。

アルゴリズムとして定義する事で、検証が容易になります。


### PeerInfoクラスを定義する

Chokeアルゴリズムに必要な要素を考えて、PeerInfoというクラスを定義しました。

PeerInfoには、Peerのステータスの一覧が定義しました。


```
abstract class TorrentClientPeerInfo {
  static const int STATE_NONE = 0;
  static const int STATE_ON = 1;
  static const int STATE_OFF = 2;
  String ip = "";
  int port = 0;
  List<int> get peerId;
  int get downloadedBytesFromMe;
  int get uploadedBytesToMe;
  int get chokedFromMe;
  int get chokedToMe;
  int get interestedToMe;
  int get interestedFromMe;
  bool get amI;
  bool get isClose;
  int get uploadSpeedFromUnchokeFromMe;
}
```
```
class TorrentClientPeerInfos {
  List<TorrentClientPeerInfo> _peerInfos = [];
  List<TorrentClientPeerInfo> get rawpeerInfos => _peerInfos;
  int get numOfPeerInfo => _peerInfos.length;

  TorrentClientPeerInfos() {}

  List<TorrentClientPeerInfo> getPeerInfos(Function filter) {
    List<TorrentClientPeerInfo> t = [];
    for (TorrentClientPeerInfo x in _peerInfos) {
      if (filter(x)) {
        t.add(x);
      }
    }
    return t;
  }

  void addPeerInfo(TorrentClientPeerInfo info) {
    _peerInfos.add(info);
  }
}

```

前章で定義したメッセージに対応したものですね。 このListからChokeする、UnchokeするPeerの一覧を返すメソッドを作れば、Choke機能を実装したことになります。



### UnchokeしたPeerからChokeするPeerを選択する

```
List<TorrentClientPeerInfo> extractChokePeerFromUnchoke(TorrentClientPeerInfos infos, int maxOfReplace, int maxOfUnchoke) {
    List<TorrentClientPeerInfo> unchokedPeers = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.chokedFromMe == TorrentClientPeerInfo.STATE_OFF && info.amI == false);
    });
    List<TorrentClientPeerInfo> alivePeer = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.amI == false);
    });

    List<TorrentClientPeerInfo> ret = [];
    if (alivePeer.length > maxOfUnchoke) {
      unchokedPeers.sort((TorrentClientPeerInfo x, TorrentClientPeerInfo y) {
        return x.uploadSpeedFromUnchokeFromMe - y.uploadSpeedFromUnchokeFromMe;
      });

      int numOfReplace = alivePeer.length - maxOfUnchoke;
      numOfReplace = ((maxOfReplace < numOfReplace) ? maxOfReplace : numOfReplace);
      for (int i = 0; i < numOfReplace && i < unchokedPeers.length; i++) {
        ret.add(unchokedPeers[i]);
      }
    }
    return ret;
  }
```

### ChokeしたPeerから、UnchokeするPeerを選択する
```
  List<TorrentClientPeerInfo> extractUnchokePeerFromChoke(TorrentClientPeerInfos infos, int numOfUnchoke) {
    List<TorrentClientPeerInfo> unchokeInterestedPeers = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.interestedToMe == TorrentClientPeerInfo.STATE_ON && info.chokedFromMe == TorrentClientPeerInfo.STATE_ON && info.amI == false);
    });
    List<TorrentClientPeerInfo> unchokeNotInterestedPeers = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.interestedToMe != TorrentClientPeerInfo.STATE_ON && info.chokedFromMe == TorrentClientPeerInfo.STATE_ON && info.amI == false);
    });
    unchokeInterestedPeers.shuffle();
    List<TorrentClientPeerInfo> ret = [];
    for (int i = 0; i < unchokeInterestedPeers.length && ret.length < numOfUnchoke; i++) {
      ret.add(unchokeInterestedPeers[i]);
    }
    for (int i = 0; i < unchokeNotInterestedPeers.length && ret.length < numOfUnchoke; i++) {
      ret.add(unchokeNotInterestedPeers[i]);
    }
    return ret;
  }
```

### Choke、UnchokeするPeerを選択する

今までに作成したメソッドを合わせことで実現できます。
```
  TorrentAIChokeTestResult extractChokeAndUnchoke(TorrentClientPeerInfos infos, int maxUnchoke, int maxReplace) {
    List<TorrentClientPeerInfo> unchokeFromMePeers = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.chokedFromMe == TorrentClientPeerInfo.STATE_OFF && info.amI == false);
    });
    List<TorrentClientPeerInfo> aliveAndNotChokePeer = infos.getPeerInfos((TorrentClientPeerInfo info) {
      return (info.isClose == false && info.amI == false && info.chokedFromMe != TorrentClientPeerInfo.STATE_OFF);
    });
    List<TorrentClientPeerInfo> chokePeers = extractChokePeerFromUnchoke(infos, maxReplace, maxUnchoke);
    for (TorrentClientPeerInfo info in chokePeers) {
      aliveAndNotChokePeer.remove(info);
    }
    int n = unchokeFromMePeers.length - chokePeers.length;
    List<TorrentClientPeerInfo> unchokePeers = extractUnchokePeerFromChoke(infos, maxUnchoke - n);
    for (TorrentClientPeerInfo info in unchokePeers) {
      aliveAndNotChokePeer.remove(info);
    }

    TorrentAIChokeTestResult ret = new TorrentAIChokeTestResult();
    ret.choke.addAll(chokePeers);
    ret.choke.addAll(aliveAndNotChokePeer);
    ret.unchoke.addAll(unchokePeers);
    return ret;
  }
```


