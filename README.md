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

## 🐛 本次修复（保存后刷新仍是旧数据）

**问题**：修改数据 → 💾 保存 → GitHub 仓库出现 `gantt-data.json`（明文）且显示已更新 → 刷新页面输入密码后，甘特图仍显示**最初的项目数据**。

**根因（两个叠加 Bug）**：
1. **`init()` 未定义**：文件末尾调用了 `init()`，但全文只有 `startApp(data)`、没有定义 `init` 函数，页面加载即抛 `ReferenceError`，密码验证流程未能正确串联。
2. **保存/加载的 base64 编码不一致（核心）**：
   - 保存：`encryptData` 返回**标准 base64 密文** → 又被 `btoa(unescape(encodeURIComponent(...)))` **二次 base64 编码**后写入仓库
   - 加载：`fetch` 拿到文本 → `decryptData(b64.trim(), pw)` 只做了**一次** base64 解码 → 得到的是"base64(密文)"而非真实密文 → `CryptoJS.AES.decrypt` 解析失败 → 落入 `catch` → 页面回退到初始默认数据

**修复**：
1. **新增 `init()` 函数**：密码已验证则直接渲染，否则显示密码遮罩，正确串联启动流程
2. **统一编码链路（与 Python `crypto_helper.py` 对齐）**：
   - 保存：`cipherB64 = encryptData(data)` → `btoa(unescape(encodeURIComponent(cipherB64)))` **仅一次** btoa → 写入仓库
   - 加载：`atob(文本)` **仅一次** → 得到 base64 密文 → 送 `decryptData` 解密
3. fetch 加 `cache:'no-store'` 避免 GitHub Pages 缓存干扰
4. 配置默认路径保持 `gantt-data.enc.json`

**验证**：端到端测试通过 —— 模拟"修改进度 + 新增任务 → 加密保存 → 刷新加载解密"，修改与新增均完整还原（E2E PASS）。

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
| `crypto_helper.py` | 改密/重新加密 + 解密验证脚本（Python + pycryptodome） |
| `verify_e2e.py` | 端到端加解密往返验证脚本（Python） |
| `README.md` | 本说明文档 |
