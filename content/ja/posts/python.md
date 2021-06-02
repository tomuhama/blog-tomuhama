---
title: "Pythonのメモ"
date: 2021-06-01
description: "自分用のPythonのメモです。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: tomuhama
authorEmoji: 🐧
#pinned: false
tags:
- python
series:
-
categories:
- python
image: 
---

## 概要

自分用のメモです。

## objectの保存について
pickleでやる方法が有名だけどpandasでやったほうが書き方が簡単だったので覚えておく<cite>[^save_object]</cite>．

- pickleでやった例

```Python
import pickle
with open("obj_data.pkl", "wb") as f:
    pickle.dump(obj_data, f)
    
with open("obj_data.pkl", "rb") as f:
    obj_data = pickle.load(f)
```

- pandasでやった例

```Python
import pandas as pd
pd.to_pickle(obj_data, "obj_data.pkl")

obj_data = pd.read_pickle("obj_data.pkl")
```

- joblibでやった例

```Python
import joblib
joblib.dump(obj_data, "obj_data.jb", compress=3)

obj_data=joblib.load("obj_data.jb")
```
joblibでやるとファイルサイズが小さくできる。`compress`は0から9までとれる。数字が大きいほどサイズの圧縮率が高い。

[^save_object]: [pickleより楽にpythonオブジェクトを保存する方法](https://aotamasaki.hatenablog.com/entry/2018/11/23/120604)