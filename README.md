# 📊 在线甘特图 - GitHub Pages 版

一个纯前端、零依赖的在线甘特图工具，数据存储在 GitHub 仓库中，通过 GitHub Pages 托管为静态网站，支持多人协作实时更新。

## ✨ 功能特性

- 🎨 **可视化甘特图** — 按天/按周视图，颜色区分阶段，今日红线标记
- ➕ **增删改任务** — 双击甘特条或任务卡片即可编辑
- 💾 **GitHub 同步** — 配置 Token 后一键保存回仓库
- 📥📤 **导入/导出** — 支持 JSON 文件本地备份
- 🔍 **搜索筛选** — 按任务名搜索、按阶段筛选
- 📈 **项目统计** — 总任务数、完成数、进行中、整体进度一目了然
- 🌙 **零依赖** — 纯 HTML + CSS + JS，无需任何框架或构建工具

## 🚀 部署教程（5分钟搞定）

### 第一步：创建 GitHub 仓库

1. 登录 [GitHub](https://github.com)，点击右上角 **+** → **New repository**
2. 仓库名填写：`gantt-chart`（或任意名称）
3. 选择 **Public**（GitHub Pages 免费版需要公开仓库）
4. 勾选 **Add a README file**
5. 点击 **Create repository**

### 第二步：上传文件

将以下三个文件上传到仓库根目录：

| 文件 | 作用 |
|------|------|
| `index.html` | 甘特图页面（核心文件） |
| `gantt-data.json` | 任务数据源 |
| `README.md` | 本说明文件 |

**上传方式**：在仓库页面点击 **Add file** → **Upload files**，拖入三个文件，然后点 **Commit changes**。

### 第三步：开启 GitHub Pages

1. 进入仓库 → 点击 **Settings** 标签页
2. 左侧菜单找到 **Pages**（在 "Code and automation" 分类下）
3. **Source** 选择 **Deploy from a branch**
4. **Branch** 选择 `main`（或 `master`），文件夹选 `/ (root)`
5. 点击 **Save**

等待约 30 秒 ~ 1 分钟，GitHub 会提示你的站点已发布：

```
Your site is published at https://你的用户名.github.io/gantt-chart/
```

打开这个链接，就能看到甘特图了！🎉

### 第四步：配置写入权限（关键！）

只读访问不需要 Token，但要**保存修改回仓库**，需要配置 Personal Access Token：

1. 打开 [GitHub Token 设置页](https://github.com/settings/tokens)
2. 点击 **Generate new token** → **Generate new token (classic)**
3. 设置名称如 `gantt-chart-write`
4. **Expiration** 选 `No expiration`（或按需选择）
5. 勾选权限：**`repo`**（完整仓库读写权限）
6. 点击 **Generate token**
7. **复制生成的 Token**（只显示一次！）

然后在甘特图页面：
1. 点击右上角 **⚙️ 配置GitHub**
2. 填写：
   - **用户名**：你的 GitHub 用户名
   - **仓库名**：`gantt-chart`
   - **分支**：`main`
   - **Token**：刚才复制的 Token
   - **路径**：`gantt-data.json`
3. 点击保存

配置信息会存在浏览器 `localStorage` 中，**仅本机可见**，不会上传到任何服务器。

## 📝 使用说明

### 添加任务
左侧面板填写任务名称、阶段、负责人、开始日期、持续天数、进度、颜色 → 点击 **添加任务**

### 编辑任务
- **双击**甘特图中的色条 或 左侧任务卡片 → 弹出编辑窗口
- 修改后点击保存

### 删除任务
鼠标悬停在左侧任务卡片上 → 点击右上角出现的 ✕ 按钮

### 保存到 GitHub
编辑完任务后，点击右上角 **💾 保存到GitHub**，数据会写入仓库的 `gantt-data.json` 文件，所有访问该页面的人刷新后即可看到更新。

### 快捷键
- `Ctrl + S`：保存到 GitHub
- `Esc`：关闭弹窗

## 🔒 安全提示

- **Token 是敏感信息**，不要分享给他人
- 如果不想配置 Token，可以使用 **📥 导出JSON** 功能下载数据，手动替换仓库中的 `gantt-data.json`
- 也可以 Fork 这个仓库，在自己的副本中操作

## 📂 文件结构

```
gantt-chart/
├── index.html          # 甘特图页面（核心）
├── gantt-data.json     # 任务数据
└── README.md           # 说明文档
```

## 🛠️ 自定义

### 修改项目信息
编辑 `gantt-data.json` 中的 `project` 字段：
```json
"project": {
  "name": "你的项目名称",
  "manager": "项目经理",
  "startDate": "2026-08-01",
  "endDate": "2026-09-30"
}
```

### 添加/修改任务
在 `tasks` 数组中添加新对象：
```json
{
  "id": 11,
  "phase": "阶段名称",
  "name": "任务名称",
  "owner": "负责人",
  "start": "2026-08-15",
  "duration": 7,
  "progress": 0,
  "color": "#4CAF50"
}
```

### 支持的颜色
`#4CAF50` 绿 | `#2196F3` 蓝 | `#FF9800` 橙 | `#9C27B0` 紫 | `#F44336` 红 | `#00BCD4` 青

## ❓ 常见问题

**Q: 页面打不开？**
A: 检查仓库是否为 Public，GitHub Pages 是否已启用，等待 1-2 分钟让 CDN 生效。

**Q: 保存时报错 404？**
A: 检查仓库名、分支名是否正确，Token 是否有 `repo` 权限。

**Q: 保存时报错 401？**
A: Token 已过期或权限不足，重新生成 Token 并配置。

**Q: 多人同时编辑会冲突吗？**
A: GitHub 会保留最后一次提交。建议协作时先刷新获取最新数据再编辑。

## 📄 License

MIT License - 随意使用、修改、分发
