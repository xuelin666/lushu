# 路书 · 安卓安装说明

这是一个 **PWA（渐进式网页应用）**。装到安卓手机上之后：

- 桌面有独立图标，点开全屏运行，**没有浏览器地址栏**
- **完全离线可用**——所有图片和数据都在文件里，飞机上、山里没信号也能开
- 数据存在手机本地，不上传任何服务器

---

## 一、为什么是 PWA 而不是 APK

这台电脑上没有 JDK、没有 Android SDK、没有 Gradle，出不了 APK。
PWA 是同样能"装到桌面像 App 一样用"的方案，而且不用签名、不用装驱动、不用连数据线。

要是以后想转成真正的 APK，看第五节。

---

## 二、装到手机（推荐：GitHub Pages，免费且自带 HTTPS）

PWA 必须通过 **https** 或 **localhost** 访问才能安装。你已经有 GitHub 账号，用它最快。

1. 在 GitHub 上新建一个仓库，比如 `lushu`
2. 把本目录里的 6 个文件全部上传上去（网页版拖拽上传即可）
3. 仓库 Settings → Pages → Source 选 `Deploy from a branch`，分支选 `main`，目录选 `/ (root)`，保存
4. 等一两分钟，访问 `https://<你的用户名>.github.io/lushu/`
5. 手机上用 **Chrome** 打开这个网址 → 右上角三个点 → **添加到主屏幕** / **安装应用**
6. 回到桌面，图标已经在那儿了

用命令行上传的话：

```
cd <本目录>
git init
git add .
git commit -m "路书"
git branch -M main
git remote add origin https://github.com/<你的用户名>/lushu.git
git push -u origin main
```

然后在仓库的 Settings → Pages 里打开即可。

---

## 三、先在电脑上试

PWA 的离线能力需要一个服务器环境，直接双击文件不行。本机跑一条命令即可：

```
cd <本目录>
python -m http.server 8777
```

浏览器打开 http://127.0.0.1:8777/ 。这时功能全都能用，
只是电脑浏览器不一定显示"安装"按钮。

**如果你只想在电脑上快速看看界面**，直接双击 `index.html` 也行——
除了离线缓存之外，其他功能（导入、增删清单、记录、导出）都能用。

---

## 四、第一次打开之后

1. 打开面板，填一张行程票，或者用一句话版生成提示词
2. 把提示词粘给联网 AI，拿回 JSON，粘回软件
3. 五个界面就按你的行程铺满了

数据存在浏览器里，**换浏览器或清理浏览器数据会丢**。
重要行程记得点「导出当前路书」存一份 JSON。

---

## 五、以后想转成真正的 APK

PWA 已经很够用，但如果哪天想要一个能直接传给朋友的 APK，两条路：

**路线 A：Capacitor（推荐，改动最小）**

需要一台装了 Android Studio 的电脑（会自动带 JDK 和 SDK）。

```
npm init -y
npm i @capacitor/core @capacitor/cli @capacitor/android
npx cap init 路书 com.yourname.lushu --web-dir=.
npx cap add android
npx cap sync
npx cap open android
```

然后在 Android Studio 里 `Build → Build Bundle(s) / APK(s) → Build APK(s)`。
产物在 `android/app/build/outputs/apk/debug/`。

**路线 B：找个在线打包服务**，把 zip 传上去直接出 APK。
这类服务质量参差，装出来的包也可能带广告，不太建议。

---

## 六、文件说明

| 文件 | 作用 |
| --- | --- |
| `index.html` | 应用本体。界面、素材、逻辑全在这一个文件里，约 1.2 MB |
| `manifest.webmanifest` | 告诉系统应用叫什么、图标是哪个、怎么启动 |
| `sw.js` | Service Worker，负责把外壳缓存下来实现离线 |
| `icon-192.png` / `icon-512.png` | 桌面图标 |
| `icon-maskable-512.png` | 安卓自适应图标（圆形/方形裁切时用） |

改完 `index.html` 之后，如果手机上还显示旧版，
把 `sw.js` 里的 `var CACHE = "lushu-v1";` 改成 `lushu-v2`，重新上传，再刷新一次即可。
