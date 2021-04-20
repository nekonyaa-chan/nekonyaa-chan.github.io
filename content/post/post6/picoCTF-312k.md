---
title: "picoCTF 312k"
date: 2021-04-02T17:08:16+09:00
author: "312k"
categories : [ "Writeup" ]
tags : [ "CTF", "Writeup", "312k" ]
---
# はじめに
今回は[picoCTF](https://picoctf.org/)に参加しました。リバースエンジニアリング、バイナリ問題を中心に考えました。
<!--more-->
## General Skills
### Obedient Cat
#### 解いた過程
この問題はASCIIで書かれたファイルのようです。
``` shell
$ file flag
flag: ASCII text
```
ということでstringsコマンドを使って可読文字を表示したいと思います。
``` shell
$ strings flag
picoCTF{s4n1ty_v3r1f13d_2fd6ed29}
``` 
#### Flag
**picoCTF{s4n1ty_v3r1f13d_2fd6ed29}**

## Wave a flag
### 解いた過程
問題ファイル拡張子がないのでとりあえずfileコマンドでファイルを確認してみます。
``` shell
$ file warm
warm: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=01b148cdedfc38125cac0d87e0537466d47927b1, with debug_info, not stripped
```
ということでこのファイルはLinuxのELF形式であるとわかりました。
よって実行してみます。
``` shell
$ ./warm
Hello user! Pass me a -h to learn what I can do!
``` 
helpを表示しろと言われたので表示してみます。
``` shell
$ ./warm -h
Oh, help? I actually don't do much, but I do have this flag here: picoCTF{b1scu1ts_4nd_gr4vy_f0668f62}
```
### Flag
**picoCTF{b1scu1ts_4nd_gr4vy_f0668f62}**

## Tab, Tab, Attack
### 解いた過程
与えられたファイルは複雑なディレクトリ構造をしています。このようなときには7zipを使うと便利です。
最下層にはELFファイルがありました。
取り出してfileコマンドで確認したところ
``` shell
$ file fang-of-haynekhtnamet
fang-of-haynekhtnamet: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=72a56ba85df661b5a985999a435927c01095cccf, not stripped
```
ということで実行。
``` shell
$ ./fang-of-haynekhtnamet
*ZAP!* picoCTF{l3v3l_up!_t4k3_4_r35t!_2bcfb2ab}
```
### Flag
**picoCTF{l3v3l_up!_t4k3_4_r35t!_2bcfb2ab}**
## Magikarp Ground Mission
### 解いた過程
この問題はssh接続してcatとls,~展開をさせる内容でした。
sshで接続したら、
``` shell
$ ls
1of3.flag.txt  instructions-to-2of3.txt
```
ということでFlagぽいファイルを発見しました。
``` shell
$ cat 1of3.flag.txt
picoCTF{xxsh_
```
何やらここのディレクトリのもう一つのファイルには次のヒントが隠されているようです。
``` shell
$ cat instructions-to-2of3.txt
Next, go to the root of all things, more succinctly `/`
```
ルートへ行けと言われました。
ということで最上位ルートディレクトリに移動したいと思います。
``` shell
$ cd /
```
同様にしてlsでファイルを確認catで表示
``` shell
$ cat 2of3.flag.txt
0ut_0f_\/\/4t3r_
$ cat instructions-to-3of3.txt
Lastly, ctf-player, go home... more succinctly `~`
```
ということで~へ行けと言われました。
なお~というのはユーザーのホームディレクトリを示します。
ということで移動します
``` shell
$ cd ~
$ ls
```
同様に
``` shell
$ cat 3of3.flag.txt
71be5264}
```
ということでこれらを組み合わせたのがFlagになります。
ただこれだとちょっと手動では面倒なのでつなげてみたいと思います。
``` shell
$ pwd
$ /home/ctf-player
$ cat  drop-in/1of3.flag.txt /2of3.flag.txt 3of3.flag.txt
picoCTF{xxsh_0ut_0f_\/\/4t3r_71be5264}
```
### Flag
picoCTF{xxsh_0ut_0f_\\/\\/4t3r_71be5264}

## Static ain't always noise
### 解いた過程
この問題はstaticとbashが与えられています。多分この問題はアセンブラ出力された結果を読む問題だと思いましたが、stringsを使って解きました...
``` shell
$ strings static |  grep pico
picoCTF{d15a5m_t34s3r_6f8c8200}
```
### Flag
**picoCTF{d15a5m_t34s3r_6f8c8200}**
## Nice netcat...
### 解いた過程
この問題ncコマンドで接続すると改行されたAsciiが与えられます。
これを変換すると答えになります。
### Flag
**picoCTF{g00d_kitty!_n1c3_kitty!_afd5fda4}**

以上がGeneral Skillsで解いた問題です。
このジャンルはバイナリ系やネットワーク系が多くてよかったです。

# Reverse Engineering
## crackme-py
### 解いた過程
この問題の13行目にROT47と書いてあります。そのため実際にROT47を使って4行目のシークレットをデコードします。
[DenCode](https://dencode.com/ja/cipher/rot47)
こちらのサイトに問題文のエンコードされたものを入力します。
### Flag
**picoCTF{1|\/|_4_p34|\|ut_f3bc410e}**

## Transformation
### 解いた過程
問題文は何やら文字化けしたような文字が表示されています。
> 灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彤㔲挶戹㍽%

ということでとりあえずこの文章を変換してみます。
[こちらのサイト](http://www.online-decoder.com/bg)ロシア語ですが、結構自動変換してくれて便利です。
自動認識によってFlagが求まりました。
### Flag
**picoCTF{16_bits_inst34d_of_8_d52c6b93}**
# 終わりに
デコードをサイトに頼りぱなししてしまったので自分の力だけで解けるように復習しようかなあと思いました。
すごい難しかったけど、次このpicoCTFに出るときに、スラスラと解法が浮かんで解ける人になりたいです。
