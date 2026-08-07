# 📊 在线甘特图 — GitHub Pages 版（密码保护）

一个零依赖、纯前端的甘特图工具，支持 GitHub Pages 静态托管 + 多人协作编辑。
**数据文件经 AES-256-CBC 加密后部署，打开页面需输入访问密码，防止公司外人员查看项目进度。**

## 🔐 访问密码

- 默认密码：**`HYNM2026`**
- 加密算法：AES-256-CBC（密钥由密码 SHA-256 派生）
- 数据文件 `gantt-data.enc.json` 为密文，未输对密码前页面不渲染任何任务数据

> ⚠️ 前端密码保护可防"随手翻看"，但非银行级安全。对机密项目建议配合私有部署 + Nginx Basic Auth 使用。

## ✨ 功能特性

- 🔒 **密码验证**：打开页面先输密码，错误无法进入
- 📅 **三种视图**：按天 / 按周 / 按月，一键切换
- ➕ **增删改查**：添加、编辑、删除任务，双击任务条或卡片即可编辑
- 🔍 **搜索筛选**：按名称搜索 + 按阶段筛选
- 📈 **项目统计**：总任务数、已完成、进行中、整体进度
- 💾 **GitHub 同步**：配置 Token 后一键保存到仓库（自动以密文上传）
- 📥📤 **导入导出**：导出为明文 JSON 备份；导入明文 JSON 后保存时自动加密
- 🔴 **今日线**：红色竖线标注当前日期位置

## 🚀 部署教程

### 第 1 步：创建 GitHub 仓库
- 登录 GitHub → 右上角 **+** → **New repository**
- 名称填 `gantt-chart`，选 **Public**，点 Create

### 第 2 步：上传文件
- 把 `index.html` 和 `gantt-data.enc.json` 拖进仓库（**不要**上传明文 `gantt-data.json`）
- Commit 提交

### 第 3 步：开启 GitHub Pages
- 仓库 → **Settings** → 左侧 **Pages**
- **Source** 选 `Deploy from a branch`
- **Branch** 选 `main` + `/ (root)`
- 点 Save → 等待约 1 分钟
- 访问地址：`https://你的用户名.github.io/gantt-chart/`

### 第 4 步：配置写入权限（可选，用于保存回仓库）
1. 打开 [GitHub Token 页面](https://github.com/settings/tokens)
2. 生成 Token，勾选 **`repo`** 权限
3. 打开甘特图页面 → 输入密码进入 → 点右上角 **⚙️ 配置GitHub** → 填入用户名、仓库名、分支、Token
4. 之后点 **💾 保存修改并上传**，修改会以**加密形式**写回仓库

## 🔑 如何修改密码

修改密码 = 改源码中的 `ACCESS_PASSWORD` 常量 + 用新密码重新生成 `gantt-data.enc.json`。

### 方法一：使用本项目脚本（推荐）
```bash
# 1) 编辑 index.html，把 ACCESS_PASSWORD 改成新密码
# 2) 用新密码重新加密（需要 Python3 + pycryptodome）
pip install pycryptodome
python3 gen_enc.py      # 读取 gantt-data.json → 生成 gantt-data.enc.json
node verify_enc.js      # 自检验证可正常解密
```
将新的 `index.html` 与 `gantt-data.enc.json` 一起上传仓库即可。

### 方法二：手动
1. 打开 `index.html`，找到 `const ACCESS_PASSWORD = 'HYNM2026';`，改成新密码
2. 用任意 AES-256-CBC 工具，以 `SHA-256(密码)` 作密钥、随机 IV，把 `gantt-data.json` 加密为 `iv(16字节)+密文` 的 base64，保存为 `gantt-data.enc.json`

## 📝 使用说明

| 操作 | 方式 |
|------|------|
| 进入页面 | 输入密码 `HYNM2026` → 验证 |
| 退出登录 | 点右上角 **🔓 退出**，回到密码页并清除内存数据 |
| 添加任务 | 左侧面板填表 → 点添加 |
| 编辑任务 | **双击**甘特色条或左侧卡片 |
| 删除任务 | 悬停卡片 → 点 ✕ |
| 搜索/筛选 | 顶部搜索框 + 阶段下拉 |
| 切换视图 | 按天 / 按周 / 按月 |
| 保存到 GitHub | 点 💾 按钮 或 `Ctrl+S`（自动加密上传） |
| 导出备份 | 📥 导出明文 JSON |
| 导入恢复 | 📤 选择明文 JSON 文件 |

## 📌 注意事项

1. GitHub Pages 只支持**公开仓库**免费托管；加密后仓库里的 `.enc.json` 为密文
2. Token 存在浏览器本地（localStorage），不会上传
3. 多人同时编辑以最后一次提交为准，建议编辑前先刷新
4. 日期范围支持 2026 ~ 2099 年
5. 忘记密码：用 `gen_enc.py` 以新密码重新生成 enc 文件即可重置

## 📄 文件说明

| 文件 | 作用 |
|------|------|
| `index.html` | 甘特图主页面（含密码验证 + CryptoJS AES） |
| `gantt-data.enc.json` | **加密后**的任务数据（部署用） |
| `gantt-data.json` | 明文任务数据（仅本地/脚本使用，勿上传） |
| `gen_enc.py` | 加密/改密脚本（Python + pycryptodome） |
| `verify_enc.js` | 解密自验脚本（Node + crypto-js） |
| `README.md` | 本说明文档 |
