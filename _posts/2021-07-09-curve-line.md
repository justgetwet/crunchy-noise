---
layout: post
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

### 曲線を描く 

一般的にベジェ曲線を使う。4個の制御点から得られる3次ベジェ曲線

（図）

#### 1. ベジェ曲線の参考文書

[A Primer on Bézier Curves](https://pomax.github.io/bezierinfo/index.html)

#### 2. 定義 [wikipedia](https://ja.wikipedia.org/wiki/%E3%83%99%E3%82%B8%E3%82%A7%E6%9B%B2%E7%B7%9A)

制御点を $$ B_0, B_1, ..., B_{N-1} $$ とすると、ベジェ曲線は、

$$ \sum_{i=0}^{N-1}B_iJ_{N-1},_i(t) $$

と表現される。ここで、$$ J_{N-1},_i(t) $$ はバーンスタイン基底関数である。

$$ J_{n},_i(t) = \begin{pmatrix} n \\ i \end{pmatrix} t^i(1-t)^{n-i} $$

t が 0 から 1 まで変化する時、$$ B_0 $$ と $$ B_{N−1} $$ を両端とするベジェ曲線が得られる。

#### 3. 二項係数

バーンスタインの式に二項係数 $$ \begin{pmatrix} n \\ i \end{pmatrix} $$ が使われる。ここでは scipy.special.comb() を利用するが、参考に一般式と再帰関数での実装を見る。

$$ \begin{pmatrix} n \\ i \end{pmatrix} = {}_nC_i = \frac{n!}{i!(n-i)!} $$

```python
def comb(n, r):
  if (n == 0 or r == 0):
    return 1
  return (n / r) * comb(n-1, r-1)

a = comb(4, 3)
print(a)
# -> 4.0
```

ワンライナー版

```python
comb = lambda n, r: (n / r) * comb(n-1, r-1) if not (n == 0 or r == 0) else 1
```

#### 4. Pythonで記述

```python
import matplotlib.pyplot as plt
from scipy.special import comb

def curve(nodes, steps=1000) -> tuple:
  """ Bezier curve """
  bernstein = lambda n, i, t: comb(n, i) * t**i * (1-t)**(n-i)

  t_values = [s / steps for s in range(steps + 1)]
  line = []
  for t in t_values:
    n = len(nodes) - 1
    x, y = 0.0, 0.0
    for i, p in enumerate(nodes):
      x += bernstein(n, i, t) * p[0]
      y += bernstein(n, i, t) * p[1]
    line.append([x, y])

  x_values, y_values = [*zip(*line)]

  return x_values, y_values
```

#### 5. プロット

```python
if __name__ == '__main__':

  fig = plt.figure(dpi=100, figsize=(4, 3))
  ax = plt.gca()
  ax.set_title("bezier courve")
  
  nodes = ((32.0, 128.0), (96.0, 64.0), (160.0, 192.0), (224.0, 128.0))
  x, y = curve(nodes)
  ax.plot(x, y, color="cyan")

  for p in nodes:
    ax.plot(*p, "o", color="hotpink")
  
  # plt.show()
  plt.savefig('bezier_sample.png')

```

![bezier curve](../../../assets/images/curve.png)