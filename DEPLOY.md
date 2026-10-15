# 🚀 部署到 GitHub Pages 操作手册

**预计用时 3 分钟** ｜ 仓库地址：https://github.com/z243069942/camera-web

网页文件已经准备好了，你只需要创建远程仓库 → 推送 → 开启 Pages 三步。

---

## 第 1 步：创建远程仓库（1 分钟）

在浏览器打开：

👉 **https://github.com/new**

按下面填：

| 填写项 | 内容 |
|---|---|
| Owner | `z243069942`（你的账号） |
| Repository name | `camera-web` |
| Description | 手机摄像头视频捕获网页 |
| Public / Private | **Public** ← Pages 免费版必须是公开仓库 |

⚠️ **不要勾选** "Add a README file"、"Add .gitignore"、"Choose a license"（本地已有提交，勾了会冲突）

点击绿色的 **Create repository**

---

## 第 2 步：推送文件（1 分钟）

### 方式 A：VSCode（推荐，你平时用这个）

1. 在 VSCode 打开文件夹：
   `D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\_deploy`

2. 点左侧「源代码管理」图标 → 「发布到 GitHub 仓库」

3. 登录你的 GitHub 账号 → 选择 **Publish to GitHub**

4. 仓库名填 `camera-web` → 确认发布

### 方式 B：命令行

打开 VSCode 的终端（Ctrl + `），确认当前目录是 `_deploy`，然后粘贴执行：

```bash
cd /d D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\_deploy
git remote add origin https://github.com/z243069942/camera-web.git
git push -u origin main
```

推送时会要求登录：

- **Username**：填 `z243069942`
- **Password**：粘贴 **Personal Access Token**（不是登录密码！）

> Token 怎么拿：https://github.com/settings/tokens → Generate new token (classic) → 勾选 `repo` → Generate → 复制（**只显示一次**）

> VSCode 可能弹窗要求登录授权，点「使用 GitHub 登录」用浏览器授权更方便。

---

## 第 3 步：开启 Pages（1 分钟）

仓库创建并推送成功后：

1. 打开仓库的 **Settings**：
   👉 https://github.com/z243069942/camera-web/settings/pages

2. 找到 **Pages** 区域 → **Source** 下拉框选择：

   ```
   ● Deploy from a branch
   ```

3. 右侧两个下拉依次选：

   | 下拉框 | 选择 |
   |---|---|
   | 分支 branch | `main` |
   | 目录 folder | `/ (root)` |

4. 点击 **Save**

5. 等待 1–2 分钟，页面会自动刷新，出现：

   ```
   ✅ Your site is live at https://z243069942.github.io/camera-web/
   ```

---

## 🎉 完成后

**手机浏览器打开：**

# https://z243069942.github.io/camera-web/

打开后：

1. 顶部徽章显示「环境正常」✅
2. 点 **「开启摄像头」**
3. 浏览器弹权限请求 → 点 **允许**
4. 点 **「● 开始录制」** 开始录像
5. 录完点 **「■ 停止录制」**，下方列表出现片段
6. 点 **「下载」** 保存视频到手机

---

## ⚠️ 常见问题

**Q：推送时提示 `remote origin already exists`**
```bash
git remote set-url origin https://github.com/z243069942/camera-web.git
git push -u origin main
```

**Q：推送时提示 `Authentication failed`**
用的是登录密码而不是 Token。去 https://github.com/settings/tokens 生成一个，勾选 `repo` 权限。

**Q：推送时提示 `rejected: fetch first` / `non-fast-forward`**
远程仓库有本地没有的提交（可能建仓库时勾了 README）。执行：
```bash
git pull origin main --allow-unrelated-histories
git push -u origin main
```

**Q：Pages 页面显示 404**
Pages 还在构建，等 1–2 分钟刷新。还不行就回 Settings → Pages 确认 Source 选的是 `main` 分支 + `/ (root)`。

**Q：Pages 地址能打开但摄像头无反应**
必须是 `https://z243069942.github.io/...` 这个地址才能用摄像头。如果你是用 `http://` 或直接打开本地 `index.html`，浏览器会拒绝授权 —— 这是浏览器的安全策略，不是网页的问题。

**Q：想改网页内容**
改 `index.html` 后，在 `_deploy` 目录执行 `git add . && git commit -m "update" && git push`，1–2 分钟后线上自动更新。

---

## 📁 本地文件位置

```
D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\
├── index.html      网页本体（部署的就是它）
├── start_https.py  本地 HTTPS 测试服务器（不部署）
├── README.md       完整使用说明
└── _deploy\        待推送的 git 仓库
    ├── index.html
    ├── README.md
    ├── .nojekyll   告诉 GitHub Pages 不要用 Jekyll 处理
    └── .git\
```