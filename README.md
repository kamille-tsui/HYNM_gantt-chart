# 📊 在线甘特图 — GitHub Pages 版

一个零依赖、纯前端的甘特图工具，支持 GitHub Pages 静态托管 + 多人协作编辑。

## ✨ 功能特性

- 📅 **三种视图**：按天 / 按周 / 按月，一键切换
- ➕ **增删改查**：添加、编辑、删除任务，双击任务条或卡片即可编辑
- 🔍 **搜索筛选**：按名称搜索 + 按阶段筛选
- 📈 **项目统计**：总任务数、已完成、进行中、整体进度
- 💾 **GitHub 同步**：配置 Token 后一键保存到仓库，刷新即同步
- 📥📤 **导入导出**：支持 JSON 文件备份恢复
- 🎨 **颜色区分**：不同阶段的任务用不同颜色标识
- 🔴 **今日线**：红色竖线标注当前日期位置

## 🚀 部署教程

### 第 1 步：创建 GitHub 仓库
- 登录 GitHub → 右上角 **+** → **New repository**
- 名称填 `gantt-chart`，选 **Public**，点 Create

### 第 2 步：上传文件
- 把 `index.html` 和 `gantt-data.json` 拖进仓库
- Commit 提交

### 第 3 步：开启 GitHub Pages
- 仓库 → **Settings** → 左侧 **Pages**
- **Source** 选 `Deploy from a branch`
- **Branch** 选 `main` + `/ (root)`
- 点 Save → 等待约 1 分钟
- 访问地址：`https://你的用户名.github.io/gantt-chart/`

### 第 4 步：配置写入权限
1. 打开 [GitHub Token 页面](https://github.com/settings/tokens)
2. 生成 Token，勾选 **`repo`** 权限
3. 打开甘特图页面 → 点右上角 **⚙️ 配置GitHub** → 填入用户名、仓库名、分支、Token
4. 之后点 **💾 保存到GitHub**，修改就会写回仓库

## 📝 使用说明

| 操作 | 方式 |
|------|------|
| 添加任务 | 左侧面板填表 → 点添加 |
| 编辑任务 | **双击**甘特色条或左侧卡片 |
| 删除任务 | 悬停卡片 → 点 ✕ |
| 搜索任务 | 顶部搜索框实时过滤 |
| 阶段筛选 | 下拉选择阶段 |
| 切换视图 | 按天 / 按周 / 按月 |
| 保存到 GitHub | 点 💾 按钮 或 `Ctrl+S` |
| 导出 JSON | 点 📥 导出备份 |
| 导入 JSON | 点 📤 选择文件恢复 |

## 🐛 Bug 修复记录

### v1.3 — 任务条位置偏移修复
- **问题**：新建任务的开始日期在甘特图上显示偏移约 5 天（如设置 7/30 开始，显示成 8/4）
- **根因**：`gantt-bar-wrapper` 的 containing block 是 `gantt-bar-area`（position:relative），而 `gantt-bar-area` 在 `gantt-row` 中已经从 150px 处开始。原代码 `left = 150 + offsetDays * dayWidth` 中的 150px 是多余的，导致任务条右移 150px ≈ 5.36 天
- **修复**：将三个视图（按天/按周/按月）中任务条的 left 计算统一改为 `left = offsetDays * dayWidth`（去掉 +150）
- **今日线和网格线不受影响**：它们在 `gantt-body` 内渲染，`left = 150 + i * dayWidth` 是正确的

## 📌 注意事项

1. GitHub Pages 只支持**公开仓库**免费托管
2. Token 存在浏览器本地（localStorage），不会上传
3. 多人同时编辑以最后一次提交为准，建议编辑前先刷新
4. 日期范围支持 2026 ~ 2099 年

## 📄 文件说明

| 文件 | 作用 |
|------|------|
| `index.html` | 甘特图主页面（纯前端，零依赖） |
| `gantt-data.json` | 任务数据源（页面自动加载） |
| `README.md` | 本说明文档 |
