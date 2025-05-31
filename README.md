# method1_v1 動作原理概要

当プログラムでは、Tableクラスを用いてソート処理を行います。  

## Tableクラスの属性

- vector<vector<int>> map ：フィールドの盤面を保存する2次元リスト。  
- vector<vector<int>> mask ：ソート済みのマス(1)とソート前のマス(0)の情報を保存する2次元リスト。  
- vector<vector<int>> linker ：フィールドの盤面の数値をインデックスとし、それぞれ対応するインデックスに2つの座標(x0,y0,x1,y1)を保存する2次元リスト。
- vector<vector<int>> log ：手順を保存するリスト2次元リスト。

(例)  
![Image](https://github.com/user-attachments/assets/94c7fe51-5d08-48a6-9bed-f21939a2deef)  
理解を深めてもらうため上の図を例にとる。  
  
上の盤面に対応するmapは盤面に書かれている数値がリストの要素になる。  
また、maskはオレンジが背景色になっているマスの要素は1(int)、白になっているマスの要素は0(int)となる。以降に出てくる図もこれに統一する。  
上の盤面に対応するlinkerは以下のようになる。  
  
{  
{5,4,2,5}, ※0の要素は(5,4)と(2,5)に存在する。  
{0,0,1,0},  ※1の要素は(0,0)と(1,0)に存在する。  
{1,2,4,4},  ※2の要素は(1,2)と(4,5)に存在する。  
...  
}  
  
## separate関数
separate関数は引数の領域を並び替える領域(ソート領域)とそれ以外の領域に分ける関数です。領域のデータは  
{y座標の最小値,{y座標の最大値+1,x座標の最小値,x座標の最大値+1}  
のようなリストを用いる。  
![Image](https://github.com/user-attachments/assets/ea2b26cc-2ec1-42ac-8b86-750bb123f3ee)  
緑の領域が並び替える領域です。正方形か縦長の時は上2列がソート領域になります。  
緑の領域のデータはsort関数に渡され並び替えられます。白の領域はseparate関数に再度渡されます。  
![Image](https://github.com/user-attachments/assets/41623c21-f257-404c-9135-d975cd02ecf3)  
横長の時は右2列がソート領域になります。  
![Image](https://github.com/user-attachments/assets/9c6058e2-f091-441c-ab87-2b88e2b55464)  
白の領域が4×2になるとmixsort関数による幅優先探索で並び替えられます。  
  
## sort関数
sort関数はseparate関数で抽出されたソート領域を並び替えます。  
![スクリーンショット 2025-05-20 143920](https://github.com/user-attachments/assets/d18c6d90-aadb-4bc4-8a8d-95e851a44bc0)  
![スクリーンショット 2025-05-21 093521](https://github.com/user-attachments/assets/3c13ff96-a184-4a4a-9996-776195430334)  
![スクリーンショット 2025-05-21 093555](https://github.com/user-attachments/assets/df363135-fbc1-4a81-8ce7-f1ad79ed87fb)  
もし、引数が上2列の領域の場合は左上から順にmatch関数に掛けてソートします。  
![スクリーンショット 2025-05-21 093615](https://github.com/user-attachments/assets/38b42e97-588e-4347-88db-5f80e66a7b1a)  
![スクリーンショット 2025-05-21 093656](https://github.com/user-attachments/assets/c10a78b4-6ead-407c-97a5-a9bcc45135bd)  
![スクリーンショット 2025-05-21 093714](https://github.com/user-attachments/assets/95e0e6a8-0187-474b-be0a-503e2f00b99c)  
もし、引数が右2列の領域の場合は左上から順にmatch関数に掛けてソートします。  

## match関数
match関数は指定されたペアをメモ化探索を用いて合わせる関数です。  
![スクリーンショット 2025-05-20 141420](https://github.com/user-attachments/assets/3b42c064-fedd-42e8-830b-893a4d000cbe)  
上の盤面を例にmatch関数の動作を解説します。ここでは、9を(3,1)と(4,1)で揃えることとする。  
![スクリーンショット 2025-05-21 094921](https://github.com/user-attachments/assets/c7d7900c-e6c7-4aea-8ce2-4ea0e4129285)  
上の盤面は(4,1)から各マスへの最短手順を記録する4次元リストである。初めに、目的地である(4,1)は最終地点の目印である{0}を記録。(3,2)は{3,1,2}の手順を記録する。  
![スクリーンショット 2025-05-21 095152](https://github.com/user-attachments/assets/d672eca4-0c27-4bc0-a8ed-53b8f139dd9a)  
上の手順では、マスの中で最短手順の数が1のマス({0}を含む)から順に左回転で巻き戻して手順を記録する。  
![スクリーンショット 2025-05-21 151412](https://github.com/user-attachments/assets/0d5d3542-730e-4e40-a1ea-2fb17aec0fbd)  
同じように、マスの中で最短手順の数が2のマス({0}を含む)から順に左回転で巻き戻して手順を記録する。  
![スクリーンショット 2025-05-21 152329](https://github.com/user-attachments/assets/f17ca7e9-3942-4ab9-a106-fa0bd3147f63)  
最後のマスまで探索し終えると、linkerをもとに対象の座標(ここでは(0,2))のマスの手順をlogに保存し、mapを回転させる。  
  
----------
# method2_v1 動作原理概要

当プログラムデでは、Tableクラスを用いてソートを行います。ただし、動作概要はmethod1_v1とは全く異なります。  
method2_v1では、すべての1手先の手順を評価し、最高評価の手順を逐次保存する方法です。  
盤面を評価する方法は様々ありますが、Tableクラスでは8つの評価関数を実装しています。  

## Tableクラスの属性

- vector<vector<int>> map ：フィールドの盤面を保存する2次元リスト。  
- vector<vector<int>> mask ：ソート済みのマス(1)とソート前のマス(0)の情報を保存する2次元リスト。  
- vector<vector<int>> log ：手順を保存するリスト2次元リスト。
- static const int FP = 0  ：fitPairメソッドに割り当てられた定数
- static const int LZS = 1 ：largestZeroSquareメソッドに割り当てられた定数
- static const int LOS = 2 ：largestOneSquareメソッドに割り当てられた定数
- static const int ZP = 3  ：ZeroPairsメソッドに割り当てられた定数
- static const int OP = 4  ：OnePairsメソッドに割り当てられた定数
- static const int UO = 5  ：underOnesメソッドに割り当てられた定数
- static const int CZ = 6  ：centerZerosメソッドに割り当てられた定数
- static const int DS = 7  ：distanceScoreメソッドに割り当てられた定数

## FitPiar

## Talbeクラスの利用方法

- コンストラクタ(type1) ：Table(int size,vector<vector<int>> map,vector<int> functions,vector<int> biases);
  - int size :フィールドの一辺の長さ。
  - vector<vector<int>> map ：フィールドの2次元リスト
  - vector<int> functions   ：評価関数に割り当てられた定数のリスト
  - vector<int> biases      ：評価関数に割り当てるバイアス(総和は1.0)

- reveiw_scoreメソッド ：double reveiw_score(vector<vector<int>> map);
  - 揃っているペアの割合を返す ( 揃っているペアの数/すべてのペアの数)
  
- コンストラクタ(type2) ：Table(int size,vector<vector<int>> map,vector<int> functions,vector<double> fp_biases,vector<double> ep_biases);
  - vector<int> fp_biases    ：reveiw_scoreメソッドの引数が0の時、評価関数に割り当てるバイアス(総和は1.0)
  - vector<int> ep_biases    ：reveiw_scoreメソッドの引数が1の時、評価関数に割り当てるバイアス(総和は1.0)  
  ※この時、`biases=fp_biases+(ep_biases-fp_biases)*reveiw_score`となる

- search_best_wayメソッド ：bool search_best_way();
  - 手順を評価しTableクラスを更新するメソッド。また、これ以上更新が可能かを返す。
  - デフォルトでは、評価結果を出力している。
 
- update_biasesメソッド ：void update_biases(double score);
  - type2でインスタンス化を行った場合、Tableクラスの更新ごとにバイアスを変えるために作られたメソッド。
  - `biases=fp_biases+(ep_biases-fp_biases)*reveiw_score`の処理を行う。
 
利用例1：
```cpp
table = Table(size,map,{t.FP,t.DS,t.CZ},{0.8,0.1,0.1}); //fitPair: 0.8, distanceScore: 0.1, centerZeros: 0.1
while (t.search_best_way());
```

利用例2：
```cpp
table = Table(size,map,{t.FP,t.DS,t.CZ},{0.8,0.5,0.15},{0.8,0.15,0.5}); //fitPair: 0.8, distanceScore: 0.5→0,15, centerZeros: 0.15→0,5
while (t.search_best_way()) t.update_biases(t.reveiw_score(t.map));
```
