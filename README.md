# CATCN: Condition-Aware Temporal Causal Modeling for Online Joint Anomaly Detection in Robotic Manipulators

[English](#english) | [中文说明](#中文说明)

> **Release Roadmap / 开源计划说明**  
> This repository currently hosts the **three benchmark datasets** used in our manuscript (*Condition-Aware Temporal Causal Modeling for Online Joint Anomaly Detection in Robotic Manipulators Under Varying Operating Conditions*). **Upon acceptance of the paper, the complete training, evaluation, and Jetson Orin NX onboard deployment codebase will also be uploaded to this repository.**  
> 本仓库目前首先整理并存放论文所使用的**三个基准数据集**。**未来论文正式录用后，我们还将把 CATCN 项目的完整训练、评估与板端部署代码上传至本仓库。**

---

<a id="english"></a>
## English

### 1. Overview of the Three Datasets

All three dataset archives are hosted under [**GitHub Releases (`v1.0-datasets`)**](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/tag/v1.0-datasets) with SHA-256 verification checksums (`SHA256SUMS.txt`).

| # | Dataset | Platform / Environment | DOF | Sampling Rate | Trajectories / Files | Archive Size | Download |
| :-: | :--- | :--- | :-: | :-: | :---: | :---: | :--- |
| 1 | **Unitree G1 Physical Robot** | Unitree G1 Dual 7-DOF Humanoid Arms (Real Hardware) | 7-DOF / arm | 100 Hz | 140 CSVs (5 classes: C0–C4) | 22.0 MB | [`01_Unitree_G1_Real_140trajs.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/01_Unitree_G1_Real_140trajs.zip) |
| 2 | **MuJoCo 7-DOF Simulation** | MuJoCo 3.17.0 Controlled Dynamics Simulation | 7-DOF | 100 Hz | 180 CSVs + XML + Split Manifest | 112.0 MB | [`02_MuJoCo_7DOF_Simulation_180trajs.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/02_MuJoCo_7DOF_Simulation_180trajs.zip) |
| 3 | **External UR10e Industrial Cobot** | Universal Robots UR10e (Upstream Open-Access Data) | 6-DOF | 100 Hz | 21 CSVs (Identification / Validation / Collision) | 3.5 MB | [`03_UR10e_External_Cobot_raw.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/03_UR10e_External_Cobot_raw.zip) |

### 2. Dataset Details & Schemas

#### (1) Physical Unitree G1 Dataset (`01_Unitree_G1_Real_140trajs.zip`)
- **Classes (5 operating states, 140 continuous trajectories total)**:
  - `normal/` (C0, 24 trajectories): Free motion across multiple speeds (`0.6x`, `0.8x`, `1.0x`, `1.3x`).
  - `payload/` (C1, 36 trajectories): Legitimate payload grasping and transfer (0.5–2.0 kg, including 1.2 kg benchmark runs).
  - `collision/` (C2, 28 trajectories): Physical collisions against rigid and padded obstacles.
  - `stagnation/` (C3, 28 trajectories): Joint stagnation induced via external joint-axis damping emulating joint jamming.
  - `noise/` (C4, 24 trajectories): Sensor noise disturbances.
- **Trajectory-Level Protocol**: 92 trajectories for training (18 of which are also used for threshold calibration) and 48 held-out test trajectories (20 hazardous: 10 C2 + 10 C3; 28 non-hazardous: 9 C0 + 10 C1 + 9 C4).
- **File List**: See [`datasets/01_Unitree_G1_Real/manifest.txt`](datasets/01_Unitree_G1_Real/manifest.txt).
- **CSV Columns**:
  ```text
  timestamp,q_0,q_1,q_2,q_3,q_4,q_5,q_6,dq_0,dq_1,dq_2,dq_3,dq_4,dq_5,dq_6,tau_act_0,tau_act_1,tau_act_2,tau_act_3,tau_act_4,tau_act_5,tau_act_6,tau_nom_0,tau_nom_1,tau_nom_2,tau_nom_3,tau_nom_4,tau_nom_5,tau_nom_6,res_0,res_1,res_2,res_3,res_4,res_5,res_6,label
  ```

#### (2) MuJoCo 7-DOF Simulation Dataset (`02_MuJoCo_7DOF_Simulation_180trajs.zip`)
- Contains 180 simulated continuous trajectories generated in MuJoCo 3.17.0 for reproducible multi-model benchmarking and additive Gaussian noise stress testing (Clean / 30 dB / 20 dB / 10 dB SNR).
- Also includes the robot model definition [`datasets/02_MuJoCo_7DOF_Simulation/arm7dof.xml`](datasets/02_MuJoCo_7DOF_Simulation/arm7dof.xml) and split manifest [`datasets/02_MuJoCo_7DOF_Simulation/mujoco_split_manifest.json`](datasets/02_MuJoCo_7DOF_Simulation/mujoco_split_manifest.json).
- **CSV Columns**:
  ```text
  time,speed,label,onset_time,q_1,dq_1,tau_meas_1,tau_nom_1,r_1,q_2,dq_2,tau_meas_2,tau_nom_2,r_2,q_3,dq_3,tau_meas_3,tau_nom_3,r_3,q_4,dq_4,tau_meas_4,tau_nom_4,r_4,q_5,dq_5,tau_meas_5,tau_nom_5,r_5,q_6,dq_6,tau_meas_6,tau_nom_6,r_6,q_7,dq_7,tau_meas_7,tau_nom_7,r_7
  ```

#### (3) External UR10e Cobot Dataset (`03_UR10e_External_Cobot_raw.zip`)
- 6-DOF industrial collaborative robot telemetry (100 Hz) organized into `identification_data/`, `validation_data/`, and `collision/` (Pose 1 for training/validation; Pose 2 disjoint workspace for testing).
- **File List**: See [`datasets/03_UR10e_External_Cobot/manifest.txt`](datasets/03_UR10e_External_Cobot/manifest.txt).
- **Attribution**: The raw UR10e dataset is originally published by Park et al. (2021, 2022). Please cite the original authors when using the UR10e data:
  - K. M. Park, J. Kim, J. Park, and F. C. Park, *"Learning-based real-time detection of robot collisions without joint torque sensors,"* IEEE Robotics and Automation Letters, vol. 6, no. 1, pp. 103–110, 2021. DOI: `10.1109/LRA.2020.3033269`
  - K. M. Park, Y. Park, S. Yoon, and F. C. Park, *"Collision detection for robot manipulators using unsupervised anomaly detection algorithms,"* IEEE/ASME Transactions on Mechatronics, vol. 27, no. 5, pp. 2841–2851, 2022. DOI: `10.1109/TMECH.2021.3119057`

### 3. Quick Start (Python)

```python
import pandas as pd

# Example: load one Unitree G1 trajectory after extracting 01_Unitree_G1_Real_140trajs.zip
df = pd.read_csv("01_Unitree_G1_Real/collision/left_arm/collision_speed0.6_run09.csv")
print(df.shape, df.columns.tolist()[:10])
```

### 4. Upcoming Code Release
Upon formal acceptance of the manuscript, we will also upload the complete codebase of this project to this repository, including:
- Stage A–D dynamics feedforward, three-stream causal feature pipeline, and CATCN (KC-BN + tri-stream readout) PyTorch implementation;
- Five-run benchmarking, progressive ablation, cross-speed generalization, noise-stress, and UR10e re-training scripts;
- Streaming debouncing arbitration and Jetson Orin NX onboard deployment scripts.

---

<a id="中文说明"></a>
## 中文说明

### 1. 三个基准数据集概览

论文所使用的全部三个数据集已打包发布在 [**GitHub Releases (`v1.0-datasets`)**](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/tag/v1.0-datasets) 中，并附带 `SHA256SUMS.txt` 完整性校验文件：

| 序号 | 数据集名称 | 实验平台 / 仿真环境 | 自由度 | 采样率 | 轨迹/文件数量 | 压缩包体积 | 下载链接 |
| :-: | :--- | :--- | :-: | :-: | :---: | :---: | :--- |
| 1 | **Unitree G1 实机数据集** | 宇树 G1 人形机器人双 7-DOF 机械臂（真实硬件） | 7-DOF / 臂 | 100 Hz | 140 条连续轨迹（C0–C4 五类） | 22.0 MB | [`01_Unitree_G1_Real_140trajs.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/01_Unitree_G1_Real_140trajs.zip) |
| 2 | **MuJoCo 7-DOF 仿真数据集** | MuJoCo 3.17.0 受控动力学仿真 | 7-DOF | 100 Hz | 180 条轨迹 + 模型 XML + 划分清单 | 112.0 MB | [`02_MuJoCo_7DOF_Simulation_180trajs.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/02_MuJoCo_7DOF_Simulation_180trajs.zip) |
| 3 | **UR10e 外部工业协作臂数据集** | Universal Robots UR10e（公开外部基准） | 6-DOF | 100 Hz | 21 个 CSV（辨识 / 验证 / 碰撞） | 3.5 MB | [`03_UR10e_External_Cobot_raw.zip`](https://github.com/SunYuanJie2002/CATCN-Robot-Anomaly-Detection/releases/download/v1.0-datasets/03_UR10e_External_Cobot_raw.zip) |

### 2. 数据集结构说明

1. **Unitree G1 实机数据集（140 条连续轨迹）**：
   - 包含 5 类运行状态：`normal/`（C0 正常空载，24 条）、`payload/`（C1 合法负载抓取与搬运，36 条）、`collision/`（C2 物理碰撞，28 条）、`stagnation/`（C3 外加阻尼模拟关节卡滞/停滞，28 条）、`noise/`（C4 传感器噪声干扰，24 条）；
   - 按轨迹级物理隔离划分：92 条用于训练（其中 18 条亦用于阈值校准），48 条独立保留测试（20 条危险轨迹：10 C2 + 10 C3；28 条非危险轨迹：9 C0 + 10 C1 + 9 C4）。
2. **MuJoCo 7-DOF 仿真数据集（180 条连续轨迹）**：
   - 用于受控动力学基准对比与加性高斯白噪声压力测试（Clean / 30 dB / 20 dB / 10 dB SNR）；
   - 配套的 `arm7dof.xml` 仿真模型和 `mujoco_split_manifest.json` 划分清单已同时直接放在仓库 [`datasets/02_MuJoCo_7DOF_Simulation/`](datasets/02_MuJoCo_7DOF_Simulation/) 目录下，方便在线预览。
3. **UR10e 外部工业协作臂数据集（21 个 CSV 文件）**：
   - 6-DOF 协作机械臂 100 Hz 遥测数据，按工作空间位姿隔离划分（Pose 1 训练/验证，Pose 2 独立测试）；
   - **版权与引用说明**：UR10e 原始数据来源于 Park 等人（2021、2022）公开发表的工作，使用该部分数据时请规范引用上方列出的原论文。

### 3. 后续开源计划
- **未来论文正式录用后，我们还将把本项目的其余全部代码上传至该仓库**，包括：
  - 动力学前馈残差计算、三流因果特征构造与 CATCN（KC-BN 条件调制 + 三分支读出）完整 PyTorch 模型代码；
  - 五次重复实验、架构消融、跨速度泛化、噪声鲁棒性及 UR10e 外部重训评估脚本；
  - 流式去抖动仲裁与 Jetson Orin NX 板端推理部署脚本。

---

### License / 开源协议
- Repository documentation, manifests, and original Unitree G1 / MuJoCo dataset assets are provided for academic research use (see [`LICENSE`](LICENSE)).
- The external UR10e telemetry files remain subject to the original terms and attribution requirements of Park et al. (2021, 2022).
