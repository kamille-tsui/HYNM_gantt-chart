# 📊 在线甘特图 — GitHub Pages 版（AES 加密 + 密码保护）

零依赖、纯前端甘特图，支持 GitHub Pages 静态托管 + 多人协作编辑，所有数据 **AES-256-CBC 加密** 后存放于仓库，页面需输入密码 `HYNM2026` 才能解密查看/编辑。

## ✨ 功能特性

- 🔒 **密码保护**：页面打开需输入 `HYNM2026`，密码错误不加载任何数据
- 🔐 **AES-256-CBC 加密**：仓库只存密文（`gantt-data.enc.json`），外人拿到也无法读取
- 📅 **三种视图**：按天 / 按周 / 按月，一键切换
- ➕ **增删改查**：添加、编辑、删除任务，双击任务条或卡片即可编辑
- 🔍 **搜索筛选**：按名称搜索 + 按阶段筛选
- 📈 **项目统计**：总任务数、已完成、进行中、整体进度
- 💾 **GitHub 同步**：配置 Token 后一键保存（自动加密后写回仓库）
- 📥📤 **导入导出**：支持 JSON 文件备份恢复（明文备份）
- 🎨 **颜色区分**：不同阶段用不同颜色标识
- 🔴 **今日线**：红色竖线标注当前日期位置
- 📆 **日期范围**：2026 ~ 2099 年，与真实日历一致（含闰年）

## 🐛 本次修复（数据不更新问题）

**问题**：修改数据保存后，GitHub 仓库出现了 `gantt-data.json`（明文），但刷新页面显示的仍是**最初的项目数据**。

**根因**：原版 `saveToGitHub` 把数据 `btoa(base64)` 编码后写入文件，而页面加载时用 `res.json()` 直接按 JSON 解析——写入的是 base64 字符串、读取时期望明文 JSON，格式不匹配导致解析失败，触发 `catch` 回退到默认初始数据。

**修复**：
1. 统一为 **加密体系**：页面加载 `gantt-data.enc.json` → 密码验证 → AES 解密 → 明文数据
2. `saveToGitHub` 保存时：**明文 → AES 加密 → base64 密文 → 写入 `gantt-data.enc.json`**
3. 读写走同一套加密格式，且对外始终是密文；fetch 加 `cache:'no-store'` 避免 Pages 缓存
4. 配置默认路径改为 `gantt-data.enc.json`

## 🚀 部署教程

### 第 1 步：创建 GitHub 仓库
- 登录 GitHub → 右上角 **+** → **New repository**
- 名称填 `gantt-chart`，选 **Public**，点 Create

### 第 2 步：上传文件
- 把 **`index.html` 和 `gantt-data.enc.json`** 拖进仓库（**不要**上传明文 `gantt-data.json`）
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
3. 打开甘特图页面 → 输入密码 `HYNM2026` 进入 → 点右上角 **⚙️ 配置GitHub** → 填入用户名、仓库名、分支、Token（数据文件路径保持默认 `gantt-data.enc.json`）
4. 之后点 **💾 保存修改并上传**，修改会加密后写回仓库，所有人刷新即可看到最新甘特图

## 🔑 修改密码

1. 编辑 `index.html`，把 `const ACCESS_PASSWORD = "HYNM2026";` 改成新密码
2. 运行 `python3 gen_enc.py 新密码` 重新生成 `gantt-data.enc.json`
3. 把 `index.html` 和新的 `gantt-data.enc.json` 一起上传仓库

## 📝 使用说明

| 操作 | 方式 |
|------|------|
| 进入甘特图 | 打开页面 → 输入密码 `HYNM2026` |
| 退出/锁定 | 点 🔓 退出，回到密码页并清除内存数据 |
| 添加任务 | 左侧面板填表 → 点添加 |
| 编辑任务 | **双击**甘特色条或左侧卡片 |
| 删除任务 | 悬停卡片 → 点 ✕ |
| 搜索/筛选 | 顶部搜索框 + 阶段下拉 |
| 切换视图 | 按天 / 按周 / 按月 |
| 保存到 GitHub | 点 💾 按钮 或 `Ctrl+S`（自动加密） |
| 导出 JSON | 点 📥 导出（明文备份） |
| 导入 JSON | 点 📤 选择文件恢复 |

## ⚠️ 注意事项

1. GitHub Pages 只支持**公开仓库**免费托管
2. Token 存在浏览器本地（localStorage），不会上传
3. 多人同时编辑以最后一次提交为准，建议编辑前先刷新
4. 前端密码保护可防"随手翻看/公司外无关人员"，但非银行级安全；极度机密项目建议配合私有部署
5. **仓库里只保留 `gantt-data.enc.json`（密文）**，不要提交明文 `gantt-data.json`

## 📄 文件说明

| 文件 | 作用 |
|------|------|
| `index.html` | 甘特图主页面（密码验证 + CryptoJS AES，密码 HYNM2026） |
| `gantt-data.enc.json` | **加密后**的任务数据（部署用，已验证可正确解密还原） |
| `gantt-data.json` | 明文任务数据（仅本地/脚本使用，**勿上传仓库**） |
| `gen_enc.py` | 改密/重新加密脚本（Python + pycryptodome） |
| `verify_enc.js` | 解密自验脚本（Node + crypto-js） |
| `README.md` | 本说明文档 |
