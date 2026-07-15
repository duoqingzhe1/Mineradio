# Mineradio 二创 — 进度日志

## Phase 1: 根因分析 ✅ 完成
- 读取全部 33,412 行源码
- 定位 6 个功耗因子
- 输出 findings.md

## Phase 2: 性能优化 ✅ 完成并验证
- commit 8af11f6: 渲染层优化（失焦休眠 + 45fps + balanced + FFT降频）
- commit 161cfff: Electron Chromium flags 优化
- commit 55446dc: 托盘完整控制 + 热键扩展
- 歌词修复: balanced quality min:1.0 budget:4500000
- 用户反馈: 全部正常 ✅

## Phase 3: UI/交互修复 ✅ 完成 (v3b/v3c/v3d)

### 3b: 侧边栏悬浮热区
- 修复 `canShowShelfHoverCueAt`: auto 模式下不再依赖 guide，热区始终可用
- 新增 `#shelf-edge-hint` 右边缘渐隐提示
- body class `shelf-auto-edge-hint` 控制提示显示

### 3c: 护眼暗色主题
- CSS `body.eyecare-mode`: canvas sepia 滤镜 + 暖色背景
- HTML toggle 在 fx panel
- localStorage 持久化 (`fx-eyecare-mode`)
- JS: `toggleEyecareMode()` / `applyEyecareMode()` / auto-init

### 3d: 右键菜单增强
- 自定义 `#context-menu` 浮动菜单
- 操作: 播放/暂停, 下一首, 上一首, 音量+/-, 歌单架, 护眼模式, 视觉控制台
- 自适应位置 + 选中状态指示
- 同时响应 canvas 和 HTML UI 区域

## Phase 4: 特色功能 ⏳ 待做
- 专注模式
- 双语歌词
- 均衡器
- 本地离线模式

## 下一步
1. 复制到安装目录测试
2. Git commit + push
3. 继续 Phase 4
