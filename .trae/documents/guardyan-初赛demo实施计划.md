# 守檐·GuardYan 初赛 Demo 实施计划

## 一、Summary

本计划旨在将报名阶段输出的创意提案转化为**初赛可提交的、可在线体验的 Demo 作品**。根据 TRAE AI 创造力大赛硬件交互赛道规则，初赛交付物需让评审能够直接体验作品核心价值。策略为：**Web Dashboard 作为可在线交互的主入口（评审直接点击体验），硬件交互部分通过固件代码 + 演示视频呈现（符合赛道"硬件不便在线体验的部分用演示视频呈现"的规则）**。

Demo 核心交付物包括：
1. **可交互 Web Dashboard**（三页完整功能：事件总览、老人档案、用药计划），内置硬件数据模拟器，评审无需硬件即可完整体验产品闭环；
2. **ESP32 固件代码**（MicroPython），含跌倒检测算法、SOS 处理、MQTT 通信；
3. **演示视频素材**（设备外观、跌倒检测、SOS 呼救、用药提醒、Dashboard 录屏）；
4. **在线部署**（GitHub Pages 等），提交作品时直接提供 URL。

---

## 二、Current State Analysis

### 2.1 已有资产

| 资产 | 状态 | 说明 |
|------|------|------|
| `guard-yan-proposal.html` | 已完成 | 报名阶段创意提案，含完整产品定义、架构图、BOM、Wireframe、配色方案（`#FFFAF5` 暖底色、`#C0392B` 红色强调等） |
| `assets/hero_1280x720.jpg` | 已有 | 提案封面图 |
| `assets/architecture_1024x576.jpg` | 已有 | 系统架构概念图 |
| `_shared/js/mermaid.min.js` | 已有 | 提案中渲染 Mermaid 图表用的库 |

### 2.2 待建设资产

| 资产 | 优先级 | 说明 |
|------|--------|------|
| Web Dashboard（3 页） | P0 | 评审唯一可亲手交互的部分，决定第一印象 |
| Dashboard 数据模拟器 | P0 | 不依赖硬件即可生成事件流，是 Demo 完整性的关键 |
| ESP32 固件 | P1 | 技术深度体现，视频素材来源 |
| 演示视频（5 段） | P1 | 硬件交互赛道的必要补充材料 |
| GitHub Pages 部署 | P2 | 在线体验入口，强烈建议提交 |

### 2.3 外部约束

- **比赛截止日期**：2026 年 8 月 22 日（官方页面信息）。
- **硬件交互赛道规则**：硬件部分不便在线体验时，可用演示视频呈现。
- **评审关注点**：创新性（零学习成本设计）、完成度（端到端闭环可演示）、TRAE 工具使用痕迹、可体验性。
- **技术约束**：用户技术栈为 Python + VectorBT，有嵌入式课程设计经验，熟悉 ESP32 和传感器开发。

---

## 三、Proposed Changes

### 变更组 A：Web Dashboard 可交互 Demo

#### A1. 创建 Dashboard 项目骨架

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\index.html`
- **What**：创建事件总览页入口 HTML，包含顶部导航栏、统计卡片区、事件时间线主区域。
- **Why**：这是评审打开作品后看到的第一个页面，必须直观展示产品价值——"今天发生了什么、有多少待处理"。
- **How**：
  - 使用 Vanilla HTML/CSS/JS，零构建工具依赖，单文件可直接用浏览器打开运行。
  - 复用提案中已定义的 CSS 变量（`--bg: #FFFAF5`、`--accent: #C0392B` 等），保持品牌视觉一致性。
  - 导航栏包含四个链接：事件总览 | 老人档案 | 用药计划 | 模拟面板。
  - 统计卡片区用 4 列网格展示：今日事件数、待确认数、绑定老人数、今日用药数。
  - 事件时间线按时间倒序排列，每条事件左侧带颜色竖线（红色=SOS、橙色=跌倒、灰色=用药）。

#### A2. 创建老人档案页

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\profile.html`
- **What**：展示每位老人的基本信息、设备状态、近 7 天事件统计图表。
- **Why**：让评审理解"一个设备对应一位老人"的管理逻辑，体现产品对护工/子女场景的适配。
- **How**：
  - 左侧老人列表（张奶奶/李爷爷/王爷爷），点击切换右侧详情。
  - 详情区分三块：基本信息卡片（头像占位、年龄、住址、慢病史、紧急联系人）、设备状态（电量进度条、在线状态圆点、固件版本、最后上报时间）、7 天事件统计柱状图。
  - 柱状图使用 Chart.js（CDN 引入），数据从 `data-store.js` 读取，配色使用 `accent` / `accent2` / `muted`。
  - 提供【远程重启】【远程静音】按钮，点击后模拟指令下发（Toast 提示）。

#### A3. 创建用药计划页

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\medication.html`
- **What**：周视图用药日历 + 用药规则配置区。
- **Why**：用药提醒是三大核心功能之一，日历视图直观展示"老人本周吃药情况"。
- **How**：
  - 周视图用 7 列网格（周一~周日），每天显示药品名、剂量、时间、服用状态（绿色已服 / 红色未服 / 灰色跳过）。
  - 用药规则配置区提供表单：药品名称输入框、剂量输入框、时间选择器、重复规则下拉框（每日/隔天/自定义星期）。
  - 已配置规则列表，每项带编辑/删除按钮。
  - 所有数据通过 `data-store.js` 读写 localStorage，刷新不丢失。

#### A4. 实现全局数据存储与模拟器

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\js\data-store.js`
- **What**：统一数据模型、预填充模拟数据、提供 CRUD API。
- **Why**：Dashboard 三页共享数据，且必须在无硬件、无网络时完整运行。
- **How**：
  - 数据模型：
    - `elders[]`：含 `id`、`name`、`age`、`address`、`conditions`、`contactName`、`contactPhone`、`deviceId`、`battery`、`online`、`firmware`。
    - `events[]`：含 `id`、`type`（`fall`/`sos`/`med`）、`elderId`、`timestamp`、`status`（`pending`/`acknowledged`/`resolved`）、`audioUrl`（仅 SOS）。
    - `medications[]`：含 `id`、`elderId`、`drugName`、`dosage`、`time`、`repeatRule`（`daily`/`alternate`/`custom`）、`customDays[]`、`records[]`（按日期记录服用状态）。
  - 预填充 3 位老人、7 天历史事件（2 条 SOS、1 条跌倒、14 条用药）、2 条用药规则。
  - 提供 `addEvent()`、`acknowledgeEvent()`、`addMedication()`、`toggleMedicationRecord()` 等 API。

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\js\simulator-panel.js`
- **What**：一个可折叠的控制面板，用于在 Demo 时手动触发模拟事件。
- **Why**：评审没有真实硬件，必须通过模拟器体验"事件发生 → Dashboard 收到通知 → 确认处理"的完整闭环。
- **How**：
  - 面板默认折叠在页面右下角，标题为"模拟控制台"。
  - 四个按钮：【模拟跌倒】【模拟 SOS】【模拟用药提醒】【切换设备离线】。
  - 每次触发后，通过 `setTimeout` 延迟 1.5~2.5 秒再调用 `addEvent()`，制造"网络传输感"。
  - 模拟跌倒时：设备先显示"在线"，事件生成后，如果评审在 10 秒内未确认，模拟蜂鸣器状态为"鸣响中"。

#### A5. 统一样式与交互组件

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\css\main.css`
- **What**：提取三页共用样式，定义 CSS 变量、布局、组件规范。
- **Why**：保持视觉一致性，复用提案已验证的配色方案，减少重复代码。
- **How**：
  - `:root` 中定义全部 CSS 变量（`--bg`、`--bg2`、`--ink`、`--muted`、`--rule`、`--accent`、`--accent2`），与提案 HTML 中的变量值完全一致。
  - 定义 `.card`、`.btn-primary`、`.btn-ghost`、`.timeline-item`、`.status-dot` 等组件类。
  - 响应式断点：`< 768px` 时网格单列、导航栏折叠为汉堡菜单（可选简化方案：仅缩小间距）。
  - 打印样式：隐藏模拟面板、导航栏，确保打印事件时间线可读。

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\js\app.js`
- **What**：全局初始化逻辑：页面加载时从 localStorage 恢复数据、渲染当前页内容、绑定事件监听。
- **Why**：每页都需要执行数据初始化和事件绑定，提取公共逻辑。
- **How**：
  - 页面 `DOMContentLoaded` 时调用 `DataStore.init()`。
  - 根据当前页面 ID 调用对应渲染函数（`renderHome()` / `renderProfile()` / `renderMedication()`）。
  - 绑定导航栏当前页高亮。

### 变更组 B：ESP32 硬件固件

#### B1. 创建固件主程序框架

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\main.py`
- **What**：主事件循环 + 状态机调度，协调各模块工作。
- **Why**：固件是"技术深度"的核心证明，也是演示视频的素材来源。
- **How**：
  - 使用 MicroPython（用户 Python 熟练，开发周期短）。
  - 主循环 `while True`：
    1. 读取 MPU6050 数据；
    2. 更新跌倒检测状态机；
    3. 检查 SOS 按钮状态；
    4. 检查 MQTT 消息（非阻塞，超时 100ms）；
    5. 更新蜂鸣器/LED 输出；
    6. `machine.lightsleep(50)` 降低功耗。
  - 事件触发后，调用 `mqtt_client.publish_event(event_dict)`。

#### B2. 实现 MPU6050 驱动与姿态解算

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\mpu6050.py`
- **What**：I2C 通信读取原始数据，计算合加速度和姿态角。
- **Why**：跌倒检测的底层数据基础。
- **How**：
  - I2C 初始化：`machine.I2C(0, sda=machine.Pin(8), scl=machine.Pin(9), freq=400000)`。
  - 读取 `ACCEL_XOUT_H` 等寄存器，合成 16 位有符号整数。
  - 合加速度公式：`a_total = sqrt(ax^2 + ay^2 + az^2) / 16384.0`（单位：g）。
  - 俯仰角：`pitch = atan2(ay, sqrt(ax^2 + az^2)) * 57.3`；滚转角：`roll = atan2(-ax, sqrt(ay^2 + az^2)) * 57.3`。

#### B3. 实现跌倒检测算法

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\fall_detector.py`
- **What**：基于阈值 + 姿态角 + 静置确认的状态机。
- **Why**：产品的核心创新功能，算法逻辑必须可展示、可解释。
- **How**：
  - 五级状态机：`NORMAL` → `FREE_FALL` → `IMPACT` → `STILL` → `FALL_CONFIRMED`。
  - 状态转移条件：
    1. `NORMAL` → `FREE_FALL`：合加速度 `< 0.5g` 持续超过 100ms（失重）。
    2. `FREE_FALL` → `IMPACT`：在 500ms 窗口内合加速度 `> 2.5g`（撞击）。
    3. `IMPACT` → `STILL`：俯仰角或滚转角绝对值 `> 60°`（姿态剧变）。
    4. `STILL` → `FALL_CONFIRMED`：合加速度在 `0.9g~1.1g` 范围内持续 3 秒（静置确认）。
  - 误报抑制：若 `IMPACT` 后 5 秒内检测到角度恢复至 `< 30°`，则回到 `NORMAL`。
  - 确认跌倒后，返回事件字典 `{'type': 'fall', 'device_id': DEVICE_ID, 'timestamp': time.time()}`。

#### B4. 实现 SOS 按钮处理

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\sos_handler.py`
- **What**：GPIO 中断 + 长按消抖 + 录音触发。
- **Why**：一键呼救是产品的第二个核心场景，"长按 3 秒防误触"是设计亮点。
- **How**：
  - GPIO2 配置为输入，启用内部上拉电阻，`machine.Pin.IRQ_FALLING` 触发中断。
  - 中断处理函数记录按下时间，主循环中检查：若低电平持续 `> 2.5 秒`，判定为有效 SOS。
  - 有效 SOS 后：
    1. 设置全局标志 `sos_triggered = True`；
    2. 蜂鸣器长鸣模式（`buzzer.start_alert()`）；
    3. LED 快闪模式（`led.start_fast_blink()`）；
    4. 调用 `audio.record_3s()` 录制环境音（8kHz ADC 采样，3 秒，约 24KB）；
    5. Base64 编码音频数据，打包 MQTT 消息。

#### B5. 实现 MQTT 通信模块

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\mqtt_client.py`
- **What**：Wi-Fi 连接管理、MQTT 连接、发布/订阅。
- **Why**：硬件与 Dashboard 之间的桥梁，端到端闭环的必要条件。
- **How**：
  - 使用 `umqtt.simple.MQTTClient`（MicroPython 内置库）。
  - 连接公共 Broker `broker.hivemq.com`（端口 1883，无需认证），降低 Demo  setup 门槛。
  - Publish Topic：`guardyan/{device_id}/events`，QoS=1。
  - Subscribe Topic：`guardyan/{device_id}/cmd`，处理远程指令（`reboot`、`mute`）。
  - 连接断开后自动重连，重试间隔 5 秒，最多 10 次后进入低功耗等待。

#### B6. 实现声光反馈驱动

- **文件**：`e:\Work\GuardYan\guard-yan-firmware\buzzer.py`、`e:\Work\GuardYan\guard-yan-firmware\led.py`
- **What**：蜂鸣器 PWM 频率控制、LED 呼吸灯/闪烁效果。
- **Why**：硬件交互的"反馈层"，老人能听见/看见设备响应，是零学习成本设计的关键。
- **How**：
  - 蜂鸣器：GPIO3 输出 PWM，不同事件对应不同模式——跌倒确认后短鸣 3 声（频率 2kHz，每声 200ms，间隔 200ms）；SOS 触发后长鸣（持续 1 秒，间隔 500ms，循环直到收到 ACK）。
  - LED：GPIO4 输出，支持三种模式——呼吸灯（用药提醒，PWM 占空比正弦变化，周期 2 秒）；快闪（SOS，200ms 周期）；慢闪（设备离线，1 秒周期）。

### 变更组 C：演示材料与部署

#### C1. 录制演示视频（5 段）

- **文件**：`e:\Work\GuardYan\guard-yan-demo\video\*.mp4`
- **What**：覆盖产品核心场景的视频素材。
- **Why**：硬件交互赛道要求"不便在线体验的部分用视频呈现"，视频是评审理解硬件交互的唯一窗口。
- **How**：
  - `01-device-intro.mp4`（30 秒）：设备外观特写（28mm 圆形 PCB、红色 SOS 按钮、钥匙圈孔），佩戴方式（挂钥匙扣、放口袋）。
  - `02-fall-detection.mp4`（60 秒）：设备固定在手臂/人形模型上，模拟跌倒动作；同时录制设备端（蜂鸣器短鸣 3 声、LED 快闪）和 Dashboard 端（事件卡片弹出）；展示家属点击"确认"后蜂鸣停止的闭环。
  - `03-sos-alert.mp4`（45 秒）：长按红色按钮 3 秒；设备端蜂鸣器长鸣 + LED 快闪 + 录音；Dashboard 端红色 SOS 卡片弹出，点击播放语音；确认处理流程。
  - `04-med-reminder.mp4`（30 秒）：Dashboard 预设用药时间；到达时间后设备 LED 呼吸闪烁 + 蜂鸣器节奏鸣响；模拟老人按 SOS 确认已服药。
  - `05-dashboard-demo.mp4`（90 秒）：一镜到底录屏展示三页功能切换；使用模拟器面板触发事件，展示实时响应；结尾显示在线体验链接。
  - 视频规格：1080p，H.264 编码，总剪辑后控制在 4~5 分钟。

#### C2. 部署到 GitHub Pages

- **文件**：`e:\Work\GuardYan\guard-yan-dashboard\README.md`
- **What**：将 Dashboard 推送到 GitHub 仓库并启用 GitHub Pages。
- **Why**：提交作品时直接提供 URL，评审打开即可体验，大幅提升作品完整度印象。
- **How**：
  - 在 GitHub 创建仓库 `guardyan-dashboard`。
  - 将 `guard-yan-dashboard/` 目录内容推送到 `main` 分支。
  - 在仓库 Settings → Pages 中启用 GitHub Pages（Source: Deploy from a branch, Branch: main / root）。
  - 等待 1~2 分钟，验证 `https://<username>.github.io/guardyan-dashboard/` 可正常访问。
  - `README.md` 中写明：项目简介、在线体验链接、本地运行方式（直接打开 `index.html`）。

#### C3. 撰写作品提交说明

- **文件**：`e:\Work\GuardYan\guard-yan-demo\submission\作品说明.md`
- **What**：面向评审的技术说明文档。
- **Why**：评审需要快速理解"这是什么、怎么运行、技术亮点在哪"。
- **How**：
  - 结构：项目简介（100 字内）→ 技术架构（端-边-云三层图）→ 在线体验链接 → 视频链接（或附件）→ 硬件运行说明（烧录步骤、所需硬件清单）→ 技术亮点（零学习成本设计、BOM < 60 元、MicroPython 快速开发）。
  - 语言简洁，避免论文式叙述，多用 bullet point 和加粗。

---

## 四、Assumptions & Decisions

### 4.1 关键技术决策

| 决策点 | 选择 | 理由 |
|--------|------|------|
| Dashboard 技术栈 | Vanilla HTML/CSS/JS，无框架 | 零构建依赖，单文件可运行，部署极简，符合 TRAE 快速开发理念 |
| 固件语言 | MicroPython | 用户 Python 熟练，开发周期短；`umqtt.simple` 原生支持 MQTT；代码可读性强，视频可展示源码 |
| 数据持久化 | localStorage | 无需后端/数据库，评审离线也能完整体验 |
| MQTT Broker | HiveMQ 公共 Broker | 无需自建服务器，降低 Demo setup 门槛；离线模拟模式作为 fallback |
| 图表库 | Chart.js（CDN） | 轻量、美观、零配置，满足 7 天事件统计柱状图需求 |
| 固件开发板 | 先用 ESP32-C3 开发板 + 面包板 | 初赛时间有限，PCB 打样可延后到复赛；面包板足够验证算法和录制视频 |

### 4.2 假设条件

1. **评审主要体验 Web Dashboard**：硬件交互赛道允许视频补充，因此 Dashboard 的交互体验优先级绝对最高。
2. **用户可在本地运行 MicroPython**：假设用户已有 ESP32-C3 开发板、MPU6050 模块、USB 数据线，或可在短期内采购（模块成本 < 30 元）。
3. **公共 MQTT Broker 可用**：HiveMQ 公共 Broker 在 Demo 期间保持稳定；若不可用，Dashboard 的离线模拟模式仍能完整演示。
4. **用户有视频录制能力**：假设用户可用手机/屏幕录制软件完成 1080p 视频拍摄，或使用 TRAE 辅助剪辑（非必须）。
5. **用户有 GitHub 账号**：用于 GitHub Pages 部署；如无，可用 Gitee Pages 或 Netlify 替代。

### 4.3 风险与应对

| 风险 | 影响 | 应对 |
|------|------|------|
| 公共 MQTT Broker 不稳定 | Dashboard"真实模式"演示失败 | 离线模拟模式作为默认和 fallback，MQTT 仅作加分项 |
| 跌倒检测算法误报率高 | 评审质疑技术可行性 | Demo 阶段明确标注"算法待标定"，视频演示在可控场景下完成 |
| 硬件视频拍摄效果差 | 无法体现交互感 | 双机位录制（设备特写 + Dashboard 录屏），后期可合成画中画 |
| 时间不足无法焊板 | 无实体硬件 | 用 ESP32-C3 开发板 + 面包板搭建即可，外壳 3D 打印延后到复赛 |

---

## 五、Verification Steps

### 5.1 Dashboard 功能验证

| 检查项 | 验证方法 | 通过标准 |
|--------|---------|---------|
| 离线完整运行 | 断开网络，浏览器直接打开 `index.html` | 三页数据正常加载，所有按钮可点击，无报错 |
| 事件时间线响应 | 点击模拟面板"模拟 SOS" | 1.5~2.5 秒后顶部出现红色 SOS 事件卡片，带"播放语音"按钮 |
| 确认闭环 | 点击事件卡片"确认已处理" | 卡片状态变为"已处理"，localStorage 中 `status` 字段更新为 `resolved` |
| 用药添加持久化 | 用药页添加新规则，刷新页面 | 新规则仍显示，日历视图正确更新 |
| 柱状图数据一致 | 在档案页查看 7 天统计 | 柱状图高度与 data-store 中事件数量匹配 |
| 响应式布局 | Chrome DevTools 切换 iPhone 14 Pro 尺寸 | 页面不崩坏，时间线可读，导航可点击 |
| 在线部署有效 | 不同浏览器/手机打开 GitHub Pages 链接 | Chrome/Safari/微信内置浏览器均正常加载，首屏 < 3 秒 |

### 5.2 硬件固件验证

| 检查项 | 验证方法 | 通过标准 |
|--------|---------|---------|
| MPU6050 数据读取 | Thonny 串口监视器打印 | 静止时 `az ≈ 1g`，其他轴接近 0；晃动时各轴有变化 |
| 合加速度计算 | 快速甩动设备 | 峰值 `> 2g` 时串口打印 `IMPACT DETECTED` |
| 跌倒状态机 | 模拟完整跌倒动作（手持设备快速下落至软垫） | 串口按顺序输出 `NORMAL → FREE_FALL → IMPACT → STILL → FALL_CONFIRMED` |
| SOS 长按检测 | 按住按钮 3 秒 | 触发 `sos_event`，蜂鸣器长鸣，LED 快闪 |
| MQTT 连接 | 设备连接 Wi-Fi 后 | 串口打印 `MQTT connected`，Dashboard 模拟模式切换为真实模式可收到消息 |
| 声光模式 | 分别触发跌倒、SOS、用药提醒 | 跌倒=短鸣 3 声；SOS=长鸣循环；用药=呼吸灯 + 节奏鸣响 |

### 5.3 演示材料验证

| 检查项 | 验证方法 | 通过标准 |
|--------|---------|---------|
| 视频清晰度 | 播放器全屏查看 | 设备细节（按钮、LED）清晰可读，Dashboard 文字无模糊 |
| 视频时长 | 剪辑软件查看 | 总时长 4~5 分钟，节奏紧凑，无冗余片段 |
| 在线链接有效 | 提交前 24 小时再次访问 | 页面正常加载，模拟器可触发事件 |
| 作品说明完整 | 对照检查清单 | 含：简介、架构图、体验链接、视频链接、硬件清单、烧录步骤 |

---

## 六、文件总览（实施时需创建/修改）

```
e:\Work\GuardYan\                              # 已有
├── guard-yan-proposal.html                     # 已有（报名提案）
├── assets/                                     # 已有
├── _shared/                                    # 已有
├── guard-yan-dashboard\                        # 新建（Demo 主入口）
│   ├── index.html                              # 事件总览页
│   ├── profile.html                            # 老人档案页
│   ├── medication.html                         # 用药计划页
│   ├── css/
│   │   └── main.css                            # 统一样式
│   ├── js/
│   │   ├── app.js                              # 全局初始化
│   │   ├── data-store.js                       # 数据模型与 localStorage
│   │   ├── simulator-panel.js                  # 模拟控制面板
│   │   ├── event-timeline.js                   # 时间线渲染
│   │   ├── elder-card.js                       # 老人卡片渲染
│   │   ├── med-schedule.js                     # 用药日历渲染
│   │   └── mqtt-client.js                      # MQTT 连接（可选）
│   └── README.md                               # 部署说明
├── guard-yan-firmware\                        # 新建（固件代码）
│   ├── main.py                                 # 主循环
│   ├── mpu6050.py                              # 传感器驱动
│   ├── fall_detector.py                        # 跌倒检测算法
│   ├── sos_handler.py                          # SOS 按钮处理
│   ├── mqtt_client.py                          # MQTT 通信
│   ├── buzzer.py                               # 蜂鸣器驱动
│   ├── led.py                                  # LED 驱动
│   ├── audio.py                                # 麦克风录音
│   └── config.py                               # 配置参数
└── guard-yan-demo\                            # 新建（演示材料）
    ├── video/                                  # 5 段演示视频
    ├── screenshots/                            # Dashboard 截图
    └── submission/
        └── 作品说明.md                          # 评审说明文档
```
