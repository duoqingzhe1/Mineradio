# Mineradio 二次开发 — 任务规划 v2

## 目标硬件
联想拯救者 R9000P: RTX 独显 + 165Hz 2560×1600 屏

## 根因总结
1. 🔴 窗口可见但不聚焦时全速渲染（缺 blur-based 降功耗）
2. 🔴 165Hz VSYNC 全速渲染
3. 🟡 默认 high 画质 + 高 DPR
4. 🟡 每帧 FFT 频谱分析（165fps 下浪费）
5. 🟢 23 个独立 Mesh（可优化）
6. 🟢 dGPU 强制（不会切集显）

---

## Phase 1: 🔍 根因分析 ✅ 完成
- [x] 读取全部核心源码
- [x] 定位 6 个功耗因子
- [x] 输出根因分析报告（见 findings.md）

---

## Phase 2: 🚀 性能优化 ✅ 完成
- [x] 失焦 30s 自动休眠
- [x] FPS: 165→45
- [x] 默认画质: high→balanced (safe: min 1.0, budget 4.5M)
- [x] Chromium 反节能 flags 移除
- [x] 系统托盘完整控制

---

## Phase 3: 🎨 UI/交互修复 ✅ 完成

### Step 3.1: 侧边栏悬浮热区 ✅
- [x] auto 模式下热区始终可用（无需 guide 激活）
- [x] 右边缘渐隐提示 `#shelf-edge-hint`
- [x] body class 切换机制

### Step 3.2: 护眼暗色主题 ✅
- [x] CSS sepia + 暖色滤镜
- [x] fx panel 内 toggle
- [x] localStorage 持久化

### Step 3.3: 托盘完整控制 ✅ (Phase 2 已完成)

### Step 3.4: 右键菜单增强 ✅
- [x] 自定义 `#context-menu` 浮动菜单
- [x] 播放控制 + 音量 + 歌单架 + 护眼 + 设置
- [x] 自适应位置 + 选中状态

---

## Phase 4: ✨ 特色功能
**状态**: pending

### Step 4.1: 专注模式（白噪音+番茄钟+极简UI）
### Step 4.2: 音乐律动壁纸联动
### Step 4.3: 双语歌词
### Step 4.4: 本地离线模式

---

## Phase 5: 🧪 测试验证
**状态**: pending
- 启动性能面板验证 FPS/内存
- 回归测试
- npm run build:win 构建

---

## 挂载点
- `isDeepBackgroundMode()` @ L3443 → 修改触发条件
- `RENDER_ACTIVE_FPS` @ L3727 → 默认值修改
- `performanceQuality` defaults @ L3265 → 默认值修改
- `canShowShelfHoverCueAt()` @ L12910 → v3b 热区修复
- `desktop/main.js` L48-58 → Chromium switches 已就绪
- `desktop/main.js` tray → Phase 3a 完成

## 错误记录
| 错误 | 尝试 | 解决方案 |
|------|------|---------|
| API classifier blocked git clone | 3 | 重试后成功 |
| API classifier blocked npm install | 1 | 用户手动或下次重试 |
| skills.sh 无法访问 | 1 | 跳过，现有技能已够用 |
| gh CLI not found | 1 | 直接用 git 命令 |
| 歌词消失 | 3 | balanced min:1.0 budget:4.5M |
| main.js 版本不匹配 | 1 | 保留修改版 main.js |
