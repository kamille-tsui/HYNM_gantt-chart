# 📊 HYNM 在线甘特图（密码保护版）

纯前端甘特图，部署在 GitHub Pages，访问需密码（默认 `HYNM2026`，AES-256-CBC 加密数据）。

## ✨ 功能
- 🔒 **密码访问**：未输对密码不渲染任何数据
- 📅 三种视图：按天 / 按周 / 按月
- ➕ 增删改查任务，双击任务条/卡片编辑
- 💾 GitHub 同步：配置 Token 后一键加密写回仓库
- 📥📤 明文 JSON 导入/导出（本地备份）
- 🔴 今日线 + 按阶段颜色区分

## 🚀 部署
1. 解压，把 **`index.html` 和 `gantt-data.enc.json`** 上传到 GitHub 仓库（`gantt-data.json` 明文文件**不要上传**）
2. Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / `(root)` → Save
3. 访问 `https://你的用户名.github.io/仓库名/` → 输入密码 `HYNM2026` 进入

## 🔑 改密码
1. 编辑 `index.html`：把 `const ACCESS_PASSWORD = 'HYNM2026';` 改成新密码
2. 同步修改 `crypto_helper.py` 的 `PASSWORD = "HYNM2026"`
3. 运行 `python3 crypto_helper.py` 重新生成 `gantt-data.enc.json`
4. 把新的 `index.html` 和 `gantt-data.enc.json` 一起上传仓库

## 🧪 本地验证
- 生成/重新生成加密文件：`python3 crypto_helper.py`
- 验证 enc.json 可正确解密还原：`python3 crypto_helper.py verify`
- 端到端 save→load 往返测试：`python3 verify_e2e.py`

## ⚠️ 说明
- 前端密码保护可防"随手翻看/公司外无关人员"，非银行级安全；极度机密项目建议配合私有部署。
- 仓库里**只保留 `gantt-data.enc.json` 一个数据文件**，不要同时存在明文 `gantt-data.json`，避免格式混淆导致"刷新后显示旧数据"。
- 加密格式约定：仓库文件 = 标准 base64 密文 `cipherB64`（纯 ASCII），由 `btoa(cipherB64)` 包装后写入 GitHub Contents API；加载端 `atob` 还原后解密，保存/加载格式完全对称。
