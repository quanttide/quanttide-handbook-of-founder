# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 变更

- `write/` 三篇按工作手册体裁重写：去掉原始日志摘录式叙述与「定位」节，改为主题化的做法、判断与边界

### 新增

- 初始化手册仓库：`README.md`（定位与同域分工）、`index.md`（目录入口，待补充）
- 迁入 `write/`：创作的做法与卡点三篇（创作动机 / 创作方法 / 创作困境），源自 `data/context/write/essay/`
- `write/` 文件名改英文（`motivation.md` / `methods.md` / `blockers.md`），按手册风格补「定位」节；新增 `myst.yml`（书名、目录与导航）
- 新增 `work/human-agent-collaboration.md`：人机协作的三步做法（识别信号、冷静处理、沉淀规则），从 `data/profile/think/ai-collaboration.*` 的关键逻辑整理
- `auth/icloud.md`：Apple 账号的命令行认证（区域域名、会话与常见排查）
- 新增 `media/` 目录：`material/photo.md` 从 iCloud 相册同步照片与视频素材（自 `auth/icloud.md` 迁入），`content/video.md` 待补
