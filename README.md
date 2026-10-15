# 📷 手机摄像头视频捕获网页

一个纯前端的手机摄像头捕获页面，打开网页即可调用手机摄像头预览、录像、拍照并下载视频。**无需安装 App，无后端服务器，视频不上传，全部在浏览器本地处理。**

---

## 一、先看这里：为什么双击 HTML 打不开摄像头

这是最容易踩的坑，**不是代码问题，是浏览器安全策略**：

| 打开方式 | 协议 | 能否访问摄像头 |
|---|---|---|
| 双击 `index.html` | `file://` | ❌ 被浏览器拒绝 |
| 电脑上 `http://localhost:8000` | `http://localhost` | ✅ 可以（localhost 被视为安全上下文） |
| 电脑上 `http://192.168.x.x:8000` | `http://` | ❌ 被拒绝 |
| **手机上 `https://...`** | `https://` | ✅ **可以（手机唯一可行的方式）** |

> 手机浏览器（Chrome / Safari / Edge）只在 **HTTPS** 或 **localhost** 下才开放摄像头 API。
> 这是硬性限制，任何 JavaScript 都绕不过去，必须用下面的方案。

页面内置了环境自检：打开后会显示「环境正常 / 环境受限」徽章，并在不满足条件时直接给出对应解决办法，不会让你对着黑屏猜原因。

---

## 二、在手机上运行（推荐方式）

### 方式 A：本地 HTTPS 服务器（局域网内测试，无需联网）

**适用**：想快速在手机上测试，手机和电脑连同一 WiFi。

```bash
# 在本目录执行（用你实际的 python 路径）
python start_https.py
```

脚本会自动：

1. 用 openssl 生成自签名证书（已安装 Git 的话自带 openssl）
2. 启动 HTTPS 服务并打印手机访问地址，形如：
   ```
   本机访问：  https://localhost:8443/index.html
   手机访问：  https://172.16.x.x:8443/index.html
   ```

然后：

1. 手机连**同一个 WiFi**
2. 用手机浏览器访问脚本打印的 `https://192.168.x.x:8443/index.html`
3. 首次会提示证书不受信任 → 选**「高级」→「继续访问」**
4. 点「开启摄像头」→ 允许权限 → 开始使用

> 指定端口：`python start_https.py 9000`

### 方式 B：部署到 GitHub Pages（免费 HTTPS，永久可用）

**适用**：长期使用、演示给学生看、多台设备随时访问。

你 GitHub 账号是 `z243069942`，直接：

1. 在仓库里建一个目录（如 `camera-web`），把 `index.html` 放进去
2. 仓库 **Settings → Pages → Source** 选 `main` 分支 + `/ (root)`
3. 得到地址 `https://z243069942.github.io/camera-web/index.html`
4. 手机浏览器打开该地址即可，无需任何额外操作

其他免费 HTTPS 托管同样可用：Netlify Drop、Vercel、Cloudflare Pages。

---

## 三、功能说明

| 功能 | 说明 |
|---|---|
| **实时预览** | 优先请求 1920×1080，顶部显示实际分辨率 / 帧率 / 前后置 |
| **● 录制 / ■ 停止** | 顶部 REC 徽章闪烁 + 计时器，录制结束显示时长与文件大小 |
| **多摄像头切换** | 下拉选择具体设备；单设备时自动按 `facingMode` 切前后置 |
| **补光灯** | 调用 `torch` 能力，无补光灯的设备自动隐藏该按钮 |
| **全屏预览** | 按钮或双击画面进入全屏，适合演示 |
| **录制结果管理** | 每段可播放 / 下载 / 删除，支持全部批量下载 |
| **镜像画面** | 前置镜头镜像，符合自拍习惯 |
| **九宫格构图线** | 拍摄时辅助对齐构图 |
| **后台自动停止** | 切到后台自动停止录制，防止空耗和数据异常 |
| **静音录制** | 关闭后只录画面不录音 |

### 兼容性说明

- **容器格式自动适配**：iOS Safari 输出 `.mp4`，Android Chrome 输出 `.webm`，页面通过 `MediaRecorder.isTypeSupported()` 自动探测，无需手动配置
- **iOS 要求**：Safari 14.3 以上才支持录制
- **首次授权**：必须由用户点击触发（浏览器规定），页面已用按钮引导

---

## 四、技术要点

核心就两个 API：

```javascript
// 1. 获取摄像头视频流
const stream = await navigator.mediaDevices.getUserMedia({
  audio: true,
  video: { facingMode: "user", width: { ideal: 1920 } }
});
video.srcObject = stream;

// 2. 录制并封装成 Blob
const recorder = new MediaRecorder(stream, { mimeType: "video/webm" });
recorder.ondataavailable = e => chunks.push(e.data);
recorder.onstop = () => {
  const blob = new Blob(chunks, { type: recorder.mimeType });
  const url = URL.createObjectURL(blob);
  // 用 <a download> 触发下载
};
```

几个容易踩的坑，页面里都已处理：

1. **`playinline` 属性** —— iOS Safari 默认会强制全屏播放竖屏视频，加上它才能内嵌显示
2. **容器格式探测** —— 不同浏览器支持的 `mimeType` 不同，必须逐个尝试 `isTypeSupported`
3. **资源释放** —— 切镜头前必须 `track.stop()` 释放旧设备，否则指示灯常亮且第二次调用可能失败
4. **URL 泄漏** —— 每个 Blob 的 objectURL 都要 `revokeObjectURL`，否则内存持续增长

---

## 五、常见问题

**Q：手机上打开是黑屏，提示"环境受限"**
A：用了 `http://` 访问。手机必须 HTTPS，请走上面的方式 A 或 B。

**Q：点了"开启摄像头"没反应**
A：检查浏览器地址栏是否已授权摄像头。Chrome 需要点地址栏左侧图标 → 网站设置 → 允许摄像头。

**Q：提示"摄像头被占用"**
A：相机、微信、通话软件正在使用摄像头，关闭后重试。

**Q：录出来的视频没有声音**
A：确认「录制声音」是勾选状态；且该开关只对**之后**录制的片段生效，已录片段无法补录。

**Q：iPhone 上录制按钮灰色**
A：Safari 14.3 以下不支持 MediaRecorder，请升级系统或换 Chrome。

---

## 六、文件说明

```
mobile_camera_capture/
├── index.html      网页本体（单文件，含全部 HTML/CSS/JS，无外部依赖）
└── start_https.py  本地 HTTPS 服务器（自动生成自签名证书）
```

`index.html` 是完全独立的单文件，可以直接拷到任何地方使用，不需要 `start_https.py`。