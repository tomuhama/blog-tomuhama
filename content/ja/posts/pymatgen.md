---
title: "pymatgenの使い方(1)"
date: 2021-06-01
description: "Materials Projectのデータをpymatgenを用いて引っ張ってきたい時に"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: tomuhama
authorEmoji: 🐧
#pinned: false
tags:
- pymatgen
- materials informatics
series:
- pymatgen
categories:
- materials informatics
image: 
---
<script type="text/javascript" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">
 MathJax.Hub.Config({
 tex2jax: {
 inlineMath: [['$', '$'] ],
 displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
 }
 });
</script>

## 概要

**Materials Project**の概要は次。<cite>これら[^issp][^qiita]</cite>から引用。
> 物質材料の第一原理計算結果のデータベース。結晶構造、バンド構造、熱力学量、相図、磁気モーメントなどを提供する。MITの研究グループが運営を行っており、Li電池材料のデータが特に充実している。ウェブベースのユーザーインタフェースに加えて、httpベースのAPIも提供されており、ユーザ独自のスクリーニングが可能。無料であるが、要登録。

元々、Materials Projectは2011年にアメリカで始まったプロジェクトMaterials Genome Initiative(MGI)の一環で生まれたデータベース。沢山のデータを用いてハイスループットに(言葉の使い方あってるのか？)材料探索を行うことが期待されている。AFLOWもMGIの一環。pymatgenは、Python Materials Genomicsの略で、Materials API(Materials ProjectのAPI)を利用して簡単に材料データを取得することを可能にするPythonパッケージ。その他にも色々できるが今回は省略。詳しくは<cite>論文[^pymatgen]</cite>を読もう！


## 登録

登録しなくても引っ張ってこれる情報はあるが、当然物足りないので登録して沢山情報を持ってこれるようにする。

1. 普通に登録する。[公式サイト](https://www.materialsproject.org/)のLoginのところから登録できる。
1. 登録ができたら[ここ](https://materialsproject.org/open)を開く。意味わからん文字列があるのでそれがAPI KEYです。

## 使い方

pymatgenをインストールしてない人はpipでインストール。
```Python
pip install pymatgen
```

まず、登録したAPI_KEYを使ってMPResterのオブジェクトを作成。
```Python
from pymatgen import MPRester

MY_API_KEY="API_KEY"
mp = MPRester(MY_API_KEY)
```
と思ったけど書き方変わったみたいでキレてる。普通に先月くらいまではこの書き方でちゃんと動いてたんですけどねぇ...(2021/06/01現在)。次のように書きます。
```Python
from pymatgen.ext.matproj import MPRester

MY_API_KEY="API_KEY"
mp = MPRester(MY_API_KEY)
```
あとはmpを使って適宜呼び出し。様々に呼び出せる方法はあるが、最も簡易的な、番号から呼び出す方法を書く。
```Python
>>> structure = mp.get_structure_by_material_id("mp-1143")
>>> print(structure)
Full Formula (Al4 O6)
Reduced Formula: Al2O3
abc   :   5.177955   5.177955   5.177955
angles:  55.289612  55.289612  55.289616
Sites (10)
  #  SP           a         b         c    magmom
---  ----  --------  --------  --------  --------
  0  Al    0.147904  0.147904  0.147904         0
  1  Al    0.352096  0.352096  0.352096         0
  2  Al    0.647904  0.647904  0.647904         0
  3  Al    0.852096  0.852096  0.852096         0
  4  O     0.556146  0.943854  0.25            -0
  5  O     0.75      0.443854  0.056146        -0
  6  O     0.25      0.556146  0.943854        -0
  7  O     0.943854  0.25      0.556146        -0
  8  O     0.056146  0.75      0.443854        -0
  9  O     0.443854  0.056146  0.75            -0
```
これでstructureが取り出せる。ここでは$\textrm{Al}_2\textrm{O}_3$をとってきた。structureだけでも結構な情報がとってこれる。`dir(structure)`で調べればわかるが、superlatticeを作れたりもするし、replace methodを使えば特定の元素を別の元素に交換できる。
構造以外の情報を引き出すためには、次のように書く。
```Python
Al2O3 = mp.query(criteria={"element":{"task_id":"mp-1143"}}, properties=["band_gap", "cif", "elasticity"])
```
と思ったけどこっちも書き方かわったんですね...`"element"`の部分が必要でなくなったみたい。
```Python
Al2O3 = mp.query(criteria={"task_id":"mp-1143"}, properties=["band_gap", "cif", "elasticity"])
```
`criteria`では材料の条件を指定する。ここではtask_idで指定したが、例えば「O元素を2つ含む構造」など指定して情報を取り出すこともできる。structureはデフォルトで引き出せるので問題ない。また、propertiesは取得したい物性を指定する。propertiesで引数にできる値は[ここ](https://github.com/materialsproject/mapidoc/tree/master/materials)に全て載っている。今回の場合は、バンドギャップやcifファイルの形式、elasticityの情報を含んだオブジェクトが作られる。ちなみにprintすると直接情報が入ったリストが出てくる。
ちなみにAl2O3はこうなってる。今回は1つしかないが、criteriaに複数の材料が当てはまる場合はそれぞれの情報がdict型で返ってくる。
```Python
[{'band_gap': 6.043599999999999, 
  'cif': "# generated using pymatgen\ndata_Al2O3\n_symmetry_space_group_name_H-M   'P 1'\n_cell_length_a   5.17795525\n_cell_length_b   5.17795525\n_cell_length_c   5.17795522\n_cell_angle_alpha   55.28961179\n_cell_angle_beta   55.28961179\n_cell_angle_gamma   55.28961598\n_symmetry_Int_Tables_number   1\n_chemical_formula_structural   Al2O3\n_chemical_formula_sum   'Al4 O6'\n_cell_volume   87.42003700\n_cell_formula_units_Z   2\nloop_\n _symmetry_equiv_pos_site_id\n _symmetry_equiv_pos_as_xyz\n  1  'x, y, z'\nloop_\n _atom_site_type_symbol\n _atom_site_label\n _atom_site_symmetry_multiplicity\n _atom_site_fract_x\n _atom_site_fract_y\n _atom_site_fract_z\n _atom_site_occupancy\n  Al  Al0  1  0.14790400  0.14790400  0.14790400  1\n  Al  Al1  1  0.35209600  0.35209600  0.35209600  1\n  Al  Al2  1  0.64790400  0.64790400  0.64790400  1\n  Al  Al3  1  0.85209600  0.85209600  0.85209600  1\n  O  O4  1  0.55614600  0.94385400  0.25000000  1\n  O  O5  1  0.75000000  0.44385400  0.05614600  1\n  O  O6  1  0.25000000  0.55614600  0.94385400  1\n  O  O7  1  0.94385400  0.25000000  0.55614600  1\n  O  O8  1  0.05614600  0.75000000  0.44385400  1\n  O  O9  1  0.44385400  0.05614600  0.75000000  1\n", 
  'elasticity': {'G_Reuss': 145.0, 'G_VRH': 147.0, 'G_Voigt': 149.0, 'G_Voigt_Reuss_Hill': 147.0, 'K_Reuss': 232.0, 'K_VRH': 232.0, 'K_Voigt': 232.0, 'K_Voigt_Reuss_Hill': 232.0, 'elastic_anisotropy': 0.16, 'elastic_tensor': [[452.0, 150.0, 107.0, 20.0, 0.0, 0.0], [150.0, 452.0, 107.0, -20.0, 0.0, 0.0], [107.0, 107.0, 454.0, 0.0, 0.0, 0.0], [20.0, -20.0, 0.0, 132.0, 0.0, 0.0], [0.0, 0.0, 0.0, 0.0, 132.0, 20.0], [0.0, 0.0, 0.0, 0.0, 20.0, 151.0]], 'homogeneous_poisson': 0.24, 'poisson_ratio': 0.24, 'universal_anisotropy': 0.16, 'elastic_tensor_original': [[452.88438075058264, 149.32230924280603, 107.84796496254714, -20.10675793699231, 0.0, 0.0], [148.62120151839645, 453.5170195225491, 107.90847433611715, 20.173114161264873, 0.0, 0.0], [106.9405661066039, 106.94049665460224, 454.3703632383968, 0.0, 0.0, 0.0], [-20.86619534535896, 19.832316552076684, -0.17929756761829355, 131.71960640208465, 0.0, 0.0], [-0.0013251095003368445, -0.015841534176974235, -0.0003537556433960988, 0.012846932839122636, 131.7651980776287, -20.304943009508005], [-1.4040031587713696e-05, -1.1880019661839212e-05, 2.6599853168491205e-06, -1.9746683263811934e-05, -20.3341646730955, 149.8292238793223]], 'compliance_tensor': [[2.6, -0.8, -0.4, -0.5, -0.0, -0.0], [-0.8, 2.6, -0.4, 0.5, 0.0, -0.0], [-0.4, -0.4, 2.4, -0.0, -0.0, 0.0], [-0.5, 0.5, -0.0, 7.8, -0.0, 0.0], [-0.0, 0.0, -0.0, -0.0, 7.8, -1.0], [-0.0, -0.0, 0.0, 0.0, -1.0, 6.8]], 'warnings': [], 'nsites': 10}}]
```
最後に.cifで保存してVestaで見てみる。
```Python
with open("al2o3.cif", "r") as f:
    f.write(Al2O3[0]["cif"])
```

![Al2O3](../images/al2o3.png)

終わり。こういう感じでしか使ったことないけどフォノンバンドとかも取得できるし相図も引っ張ってこれるらしいので次回以降(あるのか？)やる。


[^issp]: [pymatgenを使ったMaterials Projectのデータ収集](https://ma.issp.u-tokyo.ac.jp/app-post/2331)

[^qiita]: [Materials Projectのクエリ機能を使用して所望の計算材料データを大量に取得する。](https://qiita.com/qmiyajun/items/543cdf6e5d13d297953a)

[^pymatgen]: [Python Materials Genomics (pymatgen): A robust, open-source python library for materials analysis](https://www.sciencedirect.com/science/article/abs/pii/S0927025612006295)