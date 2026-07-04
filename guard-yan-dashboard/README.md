# 守檐·GuardYan — Web Dashboard

长者随身应急呼救设备的可视化管理面板，面向子女/护工/社区工作人员。

## 在线体验

👉 **[点击在线体验](https://你的用户名.github.io/guardyan-dashboard/)**

## 本地运行

无需任何构建工具，直接用浏览器打开 `index.html` 即可。

```bash
# 方式一：直接打开
cd guard-yan-dashboard
start index.html

# 方式二：用 Python 简易 HTTP 服务器
cd guard-yan-dashboard
python -m http.server 8080
# 然后浏览器访问 http://localhost:8080
```

## 功能说明

| 页面 | 功能 |
|------|------|
| **事件总览** | 统计卡片（今日事件/待确认/绑定长者/用药）、事件时间线（SOS/跌倒/用药）、一键确认处理 |
| **长者档案** | 长者列表切换、基本信息、设备状态（电量/在线/固件）、7天事件统计图表 |
| **用药计划** | 周视图用药日历、用药规则添加/删除、服用状态追踪 |

## 模拟控制台

页面右下角可展开"模拟控制台"，支持：
- 模拟跌倒事件
- 模拟 SOS 呼救
- 模拟用药提醒
- 切换设备在线/离线状态

## 技术栈

- Vanilla HTML / CSS / JavaScript
- Chart.js（CDN）柱状图
- localStorage 数据持久化
- 零后端依赖

## 部署到 GitHub Pages

1. Fork / 创建 GitHub 仓库 `guardyan-dashboard`
2. 将本目录内容推送到 `main` 分支
3. 仓库 Settings → Pages → Source: Deploy from a branch → main / root
4. 等待 1~2 分钟，访问 `https://<你的用户名>.github.io/guardyan-dashboard/`
