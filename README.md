# 📊 在线甘特图 — GitHub Pages 版（AES 加密 + 密码保护）

零依赖、纯前端甘特图，支持 GitHub Pages 静态托管 + 多人协作编辑，所有数据 **AES-256-CBC 加密** 后存放于仓库，页面需输入密码 `HYNM2026` 才能解密查看/编辑。

## ✨ 功能特性

- 🔒 **密码保护**：访问需输入密码 `HYNM2026`，数据 AES-256-CBC 加密存储
- 📅 **三种视图**：按天 / 按周 / 按月，一键切换
- ➕ **增删改查**：添加、编辑、删除任务，双击任务条或卡片即可编辑
- 🔍 **搜索筛选**：按名称搜索 + 按阶段筛选
- 📈 **项目统计**：总任务数、已完成、进行中、整体进度
- 💾 **GitHub 同步**：配置 Token 后一键保存到仓库，刷新即同步
- 📥📤 **导入导出**：支持 JSON 文件备份恢复
- 🎨 **颜色区分**：不同阶段的任务用不同颜色标识
- 🔴 **今日线**：红色竖线标注当前日期位置
- 📅 **日期范围**：2026 ~ 2099 年，与真实日历（含闰年）一致

## 🚀 部署教程

### 第 1 步：创建 GitHub 仓库
- 登录 GitHub → 右上角 **+** → **New repository**
- 名称填 `gantt-chart`，选 **Public**，点 Create

### 第 2 步：上传文件
- 把本包的 **`index.html` 和 `gantt-data.enc.json`** 拖进仓库（**不要**上传明文 `gantt-data.json`）
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
3. 打开甘特图页面 → 点右上角 **⚙️ 配置** → 填入用户名、仓库名、分支、Token
4. 之后点 **💾 保存修改并上传**，修改会加密写回仓库，刷新即同步

## 📝 使用说明

| 操作 | 方式 |
|------|------|
| 进入甘特图 | 输入密码 `HYNM2026` → 进入 |
| 添加任务 | 左侧面板填表 → 点添加 |
| 编辑任务 | **双击**甘特色条或左侧卡片 |
| 删除任务 | 悬停卡片 → 点 ✕ |
| 搜索/筛选 | 顶部搜索框 + 阶段下拉 |
| 切换视图 | 按天 / 按周 / 按月 |
| 保存到 GitHub | 点 💾 按钮 或 `Ctrl+S` |
| 退出登录 | 点 🔓 退出（清空内存数据） |

## 🔑 修改密码

1. 编辑 `index.html`，把 `const ACCESS_PASSWORD = 'HYNM2026';` 改成新密码
2. 运行 `python3 crypto_helper.py encrypt gantt-data.json gantt-data.enc.json 新密码`
3. 把新 `index.html` 和 `gantt-data.enc.json` 一起上传仓库

## 🐛 Bug 修复记录

### v1.5 — 修复"正确密码也提示 Malformed UTF-8 data / 密码错误"
- **问题**：输入正确密码 `HYNM2026` 仍提示 `Malformed UTF-8 data` 或 `密码错误`，无法进入
- **根因**：保存端与加载端对仓库文件的 base64 编码层数不一致
  - 保存端 `saveToGitHub` 使用 `btoa(unescape(encodeURIComponent(cipherB64)))` 这套过时的 UTF-8 绕路方式对密文二次包装，在严格/异常路径下会写出损坏内容
  - 加载端又对 fetch 文本做 `atob` 试图"解一层"，与 Python 生成的纯 `cipherB64` 文件格式互相不兼容，导致密文被错误解码后送入 AES，触发 `Malformed UTF-8 data`
- **修复**：统一约定**仓库文件 = 标准 base64 密文 `cipherB64`（纯 ASCII）**
  - 保存端：`encryptData` 得到 `cipherB64`，直接 `btoa(cipherB64)`（因 cipherB64 为纯 ASCII，`btoa` 即"对原始字节做 base64"，符合 GitHub Contents API 要求）
  - 加载端：`fetch` 文本 `trim()` 即为 `cipherB64`，**直接送 `decryptData`**，去掉 `atob` 兼容分支
  - 与 Python `crypto_helper.py` 的 `b64encode(ct).decode('ascii')` 输出格式完全一致
- **验证**：端到端测试通过 —— 正确密码解密 11 个任务全部还原；错误密码正确拒绝；save→load 往返（修改进度 + 新增任务）完整还原

## 📌 注意事项

1. GitHub Pages 只支持**公开仓库**免费托管
2. Token 存在浏览器本地（localStorage），不会上传
3. 仓库里**只保留 `gantt-data.enc.json` 一个数据文件**，不要同时存在明文 `gantt-data.json`
4. 前端密码保护可防"随手翻看/公司外无关人员"，但非银行级安全；极度机密项目建议配合私有部署
5. 日期范围支持 2026 ~ 2099 年，含闰年正确计算

## 📄 文件说明

| 文件 | 作用 |
|------|------|
| `index.html` | 甘特图主页面（密码验证 + CryptoJS AES） |
| `gantt-data.enc.json` | **加密后**的任务数据（部署用） |
| `gantt-data.json` | 明文任务数据（仅本地/脚本使用，勿上传仓库） |
| `crypto_helper.py` | 改密/重新加密 + 解密验证脚本（Python + pycryptodome） |
| `README.md` | 本说明文档 |
