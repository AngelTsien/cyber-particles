# NEON//PARTICLES · 赛博朋克手势粒子

单文件 `index.html`，用 Three.js 0.160（ShaderMaterial + 辉光/拖尾后期）和 MediaPipe Tasks Vision 0.10.14 HandLandmarker 实现。依赖全部从 jsDelivr CDN 加载，不需要 npm。

## 运行
摄像头只能在 **https 或 localhost** 下打开：
```bash
cd hand-particles && python3 -m http.server 8770
# 打开 http://localhost:8770
```
手机上测试要用 https，比如 GitHub Pages、Cloudflare Pages，或者 `npx localtunnel --port 8770`。

## 手势速查
| 手势 | 作用 |
|---|---|
| ✋ 张开 / ✊ 握拳（连续） | 粒子扩散放大 / 聚拢缩小 |
| ☝ 食指尖 | 粒子群跟着平移，同时是力场中心 |
| 🤏 捏合 | 抓住拖动，松开放下 |
| 双手 | 拉开、靠近缩放，相对转动旋转 |
| ↔ 快速挥手 | 切换场景 |
| ✌ | 换配色 |
| 👍 | 截图 |
| ✊ 握住超过 1 秒 | 爆炸成星尘再重组 |

没有摄像头时：单指拖动移动，双指捏合缩放和旋转，滚轮缩放，Shift 加拖动旋转，双击爆炸。
键盘：←/→ 切场景，C 换色，P 截图，R 录屏，F 全屏，S 设置，空格爆炸。

## 设置
⚙️ 面板里可以调：粒子数（手机最多 10 万，电脑最多 20 万）、发光、拖尾、力场强度、排斥或吸引、平滑度、自动降画质、配色、文字、照片、glTF、歌曲、麦克风。设置会存进 localStorage（`handParticles.settings.v2`）。

## 调试
- **摄像头权限**：地址栏的锁图标 → 网站设置 → 允许摄像头。如果以前拒绝过，要手动改回来。
- **https / 跨域**：用 `file://` 打开会拦截 ES Module，必须起一个本地服务器。CDN 本身支持 CORS；模型文件来自 storage.googleapis.com，被墙的网络需要代理，或者把 `.task` 和 `wasm` 下载到本地，再改 `loadLandmarker()` 里的路径。
- **手机摄像头选择**：点“⟲镜头”切换前后摄像头（`facingMode` 设为 user 或 environment），前置画面会镜像。
- **iOS Safari**：iPhone 不支持全屏 API，会改用伪全屏，建议“添加到主屏幕”；`<video playsinline muted>` 必须保留；音频要点一下按钮才能开始播放；GPU 代理失败时会自动改用 CPU。
- 卡顿时看左上角 FPS，自动降画质会先关辉光，再减少粒子。

## 扩展方向
- **真 glTF**：内置场景可以换成 `GLTFLoader` 加 `MeshSurfaceSampler`（`shapeModel()` 里已经写好）；`.gltf` 外链贴图的文件请打包成 `.glb`。
- **Shader 优化**：噪声换成 simplex/curl noise；把 aRnd 打包成半精度；根据距离减小 PointSize。
- **深度排序**：现在用加色混合，不需要排序。如果改成普通 alpha 混合，可以每隔几帧在 Worker 里按相机深度排序 index，或者用 OIT。
- **GPGPU**：用 `GPUComputationRenderer` 把位置和速度存到浮点纹理里，做真正的物理、力场和碰撞，百万级粒子也能跑。
