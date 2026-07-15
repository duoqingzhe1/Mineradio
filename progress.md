# Mineradio 二创 — 进度日志

## 完成状态总览

| 阶段 | 内容 | 状态 |
|------|------|------|
| Phase 1 | 根因分析 (6个功耗因子) | ✅ |
| Phase 2 | 性能优化 (45fps + 失焦休眠 + 托盘) | ✅ |
| Phase 3 | UI/交互修复 (热区/护眼/右键/全屏按钮) | ✅ |
| Phase 4.1 | 专注模式 (白噪音 + 番茄钟) | ✅ |
| Phase 4.3 | 双语歌词 (API tlyric + 桌面歌词双语) | ✅ |
| UI重构 | 网易云风格全局UI + 双模式切换 | ✅ |
| Phase 5 | 测试 + 构建 | ⏳ 待做 |

## 改动文件

| 文件 | 改动内容 |
|------|---------|
| `public/index.html` | 全局UI网易云风格重构、双模式(浏览/沉浸)、专注模式、双语歌词、护眼模式、热区、右键菜单、全屏按钮、性能优化 |
| `public/desktop-lyrics.html` | 双语歌词译文行渲染 + cascade reveal动画 |
| `desktop/main.js` | Chromium性能flags优化 + 系统托盘完整控制 |
| `task_plan.md` | 任务规划文档 |
| `progress.md` | 本文档 |
| `findings.md` | 根因分析报告 |

## 核心功能清单

### 性能优化
- 失焦30s自动休眠
- FPS: VSYNC→45fps
- 默认画质: high→balanced (min:1.0, budget:4.5M)
- Chromium反节能flags移除
- 系统托盘完整播放控制

### UI功能
- 侧边栏悬浮热区 (右边缘hover呼出歌单架)
- 护眼暖色模式 (sepia滤镜 + localStorage持久化)
- 右键增强菜单 (播放控制/音量/歌单架/护眼/设置/退出全屏)
- 全屏三按钮 (最小化/退出全屏/关闭 — 全屏时右上角显示)
- 网易云风格全局UI (毛玻璃/大圆角/金色强调色/胶囊按钮)
- 浏览/沉浸双模式 (暂停→3s→浏览态; 播放→立即沉浸态)

### 特色功能
- 专注模式: 5种白噪音(Web Audio) + 25+5番茄钟 + SVG进度环
- 双语歌词: 网易API tlyric解析 + 桌面歌词双行显示 + cascade reveal

## 已知问题
- setup_fork.js 含 GitHub token，已从最新commit移除但历史中仍有
- npm run build:win 尚未执行

## 下一步 (WorkBuddy)
1. npm run build:win 构建 Windows 安装包
2. 全功能回归测试
3. 可选: Phase 4.2 音乐律动壁纸 / Phase 4.4 本地离线模式
