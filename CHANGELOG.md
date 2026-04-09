# 团队 Skill 迭代记录

## [1.2.0] - 2026-04-09

### 变更摘要
简化双目录结构：删除 ai-dev-team/ 下的 skill 副本（team-*），改用 zip 打包发布；新增打包发布流程。

### 变更明细
- [team-team-up] SKILL.md 双目录规则重写：去掉"发行快照"概念，改为 zip 打包到 `ai-dev-team/releases/`
- [team-team-up] 新增"打包发布流程"章节（含打包命令参考）
- [team-team-up] 权限边界新增"执行打包命令"，去掉"禁止修改 ai-dev-team/team-*"（因为已删除）
- [ai-dev-team] 删除 7 个 `team-*` 旧副本目录（team-pm/pd/arch/dev/qa/cr/board）
- [ai-dev-team] 删除 `export.sh`（不再需要同步副本）

### 影响范围
- 受影响的角色: 仅 team-team-up 自身
- 是否影响已有任务流程: 否
- ai-dev-team/ 目录结构变化: 从 ~30 个文件精简到 6 个文档 + 脚本

---

## [1.1.0] - 2026-04-09

### 变更摘要
建立双目录规则，CHANGELOG 迁移到 ai-dev-team/ 源码仓库。

### 变更明细
- [team-team-up] SKILL.md 新增"双目录规则"章节，明确 `.claude/skills/team-*`（运行时）与 `ai-dev-team/`（源码仓库）的职责分工
- [team-team-up] 权限边界新增 `ai-dev-team/` 读写权限
- [team-team-up] CHANGELOG 路径从 `.claude/skills/team-team-up/CHANGELOG.md` 迁移到 `ai-dev-team/CHANGELOG.md`
- [team-team-up] 删除 `.claude/skills/team-team-up/CHANGELOG.md` 旧文件

### 影响范围
- 受影响的角色: 仅 team-team-up 自身
- 是否影响已有任务流程: 否

---

## [1.0.0] - 2026-04-09

### 变更摘要
team-team-up skill 自身从骨架升级为完整定义，建立迭代工作流和质量标准。

### 变更明细
- [team-team-up] SKILL.md 从 20 行骨架扩展为完整 skill 定义，包含：人设、核心身份、权限边界、管理范围、工作流（5 步）、CHANGELOG 格式规范、版本号规则、质量检查清单、行为约束
- [team-team-up] 新建 CHANGELOG.md，建立迭代记录机制

### 影响范围
- 受影响的角色: 仅 team-team-up 自身
- 是否影响已有任务流程: 否
