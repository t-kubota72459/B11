# Matplotlib グラフ描画チートシート (Google Colab ＆ オブジェクト指向版)

### 1. 基本テンプレート (まずはこれをコピペ)

```python
!pip install japanize-matplotlib -q  # 初回のみ（日本語化）
import matplotlib.pyplot as plt
import japanize_matplotlib
import numpy as np

# データの準備
x = np.linspace(-5, 5, 100)
y = x**2

# 土台の準備 (figsize = 横幅, 縦幅 のインチ数)
fig, ax = plt.subplots(figsize=(6, 4))

# グラフ描画・各種設定 (詳細は下記リファレンス参照)
ax.plot(x, y, color="blue", label="y = x^2")

# 仕上げと画面表示
ax.legend()  # 凡例を表示
plt.show()
```
---

### 2. よく使うプロット関数 (`ax.〇〇`)

| 関数・コード | 役割 | 主要なパラメーター (引数) |
| --- | --- | --- |
| `ax.plot(x, y)` | **折れ線グラフ** / 数式関数 | `color="red"`, `linewidth=2`, `linestyle="--"` |
| `ax.scatter(x, y)` | **散布図** (点プロット) | `color="blue"`, `s=100` (点の大きさ), `marker="o"` |
| `ax.axhline(y_val)` | **水平な補助線** を引く | `color="black"`, `linewidth=0.5`, `linestyle=":"` |
| `ax.axvline(x_val)` | **垂直な補助線** を引く | `color="black"`, `linewidth=0.5`, `linestyle=":"` |
| `ax.fill(x, y)` | 多角形の **塗りつぶし** | `color="lightgray"`, `edgecolor="black"` |

> **💡 linestyle の種類：** `"-"` (実線), `"--"` (破線), `":"` (点線), `"-."` (1点鎖線)

---

### 3. 見栄え・軸の調整 (エンジニアの必須設定)

```python
# 軸ラベルの設定
ax.set_xlabel("横軸のタイトル (単位)")
ax.set_ylabel("縦軸のタイトル (単位)")
ax.set_title("グラフのメインタイトル", fontsize=12, fontweight="bold")

# 軸の表示範囲を固定 (見切れ・自動調整の防止)
ax.set_xlim(-10, 10)  # X軸を -10 から 10 までに固定
ax.set_ylim(0, 100)   # Y軸を 0 から 100 までに固定

# デジタル方眼紙（グリッド線）
ax.grid(True, linestyle="--", alpha=0.5)  # alpha: 透明度 (0.0～1.0)

# 【重要】図形の縦横比（縮尺）を 1:1 に強制する (直角三角形や円を正確に描く)
ax.set_aspect('equal')
```

---

### 4. グラフ上に文字や注釈を入れる

```python
# 1. 単純なテキスト配置 (配置するX座標, Y座標, 文字列)
ax.text(2, 50, "注釈テキスト", fontsize=10, color="red", ha="center", va="bottom")
# ※ ha: 水平揃え (left, center, right) / va: 垂直揃え (top, center, bottom)

# 2. 寸法線・矢印の配置 ( xy: 矢印の先端座標 / xytext: 文字の開始座標 )
ax.annotate('寸法 10mm', xy=(10, 0), xytext=(0, 0), arrowprops=dict(arrowstyle='<->', color='black'))
```
