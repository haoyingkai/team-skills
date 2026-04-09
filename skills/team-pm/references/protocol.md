# 团队协作协议

## 角色清单

| 角色 | Skill 名称 | 职责 |
|------|-----------|------|
| 项目经理 | team-pm | 接收指令、拆解任务、调度团队、汇报进度 |
| 产品经理 | team-pd | 需求分析、PRD 编写、验收标准定义 |
| 架构师 | team-arch | 技术选型、架构设计、影响评估 |
| 开发工程师 | team-dev | 编码实现 |
| 测试工程师 | team-qa | 测试用例、执行测试、测试报告 |
| 代码评审 | team-cr | 代码质量审查 |

## 任务结构：Epic + Task 两层模型

### 什么是 Epic

Epic 是一个**大任务的容器**，当任务需要多个角色协作、产出多个独立产出物时使用。每个 Epic 包含多个子 Task，各子 Task 独立执行、独立恢复上下文。

### 什么时候用 Epic vs 单独 Task

| 场景 | 用什么 | 判断依据 |
|------|--------|---------|
| "做一个 TODO 应用" | Epic | 多角色、多阶段、多产出物 |
| "重构认证模块" | Epic | 需要设计 + 开发 + 测试 |
| "小林跑一下回归测试" | Task | 单角色、单产出 |
| "修一下登录 bug" | Task | 可能只需 DEV |
| "老陈看看方案可行不" | Task | 单角色、单产出 |

**PM 判断后必须征求用户确认**（详见 PM handbook "任务规模确认"章节）。

**超大需求可拆多 Epic**：如果需求特别大（如"做一个电商系统"），PM 应建议按可独立交付的模块拆成多个 Epic，每个 Epic 各自走完整流程，可并行也可按顺序推进。Epic 之间的依赖通过 `depends_on_epics` 管理。

### Task 可升级为 Epic

初始判断不一定准确，Task 随时可升级为 Epic：

```
升级前:
  .team/tasks/TASK-005/
  ├── task.json
  └── progress.md

升级后:
  .team/tasks/EPIC-003/
  ├── epic.json
  ├── TASK-005-修复登录bug/      ← 原任务平移进来
  │   ├── task.json
  │   └── progress.md
  ├── TASK-006-代码评审/          ← 新建
  └── TASK-007-测试验收/          ← 新建
```

### 目录结构

#### Epic（大任务）
```
.team/tasks/EPIC-001/
├── epic.json                      # Epic 元数据 + 子任务列表
├── TASK-001-需求分析/             # 子任务目录
│   ├── task.json                  # depends_on: []
│   ├── progress.md
│   └── 需求规格.md
├── TASK-002-架构设计/
│   ├── task.json                  # depends_on: ["TASK-001"]
│   ├── progress.md
│   └── 概要设计.md
├── TASK-003-编码实现/
│   ├── task.json                  # depends_on: ["TASK-001", "TASK-002"]
│   └── progress.md
├── TASK-004-代码评审/
│   └── ...
└── TASK-005-测试验收/
    └── ...
```

#### 独立 Task（小任务）
```
.team/tasks/TASK-008/
├── task.json
├── progress.md
└── 测试报告.md
```

### epic.json Schema

```json
{
  "id": "EPIC-001",
  "title": "TODO List 应用开发",
  "description": "详细描述",
  "project": "项目名称",
  "project_path": "/path/to/project",
  "status": "pending | in_progress | done | blocked",
  "priority": "high | medium | low",
  "depends_on_epics": ["上游 Epic ID（超大需求拆分时用），如 EPIC-001"],
  "created": "2026-04-09",
  "updated": "2026-04-09T15:30:00",
  "approval_mode": "strict | semi-auto | auto",
  "tasks": [
    { "id": "TASK-001", "title": "需求分析", "role": "PD", "status": "done" },
    { "id": "TASK-002", "title": "架构设计", "role": "ARCH", "status": "in_progress" },
    { "id": "TASK-003", "title": "编码实现", "role": "DEV", "status": "pending" },
    { "id": "TASK-004", "title": "代码评审", "role": "CR", "status": "pending" },
    { "id": "TASK-005", "title": "测试验收", "role": "QA", "status": "pending" }
  ],
  "progress_summary": "需求分析完成，架构设计进行中",
  "history": [
    {"role": "pm", "action": "created", "time": "2026-04-09T10:00:00", "note": "备注"}
  ]
}
```

### task.json Schema（子任务增加 depends_on）

```json
{
  "id": "TASK-001",
  "title": "任务标题",
  "epic_id": "EPIC-001 或 null（独立任务时为 null）",
  "depends_on": ["上游子任务 ID，如 TASK-001"],
  "project": "项目名称",
  "project_path": "/path/to/project",
  "status": "pending | in_progress | review | testing | done | blocked",
  "type": "feature | bugfix | refactor | research | design",
  "priority": "high | medium | low",
  "created": "2026-03-23",
  "updated": "2026-03-23T15:30:00",
  "assignee": "当前负责角色",
  "workflow": ["pd", "arch", "dev", "cr", "qa"],
  "current_step": 0,
  "approval_mode": "strict | semi-auto | auto",
  "artifacts": {
    "prd": "路径或 null",
    "prototypes": "原型目录路径或 null",
    "arch": "路径或 null",
    "dev_handoff": "DEV 交接信息（progress.md 中的对应条目日期）或 null",
    "review": "路径或 null",
    "qa_report": "路径或 null"
  },
  "qa_approved": "null | true | false",
  "pd_approved": "null | true | false",
  "progress_summary": "当前进展的一句话摘要，供跨 session 恢复用",
  "blocked_reason": "阻塞原因（status 为 blocked 时填写）",
  "history": [
    {"role": "pm", "action": "created", "time": "2026-03-23T10:00:00", "note": "备注"}
  ]
}
```

### 状态机

**Epic 状态机：**
```
pending → in_progress → done
              ↓
           blocked
              ↓
         in_progress
```
Epic 的 status 由子任务自动推导：全部 done → Epic done，任一 blocked → Epic blocked。

**Task 状态机（不变）：**
```
pending → in_progress → review → testing → done
                ↓           ↓        ↓
              blocked     blocked   blocked
                ↓           ↓        ↓
           in_progress → review → testing
```

### 子任务依赖与上下文恢复

角色启动时，按 `depends_on` 字段定位上游：

```
1. 读自己的 task.json → 了解当前状态
2. 按 depends_on 读上游任务的 task.json.progress_summary（一句话）
3. 需要详细内容时，读上游任务目录下的具体产出物
4. 不读无依赖关系的平行子任务
```

**上下文消耗从 O(所有产出物) 降到 O(自己的 + 上游摘要)**。

### 编号规则

- Epic 编号: `EPIC-001`、`EPIC-002`（全局自增）
- 子任务编号: `TASK-001`、`TASK-002`（全局自增，不区分是否属于 Epic）
- 独立 Task 编号: 与子任务共享同一编号序列
- team-config.yaml 中分别维护 `epic_counter` 和 `task_counter`

### 多 Epic 并行

不同 Epic 可以在不同会话窗口同时执行，互不干扰：

```
.team/tasks/
├── EPIC-001-TODO应用/        ← 窗口 A 在做
├── EPIC-002-认证重构/        ← 窗口 B 在做
├── TASK-020/                 ← 独立小任务
└── TASK-021/                 ← 独立小任务
```

## 任务创建规则

### 任务规模确认（必做）

PM 收到任务后，先快速评估规模，然后 **征求用户确认**：

**需要问的场景**（涉及 ≥2 角色产出，或不确定时）：
```
老板，这个任务我初步判断需要 PD → ARCH → DEV → CR → QA 多角色协作。

您看怎么走：
A) 完整流程（按大需求管理，各角色独立产出）
B) 快速执行（直接干，不拆分）
```

**不需要问的场景**（明显的小任务）：
- 指名单角色（"小林跑一下测试"）→ 直接 Task
- 一句话能说清、单产出物 → 直接 Task

**选择后的处理**：
- 选 A → 创建 Epic，拆分子任务
- 选 B → 创建独立 Task
- 选了 B 后发现其实很大 → PM 主动建议升级为 Epic

### 一事一任务

每个需要团队成员干活、有明确产出物的事项，**必须创建独立的 TASK-XXX**。

- Epic 内：每个角色的产出 = 一个独立子 Task
- Epic 外：每个独立指令 = 一个独立 Task

### 区分任务 vs 对话回复

| 类型 | 判断标准 | 处理方式 |
|------|---------|---------|
| **任务** | 需要团队成员执行、有明确产出物（报告、文档、代码等） | 创建 EPIC 或 TASK，派人干活 |
| **对话** | 问进度、问意见、随口聊、简单问答、确认信息 | 直接对话回复，不建任务 |

### 跨项目支持

用户交代的任务不一定属于同一个项目。创建任务时必须准确标注 `project` 和 `project_path` 字段，支持不同项目的任务并存。

## 产出物规范

### 文档命名规则

**所有输出的文档文件名必须使用中文命名**，例如：

| 原英文名 | 改为中文名 |
|---------|-----------|
| competitive-analysis.md | 竞品调研.md |
| test-plan.md | 测试方案.md |
| architecture.md | 架构设计.md |
| prd.md | 需求规格.md |
| design-overview.md | 概要设计.md |
| qa-report.md | 测试报告.md |
| review.md | 评审记录.md |

> 例外：task.json、progress.md 等系统文件保持英文。

### 文档职责分配

| 文档 | 负责角色 | 说明 |
|------|---------|------|
| PRD（需求规格） | PD | 需求分析、用户故事、验收标准 |
| 原型设计 | PD | HTML 线框图/流程图 |
| 概要设计 | ARCH | 系统架构、模块划分、接口定义、技术选型 |
| 详细设计 | ARCH / DEV 协作 | ARCH 定义结构，DEV 补充实现细节 |
| 测试方案 | QA | 测试策略、用例设计、环境说明（编码前产出） |
| 测试报告 | QA | 执行结果、通过率、Bug 列表（编码后产出） |
| 代码评审记录 | CR | 审查意见、问题列表 |
| 汇报 PPT | PM | 任务/项目汇报幻灯片 |

### 文件目录

**Epic 内子任务：** 每个子任务有独立目录，产出物放在各自目录下：

```
.team/tasks/EPIC-001/
├── epic.json                          # Epic 元数据
├── TASK-001-需求分析/
│   ├── task.json
│   ├── progress.md
│   ├── 需求规格.md                     # [PD]
│   └── prototypes/                    # [PD] 原型
├── TASK-002-架构设计/
│   ├── task.json
│   ├── progress.md
│   └── 概要设计.md                     # [ARCH]
├── TASK-003-编码实现/
│   ├── task.json
│   └── progress.md
├── TASK-004-代码评审/
│   ├── task.json
│   └── 评审记录.md                     # [CR]
└── TASK-005-测试验收/
    ├── task.json
    └── 测试报告.md                     # [QA]
```

**独立 Task：** 产出物直接放在任务目录下（与之前一致）：

```
.team/tasks/TASK-008/
├── task.json              # 任务元数据
├── progress.md            # 进度日志
├── 需求规格.md             # [PD]（如有）
├── 概要设计.md             # [ARCH]（如有）
├── 测试报告.md             # [QA]（如有）
├── 评审记录.md             # [CR]（如有）
└── report/                # [PM] 汇报 PPT（如有）
```

### 文档产出时机

```
PD 阶段:  prd.md + prototypes/
ARCH 阶段: design-overview.md（概要设计）
DEV 前:   design-detail.md（ARCH 起草 → DEV 补充）+ test-plan.md（QA 提前写）
DEV 后:   qa-report.md + review.md
完成后:   report/（PM 按需生成汇报 PPT）
```

**注意**：概要设计和详细设计不是所有任务都需要。PM 根据任务复杂度决定：

| 任务复杂度 | 需要的文档 |
|-----------|-----------|
| 小任务/Bug 修复 | 无（直接写代码） |
| 中等功能 | prd + 概要设计 |
| 大功能/新系统 | prd + 概要设计 + 详细设计 + 测试方案 |

## 流程类型

| 类型 | 触发关键词 | 流程 |
|------|-----------|------|
| 完整开发 | "新功能""加个""实现" | PD → ARCH → DEV → CR → QA |
| 快速修复 | "Bug""修复""报错" | DEV → CR → QA |
| 技术重构 | "重构""优化""性能" | ARCH → DEV → CR → QA |
| 项目调研 | "分析""看看""调研" | ARCH |
| 方案设计 | "方案""设计""评估" | PD → ARCH |
| 需求梳理 | "需求""梳理""理一下" | PD |

## 门控策略

- **strict**: 每步完成后暂停，等老板确认
- **semi-auto**: 只在 PRD 完成和最终完成时暂停
- **auto**: 全程自动，最后汇报

## 质量门禁

### QA 是最后一道技术质量防线

任务从 `testing` → `done` **必须** QA 签字。QA 拥有与 CR 同等的审批权力。

### 通过标准

| 条件 | 要求 |
|------|------|
| P0 验收标准用例 | 100% 通过 |
| P1 验收标准用例 | ≥ 90% 通过 |
| 阻塞/严重 Bug | 零残留 |
| 回归测试 | 全部通过 |
| E2E 关键路径 | 已验证可执行 |

### QA 审批输出

- **APPROVE** → 任务可进入 done，task.json 设置 `qa_approved: true`
- **REQUEST_CHANGES** → 任务退回 DEV，附 Bug 列表和修复建议，task.json 设置 `qa_approved: false`

### QA-PD 双签放行

上线前需要两道签字：
1. **QA 签字**（技术质量）：功能正确性、边界处理、回归无破坏
2. **PD 签字**（产品体验）：需求符合度、交互合理性、视觉还原度

任一方发现阻塞性问题都可以拦截交付。PM 汇总两方结论后向老板汇报。

### Bug 严重性定义

| 级别 | 定义 | 处理规则 |
|------|------|---------|
| **阻塞** | 核心功能不可用，无法绕过 | 必须修复，QA 不可放行 |
| **严重** | 功能异常但有临时绕过方案 | 必须修复，QA 不可放行 |
| **一般** | 非核心功能异常或体验问题 | 建议修复，不阻塞交付，记录到下个迭代 |
| **建议** | 优化项、代码风格、文案改进 | 记录，视排期处理 |

### Bug 仲裁

QA 和 DEV 对 Bug 严重性有分歧时：
1. 双方各自说明理由
2. PM 根据 PRD 验收标准和用户影响裁决
3. 如仍有争议，升级给老板决定

### DEV → QA 交接清单

DEV 完成编码后，**必须在 progress.md 中追加以下内容**，作为正式交接：

```markdown
## [日期] DEV - 提交测试

### 改动文件清单
- src/xxx.ts — 新增/修改了什么
- src/yyy.ts — 新增/修改了什么

### 影响范围
- 直接影响: 哪些模块
- 间接影响: 哪些上下游可能受影响

### 自检结果
- [ ] 功能完整性：PRD 验收标准逐条对照通过
- [ ] 构建成功：项目可正常编译/启动
- [ ] 已有测试通过：运行现有测试无失败
- [ ] 接口一致：与 arch.md 定义的接口一致

### 已知风险点
- （如有发现的边界情况、未覆盖的场景等）
```

QA 收到交接后，基于此清单确定测试范围和重点。**没有交接清单的代码，QA 有权退回。**

### QA 左移参与

QA 不只是"最后测一下"，在以下环节提前介入：

| 环节 | QA 参与内容 |
|------|-----------|
| PRD 评审 | 检查验收标准是否可测、边界是否清晰、遗漏场景补充 |
| 架构评审 | 确认系统是否提供足够的测试 hook（API 可测、状态可观测） |
| 编码前 | 产出测试方案（test-plan.md），让 DEV 了解"写完要过什么关" |

## 升级规则

Agent 遇到以下情况必须上报 PM：
1. 需求不明确，无法继续
2. 技术方案有多种选择，需要决策
3. 发现方案与需求冲突
4. 依赖外部资源或权限不足
5. 工作被阻塞

PM 遇到以下情况必须上报老板：
1. 需求需要澄清
2. 方案需要取舍决策
3. 发现重大风险
4. 任务完成，提交汇报

## 上下文控制（防爆炸）

### 问题

Claude Code 每个 session 的上下文窗口是有限的。如果角色启动时把 lessons.md、task.json、prd.md、arch.md、progress.md、context.yaml 全部完整读入，还没开始干活上下文就占了一大半，后面写代码或讨论时容易撞墙。

### 核心原则：按需加载，分层摘要

**不要一次读完所有文件。** 先读摘要，需要细节时再读原文。

### 加载优先级

每个角色启动时，按以下顺序**逐层加载**，每层判断是否需要下一层：

```
第 1 层 [必读，极小]:
  - task.json              → 只看 status, current_step, progress_summary
  - lessons.md             → 只看与当前角色相关的条目（用 Grep 过滤）

第 2 层 [按需，中等]:
  - progress.md 最后 3 条  → 了解最近进展（不要读全部历史）
  - context.yaml           → 只在首次接触项目时读

第 3 层 [干活时才读]:
  - prd.md                 → ARCH/DEV/QA 开始工作时才读
  - arch.md                → DEV 开始编码时才读
  - review.md              → DEV 收到打回时才读
  - 项目源代码             → 只读当前要改的文件，不要全局扫描
```

### 各角色的上下文预算指引

| 角色 | 启动时最多读 | 工作时按需读 |
|------|------------|------------|
| PM | task.json(摘要) + lessons.md(相关条目) | 各产出物的存在性（不读内容） |
| PD | task.json(摘要) + lessons.md(PD条目) | 现有代码（理解现状时） |
| ARCH | task.json(摘要) + prd.md + context.yaml | 项目源代码（分析时） |
| DEV | task.json(摘要) + arch.md + prd.md(验收标准部分) | 要改的源文件 |
| QA | task.json(摘要) + prd.md(验收标准部分) | DEV 改动的文件 |
| CR | task.json(摘要) + DEV 改动的文件列表 | 具体文件的 diff |

### 读文件的具体规则

1. **progress.md**: 只读最后 3 条（用 `tail` 或指定 offset），不要读全部历史
2. **lessons.md**: 用 Grep 只读与当前角色相关的行，不要读全文
3. **prd.md / arch.md**: 如果超过 100 行，先读标题和验收标准部分，需要细节时再读
4. **源代码**: 只读要改的文件，不要"先整体了解一下项目"然后读 20 个文件
5. **context.yaml**: 很短，可以全读，但只在首次接触项目时读

### 写文件的控制规则

产出物也要控制长度，避免给下游角色制造上下文负担：

| 产出物 | 建议长度 | 超长怎么办 |
|--------|---------|-----------|
| task.json progress_summary | 1-2 句话 | 只保留最新状态，不累加 |
| progress.md 每条 | 3-5 行 | 超过就说明粒度太细，合并 |
| prd.md | 50-150 行 | 拆成"摘要"和"详细"两部分 |
| arch.md | 50-200 行 | 大方案拆成多个子文档 |
| review.md | 30-80 行 | 只记问题，不贴大段代码 |
| lessons.md | 每条 1 行 | 定期清理过时条目 |

### 长文档分层结构

PRD 和技术方案如果内容多，使用**摘要 + 详细**两层结构：

```markdown
# PRD: 用户登录功能

## 摘要（其他角色读这部分就够了）

- 目标: 实现邮箱 + 手机号注册登录
- 核心功能: 注册、登录、密码重置、邮箱验证
- 验收标准: 5 条（见下方）
- 原型: 3 个页面（见 prototypes/）

## 验收标准

- [ ] AC-1: ...
- [ ] AC-2: ...

---
以下为详细内容，仅需要时阅读
---

## 详细功能描述
...

## 用户故事
...
```

### 上下文即将耗尽时的处理

如果在工作过程中感觉上下文快要不够了：

1. **立即持久化** -- 把当前进展写入 progress.md 和 task.json
2. **告知老板** -- "上下文快满了，我已保存进度，请开新 session 用 /team-xxx TASK-xxx 继续"
3. **不要硬撑** -- 宁可多开一个 session，不要在上下文紧张时产出低质量内容

## 持续改进机制

### 团队经验库 (.team/lessons.md)

所有复盘经验沉淀在 `.team/lessons.md` 中，**所有角色每次启动时必须读取**。

格式：

```markdown
# 团队经验库

> 每次任务复盘后由 PM 维护，所有角色启动时必读。

## 流程改进

- [2026-03-23] TASK-001: Bug 修复类任务可以跳过 CR，直接 QA（老板确认）
- [2026-03-25] TASK-005: 大功能需要 PD 先画原型再写 PRD，不要跳过原型

## 角色行为

- [2026-03-23] DEV: 不要自作主张加 lint 规则或格式化工具，先问老板
- [2026-03-24] ARCH: 技术方案里要列出"不选方案 B 的理由"，不要只写选了什么
- [2026-03-26] PD: 需求讨论时先问优先级，避免一次规划太大

## 项目特定经验

- [2026-03-24] my-app: 这个项目的测试用 Vitest 不是 Jest，QA 注意
- [2026-03-25] backend: 数据库迁移必须先备份，DEV 不要直接改表

## 协作规则

- [2026-03-23] 老板偏好半自动模式，只在 PRD 和最终完成时暂停
- [2026-03-24] 老板不喜欢太长的汇报，结论先行，细节放文件里
```

### 三层改进路径

| 层级 | 改什么 | 怎么改 | 谁来改 |
|------|--------|--------|--------|
| **经验层** | 团队经验库 lessons.md | 每次任务完成后复盘追加 | PM |
| **角色层** | 某个角色的 SKILL.md | 反馈涉及特定角色行为时 | PM 引导老板确认后修改 |
| **流程层** | 协议文件 protocol.md | 反馈涉及流程增删调整时 | PM 引导老板确认后修改 |

### 改进触发时机

1. **任务复盘**（必做）-- 每个任务完成后 PM 主动引导
2. **老板随时反馈** -- 任何时候老板说"以后别这样"或"这个方式不错"
3. **定期回顾**（可选）-- 每完成 5 个任务，PM 主动提议回顾经验库

### 改进原则

- **只记可复用的经验** -- "TASK-001 的登录按钮颜色改成蓝色"不需要记，"原型确认后再写 PRD"需要记
- **经验要具体** -- 不写"代码质量要好"，写"DEV 提交前必须自跑一遍测试"
- **允许修正** -- 之前记的经验如果后来发现不对，直接改或删
- **角色启动时必读** -- 经验库的价值在于被读取和执行，不是写完就完

## 项目快速接入

### 如何将团队应用到一个新项目

只需要在目标项目根目录下创建一个 `.team/` 目录即可。PM 会在首次对接时自动完成初始化。

#### 方式 A: 在项目目录中直接使用

在任意项目中调用 `/team-pm`，PM 会自动：

1. 创建 `.team/` 目录结构
2. 扫描项目，建立项目上下文
3. 开始接收指令

#### 方式 B: 集中管理多个项目（推荐）

把团队总部放在一个固定位置，通过 `project_path` 指向不同项目：

```
~/.team/                          # 团队总部（固定位置）
├── lessons.md                    # 经验库（跨项目共享）
├── team-config.yaml              # 团队全局配置
├── projects/
│   ├── my-app/
│   │   ├── context.yaml          # 项目上下文
│   │   └── tasks/
│   │       ├── TASK-001/
│   │       └── TASK-002/
│   ├── backend-api/
│   │   ├── context.yaml
│   │   └── tasks/
│   └── mobile-app/
│       ├── context.yaml
│       └── tasks/
```

### 项目上下文 (context.yaml)

每个项目首次接入时，PM 协调 ARCH 扫描后生成：

```yaml
project_name: "my-app"
project_path: "/Users/xxx/projects/my-app"
type: existing                     # new | existing
language: [TypeScript, Python]
framework: [Next.js, FastAPI]
test_framework: [Vitest, pytest]
package_manager: pnpm
conventions:
  - "使用 ESLint + Prettier"
  - "组件文件名用 PascalCase"
  - "API 路由在 /app/api/ 下"
git_branch_pattern: "feature/{task-id}-{short-desc}"
special_notes:
  - "数据库迁移前必须备份"
  - "不要动 legacy/ 目录"
created: "2026-03-23"
last_updated: "2026-03-25"
```

### 团队全局配置 (team-config.yaml)

```yaml
# 团队默认行为
default_approval_mode: semi-auto   # strict | semi-auto | auto
default_priority: medium

# 任务编号
task_counter: 7                    # 自增，下一个任务是 TASK-008

# 老板偏好（从经验库中提炼）
boss_preferences:
  report_style: concise            # concise | detailed
  prototype_required: true         # PD 是否必须出原型
  cr_for_bugfix: false             # Bug 修复是否需要 CR
```

## 多 Session 协作规范

### 核心原则

Claude Code 每个 session 上下文独立，不会自动继承之前 session 的对话。
因此所有状态必须持久化到 `.team/` 目录，让任意新 session 都能恢复。

### 必须持久化的内容

每个角色在工作时，**必须把以下信息写入文件**，不能只在对话中说：

| 信息 | 写入位置 | 时机 |
|------|---------|------|
| 任务状态变更 | `task.json` 的 `status`、`current_step`、`updated` | 每次状态变化时 |
| 进展摘要 | `task.json` 的 `progress_summary` | 每次有实质进展时 |
| 操作记录 | `task.json` 的 `history[]` | 每次操作完成时 |
| 产出物 | 对应的 md 文件（prd.md、arch.md 等） | 产出完成时 |
| 进度日志 | `progress.md`（追加写入） | 阶段性进展时 |

### progress.md 格式

每个任务目录下可选的进度日志，追加写入：

```markdown
## [2026-03-23 15:30] DEV - 完成注册 API

- 已完成 POST /api/auth/register 接口
- bcrypt 加密已集成
- 待做: OAuth 微信登录部分

## [2026-03-23 10:00] ARCH - 技术方案已确认

- 方案使用 JWT + bcrypt
- 老板已确认，进入开发阶段
```

### 新 Session 恢复流程

任何角色在新 session 中被调用时，**第一步必须是恢复上下文**：

```
1. 读取 task.json → 了解当前在哪一步
2. 读取 progress.md（如有）→ 了解上次做到哪
3. 读取相关产出物（prd.md、arch.md 等）→ 了解前序产出
4. 向老板确认理解是否正确
5. 继续工作
```

### 任务看板

使用 `/team-board` 查看所有任务的全局状态，快速切换任务。

### 并行任务注意事项

1. 不同 Epic / Task 之间互不干扰，各自有独立的目录
2. 同一项目的多个任务，注意代码改动不要冲突
3. PM 应在看板中标注任务优先级，帮老板决定先做哪个
4. 同一 Epic 内的子任务按 `depends_on` 顺序执行，无依赖关系的可并行
5. 不同 Epic 可在不同会话窗口并行执行
