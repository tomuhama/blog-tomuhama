---
title: "Linuxのメモ"
date: 2021-06-01
description: "自分用のLinuxコマンドなどのメモです。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: tomuhama
authorEmoji: 🐧
#pinned: false
tags:
- linux
series:
-
categories:
- linux
image: 
---

## 概要

Linuxコマンドに一向に慣れないので覚えるため・わかんなくなった時にすぐに参照できるようにメモしておく場所。あと普通に知らなかったことも書いておく。主に<cite>MITの授業[^MIT_CS]</cite>を読んで知ったことを書いてる。これはイチから使い方を教えるタイプの文章ではないので悪しからず。

[^MIT_CS]: [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/)

## シェバン(shebang)

今までずっと雰囲気で使っていたので知らなかった。計算投げる時のファイルの冒頭の
`#!`で始まる文のこと。シェバンは実行したときにどういうスクリプト言語で処理するかを指定する文言。つまり
```bash:path.sh
#!/bin/bash

echo $PATH
```
とすると、このファイルをbashで実行するという意味になる。このファイルを
```bash
./path.sh
```
とすると、`bash path.sh`と同じように実行してくれる。シェバンはbashに限らずpythonなどでもできるが、人々のpythonのbinの位置は異なるので、envを使う。
```bash
#!/usr/bin/env python
```
このようにすると、下のほうのコードがpythonで実行される。envは各々の環境変数の中の特定のアプリケーションの場所を教えてくれて、そのまま実行してくれる。

## tldr

tldrは"Too Long. Did't read."の略。何かしらのコマンドの使い方が分からなくなった場合、`--help`や`man`コマンドを使えば一応は知ることができるが、非常に長くてその場ですぐ知って使いたいという場合には不便。そこで便利なのが`tldr`コマンド。これは言葉の意味通り「(長くて読んでられないので)短くまとまった説明を与える」コマンド。実際に使った結果が次。
```bash
$ tldr grep
grep
Find patterns in files using regular expressions.More information: https://man7.org/linux/man-pages/man1/grep.1.html.

 - Search for a pattern within a file:
   grep "{{search_pattern}}" {{path/to/file}}

 - Search for an exact string (disables regular expressions):
   grep --fixed-strings "{{exact_string}}" {{path/to/file}}

 - Search for a pattern in all files recursively in a directory, showing line numbers of matches, ignoring binary files:
   grep --recursive --line-number --binary-files={{without-match}} "{{search_pattern}}" {{path/to/directory}}

 - Use extended regular expressions (supports ?, +, {}, () and |), in case-insensitive mode:
   grep --extended-regexp --ignore-case "{{search_pattern}}" {{path/to/file}}

 - Print 3 lines of context around, before, or after each match:
   grep --{{context|before-context|after-context}}={{3}} "{{search_pattern}}" {{path/to/file}}

 - Print file name and line number for each match:
   grep --with-filename --line-number "{{search_pattern}}" {{path/to/file}}

 - Search for lines matching a pattern, printing only the matched text:
   grep --only-matching "{{search_pattern}}" {{path/to/file}}

 - Search stdin for lines that do not match a pattern:
   cat {{path/to/file}} | grep --invert-match "{{search_pattern}}"
```
便利～。ハイフン(-)でつなげることで、subcommandにも使える。
```bash
$ tldr git-diff
git diff
Show changes to tracked files.More information: https://git-scm.com/docs/git-diff.

 - Show unstaged, uncommitted changes:
   git diff

 - Show all uncommitted changes (including staged ones):
   git diff HEAD

 - Show only staged (added, but not yet committed) changes:
   git diff --staged

 - Show changes from all commits since a given date/time (a date expression, e.g. "1 week 2 days" or an ISO date):
   git diff 'HEAD@{3 months|weeks|days|hours|seconds ago}'

 - Show only names of changed files since a given commit:
   git diff --name-only {{commit}}

 - Output a summary of file creations, renames and mode changes since a given commit:
   git diff --summary {{commit}}

 - Compare a single file between two branches or commits:
   git diff {{branch_1}}..{{branch_2}} [--] {{path/to/file}}

 - Compare different files from the current branch to other branch:
   git diff {{branch}}:{{path/to/file2}} {{path/to/file}}
```

## ワイルドカード

`*.py`みたいなやつのこと。`?`と`*`の2タイプがあり、それぞれ異なる振る舞いをする。たとえばいま、foo1、foo2、foo10、barという4つのファイルがあったとき、`?`と`*`を使うと次のようにふるまう。
```bash
$ ls
foo1 foo2 foo10 bar
$ rm foo?
$ ls
foo10 bar
```
```bash
$ ls
foo1 foo2 foo10 bar
$ rm foo*
$ ls
bar
```
`?`は1文字指定のワイルドカードであり、`*`は全て指定するワイルドカード。

## コマンドライン上での画像の可視化

```bash
display test.jpg
```

## 画像の拡張子の変換

```bash
convert test.png test.jpg

# もしくは、次のようにも書ける

convert test.{png,jpg}
```

## 'と"の違い

```bash
foo=bar
echo $foo
echo "$foo"
echo '$foo'
```
文字色で分かる通り、''で囲まれた部分はリテラルの文字列なので、$fooと表示される。他はbarと表示される。

## 特別な変数

- `$0`：スクリプト名
- `$1`から`$9`：スクリプトに渡される引数。`$1`は一番目の引数で、以下同様。
- `$@`：全ての引数
- `$#`：引数の総数
- `$?`：直前のコマンドのリターンコード
- `!!`：引数を含む直前のコマンド全体。よくあるパターンは権限がなかっただけでコマンドを実行できなかったときで、sudo !!と打てばそのコマンドをsudoで速やかに再実行できる。
- `$_`：直前のコマンドの最後の引数。もしインタラクティブシェルを使っているなら、`$_`を使わずとも、Escからの.と打てばすぐにこの値を取得できる。
