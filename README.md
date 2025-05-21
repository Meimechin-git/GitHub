# method1_v1 動作原理概要

当プログラムでは、Tableクラスを用いてソート処理を行います。  

## Tableクラスの属性

- vector<vector<int>> map ：フィールドの盤面を保存する2次元リスト。  
- vector<vector<int>> mask ：ソート済みのマス(1)とソート前のマス(0)の情報を保存する2次元リスト。  
- vector<vector<int>> linker ：フィールドの盤面の数値をインデックスとし、それぞれ対応するインデックスに2つの座標(x0,y0,x1,y1)を保存する2次元リスト。  

(例)  
![Image](https://github.com/user-attachments/assets/94c7fe51-5d08-48a6-9bed-f21939a2deef)  
理解を深めてもらうため上の図を例にとる。  
  
上の盤面に対応するmapは盤面に書かれている数値がリストの要素になる。  
また、maskはオレンジが背景色になっているマスの要素は1(int)、白になっているマスの要素は0(int)となる。以降に出てくる図もこれに統一する。  
上の盤面に対応するlinkerは以下のようになる。  
{{5,4,2,5}, ※0の要素は(5,4)と(2,5)に存在する。  
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

