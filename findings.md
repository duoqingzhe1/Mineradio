# Mineradio 性能根因分析报告

## 源码规模
| 文件 | 行数 | 大小 | 角色 |
|------|------|------|------|
| public/index.html | 26,879 | 1.35 MB | 全部 UI + CSS + Three.js 粒子 + 3D 歌单架 + 视觉控制台 |
| server.js | 4,203 | 168 KB | Express 后端 API 代理 |
| desktop/main.js | 1,466 | 50 KB | Electron 主进程 |
| dj-analyzer.js | 864 | 34 KB | 音频节拍分析 |

---

## 一、现有性能基础设施（代码中已存在）

Mineraio 的底层优化基础**出乎意料地完善**：

| 机制 | 实现位置 | 效果 |
|------|---------|------|
| 自适应帧率 | `shouldSkipAdaptiveRenderFrame()` L26595 | 按 target FPS 跳帧 |
| 深度休眠模式 | `render-deep-sleep` CSS class L101 | 隐藏全部 canvas |
| 画质档位 | eco/balanced/high/ultra L3738-3741 | 控制 pixel ratio cap |
| 渲染器休眠 | `applyRendererPowerMode()` L3658 | 休眠时缩小到 4×4 像素 |
| GPU 内存清理 | `trimRuntimeCaches()` L3619 | 清理纹理/封面/节拍图缓存 |
| dGPU 强制 | `powerPreference: 'high-performance'` L3773 | 强制独显 |
| Chromium 加速 | d3d11, zero-copy, gpu-raster L48-58 | GPU 光栅化 |
| 性能面板 | `__mineradioPerfSnapshot` L3618 | FPS/内存/几何体数 |
| Heap 采样 | `performance.memory` L3585 | 12秒间隔检查 |
| 缓存上限 | 封面180张/节拍36首/深度图10张 | 自动淘汰旧缓存 |

---

## 二、根因分析：你的 R9000P 为什么风扇狂转

### 🔴 根因 #1（最严重）：可见但不聚焦时仍全速渲染

```javascript
// L3443 — isDeepBackgroundMode()
function isDeepBackgroundMode() {
  if (isLiveBackgroundKeepMode()) return false;
  return !!(document.hidden || desktopRuntimeState.minimized || desktopRuntimeState.visible === false);
}
```

**问题**：休眠模式只在窗口**最小化**或**完全隐藏**时触发。窗口在副屏可见但你在 VSCode 里写代码 → `document.hidden=false` + `minimized=false` → **全功率渲染**。

### 🔴 根因 #2：165Hz 屏幕以 VSYNC 全速渲染

```javascript
// L3726-3727
var RENDER_VISIBLE_VSYNC = true;
var RENDER_ACTIVE_FPS = 0;  // 0 = 显示器原生刷新率
```

**问题**：R9000P 的 165Hz 屏幕 = 每秒 165 帧。听歌不需要 165fps，30fps 肉眼无差别。**每帧都做完整 Three.js 渲染 + FFT 频谱分析**，功耗至少是 60fps 的近 3 倍。

### 🟡 根因 #3：默认画质 'high' + 高 DPR

```javascript
// L3265
performanceQuality: 'high',  // cap: 1.75x pixelRatio
```

R9000P 2560×1600 + dpr=2 → `high` 模式下可渲染 1.75x = 约7M 像素/帧 × 165fps ≈ **每秒 11.5 亿像素**的 WebGL 渲染量。

### 🟡 根因 #4：每帧 FFT 频谱分析

```javascript
// L26645-26683 — animate() 中每帧执行
analyser.getByteFrequencyData(frequencyData);   // 2048 bins
analyser.getByteTimeDomainData(timeDomainData); // 2048 samples
// + 循环计算 kick/vocal/mid/treble/RMS + 峰值追踪 + beat detection
```

即使在 165fps 下，这每 6ms 就做一次完整 FFT 分析。实际只需要 20-30fps 的音频分析频率。

### 🟢 根因 #5（次要）：23 个独立 Mesh 实例

`new THREE.Mesh|Points` 共 23 处，每个生成独立 draw call。虽然不算多，但可优化为 InstancedMesh 减少 GPU 往返。

### 🟢 根因 #6（次要）：dGPU 强制

`powerPreference: 'high-performance'` 阻止了 NVIDIA Optimus 切换到集显。听歌场景下强制独显增加 10-15W 功耗。

---

## 三、功耗计算（估算）

| 因素 | 优化前 | 优化后（预期） |
|------|--------|---------------|
| 渲染帧率 | 165fps（VSYNC） | 30fps（节能模式默认） |
| pixelRatio cap | 1.75 | 1.0（balanced） |
| 每帧像素 | ~7M | ~4M |
| 每秒像素 | ~1.15B | ~120M |
| dGPU 功耗 | ~30-40W | 可切集显 ~5-10W |
| 窗口失焦行为 | 全速 | 自动降档 |
| **总预期 GPU 功耗降幅** | — | **70-80%** |

---

## 四、代码质量评估

| 维度 | 评分 | 说明 |
|------|------|------|
| 性能基础架构 | 8/10 | 休眠/自适应帧率/画质档位已就绪 |
| UI 代码 | 4/10 | 单文件巨石，CSS 变量优秀但耦合严重 |
| 内存管理 | 7/10 | 缓存上限+清理逻辑到位 |
| 错误处理 | 6/10 | 有 try-catch 但部分回调无保护 |
| 可维护性 | 3/10 | 无模块化、无测试、无 JSDoc |
