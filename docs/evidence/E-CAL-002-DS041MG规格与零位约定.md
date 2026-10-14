---
evidence_id: E-CAL-002-DS041MG规格与零位约定
title: DS041MG规格与零位约定
status: VALID
created: 2026-10-13
last_verified: 2026-10-13
sources:
  - source_id: SRC-CFG-006
    type: CONFIG
    path_or_url: docs/sources/user-supplied/2026-10-13-ds041mg-variant-correction.md
    revision: 2026-10-13
  - source_id: SRC-HW-012
    type: MANUFACTURER_SPEC
    path_or_url: page/assets/images/E-HW-005-DS041MG舵机包装铭牌与标称规格/ds041mg-package-label.jpg
    revision: user-supplied-photo-2026-10-13
  - source_id: SRC-FW-001
    type: IMPLEMENTATION
    path_or_url: docs/evidence/E-FW-001-双足轮腿舵机标定固件项目.md
    revision: 2026-10-13
  - source_id: SRC-M1-REAUDIT-20260917
    type: PHYSICAL_AND_AUDIT
    path_or_url: docs/evidence/E-M1-002-M1实机契约复审与Powered全通道回归.md
    revision: 2026-09-17
epistemic_role: CONFIGURATION_AND_DERIVATION
---

# E-CAL-002 — DS041MG 规格与零位约定

> 证据编号：`E-CAL-002-DS041MG规格与零位约定`
> 认识论角色：`CONFIGURATION_AND_DERIVATION`
> 状态：`VALID`
> 边界：本文登记当前训练合同的输入前提，不将名义 300° 行程、机械零位或安全端点表述为物理实测结果。

## 1. Scope

本文冻结动作范围修复计划使用的 A1–A6 前提，使训练仓库可以复现名义舵机到仿真关节的换算。安全关节范围仍由 Isaac Sim 的闭环、碰撞和跟踪扫描确定。

## 2. 已登记前提

| 编号 | 当前约定 | 证据性质与边界 |
|---|---|---|
| A1 | 当前按 `DS041MG 300°` 版本计算；固件 `500–2500 μs` 名义对应 `0–300°`，即 `0.15°/μs` | `E-HW-007#P01` 配置与 `E-FW-001#P03` 实现一致；未实测物理端点 |
| A2 | 训练推导把 `180° + servo_off[i]` 定义为各通道的伸直腿参考，此时五连杆曲柄 `α=β=90°` | 项目坐标约定；不是外部量角得到的 mechanical `u0` |
| A3 | 300° 舵机的名义角度尺度与固件角度尺度一致；0.9 行程按中心收缩为命令 `15–285°` | 由 A1 和固件线性映射推导 |
| A4 | 通道顺序和安装方向按 `E-M1-002#P02-P03`；仿真零位曲柄角由 URDF 得到 `α≈154°、β≈6.5°` | 通道和 reference command 已冻结；绝对物理关节角仍非外部测量 |
| A5 | 名义舵机区间只提供 G3 扫描上界；机构干涉、闭环残差、跟踪误差和碰撞会进一步收窄最终关节范围 | 安全规则，不声称完整 300° 可装机运行 |
| A6 | 操作者检查确认外髋可接近整圈转动；内髋向结构内侧转动存在挡止、向外侧较自由 | 方向性机械观察，尚无量角端点；G3 必须按四腿仿真正负方向分别扫描，不能采用统一对称限位 |

## 3. 名义换算

固件零位命令为

```text
u0[i] = 180° + servo_off[i]
```

当前 300° 配置下，命令相对零位的名义舵机转角为 `r = u - u0[i]`。FR/RL 曲柄角使用 `90° - r`，FL/RR 使用 `90° + r`，再依据 URDF 的关节正方向换算为仿真 `q`。

原厂 reference command `[112,214,146,237,113,214,133,252]°` 在该尺度下对应约 `α=154°、β=51°`，不能再沿用 180° 假设下的 `128.4°/66.6°` 解释。训练计划仍可选择 `128.4°/66.6°` 作为候选站姿，但它是待 G3 验证的几何设计值，不是原厂 reference command 的换算结果。

## 4. 力矩与速度边界

- 包装铭牌在 7.4 V 档标称 `5.0 kgf·cm` 与 `0.13 s/60°`（`E-HW-005#P02`）。
- 训练暂时保留更保守的 `0.392 N·m` 峰值和 `8.73 rad/s` 速度上限；这些是仿真上限，不是连续安全输出保证。
- 刚度、阻尼、额定连续力矩和热限制仍属于后续动力学标定债务。

## 5. 未决项

- 每个装机舵机的实际 PWM 端点、回差与有效行程未测。
- mechanical `u0` 和绝对关节角未由量角器、编码器或运动捕捉测得。
- G3 必须在名义范围内筛选闭环、无碰撞、可跟踪的最终范围；内髋结构内侧挡止需由逐腿非对称可行域表达，不得把本文直接当作部署安全许可。

## 更新日志

- 2026-10-13：按用户确认的 300° 当前配置登记 A1–A5，并撤销 180° 比例换算。
- 2026-10-13：登记 A6 的方向性机械观察；角度端点仍待测。
