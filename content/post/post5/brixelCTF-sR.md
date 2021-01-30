---
title: "BrixelCTF-sR write up"
date: 2021-01-30T15:58:12+09:00
author: "sR"
categories: ["Writeup"]
tags: ["CTF", "Writeup", "sR"]
---
# はじめに
BrixelCTFに参加しました。
2問解くことができ、10ポイント獲得することが出来ました。

# Cryptography
## Merda
イタリア人のメッセンジャーが残した```ymj kqfl nx gwncjqHYK{uneefsfutqn}```の```gwncjqHYK{}```を今回のBrixelCTFのフラグの形```brixelCTF```に合わせてみると、全てアルファベット順で見て左に5ずつずれていることがわかります。これを全体に当てはめてみると、```the flag is brixelCTF{pizzanapoli}```となり、フラグは```brixelCTF{pizzanapoli}```だとわかります。

## Merde
Merdaより、フランス人のメッセンジャーが残した```Vvr ktdk vl jvtzsyHBI{fnzcievs}```という文字列が```The flag is brixelCTF{}```になると予測して合わせてみると、```Vvr ktdk vl jvtzsyHBI{}```がアルファベット順で見て左に```2,14,13 5,8,3,4 13,19 8,4,11,2,14,13,5,8,3```とずれていることがわかります。よく見るとこの数字の列は循環していて、```{fnzcievs}```の部分を```4,13,19,8,4,11,2,14```と左にずらしてみると```The flag is brixelCTF{baguette}```となり、フラグは```brixelCTF{baguette}```だとわかります。

# おわりに
CTFには初めて参加しました。しかし、2問しか解くことが出来ずあまりチームに貢献することが出来なかったので、頑張って勉強して次回はより多くの問題が解けるようにしたいです。