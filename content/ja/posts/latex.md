---
title: "LaTeXのメモ"
date: 2021-06-01
description: "自分用のLaTeXのメモです。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: tomuhama
authorEmoji: 🐧
#pinned: false
tags:
- latex
series:
-
categories:
- latex
image: 
---

## 概要

LaTeXで書いてて困ったりわかんなかったりそらんじられなかったりしたことをメモしておく場所。

## 特殊文字

表示 | 記法
-----|-----
\ | \textbackslash
① | \textcircled\{\scriptsize 1\}

### メモ
①については<cite>kn1cht氏[^kn1cht]</cite>が言及していた`\usepackage{otf}`の`\ajMaru{1}`を使うほうがよさそう。

[^kn1cht]: [学振特別研究員申請のための科研費LaTeX Tips](https://zenn.dev/kn1cht/articles/kakenhi-latex-tips#%E4%B8%B8%E6%95%B0%E5%AD%97%E3%82%92%E4%BD%BF%E3%81%86)

## section内に数式が入る場合

sectionとかsubsection内に数式を$$で入れると警告文が出てくる。例えば、`\section{$E=MC^2$}`と書くと次の警告文が発生する。
```
Package hyperrefWarning: Token not allowed in a PDF string (PDFDocEncoding):(hyperref) removing
```
これは、pdfのしおり機能がTeXで組まれておらず、普通の文字列で表現する必要があるかららしい<cite>[^hyperref1]</cite>。そのため、数式などの表現を入れるとエラーが出てしまう(Tokenは無理ダヨーと言われてる)。そんな時に使えるのが`\texorpdfstring`。`\texorpdfstring`は、`\hyperref`に組み込まれたマクロで、pdfのしおりに渡す文字列とTeXに渡すトークンをそれぞれ別にする書き方ができる。
```LaTeX
\texorpdfstring{TeXに渡すトークン列}{PDFに渡す文字列}
```
このようにすることで、警告文を回避できる。
さっきの例だと、`\section{\texorpdfstring{$E=MC^2$}{E=MC2}}`のようにしておく。ちなみにOverleafで書いて適当にpdfにしたけど普通に\texorpdfstringしなくてもそのまま表現はしてくれたししおり機能も動いてた。内部でどうなってるのかは知らない。

その他参考にした<cite>サイト[^hyperref2][^hyperref3]</cite>

## 目次の階層表示

修論書いてた時に気になった。デフォルトだと(たぶん)sectionくらいまでしか表示されなかったけど、見栄え微妙だな～と思って直したくて調べた。次のようにすれば深さを<cite>設定できる[^table_depth]</cite>。
```LaTeX
\setcounter{tocdepth}{n}
\tableofcontents
```
nの大きさで深さが決定される。

n | 表示
----|-----
0 | \chapterのみ
1 | \sectionまで
2 | \subsectionまで
3 | \subsubsectionまで

[^hyperref1]: [Hyperref - Token not allowed [duplicate]](https://tex.stackexchange.com/questions/53513/hyperref-token-not-allowed)

[^hyperref2]: [LaTeXでPDF出力する際のTips その2](https://blog.miz-ar.info/2015/09/latex-hyperref-tips-2/)

[^hyperref3]: [hyperref/提供されるコマンド](https://texwiki.texjp.org/?hyperref#dd6f0c82)

[^table_depth]: [texの\tableofcontentsで表示させる階層の深さを変更する](https://phithon.hatenablog.jp/entry/20110224/tex_change_the_depth_of_the_table_of_contents)

## sectionの深さを増やす

研究室で輪読を行っているときにsubsubsubsectionが欲しくなったので調べた。完全に次のサイトで提案されている書き方のコピペ<cite>[^subsubsub]</cite>。
```LaTeX
  \makeatletter
  \newcommand{\subsubsubsection}{\@startsection{paragraph}{4}{\z@}%
    {1.0\Cvs \@plus.5\Cdp \@minus.2\Cdp}%
    {.1\Cvs \@plus.3\Cdp}%
    {\reset@font\sffamily\normalsize}
  }
  \makeatother
  \setcounter{secnumdepth}{4}
```

[^subsubsub]: [\paragraphをsubsubsubsectionに拡張する](http://yoppi.hatenablog.com/entry/20091229/1262088390)
