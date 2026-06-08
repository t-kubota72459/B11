# 第15回 シミュレーション：数式で解けない「確率」を数で殴る

### ー モンテカルロ法で、部品のばらつきと合格率を予測する ー

---

## ⏱ 授業時間の目安（100分授業・正味85分）

| 時間 | 内容 |
|---:|---|
| 0〜5分 | 今日のゴール、第14回の復習 |
| 5〜20分 | シミュレーションとは何か |
| 20〜35分 | 乱数でばらつきを作る |
| 35〜55分 | 軸と穴のすきまをシミュレーションする |
| 55〜70分 | 合格率を求める |
| 70〜85分 | 第14・15回 合同提出課題の説明・作業 |

---

## 🎯 今日のゴール

今日の授業では、次のことを目標にします。

1. シミュレーションを「現実の実験をパソコンの中で何回も試すこと」として理解する。
2. 乱数を使って、部品の寸法ばらつきを作れるようになる。
3. 合格率・不合格率を、実験回数から計算できるようになる。
4. 第14回の行列計算と組み合わせて、ばらつきのある回路を評価する準備をする。

---

## 1. シミュレーションとは何か

現場では、すべてのパターンを実物で試すことはできません。

たとえば、

- 抵抗値が少しずれたら、回路の電圧はどうなるか
- 軸と穴の寸法がばらついたら、組み立てできるか
- 1000個作ったら、何個くらい不良品になるか

を実物で全部試すのは大変です。

そこで、コンピュータの中で何回も仮想実験を行います。

```mermaid
flowchart LR
    A[条件を決める] --> B[乱数でばらつきを作る]
    B --> C[計算する]
    C --> D[合格か判定する]
    D --> E[何回も繰り返す]
    E --> F[合格率を求める]
```

このように、乱数を使って何回も試す方法を、**モンテカルロ法** と呼びます。

名前は難しいですが、やっていることは、

> たくさん試して、だいたいの確率を調べる

ということです。

---

## 2. 乱数で「ばらつき」を作る

たとえば、平均が10.00mm、標準偏差が0.02mmの寸法ばらつきを作るには、次のようにします。

```python
import numpy as np

values = np.random.normal(10.00, 0.02, 10)

print(values)
```

これは、10個分の部品寸法を仮想的に作っています。

授業では、細かい文法は気にしなくてよいです。

```python
np.random.normal(平均値, 標準偏差, 個数)
```

と読めば十分です。

---

## 3. ワーク1：100個の軸の直径を作る

```python
import numpy as np
import matplotlib.pyplot as plt

shaft = np.random.normal(10.00, 0.02, 100)

print("最初の10個だけ表示")
print(shaft[:10])

plt.hist(shaft, bins=10)
plt.xlabel("diameter [mm]")
plt.ylabel("count")
plt.title("Shaft diameter")
plt.show()
```

### 読み取り

ヒストグラムは、データの分布を見るためのグラフです。

- 中央あたりに多い
- 極端に小さいものや大きいものは少ない

という形になれば、自然なばらつきに見えます。

---

## 4. 工学問題：軸と穴のすきまを調べる

軸と穴を組み合わせる場面を考えます。

軸の直径は、平均10.00mm、標準偏差0.02mmでばらつきます。

$$
D_{shaft}\sim N(10.00, 0.02)
$$

穴の直径は、平均10.10mm、標準偏差0.03mmでばらつきます。

$$
D_{hole}\sim N(10.10, 0.03)
$$

すきまは、

$$
C=D_{hole}-D_{shaft}
$$

です。

すきまが

$$
0.03\mathrm{mm}\leq C\leq 0.17\mathrm{mm}
$$

なら合格とします。

---

## 5. ワーク2：10000個作ったら何個合格するか

```python
import numpy as np
import matplotlib.pyplot as plt

N = 10000

shaft = np.random.normal(10.00, 0.02, N)
hole = np.random.normal(10.10, 0.03, N)

clearance = hole - shaft

ok = (clearance >= 0.03) & (clearance <= 0.17)

ok_count = np.sum(ok)
ok_rate = ok_count / N

print("試行回数 =", N)
print("合格数 =", ok_count)
print("合格率 =", ok_rate * 100, "%")

plt.hist(clearance, bins=40)
plt.axvline(0.03, linestyle="--")
plt.axvline(0.17, linestyle="--")
plt.xlabel("clearance [mm]")
plt.ylabel("count")
plt.title("Clearance distribution")
plt.show()
```

---

## 6. 結果の読み方

合格率がたとえば 94% なら、10000個作ったときに、だいたい9400個が合格するという意味です。

ただし、乱数を使っているので、実行するたびに少し結果が変わります。

この「少し変わる」ことも、シミュレーションの大事な特徴です。

---

## 7. ワーク3：設計を変えて合格率を上げる

穴の平均直径を変えてみましょう。

```python
hole = np.random.normal(10.08, 0.03, N)
```

```python
hole = np.random.normal(10.10, 0.03, N)
```

```python
hole = np.random.normal(10.12, 0.03, N)
```

どの設定が一番よいでしょうか。

### 考えること

穴を大きくしすぎると、すきまが大きくなりすぎます。

穴を小さくしすぎると、すきまが小さくなりすぎます。

つまり、設計では、

> ちょうどよい中心値を探す

ことが重要です。

---

## 8. まとめ

今日の内容を整理します。

| 内容 | 意味 | Colabでの計算 |
|---|---|---|
| 乱数 | ばらつきを持つ仮想データ | `np.random.normal()` |
| シミュレーション | パソコンの中で何回も試す | `N = 10000` |
| 合格判定 | 条件に入るか調べる | `(x >= 下限) & (x <= 上限)` |
| 合格数 | 合格した個数 | `np.sum(ok)` |
| 合格率 | 合格数 ÷ 全体数 | `ok_count / N` |

---

# 第14・15回 合同提出課題

## 提出物

Google Colab で作成した `.ipynb` ファイルを提出すること。

ファイル名の例：

```text
学籍番号_氏名_第14_15回合同課題.ipynb
```

---

## 課題：抵抗値のばらつきが回路電圧に与える影響を調べる

第14回で扱った回路では、抵抗値がぴったり決まっているとして点A・点Bの電圧を求めました。

今回は、抵抗値にばらつきがある場合を考えます。

抵抗値は、次のようにばらつくものとします。

| 抵抗 | 平均値 | 標準偏差 |
|---|---:|---:|
| R1 | 100Ω | 1Ω |
| R2 | 300Ω | 3Ω |
| R3 | 200Ω | 2Ω |
| R4 | 400Ω | 4Ω |
| R5 | 500Ω | 5Ω |

点Bの電圧が、

$$
2.0\mathrm{V}\leq V_B\leq 3.5\mathrm{V}
$$

の範囲に入れば合格とします。

10000回シミュレーションして、合格率を求めなさい。

---

## 参考コード

```python
import numpy as np
import matplotlib.pyplot as plt

N = 10000

VB_list = []

for i in range(N):
    R1 = np.random.normal(100, 1)
    R2 = np.random.normal(300, 3)
    R3 = np.random.normal(200, 2)
    R4 = np.random.normal(400, 4)
    R5 = np.random.normal(500, 5)

    A = np.array([
        [1/R1 + 1/R2 + 1/R3, -1/R3],
        [-1/R3, 1/R3 + 1/R4 + 1/R5]
    ])

    b = np.array([
        12/R1,
        5/R5
    ])

    V = np.linalg.solve(A, b)
    VB = V[1]

    VB_list.append(VB)

VB_list = np.array(VB_list)

ok = (VB_list >= 2.0) & (VB_list <= 3.5)
ok_rate = np.sum(ok) / N

print("点B電圧の平均 =", np.mean(VB_list), "V")
print("点B電圧の標準偏差 =", np.std(VB_list), "V")
print("合格率 =", ok_rate * 100, "%")

plt.hist(VB_list, bins=40)
plt.axvline(2.0, linestyle="--")
plt.axvline(3.5, linestyle="--")
plt.xlabel("VB [V]")
plt.ylabel("count")
plt.title("Simulation result of VB")
plt.show()
```

---

## レポートに書くこと

1. 回路のどの値を評価したか
2. 抵抗値にどのようなばらつきを与えたか
3. 点B電圧の平均値と標準偏差
4. 合格率
5. 結果から、設計が安定しているといえるか

### 書き方の例

```text
10000回のシミュレーションを行った結果、点B電圧の平均は○○V、標準偏差は○○Vであった。
規格範囲を2.0V以上3.5V以下とした場合、合格率は○○%であった。
したがって、この条件では点B電圧は規格範囲に入る可能性が高いことを確認した。
```

数値は、自分の実行結果に置き換えること。
