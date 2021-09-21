---
layout: post
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

### 直線を引く

それは2点a,b間の点の座標を求めること。

![example](../../../assets/images/example.png)

##### 1) $$ a(x_{1}, y_{1}), b(x_{2}, y_{2}) $$ の距離

ピタゴラスの定理 $$ a^{2} + b^{2} = h^{2} $$ から。

$$ distance = \sqrt{(x_{2}-x_{1})^2+(y_{2}-y_{1})^2} $$

##### 2) $$ a(x_{1}, y_{1}), b(x_{2}, y_{2}) $$ の角度

アークタンジェント（逆正接）を求める。プログラミングでは[atan2](https://ja.wikipedia.org/wiki/Atan2)を利用する。

$$ \theta = \arctan(y_{2}-y_{1} / x_{2}-x_{1})  $$

##### 3) $$ b(x_{2}, y_{2}) $$ の座標

$$ x_{2} = \cos(\theta) * distance $$

$$ y_{2} = \sin(\theta) * distance $$

##### 4) tが0から1まで変化する時、a,b間の点座標が得られる。

$$ x = \cos(\theta) * distance * t $$

$$ y = \cos(\theta) * distance * t $$

##### 5) pythonで記述

```python
import matplotlib.pyplot as plt
import math
import types

def straight(nodes: tuple, steps=1000) -> tuple:

  a = types.SimpleNamespace()
  b = types.SimpleNamespace()

  (a.x, a.y), (b.x, b.y) = nodes
  dist = math.sqrt((b.x - a.x)**2 + (b.y - a.y)**2)
  rad = math.atan2(b.y - a.y, b.x - a.x)
 
  t_values = [s / steps for s in range(steps + 1)]
  x_values, y_values = [], []
  for t in t_values:
    x_values.append(math.cos(rad) * dist * t + a.x)
    y_values.append(math.sin(rad) * dist * t + a.y)
  
  return x_values, y_values
```

##### 6) プロット

```python
if __name__ == '__main__':

  fig = plt.figure(dpi=100, figsize=(4, 3))
  ax = plt.gca()
  ax.set_title("a straight line")
  ax.set_xlim(0, 128)
  ax.set_ylim(0, 128)
  
  nodes = ((20, 30), (100, 80))
  x, y = straight(nodes)
  ax.plot(x, y, color="lightblue")

  for p in nodes:
    ax.plot(*p, "o", color="blue")
  
  # plt.show()
  plt.savefig('straight.png')
```

![straight](../../../assets/images/straight.png)

なおmatplotlibの標準機能でプロットした点は直線で結ばれますが、それは別の話です。