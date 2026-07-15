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

## Phase 2: 🚀 性能优化（已加载 systematic-debugging + code-reviewer）
**状态**: in_progress

### Step 2.1: 模糊焦点自动降功耗 ⭐ 最高优先级
**影响**: 解决"开另一个窗口写代码时风扇狂转"的核心问题
**改动**: `public/index.html` — 修改 `isDeepBackgroundMode()` + 新增 30s 失焦计时器
**风险**: 低，现有休眠基础设施已完善

### Step 2.2: 默认 FPS 改为 45fps + 均衡画质
**影响**: 165fps → 45fps，像素量降低 70%
**改动**: 修改 `RENDER_ACTIVE_FPS = 45` 和 `performanceQuality: 'balanced'`
**风险**: 极低，两行配置

### Step 2.3: FFT 分析降频（每 3 帧跑一次）
**影响**: CPU 负载显著降低
**改动**: `animate()` 中加分析帧计数器
**风险**: 低，30fps 下每 100ms 分析一次足够

### Step 2.4: 窗口失焦降 powerPreference
**影响**: 不播放时允许集显
**改动**: `desktop/main.js` — 动态调 WebGL powerPreference

---

## Phase 3: 🎨 UI/交互修复
**状态**: pending

### Step 3.1: 侧边栏悬浮热区
### Step 3.2: 深色主题护眼方案
### Step 3.3: 托盘完整控制
### Step 3.4: 右键菜单增强

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

## 已加载技能
| 技能 | 用途 |
|------|------|
| planning-with-files | 任务规划文件管理 |
| systematic-debugging | Phase 1 根因分析 |
| superpowers | 全流程开发增强 |
| code-reviewer | 代码质量审查 |
| frontend-design | UI 主题设计 |
| find-skills | 查找其他所需技能 |

## 挂载点（Phase 1 分析时的发现）
- `isDeepBackgroundMode()` @ L3443 → 修改触发条件
- `RENDER_ACTIVE_FPS` @ L3727 → 默认值修改
- `performanceQuality` defaults @ L3265 → 默认值修改
- `animate()` @ L26620 → FFT 降频
- `desktop/main.js` L48-58 → Chromium switches 已就绪
- `powerPreference` @ L3773 → 考虑动态调整

## 错误记录
| 错误 | 尝试 | 解决方案 |
|------|------|---------|
| API classifier blocked git clone | 3 | 重试后成功 |
| API classifier blocked npm install | 1 | 用户手动或下次重试 |
| skills.sh 无法访问 | 1 | 跳过，现有技能已够用 |
| gh CLI not found | 1 | 直接用 git 命令 |
