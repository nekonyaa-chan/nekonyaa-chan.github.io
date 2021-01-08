---
title: "BrixelCTF-312k write up"
date: 2021-01-02T15:14:39+09:00
author: "312k"
categories : [ "Writeup" ]
tags : [ "CTF", "Writeup","312k" ]
---
# はじめに
年末から年始にかけてBrixelCTFに参加しました。
[解いた問題](https://ctf.brixel.space/users/104)は16問で獲得スコアが212ポイント、順位は830人中317位でした。
初めてwrite upを書いてみます。
## OSINT
### A quick search
この問題は画像からなんという名前のタワーなのか調べる問題です。[Google画像検索](https://www.google.co.jp/imghp?hl=ja)で写真のタワーを検索しましたところ、[Eben_Ezer](https://en.wikipedia.org/wiki/Eben-Ezer_Tower)がFlagでした。
## Reverse engineering / cracking
### Cookieee!
この問題はいわゆる[クッキークリッカー](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC)で10,000,000(一千万回)のクリックでFlagが表示されます。ですのでうさみみハリケーンを使ってメモリの書き換えをしました。
[Vector](https://www.vector.co.jp/soft/win95/prog/se375830.html)からうさみみハリケーンをダウンロードし、問題のファイルを実行し、UsaMimi64を起動して、リスト更新で問題ファイルを選択します。![うさ耳ハリケーンの選択](../2021-01-02プロセスの選択.png)
そしてAlt+Fで範囲検索を起動します。確保・記録して0を通常検索実行します。なぜ0なのかというと現在のClicks回数が0であるということはメモリのどこかに保存されているからです。![メモリの中0を検索](../2021-01-02メモリの中0を検索.png)
残り一つになるまで値を増やします。写真では13個クッキーをクリックしました。![通常検索実行から発見](../2021-01-02通常検索実行から発見.png)
するとアドレスが選択されるので適当に値を設定して、Flagが発見されました。![cookieflagget](../2021-01-02cookieflagget.png)

### no peeking!
この問題は[.NET Framework](https://ja.wikipedia.org/wiki/.NET_Framework)で作られた問題からFlagを探す問題です。デコンパイラが[Microsoft ストア](https://www.microsoft.com/ja-jp/p/ilspy/9mxfbkfvsq13?activetab=pivot:overviewtab)であります。問題のファイルを開きその中のshowFlag():objectに答えが隠されていました。![ILSpy](../2021-01-02ILSpy.png)
## Old tech
### Goodbye old friend
2020年度いっぱいで[サポートが終了した](https://www.adobe.com/jp/products/flashplayer/end-of-life.html)Flashplayerの問題です。<br>
ファイルを実行するとGoodbye old friendがずっと往復しています。<br>
[窓の杜](https://forest.watch.impress.co.jp/library/software/hugflash/)からFlashplayerを展開するツールを使います。
実行してドラックアンドドロップで展開されるのでhugflashディレクトリにgoodbyeフォルダが作成されます。その中のgoodbye.logにFlagが隠されていました。![hugflash](../2021-01-02hugflash.png)

## Cryptography
### flawed
この問題はパスワードハッシュからパスワードを求める問題です。<br>**32文字**のmd5でハッシュされているようなのです。そのためググりハッシュリストを保存しているサイトを使います。よって答えはnotsecureとなりました。

### shit(実際の問題タイトルとは異なります)
この問題は合字を使った問題です。<br>ニコニコ大百科で「たすけて」と調べると分解された内容が出てきます。ブラウザによって異なるため今回は実際には合字は使いません。<br>
この問題は[tt6-mzn](https://ctftime.org/team/141534)のアドバイスによってわかりました。末尾に=があることからBase64でエンコードされたものと考えました。[Base64デコード](https://tool-taro.com/base64_decode/)サイトでデコードするとバイナリが出てきます。[バイナリをテキストに変換するサイト](https://rakko.tools/tools/75/)それにバイナリからテキスト変換するとFlagが出てきます。
## Internet
### Hidden Code
この問題はかの有名のコナミコマンドをすると答えが出てきます
### robotopia
この問題は検索エンジンのクローラーの気分になってとく問題です。<br>たいていのサイトにはrobots.txtがあります。よって問題のサイトのURLに/robots.txtを追加して答えが現れます。
### Discord
この問題のFlagはDiscordの今回のCTFのチャンネル内にありました。
### login1
この問題はパスワード自体がFlagという問題です。<br>Ctrl+Shift+Iで開発者ツールを開き要素を確認するとjavascriptでパスワードと比較している部分があります。よってif文の条件がFlagです。
### Browsercheck
この問題はクローラーのユーザーエージェントと比べているようです。<br>よって開発者ツールでユーザーエージェントを偽装すると正解です。
### Flat earth
この問題はまず問題の管理者がログインするところを探すところから始まります。<br>てきとうにマウスを動かすか開発者ツールの要素からログインする画面に移ります。<br>
そして管理者ログインをします。この問題は[SQLインジェクション](https://www.ipa.go.jp/security/vuln/websecurity.html)を用いる問題です。ユーザー名に
```sql
' OR 1=1--
```
と入力するとFlagが現れます。<br>
このことについては[ipaが解説しているサイト](https://www.ipa.go.jp/security/awareness/vendor/programmingv2/contents/502.html)にあります。

## Steganography
### Doc-ception
この問題は[tt6-mzn](https://ctftime.org/team/141534)の助言によって解けました。<br>
問題ファイルにはdocxの中にdocxが入れ子のようになっているという問題です。7-zipなどで解凍するとflag.txtが見つかるので答えがわかります。
### Scan me
この問題はQRコードやバーコードを解いていく問題です。<br>
まずはQRコードの中にQRコードが隠されているので開きます。QRコードは右下以外3辺に四角があります。
そのあとはただバーコードの内容を送るだけです。アプリによっては対応バーコードが少ないものもあるので適切に切り替えてください。
### Rufus the vampire cat
この問題は写真の中に[steghide](http://steghide.sourceforge.net/)されたテキストを探す問題です。<br>steghideには多くのデコーダーがありますが、今回は[Steganographic_Decoder](https://futureboy.us/stegano/decinput.html)を用いてデコードしました。問題の画像ファイルを選択し**text/plain**と入力し、デコードするとFlagが得られました。

# おわりに
以上が私が解いた問題です。初めて知ったことが多くて勉強になりました。またチーム内でアドバイスができたのはよかったです。