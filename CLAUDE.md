# Mineradio 二次开发 — WorkBuddy 接手指南

## 项目概述
Mineradio 是 Electron + Three.js 桌面音乐播放器。源仓库 `XxHuberrr/Mineradio`，fork 到 `duoqingzhe1/Mineradio`。

## 已完成工作 (v2.0)

### 改动文件
| 文件 | 内容 |
|------|------|
| `public/index.html` | **主文件** (27000行单文件), 所有改动在这里 |
| `public/desktop-lyrics.html` | 桌面歌词窗口 |
| `desktop/main.js` | Electron 主进程 |

### 性能优化
- 失焦30s自动休眠 (`autoPowerSaveBlurTimer`)
- FPS: VSYNC→45 (`RENDER_ACTIVE_FPS = 45`)
- 画质: high→balanced safe版 (`min:1.0, budget:4500000`)
- Chromium flags 精简, 系统托盘完整控制

### 新功能
- **双模式切换**: `body.idle-browse-mode` — 暂停3s后浏览态, 播放立刻沉浸态
- **专注模式**: 5种 Web Audio 白噪音 + 番茄钟 (`#focus-mode-panel`)
- **双语歌词**: API tlyric 解析 + 桌面歌词译文行
- **护眼模式**: `body.eyecare-mode` sepia 滤镜
- **右键菜单**: `#context-menu` 浮动菜单
- **侧边栏热区**: 右边缘悬浮呼出 3D 歌单架
- **全屏按钮**: 全屏时右上角保留三按钮

### 全局 UI 风格
网易云深色风格: 毛玻璃 backdrop-filter + 大圆角 + 金色强调色 (#d2a050) + 胶囊按钮

## 测试方式
修改 `public/index.html` → 复制到 `D:\mineradio安装\Mineradio\resources\app\public\` → 重启 Mineradio

## 下一步任务
1. `npm run build:win` 构建 Windows 安装包
2. 全功能回归测试
3. 可选: 音乐律动壁纸联动 / 本地离线模式 / 均衡器

## Git
- 分支: `v2-performance-rewrite`
- origin: `duoqingzhe1/Mineradio`
- upstream: `XxHuberrr/Mineradio`
- push 需要代理: `git -c http.proxy=http://127.0.0.1:7897 push origin v2-performance-rewrite`

## 禁止事项
- ❌ 不拆分 index.html 文件
- ❌ 不修改 Three.js 粒子渲染核心代码
- ❌ 不全局改成浅色主题
- ❌ setup_fork.js 含 token, 不要提交
