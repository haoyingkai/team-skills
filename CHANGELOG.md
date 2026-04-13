# 团队 Skill 迭代记录

## [2.3.0] - 2026-04-13

### 变更摘要
新增活跃上下文追踪机制（active-context.json），解决 VS Code reload / 插件重载后会话无法自动续接的问题。不再依赖 claude_session_id。

### 变更明细
- [protocol] 多 Session 协作规范新增"活跃上下文追踪"章节：定义 `.team/active-context.json` 格式、字段、写入时机、新 Session 恢复流程改为先查 active-context 再走看板
- [team-pm] handbook Step 0 重写为三级优先级响应（A: 明确指令 → B: 自动续接 → C: 看板展示），Step 4 持久化新增 active-context.json 更新要求
- [team-pd] handbook Step 0 新增自动续接逻辑 + active-context.json 更新要求
- [team-arch] handbook Step 0 新增自动续接逻辑 + active-context.json 更新要求
- [team-dev] handbook Step 0 新增自动续接逻辑 + active-context.json 更新要求
- [team-qa] handbook Step 0 新增自动续接逻辑 + active-context.json 更新要求
- [team-cr] handbook Step 0 新增自动续接逻辑 + active-context.json 更新要求
- [DESIGN.md] 新增 5.5 活跃上下文追踪章节

### 影响范围
- 受影响的角色: PM、PD、ARCH、DEV、QA、CR（全部 6 个）
- 是否影响已有任务流程: 否（新增自动续接，不改变原有流程，只是不指定任务时多了一个快捷恢复路径）

### 废弃
- `team-sessions.json`（基于 claude_session_id 的会话注册表）不再使用，由 `active-context.json` 替代

---

## [2.2.0] - 2026-04-13

### 变更摘要
合并 Epic 支持 + 自审机制 + 远程地址迁移到 GitHub，全面整理版本。

### 变更明细
- [全角色] handbook.md Step 0 新增 Epic 感知（epic_id → epic.json）和依赖检查（depends_on → 上游 status 校验）
- [team-pm] handbook.md 场景 B+ 新增 Epic 恢复展示格式（子任务依赖关系视图）
- [team-pd] SKILL.md + handbook.md 新增 Step 4.5 PRD 自审（含 6 项清单：AC 可测性、边界覆盖、需求完整性、优先级、原型一致性、业务闭环检查）
- [team-arch] SKILL.md + handbook.md 新增 Step 3.5 方案自审（5 项清单）
- [team-dev] SKILL.md + handbook.md Step 4 从简单 checkbox 升级为结构化自审（AC 追溯、方案一致性、安全自检、构建验证）
- [team-qa] SKILL.md + handbook.md 新增 Step 2.5 用例自审（5 项清单）
- [team-cr] SKILL.md + handbook.md 新增 Step 2.5 评审自审（4 项清单）
- [DESIGN.md] 版本升级到 2.2.0，新增自审机制和 Epic 支持章节，远程地址从 Gitee 迁移到 GitHub
- [README.md] 远程地址从 Gitee 迁移到 GitHub，安装命令使用 --ref dev

### 影响范围
- 受影响的角色: PM、PD、ARCH、DEV、QA、CR（全部 6 个）
- 是否影响已有任务流程: 是（新增自审步骤 + Epic 依赖检查）

---

## [2.1.0] - 2026-04-13

### 变更摘要
为除老周外的 5 个团队角色新增自审机制，提升产出质量。

### 变更明细
- [team-pd] SKILL.md 工作流新增 Step 4.5「PRD 自审」；handbook 新增自审清单（AC 可测性、边界覆盖、需求完整性、优先级、原型一致性）
- [team-arch] SKILL.md 工作流新增 Step 3.5「方案自审」；handbook 新增自审清单（需求覆盖度、改动范围、接口合理性、风险依赖、DEV 可执行性）
- [team-dev] SKILL.md 工作流 Step 4 从「自检」升级为「编码自审」；handbook 用结构化自审替代旧的 checkbox 清单（AC 逐条追溯、方案一致性、安全自检、构建验证）
- [team-qa] SKILL.md 工作流新增 Step 2.5「用例自审」；handbook 新增自审清单（AC 覆盖完整性、边界充分性、异常路径、用例可执行性、回归用例）
- [team-cr] SKILL.md 工作流新增 Step 2.5「评审自审」；handbook 新增自审清单（AC 追溯完整性、问题可操作性、严重性判定、评审全面性）

### 设计原则
- 全部为自审（自己查自己），不搞多角色互审，避免上下文浪费
- 每个角色的自审清单针对其产出物量身定制
- 自审发现问题自行修订，不需要等别人反馈
- 自审记录追加到产出物末尾，可追溯

### 影响范围
- 受影响的角色: PD、ARCH、DEV、QA、CR（共 5 个）
- 是否影响已有任务流程: 是（流程中新增自审步骤，但不改变角色间的交接流程）

---

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
