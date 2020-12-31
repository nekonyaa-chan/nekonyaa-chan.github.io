---
title: "Visual Studio Code でMingw を使う方法"
date: 2020-12-31T23:11:51+09:00
draft: false
author: "312k"
---
# はじめに
当投稿ではMingw-w64にPATHがデフォルト状態で通っていることを前提としています。またセットアップには[Visual Studio Code](https://code.visualstudio.com/)がインストールしてください。
またユーザー名は"まぞく"としてありますのでお使いの環境に合わせて読み替えてください。
# 拡張機能のダウンロード
まずはCtrl+Shift+xと入力し、C/C++拡張機能をダウンロードしてください。

![](/c.png)

# セットアップ
File→Open Folderを選択して任意のフォルダを選択してください。
そして、File→New Fileでファイルを作成し、File→Save ASで任意の名前で保存してください(ここではmain.cとしました)

テキスト入力欄に
```
#include<stdio.h>

int main(){
    printf("こんにちは！世界");
}
```
と入力してください。
![](/main.c.png)
# 拡張機能の設定
F1キーでuiと入力し、C/C++; Edit Configurations(UI)を選択してください。すると以下の画像のように開かれます。
![](/configurationC.png)
そこのコンパイラパスでC:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/gcc.exeを入力するか右の逆三角からコンパイラパスを選択してください。

そうするとInteliSenseモードでエラーが出てくるので、プルダウンよりgcc-x64を選びます。
![](/intelligcc.png)

# コンパイル
コンパイルして実行するためCtrl+F5→Enter→Enter
するといろいろな画面が表示され最終的にはlaunch.jsonが出ます。
![](/launchjson.png)

そのタブを閉じmain.cに戻ります。
下のタブでTERMINALを選択するとこんにちは!世界と表示されます。
![](/helloworld.png)

これでvscodeでC言語が使えるようになりました。

# まとめ
[公式サイト](https://code.visualstudio.com/docs/languages/cpp#_debugging)には様々な方法が紹介されているのでご参照ください。

# 参考にしたサイト
https://qiita.com/OcoToOo/items/f1d0a125327f5659ad52
https://code.visualstudio.com/docs/languages/cpp#_debugging