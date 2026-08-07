月面に衝突したロケット残骸の軌道計算（Orbital Trajectory Calculation for Lunar Impact Rocket Debris）

**Date:** 2026-08-07  
**Category:** Astrophysics / Astrodynamics / Orbital Mechanics  
**Tags:** `#astrodynamics` `#orbital-mechanics` `#python` `#space-debris` `#lunar-impact`

---

## 📌 概要（Overview）

月面に向かうロケットの残骸（ブースター等）の軌道計算は、地球・月・太陽の3体（あるいはN体）重力場や非球形重力分布（J2項、マスコン）の影響を強く受けるため、単純なケプラー運動（2体問題）では高精度な予測が不可能である。

本日、月面衝突軌道（Lunar Impact Trajectory）の数値シミュレーションおよび軌道伝播（Orbital Propagation）の基礎概念とその実装手順について整理した。

---

## 🌌 基礎理論と運動方程式（Mathematical Formulation）

月面衝突を目指す、または月面に衝突した物体の運動方程式は、地球と月双方の重力場を考慮した**円制限3体問題（CR3BP: Circular Restricted Three-Body Problem）**、あるいは摂動項を含む数値積分モデルで記述される。

### 1. 運動方程式（Equations of Motion in Synodic Frame）

回転座標系（Synodic Frame）における質量 $m$ の残骸の位置 $\mathbf{r} = (x, y, z)^T$ の運動方程式：

$$
\ddot{x} - 2\omega \dot{y} = \frac{\partial \Omega}{\partial x}
$$

$$
\ddot{y} + 2\omega \dot{x} = \frac{\partial \Omega}{\partial y}
$$

$$
\ddot{z} = \frac{\partial \Omega}{\partial z}
$$

ここで、有効ポテンシャル $\Omega(x, y, z)$ は以下のように定義される：

$$
\Omega(x, y, z) = \frac{1}{2} \omega^2 (x^2 + y^2) + \frac{G M_e}{r_1} + \frac{G M_m}{r_2}
$$

* $M_e$: 地球の質量  
* $M_m$: 月の質量  
* $r_1$: 残骸と地球の中心間距離 ($\sqrt{(x + \mu d)^2 + y^2 + z^2}$)  
* $r_2$: 残骸と月の中心間距離 ($\sqrt{(x - (1-\mu)d)^2 + y^2 + z^2}$)  
* $\mu = \frac{M_m}{M_e + M_m}$: 月の質量比  
* $\omega$: 地球-月系の角速度  

---

## ⚡ 衝突判定と高度計算（Lunar Surface Impact Condition）

月表面（半長軸 $R_m \approx 1737.4 \text{ km}$）に達した時点を数値的に捉えるため、イベント関数（Event Function）を定義する。

$$
g(t) = \| \mathbf{r}_{debris}(t) - \mathbf{r}_{moon}(t) \| - R_m = 0
$$

$g(t) = 0$ となる時刻 $t_{impact}$ を状態変数のルートファイディング（Root-finding）により厳密に特定する。

---

## 🐍 Pythonによる軌道シミュレーション実装例（Code Example）

Pythonの `scipy.integrate.solve_ivp` を用いた、簡易的な地球-月系におけるロケット残骸の軌道伝播プログラム。

```python
import numpy as np
from scipy.integrate import solve_ivp
import matplotlib.pyplot as plt

# 定数定義 (Normalized Units: GM_total = 1, distance_earth_moon = 1)
mu = 0.0121505856  # Moon mass ratio
omega = 1.0        # Angular velocity

def cr3bp_equations(t, state):
    x, y, z, vx, vy, vz = state
    
    # 地球および月からの距離
    r1 = np.sqrt((x + mu)**2 + y**2 + z**2)
    r2 = np.sqrt((x - (1 - mu))**2 + y**2 + z**2)
    
    # 運動方程式 (回転座標系)
    ax = 2 * vy + x - (1 - mu) * (x + mu) / r1**3 - mu * (x - (1 - mu)) / r2**3
    ay = -2 * vx + y - (1 - mu) * y / r1**3 - mu * y / r2**3
    az = -(1 - mu) * z / r1**3 - mu * z / r2**3
    
    return [vx, vy, vz, ax, ay, az]

# 月面衝突イベント (r2 = R_moon)
R_moon_norm = 1737.4 / 384400.0  # 月半径 (正規化単位)

def lunar_impact_event(t, state):
    x, y, z, _, _, _ = state
    r2 = np.sqrt((x - (1 - mu))**2 + y**2 + z**2)
    return r2 - R_moon_norm

lunar_impact_event.terminal = True
lunar_impact_event.direction = -1

# 初期状態 (地月遷移軌道 LTO からの投入イメージ)
state0 = [0.2, 0.5, 0.0, 0.6, -0.4, 0.0]
t_span = (0, 10.0)

# 数値積分実行 (Runge-Kutta 8th order / Dormand-Prince)
sol = solve_ivp(
    cr3bp_equations, 
    t_span, 
    state0, 
    method='DOP853', 
    events=lunar_impact_event,
    rtol=1e-11, 
    atol=1e-11
)

if sol.t_events[0].size > 0:
    print(f"Impact detected at t = {sol.t_events[0][0]:.4f} (Normalized Time)")
else:
    print("No impact detected within the time span.")
```

---

## 🔍 主要な技術的注意点（Key Takeaways）

1. **高次ポテンシャル（GRAILモデルの適用）:**  
   月は内部の密度不均一性（マスコン: Mass Concentration）が著しいため、低軌道や衝突直前の軌道予測には spherical harmonics（球関数展開）モデル（例: LP150Q, GRGM1200A）の考慮が不可欠。
2. **太陽重力摂動と光輻射圧（SRP）:**  
   空のブースター（質重比 $A/m$ が大きい残骸）の場合、太陽光圧（Solar Radiation Pressure）による軌道変化が月面衝突位置を数 tens of kilometers 単位でずらす原因となる。
3. **バックワード伝播（Backward Propagation）:**  
   衝突クレーターが発見された後、過去の軌道を遡ってロケットの特定（Identifications）を行う場合、時間の正負を反転させた逆時間積分を行う。

---

## 📚 参考文献・ツール（References & Tools）

- **Astropy / Poliastro:** Pythonにおける天体軌道計算ライブラリ
- **NASA SPICE Toolkit:** 天体位置・探査機軌道データの取得および解析
- **JPL Horizons System:** 太陽系天体・人工物体の精密エフェメリス
