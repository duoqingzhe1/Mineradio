# Mineradio 二次开发 — 任务规划 v3

## 目标硬件
联想拯救者 R9000P: RTX 独显 + 165Hz 2560×1600 屏

---

## Phase 1-3: ✅ 全部完成

| 阶段 | 内容 | 改动量 |
|------|------|--------|
| Phase 1 | 根因分析 (6个功耗因子) | findings.md |
| Phase 2 | 性能优化 (45fps + 休眠 + 托盘) | ~150 行 |
| Phase 3a | 系统托盘 | ~60 行 main.js |
| Phase 3b | 侧边栏悬浮热区 | ~30 行 |
| Phase 3c | 护眼暗色主题 | ~60 行 |
| Phase 3d | 右键增强菜单 | ~90 行 |

---

## Phase 4: ✨ 特色功能

| Step | 内容 | 状态 | 改动量 |
|------|------|------|--------|
| 4.1 | 专注模式 | ✅ 已审查修复 | ~500 行 |
| 4.3 | 双语歌词 | ✅ 已完成 | ~80 行 index + ~30 行 lyrics |
| 4.2 | 音乐律动壁纸联动 | ⏳ | 高难度 |
| 4.4 | 本地离线模式 | ⏳ | 高难度 |

---

## Phase 5: 🧪 测试 + 构建 ← 当前优先

### 5.1 功能回归测试
- [ ] 专注模式：番茄钟正常倒数（已修复 interval bug）
- [ ] 双语歌词：fx面板"歌词翻译→双语歌词"开关
- [ ] 护眼模式：fx面板 toggle + canvas 暖色滤镜
- [ ] 右键菜单：播放控制 / 音量 / 歌单架 / 护眼
- [ ] 侧边栏热区：自动隐藏 → 右边缘悬浮呼出
- [ ] 托盘控制：播放/暂停/下一首/音量
- [ ] 确认 Phase 2 旧功能完好：歌词显示 + 30s休眠

### 5.2 构建打包
```bash
cd d:/vscode claude code/Mineradio
npm run build:win
```
- 输出：Windows .exe 安装包
- 预期位置：`dist/` 目录

---

## Phase 4.2 / 4.4: 可选后续

### 4.2 音乐律动壁纸
- 方案A：根据当前歌曲色调动态换壁纸（简单，静态）
- 方案B：实时频谱渲染到桌面壁纸（复杂，需 Windows DWM API）
- **建议**：先做方案A，方案B后续评估

### 4.4 本地离线模式
- 文件导入（拖拽 / 文件选择器）
- 本地 MP3/FLAC 解码播放
- 本地音乐库管理
- 与云歌单共存

---

## 改动文件清单

| 文件 | Phase | 内容 |
|------|-------|------|
| `public/index.html` | 2-4 | 所有前端改动 |
| `desktop/main.js` | 2, 3a | Chromium flags + 托盘 |
| `public/desktop-lyrics.html` | 4.3 | 双语桌面歌词 |
| `task_plan.md` | — | 本文档 |
| `progress.md` | — | 进度日志 |
| `findings.md` | 1 | 根因分析 |

## 错误记录
| 错误 | 尝试 | 解决方案 |
|------|------|---------|
| 歌词消失 | 3 | balanced min:1.0 budget:4.5M |
| main.js 版本不匹配 | 1 | 保留修改版 main.js |
| focusTimerComplete interval 泄漏 | 1 | clearInterval 后再创建新的 |
| focusTrackPoll interval 永不清除 | 1 | 保存引用，命名 start/stop |
| setup_fork.js 含 token 被 GitHub 拦截 | 2 | rm + .gitignore |
| 双语歌词 toggle 藏在折叠区 | 1 | 移到歌词翻译独立 section |
