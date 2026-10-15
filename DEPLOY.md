# 🚀 部署到 GitHub Pages 操作手册

**✅ 已部署完成（2026-10-15）**

**线上地址：** https://zhaojiajia1030-cloud.github.io/camera-web/

手机浏览器直接打开上面这个地址 → 点「开启摄像头」→ 允许权限 → 即可录制下载。

仓库地址：https://github.com/zhaojiajia1030-cloud/camera-web

> 以下为首次部署的操作记录，如需重新部署可参考。

---

## ⚠️ 首次部署时踩的坑：仓库名一定要手动改

VSCode 的「发布到 GitHub 仓库」会自动拿**文件夹名**当仓库名。本地文件夹叫 `_deploy`，所以第一次发布建成了：

```
zhaojiajia1030-cloud/_deploy     ← 误建
zhaojiajia1030-cloud/camera-web   ← 正确
```

**解决办法**：先在网页上建好 `camera-web` 仓库，再执行 `git remote add origin <目标URL>` + `git push`，仓库名写在 URL 里就不会错。

---

## 第 1 步：删除误建的 _deploy 仓库

1. 打开 https://github.com/zhaojiajia1030-cloud/_deploy
2. 右侧 **Settings** → 滑到最底 **Danger Zone** → **Delete this repository**
3. 按提示输入完整仓库名 `_deploy` 确认删除

> 删掉不影响本地文件，只是清掉远程的垃圾仓库。

---

## 第 2 步：创建 camera-web 仓库

打开 👉 **https://github.com/new**

| 填写项 | 内容 |
|---|---|
| Owner | `zhaojiajia1030-cloud` |
| Repository name | **`camera-web`** ← 关键，别写成 `_deploy` |
| Description | 手机摄像头视频捕获网页 |
| Public / Private | **Public**（Pages 免费版必须公开） |

⚠️ **不要勾选** "Add a README file"、"Add .gitignore"、"Choose a license"
（本地已有提交，勾了会冲突）

点击绿色 **Create repository**

---

## 第 3 步：推送文件

### 方式 A：VSCode（推荐）

1. VSCode 打开文件夹：
   `D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\_deploy`

2. 点左侧「源代码管理」图标

3. 点 **「发布 Branch」/「Publish Branch」** 按钮
   （因为 remote 已清空，这里会提示发布到 GitHub）

4. 弹出的输入框里**把仓库名改成 `camera-web`**，登录账号选 `zhaojiajia1030-cloud`

5. 确认发布

### 方式 B：命令行（更可控，推荐）

VSCode 里按 `Ctrl + `` 打开终端：

```bash
cd /d D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\_deploy
git remote add origin https://github.com/zhaojiajia1030-cloud/camera-web.git
git push -u origin main
```

推送时要求登录：

- **Username**：`zhaojiajia1030-cloud`
- **Password**：粘贴 **Personal Access Token**（不是登录密码！）

> Token 获取：https://github.com/settings/tokens
> → Generate new token (classic) → 勾选 `repo` → Generate
> → 复制（**只显示一次，务必立刻保存**）

---

## 第 4 步：开启 Pages

仓库创建并推送成功后：

1. 打开 👉 **https://github.com/zhaojiajia1030-cloud/camera-web/settings/pages**

2. 找到 **Pages** 区域 → **Source** 下拉选择：

   ```
   ● Deploy from a branch
   ```

3. 两个下拉依次选：

   | 下拉框 | 选择 |
   |---|---|
   | 分支 branch | `main` |
   | 目录 folder | `/ (root)` |

4. 点 **Save**

5. 等 1–2 分钟，页面自动刷新，出现：

   ```
   ✅ Your site is live at
   https://zhaojiajia1030-cloud.github.io/camera-web/
   ```

---

## 🎉 完成后

**手机浏览器打开：**

# https://zhaojiajia1030-cloud.github.io/camera-web/

操作流程：

1. 顶部徽章显示「环境正常」✅
2. 点 **「开启摄像头」**
3. 浏览器弹权限请求 → 点 **允许**
4. 点 **「● 开始录制」** 开始录像
5. 录完点 **「■ 停止录制」**，下方列表出现片段
6. 点 **「下载」** 保存视频到手机

---

## ⚠️ 常见问题

**Q：仓库名又变成 `_deploy` 了**
本地文件夹就叫 `_deploy`，VSCode 默认用文件夹名。重新发布时在输入框里改成 `camera-web`，或者直接用上面的命令行方式（仓库名写在命令里，不会错）。

**Q：推送提示 `remote origin already exists`**
```bash
git remote remove origin
git remote add origin https://github.com/zhaojiajia1030-cloud/camera-web.git
git push -u origin main
```

**Q：推送提示 `Authentication failed`**
用的是登录密码而不是 Token。去 https://github.com/settings/tokens 生成，勾 `repo` 权限。

**Q：推送提示 `rejected: fetch first` / `non-fast-forward`**
建仓库时勾了 README，与本地提交无关联。执行：
```bash
git pull origin main --allow-unrelated-histories
git push -u origin main
```

**Q：Pages 显示 404**
构建需要 1–2 分钟，稍等刷新。还不行就回 Settings → Pages 确认 Source 是 `main` + `/ (root)`。

**Q：地址能打开但摄像头无反应**
必须用 `https://zhaojiajia1030-cloud.github.io/...` 这个地址。打开本地 `index.html`（`file://`）或用 `http://` 访问，浏览器都会拒绝授权 —— 这是浏览器安全策略，不是网页的问题。

**Q：想改网页内容**
改完 `index.html` 后，在该目录执行：
```bash
git add . && git commit -m "update" && git push
```
1–2 分钟后线上自动更新。

---

## 📁 文件位置

```
D:\BaiduNetdiskDownload\code\code\mobile_camera_capture\
├── index.html      网页本体（部署的就是它）
├── start_https.py  本地 HTTPS 测试服务器（不部署）
├── README.md       使用说明
├── 部署指南.md      本文件
└── _deploy\        待推送的 git 仓库
    ├── index.html
    ├── README.md
    ├── DEPLOY.md
    ├── .nojekyll   告诉 GitHub Pages 不要用 Jekyll 处理
    └── .git\
```

---

## 🔐 关于账号

本次部署使用的是 **`zhaojiajia1030-cloud`** 账号（VSCode 当前登录的账号）。

如果你更希望用另一个账号托管，删掉已建的仓库后重新创建时切换 Owner 即可。