# HYK Skills — AI 开发团队

一套 Claude Code Skill，为你的项目配备完整的 AI 软件开发团队。

## 团队成员

| Skill | 角色 | 说明 |
|-------|------|------|
| team-pm | 项目经理 老周 | 团队入口，任务管理、进度跟踪、角色协调 |
| team-pd | 产品经理 小苏 | 需求分析、PRD、原型设计、验收标准 |
| team-arch | 架构师 老陈 | 技术选型、架构设计、接口定义、影响评估 |
| team-dev | 开发工程师 阿杰 | 编码实现，遵循技术方案和项目规范 |
| team-qa | 测试工程师 小林 | 测试用例、执行测试、测试报告 |
| team-cr | 代码评审 老吴 | 代码质量、安全性、规范一致性审查 |
| team-board | 任务看板 | 查看任务状态、切换任务、恢复上下文 |

## 安装

需要 Node.js 环境。

```bash
# 安装所有角色
npx skills add https://gitee.com/hao_yk/hyk-skills.git

# 只安装特定角色
npx skills add https://gitee.com/hao_yk/hyk-skills.git --skill team-pm

# 安装测试版（dev 分支）
npx skills add https://gitee.com/hao_yk/hyk-skills.git#dev

# 安装指定版本
npx skills add https://gitee.com/hao_yk/hyk-skills.git#v1.0.0
```

## 使用

安装后在 Claude Code 中直接喊名字即可：

- "老周，帮我看看进度"
- "小苏，讨论下需求"
- "老陈，做个技术方案"
- "阿杰，开始开发"
- "小林，跑一下测试"
- "老吴，review 一下代码"

## 更新

```bash
# 重新安装即可更新到最新版
npx skills add https://gitee.com/hao_yk/hyk-skills.git
```

<!-- dev-branch-marker: 1775743424 -->
