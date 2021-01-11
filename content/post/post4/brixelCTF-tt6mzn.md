---
title: "BrixelCTF-tt6mzn write up"
date: 2021-01-06T16:54:19+09:00
author: "tt6mzn"
categories : [ "Writeup" ]
tags : [ "CTF", "Writeup", "tt6mzn" ]
---
# はじめに
年末から年始にかけてBrixelCTFに参加しました。
結果は[こんな感じ](https://ctf.brixel.space/users/109)。
Internet・Programming・Cryptographyを中心に11問を解くことができました。
チームとしてはスコアが212ポイント、順位が830人中317位でした。

# Programming
## Are you fast enough?
urlを開くと次のような画面が表示されます。![Are you fast enough?](../2021-01-06areyoufastenough.png)
「毎回変わるランダムな文字列を入力ボックスに入力してenterボタンを押す」までを1秒以内にやれ、とあります。
もちろん普通に要請にこたえることはできないので、プログラムを書きます。
[selenium](https://www.selenium.dev/documentation/ja/)というフレームワークを使えばこれを実現できます。
seleniumはブラウザをプログラムで操作するツールです。
私はchromeとpythonを使って実装しました。
seleniumのインストールのほかに[ChromeDriver](http://chromedriver.chromium.org/downloads)をインストールする必要があったのでちょっとめんどくさい。
書いたコードは以下です。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

def main():
    driver = webdriver.Chrome(executable_path='./driver/chromedriver.exe')
    driver.get('http://timesink.be/speedy') # urlを開く

    ans = driver.find_element_by_id("rndstring") # ランダム文字列を取得
    inputbox = driver.find_element_by_name("inputfield") # 入力ボックスを取得
    inputbtn = driver.find_element_by_id("submitbutton") # ボタンを取得
    inputbox.send_keys(ans.text) # 入力欄に文字列を入れる
    inputbtn.click() # ボタンをクリック
    time.sleep(60) # 待機

main()
```

find_element_by_～()という関数で文字列やボタンを取得し、その文字列を取り出したり、ボタンをクリックしたりすることができます。
実行が終わるとウィンドウが自動で閉じてしまうので、すべての処理が終わったら60秒待機するようにしています。
実行するとフラグが表示されました。

## Keep walking...
変数X, Y, answerをそれぞれ初期値1に設定し、
1. answerの値を X * Y + answer + 3 に更新
2. Xをインクリメント
3. Yをインクリメント

という処理をXが525になるまで繰り返した時のanswerの値を求めなさい。という問題です。
手動でも解けないことはなさそうですがプログラムを書きます。

```python
def main():
    x = 1
    y = 1
    ans = 1

    while(x <= 525):
        ans = x * y + ans + 3
        x += 1
        y += 1
    
    print(ans)

main()
```

実行すると`48373851`と出力されます。これが答えになっています。

## Quizbot
与えられたurlを開くと、次のような画面が表示されます。![quizbot-1](../2021-01-06quizbot-1.png)
表示されているようなクイズに1000回正解すればいいようです。試しに適当な文字を入力して`answer`ボタンを押すとこうなります。![quizbot-2](../2021-01-06quizbot-2.png)
scoreは0のままですが、間違えてもそのまま次の問題へ行けるようです。さらに上部には前の問題の答えが表示されています。また、表示されるクイズの順番はいつも同じです。
以上より、とりあえず最初に1000回間違えて正しい答えを表示・記録し、それを順番に入力しなおしていけばよいと分かります。
もちろんプログラムを書いてこれを実現します。前述の「Are you fast enough?」でも使用したseleniumをここでも使用します。  
まずは1000回間違えて答えを記録していくプログラムです。
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

def main():
    driver = webdriver.Chrome(executable_path='./driver/chromedriver.exe')
    driver.get('http://timesink.be/quizbot')
    ans = []
    for i in range(1000):
        box = driver.find_element_by_id("insert_answer")
        box.send_keys("`")
        btn = driver.find_element_by_name("submit")
        btn.click()
        s = driver.find_element_by_id("answer").text
        ans.append(s)
    with open("quizbot_ans.txt", 'w') as f:
        f.write("|".join(ans))

main()
```
`ans`という配列に答えを記録し、最後に`quizbot_ans.txt`というテキストファイルに`|`区切りで答えを出力しています。

次は記録した答えを入力していくプログラムです。
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

def main():
    with open('quizbot_ans.txt', 'r') as f:
        ans = f.read().split("|")
    
    driver = webdriver.Chrome(executable_path='./driver/chromedriver.exe')
    driver.get('http://timesink.be/quizbot')
    for i in range(1000):
        box = driver.find_element_by_id("insert_answer")
        box.send_keys(ans[i])
        btn = driver.find_element_by_name("submit")
        btn.click()

main()
time.sleep(180)
```
実行に時間はかかりますが、15分ほどでフラグをゲットすることができました。

# Old tech
## punchcard
パンチカードの画像が与えられ、それを読み解く問題です。パンチカードにもいくつか種類があるようですが、問題のパンチカードは80列であることから、IBMの80欄カードであると推測しました。[wikipedia](https://ja.wikipedia.org/wiki/%E3%83%91%E3%83%B3%E3%83%81%E3%82%AB%E3%83%BC%E3%83%89#IBM%E3%81%AE80%E6%AC%84%E3%82%AB%E3%83%BC%E3%83%89%E3%81%A8%E6%96%87%E5%AD%97%E3%82%B3%E3%83%BC%E3%83%89)の対応表から一列ずつ読み取っていくとフラグが得られました。

# Cryptography
## Sea code
モールス信号を解読する問題です。`._ _... _._.`のようにモールス信号を入力すると対応するアルファベットを表示してくれる[サイト](https://l08084.github.io/morse-code-translate-website/)を活用しつつ、音声ファイルをスローで再生しながら愚直に解読していきました。

## Don't be salty
パスワードのハッシュ値とソルトという文字列が与えられて、元のパスワードを復元しろ。という問題です。
ただし前提として、
* ソルトはパスワードの末尾に加えられたこと
* パスワードは5文字で、小文字であること

が仮定されています。

ソルトとは、パスワードに付け加えられるランダムな文字列のことです。
ソルトを付け加えてからハッシュ関数にかけることで、レインボーテーブルなどを用いた事前計算による攻撃を防ぐことができます。
したがって通常ソルトを使ってハッシュされた値から平文を求めることは困難です。
しかし今回は、パスワードの長さが5文字であることが事前にわかっています。
この時点でパスワードの候補は`aaaaa`, `aaaab`,..., `zzzzz`までの　26^5 = 11881376通りと大体10^7通りです。
この程度であれば総当たりで元のパスワードを求めることができます。
ハッシュ値が32文字であることから、ハッシュ暗号はmd5と推測できます。

以上をまとめてプログラムを書きます。書いたプログラムは以下です。
```python
import hashlib

def main():
    passwordhash = '2bafea54caf6f8d718be0f234793a9be'
    salt = '04532@#!!'
    alp = 'abcdefghijklmnopqrstuvwxyz'
    for i in alp:
        for j in alp:
            for k in alp:
                for l in alp:
                    for m in alp:
                        s = i + j + k + l + m + salt
                        hs = hashlib.md5(s.encode()).hexdigest()
                        if hs == passwordhash:
                            print(s)
                            exit()

main()
```
なかなか仰々しい見た目をしていますが、一番明示的かなと個人的には思っています(言い訳)。
パスワードの長さが常に5文字なので、5重ループのごり押しをしていますが、もっと文字数の多いパスワードであれば再帰関数を使って実装するとよいでしょう(保険)。

ライブラリとしてhashlibを使っていますが、これは標準ライブラリです。メジャーなハッシュ関数を簡単に使うことができます。
総当たりで生成した文字列の末尾にソルトを付け加えた文字列をmd5でハッシュし、与えられたハッシュ値と一致するかを調べています。

実行すると`brute04532@#!!`と表示されます。これから末尾のソルトを除いた文字列が答えです。

# Internet
## Readme
[brixelCTFのGuide](https://ctf.brixel.space/guide)の下部参照です。
[312k](https://ctftime.org/user/97775)の助言で解きました(申し訳なさがあったりなかったり...)。
## login2
与えられたurlを開くと、ログインフォームっぽい画面が表示されます。
開発者モードでソースを見ると、やや難読化されたjavascriptが見れます。
どうやらソースに書かれた文字列と入力された文字列を6文字ずつ区切って比較し、一致しているか判定しているようです。
すべてのif文をtrueにする文字列を復元するとフラグを得ることができます。
ちなみに何度か登場している`.substring(a, b)`のようなメソッドは、a文字目からb文字目まで(b文字目は含まない)の連続した部分文字列を取り出す機能があります。

## login3
login2の問題と同じように、入力されたパスワードの文字列をif文で比較していますが、今度は直接文字列と比較せず、password.txtというファイルの中身と比較しているようです。
password.txtの中身は問題のurlの末尾の`/index.html`を`/password.txt`と変えることで簡単に見ることができました。
password.txtの中身がフラグになっています。

## login4
login3の問題と同じように、入力されたパスワードの文字列をpassword.txtの中身と比較していますが、今度は読み込んだpassword.txtを`atob()`という関数に入れてから比較を行っています。
調べてみると`atob()`には、Base64でエンコードされた文字列をデコードする機能があるようです。
login3と同じ要領でpassword.txtをのぞくと、Base64でエンコードされたと思われる末尾が`=`の文字列が入っています。
この文字列をBase64デコードすると、フラグを得ることができます。
[312k](https://ctftime.org/user/97775)によれば、入力した文字列をBase64デコードしてくれる[サイト](https://tool-taro.com/base64_decode/)もあるようです。

私はプログラムを書きました。pythonの標準ライブラリでbase64を扱えます。
コードは以下です。
```python
import base64

def main():
    username = b'YWRtaW4='
    print(base64.b64decode(username).decode())
    password = b'YnJpeGVsQ1RGe2V2ZW5fYmFzZTY0X3dvbnRfbWFrZV95b3Vfc2VjdXJlfQ=='
    print(base64.b64decode(password).decode())

main()
```
実行するとフラグが表示されます。

## SnackShack awards
店の人気投票が行われているサイトで不正な投票をして、負けている店に5000票加えろ。という問題です。
urlを開くと各店の画像や説明とともに、投票のためのボタンが用意されています。
最大でも5票しか投票できない仕様です。

結論としては、5000票入れられるようにhtmlファイルを書き換えます。
与えられたurlからページのhtmlファイルをダウンロードするなりして、投票のために与えるポイントを選ぶ部分(selectタグ)に記述された数字を5000に書き換えます。
あとは書き換えたファイルを実行して、目的の店に投票するだけです。

# おわりに
CTFへの挑戦はほぼ初めてでしたが、かなり楽しめました。CTFたのしい。
解けた問題のジャンルに偏りがあったので、様々な問題に対応できるように勉強していきたいです。