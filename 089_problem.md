# 第7回・第8回 合同提出課題：

- 本講義の課題は、**Google Colab** で作成したノートブック（.ipynb）を **Google Classroom** へ提出します。
- 第5回と第6回の内容を1つのノートブックにまとめ、期限までに提出してください。
- **提出先**: Google Classroom の指定された課題「第7回・第8回合同課題」にファイルを添付して提出。
  1) [ファイル] > [ダウロード] > [.ipynb をダウンロード]
  2) ダウンロードフォルダにある `工業数学_7_8_合同課題_学籍番号_氏名.ipynb` を提出する。

## 三平方の定理を用いた構造・配置設計

いずれの問題も、提示された Python コードを Google Colab に貼り付けて実行し、出力される正確な寸法図を観察しながら解答すること。

---

### 📋 課題1：【プラント設備設計】薬液タンク内の検尺ロッド（代数展開型）
> **【問題】**
> 
> 直径 $50\text{cm}$ の円柱形薬液タンクがある。この中に、タンクの深さを測るための真っ直ぐな金属製の検尺ロッド（棒）を斜めに立てかけた。
> 
> ロッドの下端はタンクの底の隅（壁のキワ）にあり、ロッドの上端は反対側の壁にぴったり寄りかかっている。
> このとき、タンクの底からロッドの上端までの垂直な高さは、**ロッドの長さそのものよりも $10\text{cm}$ 短く**なっていた。
> 
> 三平方の定理を用いて、この検尺ロッドの長さ $L\text{cm}$ を算出せよ。

#### 💻 タンクの断面寸法図をおこすコード（コピペ用）

```python
# Colab環境に日本語フォントを自動インストールして文字化けを防ぐ
!pip install japanize-matplotlib -q
import matplotlib.pyplot as plt
import japanize_matplotlib

fig, ax = plt.subplots(figsize=(6, 7))

# 1. タンクの壁と底を描画（黒の太線）
ax.plot([0, 0], [0, 150], color="black", linewidth=3)
ax.plot([50, 50], [0, 150], color="black", linewidth=3)
ax.plot([0, 50], [0, 0], color="black", linewidth=4, label="タンク断面")

# 2. 検尺ロッドを描画（斜めの青い棒）
ax.plot([0, 50], [0, 120], color="royalblue", linewidth=4, marker="o", label="検尺ロッド (長さ L)")

# 3. 図面寸法（アノテーション）の配置
ax.annotate('', xy=(0, -6), xytext=(50, -6), arrowprops=dict(arrowstyle='<->', color='black'))
ax.text(25, -14, "タンクの直径: 50cm", ha="center", fontsize=10)

ax.annotate('', xy=(54, 0), xytext=(54, 120), arrowprops=dict(arrowstyle='<->', color='black'))
ax.text(56, 60, "垂直な高さ:\n(ロッドの長さ L より 10cm 短い)", va="center", fontsize=10)

# 縮尺を1:1に固定して正確な図面にする
ax.set_aspect('equal')

ax.grid(True, linestyle="--", alpha=0.3)
ax.set_xlim(-15, 75)
ax.set_ylim(-25, 155)
ax.set_title("薬液タンクと検尺ロッドの配置図", fontsize=12, fontweight="bold")
ax.legend(loc="upper left")
plt.show()
##　加工断面積の最大化（相似比の自動設計）
```

#### 📋 課題2：【都市・交通工学】円形ロータリーと監視カメラの見通し線（幾何発見型）
**【問題】**  

あるスマートシティの交差点に、半径 $45\text{m}$ の円形ロータリー（中央の緑地帯）がある。

このロータリーの中心からまっすぐ $75\text{m}$ 離れた位置に、交通監視用の高精度カメラが設置されている（下図の赤点）。

カメラからロータリーの縁（円周）に向かって視線を走らせ、視線がロータリーの境界にちょうど「接する」ような見通し線（青線）を計画した。カメラの位置から、ロータリーと視線が交わる「接点」までの直線距離 $D\text{m}$ はいくらになるか。三平方の定理を用いて算出せよ。

（※ヒント：中学校で習った『円の接線は、接点を通る半径とどのように交わるか』という幾学のルールを思い出そう！）

```python
# Colab環境に日本語フォントを自動インストールして文字化けを防ぐ
!pip install japanize-matplotlib -q
import matplotlib.pyplot as plt
import numpy as np
import japanize_matplotlib

fig, ax = plt.subplots(figsize=(7, 7))

# 1. 円形ロータリー（中央島）を描画
theta = np.linspace(0, 2 * np.pi, 200)
r = 45
bx = r * np.cos(theta)
by = r * np.sin(theta)
ax.plot(bx, by, color="green", linewidth=2, label="円形ロータリー (半径 45m)")
ax.fill(bx, by, color="honeydew", alpha=0.5)

# 中心点をプロット
ax.scatter(0, 0, color="black", s=30)
ax.text(0, -4, "ロータリー中心", fontsize=9, ha="center")

# 2. 監視カメラの位置をプロット
camera_x = 75
camera_y = 0
ax.scatter(camera_x, camera_y, color="red", s=50, zorder=5, label="監視カメラ")
ax.text(camera_x, -4, "カメラ位置", fontsize=9, color="red", ha="center")

# 3. 中心からカメラまでの距離を示す線（点線）
ax.plot([0, camera_x], [0, camera_y], color="gray", linestyle="--", linewidth=1.5)
ax.text(37.5, 2, "距離: 75m", ha="center", fontsize=10)

# 4. カメラからの見通し線（接線）を描画
# 接点の座標は幾何学的に (27, 36) になります
ax.plot([camera_x, 27], [camera_y, 36], color="blue", linewidth=2, label="見通し線 (距離 D)")
ax.scatter(27, 36, color="blue", s=30, zorder=4)
ax.text(28, 39, "接点", fontsize=9, color="blue")

# 縮尺を1:1に固定して正確な図面にする
ax.set_aspect('equal')

ax.grid(True, linestyle="--", alpha=0.3)
ax.set_xlim(-60, 90)
ax.set_ylim(-60, 60)
ax.set_title("円形ロータリーとカメラの見通し限界図", fontsize=12, fontweight="bold")
ax.legend(loc="upper left")
plt.show()
```

### ２時間数の最大値・最小値
**【問題】**
下の図のように、底辺 $12\text{cm}$、高さ $8\text{cm}$ の直角三角形の材料から、内側にぴったり収まるような長方形のブロックを削り出したい。長方形の縦の長さを $x\text{cm}$、横の長さを $w\text{cm}$ とする。

1. 三角形の相似の関係を利用して、長方形の横の長さ $w$ を、縦の長さ $x$ を用いた式で表せ。
2. 長方形の断面積を $y\text{cm}^2$ とするとき、 $y$ を $x$ の2次関数として表せ。
3. この式を、雨樋の例にならって平方完成させる Python コードを記述せよ。
4. 計算によって出力された「平方完成された式」と、そこから読み取れる「最大面積になる縦の長さ $x$ 」をレポートに記述して提出すること。


<div align="center">
<img src="images/fig_2026-05-20-18-42-16.png" width="50%">
</div>