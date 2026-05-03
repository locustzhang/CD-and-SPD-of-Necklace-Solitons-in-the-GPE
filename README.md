本代码是模拟项链孤子在GPE控制下的碰撞动力学，内容涉及仿真，分析；
运行Main代码在4090显卡需要17小时；
我们提供了全部的6个JSON文件；
我们提供了用来提取JSON文件和Snapshots文件进行分析的代码（plot and analysis);
遗憾的是Snapshots文件太大了（2.53G)无法上传（用来做频谱验证）。但是JSON文件已经可以进行绝大多数的验证了（99%）；
运行次序把Main文件和plot and anaysis文件放到同一个目录下，先运行Main文件，再运行plot and analysis文件即可。
# Necklace Soliton Dynamics in Gross-Pitaevskii Equation

## 📖 项目简介

本项目研究 **Gross-Pitaevskii 方程 (GPE)** 中 **项链孤子 (Necklace Soliton)** 的碰撞动力学与稳定性相图。采用高精度 **分步傅里叶方法 (Split-Step Fourier Method)** 进行数值模拟，系统研究了二维项链孤子的演化、碰撞及稳定性行为。

### 主要研究成果

- ✅ 单环项链孤子的长期稳定演化验证
- ✅ 双环项链孤子碰撞动力学的四种结局分类
- ✅ $g$-$\Omega_{\rm trap}$ 二维稳定性相图（196 个参数点）
- ✅ 碰撞速度扫描（12 个速度点）
- ✅ 时空收敛性测试与守恒量验证
- ✅ 频谱分析验证（区分物理湍流与数值碎片）

---

## 📁 文件结构

```
Necklace_Soliton_PUBLISHED/
├── necklace_soliton_v2.py          # 主程序：计算 + 可视化一体化脚本
├── Necklace_Soliton_PUBLISHED/     # 输出目录
│   ├── single_ring.json            # 单环模拟数据
│   ├── collision_k1p5.json         # 双环碰撞数据 (k0=1.5)
│   ├── scan1d.json                 # 1D g 扫描数据
│   ├── convergence.json            # 收敛性测试数据
│   ├── scan2d.json                 # 2D 稳定性相图数据
│   ├── collision_scan.json         # 碰撞速度扫描数据
│   ├── Snapshots_SingleRing/       # 单环快照文件
│   ├── Snapshots_Collision_k1p5/   # 碰撞快照文件
│   ├── Snapshots_coll_*            # 碰撞扫描快照文件
│   ├── Fig1_SingleRing.pdf         # 单环演化快照
│   ├── Fig2_Collision.pdf          # 双环碰撞快照
│   ├── Fig3_Conservation.pdf       # 守恒量验证
│   ├── Fig4_Stability.pdf          # 1D 稳定性扫描
│   ├── Fig5_Convergence.pdf        # 收敛性测试
│   ├── Fig6_PhaseDiagram.pdf       # 2D 稳定性相图
│   ├── Fig7_CollisionClass.pdf     # 碰撞分类总览
│   └── Fig8_CollisionSnapshots.pdf # 四种碰撞类型快照
└── README.md                       # 本说明文件

workspace/
├── necklace_soliton_paper_final.tex  # LaTeX 论文主文件
└── apsref.bib                        # 参考文献文件
```

---

## 🔧 环境依赖

### 必需软件

| 软件 | 版本要求 | 说明 |
|------|----------|------|
| Python | ≥ 3.8 | 推荐 3.9+ |
| PyTorch | ≥ 1.9 | 支持 CUDA 加速 |
| NumPy | ≥ 1.20 | 数值计算 |
| Matplotlib | ≥ 3.4 | 图表生成 |
| SciPy | ≥ 1.7 | 信号处理 |
| tqdm | ≥ 4.60 | 进度条显示 |

### 可选软件

| 软件 | 用途 |
|------|------|
| CUDA | GPU 加速（推荐 RTX 3060+） |
| LaTeX (TeXLive) | 论文编译 |

### 安装命令

```bash
# 创建虚拟环境（推荐）
python -m venv necklace_env
source necklace_env/bin/activate  # Linux/Mac
# 或 necklace_env\Scripts\activate  # Windows

# 安装依赖
pip install torch numpy matplotlib scipy tqdm

# GPU 版本（如有 NVIDIA 显卡）
pip install torch --index-url https://download.pytorch.org/whl/cu118
```

---

## 🚀 快速开始

### 方式一：完整运行（计算 + 可视化）

```bash
cd Necklace_Soliton_PUBLISHED
python necklace_soliton_v2.py
```

**预计运行时间**：
- CPU: 约 3-4 小时
- GPU (RTX 3060): 约 1-2 小时

### 方式二：仅生成图表（使用已有 JSON 数据）

```bash
cd Necklace_Soliton_PUBLISHED
python -c "
from necklace_soliton_v2 import *
plot_fig1()
plot_fig2()
plot_fig3()
plot_fig4()
plot_fig5()
plot_fig6()
plot_fig7()
plot_fig8()
print('✅ All 8 figures generated!')
"
```

**预计运行时间**：2-5 分钟

### 方式三：分阶段运行

#### 阶段 1：数值计算

```bash
python necklace_soliton_v2.py
# 计算完成后会自动生成所有图表
```

#### 阶段 2：单独重新生成某个图表

```bash
python -c "
from necklace_soliton_v2 import *
plot_fig6()  # 重新生成相图
"
```

---

## ⚙️ 参数配置

### 核心参数（代码第 47-90 行）

```python
# 单环基线参数
NX_SINGLE = 512          # 网格点数
LX_SINGLE = 25.0         # 空间域大小
T_SINGLE = 6.0           # 模拟时间
G_SINGLE = -0.08         # 相互作用强度
V_TRAP_SINGLE = 0.001    # 陷阱势系数

# 双环碰撞参数
NX_COLL = 512
LX_COLL = 30.0
T_COLL = 15.0
G_COLL = -0.12
V_TRAP_COLL = 0.001

# 项链几何
N_PEAKS = 6              # 峰值数量
R_RING = 8.0             # 环半径
A_PEAK = 0.8             # 峰值振幅
W_PEAK = 1.5             # 峰值宽度

# 碰撞扫描速度列表
COLL_V_LIST = [0.02, 0.05, 0.1, 0.15, 0.2, 0.3, 0.5, 0.8, 1.2, 1.8, 2.5, 3.0]

# 2D 相图扫描范围
SCAN2D_G = np.linspace(-2.5, -0.04, 14)    # g 范围
SCAN2D_V = np.linspace(0.00, 0.08, 14)     # V_trap 范围
```

### 物理判据参数

```python
# 坍缩判据 [C6]
COLLAPSE_RHO_FACTOR = 5.0       # 密度比阈值
COLLAPSE_ABS_MULT = 20.0        # 绝对密度倍数
COLLAPSE_RATE_THRESH = 2.0      # 增长率阈值

# 临界粒子数修正 [C2]
NCR_CORRECTION_MAX_RATIO = 0.5  # Ω/|g| ≤ 0.5 时有效

# 峰值分类
PEAK_MIN_DIST = 10              # 最小峰间距
FRAG_MIN_PEAKS = 12             # 碎裂判据
FUSION_MAX_PEAKS = 4            # 融合判据
MAX_REASONABLE_PEAKS = 50       # 数值碎裂判据

# 频谱分析
PHYSICAL_TURBULENCE_K0_MIN = 1.5     # 物理湍流 k0 阈值
PHYSICAL_TURBULENCE_RATIO_MIN = 0.15 # 高频能量占比阈值
```

---

## 📊 输出文件说明

### JSON 数据文件

| 文件 | 内容 | 大小 |
|------|------|------|
| `single_ring.json` | 单环演化数据（含径向剖面） | ~0.1 MB |
| `collision_k1p5.json` | 双环碰撞数据 (k0=1.5) | ~0.1 MB |
| `scan1d.json` | 1D g 扫描数据（14 点） | ~0.1 MB |
| `convergence.json` | 收敛性测试数据 | ~0.1 MB |
| `scan2d.json` | 2D 相图数据（196 点） | ~2.5 MB |
| `collision_scan.json` | 碰撞扫描数据（12 点） | ~0.2 MB |

### JSON 数据结构

```json
{
  "meta": {
    "label": "SingleRing",
    "git_hash": "abc1234",
    "device": "cuda",
    "timestamp": "2026-05-03T08:00:00Z",
    "corrections": ["[C1]...", "[C2]...", ...]
  },
  "config": {
    "Nx": 512, "Ny": 512, "Lx": 25.0, "Ly": 25.0,
    "dt": 1e-05, "T": 6.0, "g": -0.08, "V_trap": 0.001,
    ...
  },
  "validation": {
    "nl_phase_satisfied": true,
    "spectral_resolved": true,
    "N_cr_trap_valid": true,
    "collapse_safe": true,
    ...
  },
  "timeseries": {
    "t": [...], "E": [...], "N": [...], "Lz": [...],
    "rho_max": [...], "E_err": [...], "N_err": [...], "Lz_err": [...]
  },
  "summary": {
    "E0": 12.345, "N0": 13.57, "Lz0": 0.0,
    "rho0_max": 0.64, "max_E_err": 5.37e-10,
    "max_N_err": 5.05e-10, "max_Lz_err": 5.77e-11,
    "rho_ratio": 0.16, "collapsed": false
  },
  "snap_files": [".../snap_00000.npy", ...],
  "snap_dir": ".../Snapshots_SingleRing"
}
```

### PDF 图表文件

| 图表 | 内容 | 尺寸 |
|------|------|------|
| `Fig1_SingleRing.pdf` | 单环演化 4 帧快照 | 9×8.5 英寸 |
| `Fig2_Collision.pdf` | 双环碰撞 6 帧快照 | 12×7 英寸 |
| `Fig3_Conservation.pdf` | 守恒量误差 + 峰值密度 | 12×5 英寸 |
| `Fig4_Stability.pdf` | 径向剖面 + 1D 扫描 | 13×5 英寸 |
| `Fig5_Convergence.pdf` | 时间 + 空间收敛 | 12×5 英寸 |
| `Fig6_PhaseDiagram.pdf` | 2D 稳定性相图（3 面板） | 15×5 英寸 |
| `Fig7_CollisionClass.pdf` | 碰撞分类总览（4 面板） | 12×10 英寸 |
| `Fig8_CollisionSnapshots.pdf` | 4 种类型最终快照 | 14×10 英寸 |

---

## 🎯 主要功能特点

### 1. 高精度数值算法

- **BAB Strang 分裂**：二阶精度 $\mathcal{O}(\Delta t^2)$
- **伪谱方法**：空间谱精度，误差达 $10^{-12}$ 量级
- **辛结构保持**：守恒量误差 $< 10^{-9}$

### 2. 物理修正 [C1]–[C10]

| 编号 | 内容 |
|------|------|
| [C1] | k0 为径向波矢量，相位局部施加于每个峰值 |
| [C2] | N_cr 陷阱修正仅在 V/|g|≤0.5 时有效 |
| [C3] | BAB 分裂：密度在步长中点计算 |
| [C4] | 旋转框架：exp(-iΩdt Lz) 作为精确双线性旋转 |
| [C5] | 非线性相位判据：|g|·ρ_max·dt < φ_NL_max |
| [C6] | 坍缩阈值相对于 ρ0，记录 dt_snap |
| [C7] | E_int = (g/2)·∫|ψ|⁴ dA（有效 2D 平均场） |
| [C8] | 收敛性：全局相位对齐，正确记录 |
| [C9] | 网格使用 linspace 保证关于原点对称 |
| [C10] | 边界检查 |ψ|/|ψ_max| < 1e-6 |

### 3. 碰撞结局分类

| 类型 | 峰数 $n_p$ | 径向相关 $C_r$ | 说明 |
|------|-----------|---------------|------|
| Fusion | ≤ 4 | — | 物理融合 |
| Fragmentation | ≥ 12 | < -0.3 | 物理碎裂 |
| Transition | 5–11 | — | 过渡态 |
| Numerical Fragmentation | > 50 | — | 数值碎片 |
| Physical Turbulence | — | — | 物理湍流（频谱验证） |

### 4. 频谱分析验证

```python
def diagnose_physical_turbulence(rho_2d, k0_val, nx):
    """
    对密度快照进行频谱分析，判断是否为物理湍流。
    
    判据：
    - k0 ≥ 1.5
    - 高频能量占比 ≥ 0.15（最外圈 20% 波数范围）
    """
```

---

## 📈 典型结果

### 守恒量验证

| 量 | 单环误差 | 碰撞误差 |
|---|----------|----------|
| 能量 $|\Delta E/E_0|$ | $5.37\times10^{-10}$ | $1.37\times10^{-9}$ |
| 粒子数 $|\Delta N/N_0|$ | $5.05\times10^{-10}$ | $1.37\times10^{-9}$ |
| 角动量 $|\Delta L_z|$ | $5.77\times10^{-11}$ | $5.37\times10^{-6}$ |

### 收敛性测试

- **时间收敛斜率**：1.72（接近二阶精度）
- **空间收敛精度**：$10^{-12}$ 量级（谱精度）

### 碰撞结局分布（12 个速度点）

| 类型 | 次数 | 比例 |
|------|------|------|
| Transition | 5 | 41.7% |
| Fusion | 2 | 16.7% |
| Fragmentation | 2 | 16.7% |
| Numerical Fragmentation | 3 | 25.0% |

### 2D 相图统计（196 个参数点）

| 状态 | 点数 | 比例 |
|------|------|------|
| Stable | 98 | 50.0% |
| Collapsed | 98 | 50.0% |

---

## 📝 使用示例

### 示例 1：修改碰撞速度列表

```python
# 在代码第 89 行修改
COLL_V_LIST = [0.1, 0.5, 1.0, 1.5, 2.0]  # 自定义速度列表
```

### 示例 2：仅运行特定模拟

```python
from necklace_soliton_v2 import *

# 只运行单环模拟
run_single_ring()

# 只运行收敛性测试
run_convergence_test(force=True)
```

### 示例 3：加载已有数据进行分析

```python
import json
import numpy as np

# 加载碰撞扫描数据
with open('Necklace_Soliton_PUBLISHED/collision_scan.json') as f:
    data = json.load(f)

# 提取 k0 和类型
k0_list = [r['k0'] for r in data]
types = [r['type'] for r in data]
n_peaks = [r['n_peaks'] for r in data]

# 绘图
import matplotlib.pyplot as plt
plt.scatter(k0_list, n_peaks)
plt.xlabel('k0')
plt.ylabel('Peak count')
plt.show()
```

---

## 🔬 物理模型

### Gross-Pitaevskii 方程

$$i\frac{\partial\psi}{\partial t} = -\frac{1}{2}\nabla^2\psi + g|\psi|^2\psi + V_{\rm trap}(x,y)\psi$$

其中：
- $\psi(x,y,t)$：波函数
- $g < 0$：吸引相互作用强度
- $V_{\rm trap}(x,y) = \frac{1}{2}\Omega_{\rm trap}(x^2+y^2)$：各向同性谐振子陷阱

### 守恒量

$$N = \int|\psi|^2\,{\rm d}x\,{\rm d}y \quad \text{(粒子数)}$$

$$E = \int\!\Bigl[\frac12|\nabla\psi|^2 + \frac{g}{2}|\psi|^4 + V_{\rm trap}|\psi|^2\Bigr]{\rm d}x\,{\rm d}y \quad \text{(能量)}$$

$$L_z = -i\!\int\!\psi^*\Bigl(x\frac{\partial\psi}{\partial y} - y\frac{\partial\psi}{\partial x}\Bigr){\rm d}x\,{\rm d}y \quad \text{(角动量)}$$

### 临界粒子数

自由空间（$\Omega_{\rm trap}=0$）：
$$N_{\rm cr}(0) = \frac{5.85}{|g|}$$

陷阱修正（$\Omega_{\rm trap}/|g| \le 0.5$）：
$$N_{\rm cr}(\Omega_{\rm trap}) = N_{\rm cr}(0)\Bigl(1 + 4\pi\frac{\Omega_{\rm trap}}{|g|}\Bigr)$$

---

## 📚 参考文献

### 关键文献

1. **Townes 孤子**: Chiao, R. Y., Garmire, E., & Townes, C. H. (1964). *Phys. Rev. Lett.*, 13, 479.
2. **NLS 坍缩理论**: Sulem, C., & Sulem, P.-L. (1999). *The Nonlinear Schrödinger Equation*. Springer.
3. **BEC 坍缩**: Pérez-García, V. M., et al. (1996). *Phys. Rev. Lett.*, 77, 5320.
4. **项链孤子**: Soljačić, M., Sears, S. M., & Segev, M. (1998). *Phys. Rev. Lett.*, 81, 4851.
5. **分步傅里叶**: Taha, T. R., & Ablowitz, M. J. (1984). *J. Comput. Phys.*, 55, 203.
6. **孤子碰撞**: Helm, J. L. (2014). *PhD Thesis*, Durham University.

### 完整参考文献

见 `apsref.bib` 文件。

---

## 📄 许可证

本项目代码采用 **MIT License** 开源。

数据文件（JSON 和快照）可用于学术研究，引用时请注明：

```bibtex
@article{Miao2026Necklace,
  title = {Collision Dynamics and Stability Phase Diagram of Necklace Solitons in the Gross-Pitaevskii Equation},
  author = {Miao, Xiaoyu and Song, Haoran and Zhang, Lipu},
  journal = {Physical Review E},
  year = {2026},
  volume = {in press},
  pages = {in press},
  doi = {10.1103/PhysRevE.in.press},
}
```

---

## 👥 联系方式

- **通讯作者**: Lipu Zhang
- **邮箱**: zhanglipu@cuz.edu.cn
- **单位**: 浙江传媒学院 媒体工程学院
- **GitHub**: [https://github.com/locustzhang/Multiscale-Dynamics-of-Necklace-Ring-Solitons-in-Two-Dimensional-Nonlinear-Schrodinger-Equations](https://github.com/locustzhang/CD-and-SPD-of-Necklace-Solitons-in-the-GPE)

---

## 🙏 致谢

本工作得到 **全省影视媒体技术研究重点实验室开放基金**（批准号：2024E10023）资助。

---

## 📅 版本历史

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v3.0 | 2026-05-03 | 频谱增强版，计算与可视化完全解耦 |
| v2.0 | 2026-04-28 | 添加 2D 相图可视化 |
| v1.0 | 2026-01-26 | 修改守恒量验证 |
| v1.0 | 2025-12-20 | 初始版本 |

---

**最后更新**: 2026 年 5 月 3 日
