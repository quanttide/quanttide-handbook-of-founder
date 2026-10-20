# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 移除

- `agent/index.md`：智能体协作升格为规格，迁出至规格仓库并改为规格体裁、增加形式化规则；来源侧不留副本
- `agent/` 四篇：电脑端工作流、移动端工作流、后台派单与协作总览迁出至案例集 `work/agent/`，来源侧不留副本
- `work/human-agent-collaboration.md`：迁出至知识工作手册 `stage/clarify.md`，按必要生命周期阶段重新定义
- `media/material/image.md`、`media/material/video.md`：迁出至案例集的导出工作流，合并为一篇；来源侧不留副本，`media/index.md` 只保留目录与格式规则
- `write/` 三篇迁出：整合为论文仓库 `write/from-reality-to-fiction.md`（从现实到虚构）；手册回归操作规范体裁，不收创作动机类个人内容

### 变更

- `agent/index.md`：重写直连与转发——直连为理想模式，判断落在仓库产物上，代价是人工操作多（移动端工作流）；转发省人工，判断经转述，结构不如直连理想（后台派单）
- `write/` 三篇按工作手册体裁重写：去掉原始日志摘录式叙述与「定位」节，改为主题化的做法、判断与边界
- `media/material/`：拆分为 `image.md`（图片）与 `video.md`（视频）；素材目录固定为 `images` / `videos`，只保留 JPG / MP4，补转码与 Live Photo 处理

### 新增

- `agent/index.md`：智能体协作的结构——owner 与 reviewer 两方，直连与转发两种连接方式，仓库为唯一交接点；不含规律与操作步骤
- 初始化手册仓库：`README.md`（定位与同域分工）、`index.md`（目录入口，待补充）
- 迁入 `write/`：创作的做法与卡点三篇（创作动机 / 创作方法 / 创作困境），源自 `data/context/write/essay/`
- `write/` 文件名改英文（`motivation.md` / `methods.md` / `blockers.md`），按手册风格补「定位」节；新增 `myst.yml`（书名、目录与导航）
- 新增 `work/human-agent-collaboration.md`：人机协作的三步做法（识别信号、冷静处理、沉淀规则），从 `data/profile/think/ai-collaboration.*` 的关键逻辑整理
- `auth/icloud.md`：Apple 账号的命令行认证（区域域名、会话与常见排查）
- 新增 `media/` 目录：`material/image.md`（照片素材）与 `material/video.md`（视频素材）从 iCloud 相册同步（自 `auth/icloud.md` 迁入），`content/` 待补
- `media/index.md`：新增媒体工作流程总览（选题、素材、加工、发布）
