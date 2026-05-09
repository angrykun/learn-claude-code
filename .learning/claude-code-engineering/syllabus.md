# Claude Code 工程实战 - 学习大纲

## 课程概述

本课程面向已有 Claude Code 使用经验的开发者，系统讲解如何在实际工程中使用 Claude Code 进行需求分析、开发最佳实践、高级特性应用以及团队工程化落地。课程基于 FASTER 学习框架设计，每个阶段都包含理论学习和实战练习。

## 前置要求

- 已安装并基本使用过 Claude Code CLI
- 熟悉基本的 Git 工作流
- 有实际软件开发经验
- 了解 MCP（Model Context Protocol）的基本概念（可选）

## 学习目标

完成本课程后，你将能够：

1. **需求分析与规划** - 使用 Claude Code 进行需求拆解、技术方案设计、架构规划
2. **开发最佳实践** - 掌握 CLAUDE.md 配置、上下文管理、代码质量保障方法
3. **高级特性实战** - 熟练运用 Subagents、MCP、Hooks、Skills、多智能体协作
4. **工程化与团队落地** - 实现 CI/CD 集成、团队规范、成本控制、安全合规

---

## Phase 1: 上下文管理与 CLAUDE.md 精通

### 核心概念

- [x] **Claude Code 工作原理** - Agent 循环、内置工具、上下文窗口机制
  - 工具集：文件操作（Read/Edit/Write）、搜索（Grep/Glob）、执行（Bash）、Web 搜索、代码智能
  - 上下文窗口：200K 标准 / 1M（Opus 4.6 / Sonnet 4.6），性能随填充度下降
  - 系统提示词结构：系统指令、工具定义、对话历史、CLAUDE.md 等

- [x] **CLAUDE.md 四层架构**
  - `~/.claude/CLAUDE.md` — 全局（所有项目）
  - `./CLAUDE.md` — 项目级（提交到 git）
  - `./CLAUDE.local.md` — 个人配置（gitignore）
  - `src/subdirectory/CLAUDE.md` — 目录级（懒加载）

- [x] **上下文管理最佳实践**
  - 精简 CLAUDE.md（< 500 tokens，只读行为改变规则）
  - 使用 `/init` 生成初始模板
  - 使用 `/context` 监控 token 分布
  - 使用 `/compact` 压缩上下文（可加焦点提示）
  - 使用 `/clear` 任务间清理
  - 使用 `.claudeignore` 排除 node_modules/、.next/ 等

- [x] **CLAUDE.md 五种设计模式**
  - Minimal（小项目，< 10 行）
  - Sectioned（中型项目，分节关键规则与风格指南）
  - Task-Focused（特定会话："今日任务 / 要动的文件 / 约束"）
  - Hierarchical（大型项目，子目录分层规则）
  - Context Index（引用 docs/，不复制内容）

- [x] **Auto Memory（2026 新特性）**
  - `~/.claude/projects/<path>/memory/MEMORY.md` 自动记忆
  - 前 200 行自动加载
  - 命令：`/memory` 查看/编辑
  - 用法："remember: always use pnpm"

### 🔨 实战练习

- [ ] 为自己当前的项目生成 CLAUDE.md（`/init` 后精简至 200 行以内）
- [ ] 配置 `.claudeignore` 排除构建产物和依赖目录
- [ ] 设置全局 `~/.claude/CLAUDE.md`（5-10 条核心规则）
- [ ] 在项目中创建子目录级 CLAUDE.md 测试懒加载
- [ ] 使用 `/context` 和 `/cost` 监控一次真实开发会话

### 参考资料

- [Claude Code 概述](https://docs.anthropic.com/zh-CN/docs/claude-code/overview)
- [官方最佳实践](https://code.claude.com/docs/en/best-practices)
- [CLAUDE.md 上下文管理指南](https://32blog.com/en/claude-code/claude-code-context-management-claude-md-patterns)
- [持久化上下文完整指南](https://www.codewithseb.com/blog/claude-md-memory-persistent-context-guide)

---

## Phase 2: 需求分析与规划实战

### 核心概念

- [x] **Explore → Plan → Code → Commit 工作流**
  - Explore：使用 Agent 工具（subagent_type=Explore）快速探索代码库
  - Plan：使用 Plan Mode（Shift+Tab 两次）分离调研与实现
  - Code：基于计划执行实现
  - Commit：创建清晰的提交信息

- [x] **需求拆解与技术方案设计**
  - 向 Claude 提供：需求描述、约束条件、参考模式、示例文件
  - 使用 `/enter-plan-mode` 进入规划模式
  - 规划输出：变更文件列表、实现步骤、风险点、测试策略
  - 使用 `Excellent` 反馈机制迭代方案

- [x] **架构规划与决策记录**
  - 使用 Claude 生成 ADR（Architecture Decision Record）
  - 多方案对比：让 Claude 列出 2-3 种方案并分析权衡
  - 技术选型：结合项目上下文做框架/库选型

- [ ] **使用 Claude 理解新代码库**
  - `Agent(subagent_type="Explore")` 定位关键文件
  - 提问方式："这个项目的认证流程是怎样的？"
  - 结合 Grep/Glob 工具交叉验证
  - 生成代码地图（Code Map）保存为 docs/

- [x] **任务拆分与估算**
  - 让 Claude 将大需求拆为独立子任务
  - 评估每个子任务的影响文件和风险
  - 生成任务清单（可用 TaskCreate 追踪）

### 🔨 实战练习

- [ ] 选一个你从未接触过的开源项目，用 Claude Code 在 15 分钟内画出架构图
- [ ] 为一个新功能（如"添加用户头像上传"）做完整的需求分析和技术方案
- [ ] 使用 Plan Mode 规划一个涉及 5+ 文件的重构任务
- [ ] 让 Claude 帮你写一个 ADR，记录某个技术选型的决策过程
- [ ] 练习用 Claude 做"反向工程"：给一段代码，让它推断原始需求

### 参考资料

- [Claude Code 工作流程教程](https://docs.claude.com/en/docs/claude-code/common-workflows)
- [理解新代码库](https://codingnomads.com/claude-code-common-development-workflows)
- [Prompt-to-PR 工作流](https://koder.ai/blog/prompt-to-pr-claude-code-cadence)

---

## Phase 3: 开发最佳实践

### 核心概念

- [x] **验证优先原则**（最高杠杆实践）
  - 给 Claude 提供：测试用例、截图、预期输出
  - 让 Claude 自己验证工作结果
  - 结合自动化测试（单元测试、集成测试、E2E）

- [x] **代码质量保障**
  - 配置 Hooks 实现：保存文件后自动 lint、提交前运行测试
  - 使用 `minimal` 权限模式减少误操作
  - 代码审查：`/review` 命令或 `Skill: code-review`
  - 安全审查：`Skill: security-review`

- [x] **Bug 修复工作流**
  - 粘贴完整错误信息（不要总结）
  - 让 Claude 追踪根因
  - 审查 diff：`/diff` 查看变更
  - 运行测试验证
  - 提交修复（清晰的 commit message）

- [ ] **重构策略**
  - 使用 Plan Mode 规划重构步骤
  - 小步迭代：每次只改一个维度
  - 增量式重构：确保每步都可运行
  - 使用 Claude 识别代码坏味道

- [ ] **测试驱动开发**
  - 让 Claude 匹配项目现有测试模式
  - 明确边界条件（不要假设 Claude 能猜对）
  - 生成测试后人工审查覆盖度
  - 使用 Claude 生成 Mock 和 Fixture

- [ ] **Git 工作流集成**
  - 提交信息规范：约定式提交（Conventional Commits）
  - PR 创建：`gh pr create` 或告诉 Claude "create a PR"
  - 分支策略：feature 分支 + PR 流程
  - 使用 Worktree 实现并行会话

### 🔨 实战练习

- [ ] 找一个真实 bug，用 Claude Code 完成"粘贴错误 → 定位 → 修复 → 测试 → 提交"完整流程
- [ ] 配置 pre-commit hook：保存文件后自动运行 lint
- [ ] 让 Claude 为现有代码补充单元测试（匹配项目测试框架）
- [ ] 用 Claude 重构一个超过 200 行的函数，保持行为不变
- [ ] 完成一次完整的"功能开发 → 创建 PR → 代码审查 → 合并"流程

### 参考资料

- [Claude Code 常见工作流](https://code.claude.com/docs/en/common-workflows)
- [Bug 修复工作流](https://claudcod.com/blog/claude-code-bug-fixing/)
- [代码审查命令](https://docs.anthropic.com/zh-CN/docs/claude-code/review)

---

## Phase 4: 高级特性实战

### 核心概念

- [ ] **Subagents（子智能体）**
  - 内置子智能体：Explore（只读搜索）、Plan（规划）、General-purpose（通用）
  - 自定义子智能体：隔离上下文窗口、自定义系统提示词、限定工具权限
  - 使用场景：并行任务、需要隔离上下文的研究任务
  - 配置层级：Managed > CLI flag > Project > User > Plugin
  - 模型选择：简单任务用 Haiku，复杂任务用 Opus/Sonnet

- [ ] **MCP（Model Context Protocol）**
  - 作用：连接 Claude 到外部服务（数据库、API、Slack 等）
  - 官方 MCP 服务器：文件系统、数据库、Web 搜索等
  - 配置方式：`.claude/settings.json` 中添加 mcpServers
  - 使用场景：查询数据库、发布到 Slack、访问内部 API
  - 安全考虑：MCP 服务器权限控制

- [ ] **Skills（技能）**
  - 定义：可复用的指令、知识和工作流，通过 `/skill-name` 调用
  - 与 Subagents 区别：Skill 是工具扩展，Subagents 是执行隔离
  - 创建方式：`.claude/skills/skill-name.md`
  - 示例：自定义的 `/deploy` 检查清单、代码审查流程
  - 可移植性：Skill 可在 Code/Apps/API 间移植

- [ ] **Hooks（钩子）**
  - 作用：在生命周期事件上自动执行脚本、HTTP 请求或提示词
  - 事件类型：SessionStart/SessionEnd、PreToolUse/PostToolUse、UserPromptSubmit、Stop 等
  - Hook 类型：Command hooks（Shell）、HTTP hooks（POST 请求）、MCP tool hooks、Prompt hooks
  - 使用场景：文件变更后自动 lint、提交前运行测试、会话开始时加载上下文
  - 配置位置：`settings.json` 中的 hooks 字段

- [ ] **Agent Teams（多智能体协作，实验性）**
  - 作用：协调多个独立的 Claude Code 会话并行工作
  - 工具：TeamCreate、TeamDelete、SendMessage
  - 使用场景：并行代码审查、多模块同时开发
  - 注意事项：实验性功能，API 可能变化

- [ ] **记忆系统进阶**
  - Memory 类型：user（用户信息）、feedback（反馈规则）、project（项目信息）、reference（外部资源）
  - 何时保存到记忆：用户纠正、确认有效方法、项目决策
  - 何时访问记忆：相关任务、用户明确要求
  - 记忆文件结构：frontmatter（name/description/type）+ 内容
  - MEMORY.md 索引文件管理（每行 < 150 字符）

- [ ] **Worktree 并行开发**
  - 作用：创建隔离的 git worktree 进行并行开发
  - 使用 `EnterWorktree` 工具创建/进入 worktree
  - 使用 `ExitWorktree` 退出（keep/remove 选项）
  - 场景：并行开发多个功能、隔离实验性改动

### 🔨 实战练习

- [ ] 创建一个自定义 Subagent：限定只访问 docs/ 目录，用于生成项目文档
- [ ] 配置一个 MCP 服务器（如 Slack 或数据库），并在开发中使用
- [ ] 创建两个自定义 Skills：`/code-review-checklist` 和 `/deploy-checklist`
- [ ] 配置一个 PostToolUse Hook：每次编辑文件后自动运行 lint
- [ ] 使用 Worktree 创建两个并行工作区，同时开发两个独立功能
- [ ] 设置 Auto Memory：让 Claude 记住你的编码偏好和项目约束
- [ ] （进阶）尝试 Agent Teams：创建两个子智能体分别做代码审查和测试生成

### 参考资料

- [扩展 Claude Code 文档](https://code.claude.com/docs/en/features-overview)
- [Subagents 文档](https://docs.anthropic.com/zh-CN/docs/claude-code/sub-agents)
- [MCP 构建器 Skill](https://code.claude.com/docs/en/mcp)
- [Hooks 参考](https://code.claude.com/docs/en/hooks)
- [Skills 与 Subagents 对比](https://colinmcnamara.com)

---

## Phase 5: 工程化与团队落地

### 核心概念

- [ ] **团队 CLAUDE.md 规范**
  - 项目级 CLAUDE.md 提交到 git，团队共享
  - 统一构建/测试/lint 命令
  - 统一代码风格规则（与 ESLint/Prettier 等对齐）
  - 已知陷阱和非显而易见的行为记录
  - 环境变量的模板（.env.example）

- [ ] **CI/CD 集成**
  - 在 CI 中运行 Claude Code（非交互模式）
  - `claude -p "query"` 模式用于自动化检查
  - 结合 GitHub Actions / GitLab CI
  - 自动化代码审查（PR 时触发 Claude 审查）
  - 自动化文档更新

- [ ] **权限与安全模型**
  - 权限模式：default（每次确认）/ plan（计划模式）/ minimal（最小权限）/ auto（自动允许）
  - `allowedTools`：允许特定工具无需确认
  - `disallowedTools`：禁止危险操作
  - `.claude/settings.local.json`：个人覆盖（不提交）
  - 敏感信息处理：防止提交 .env、密钥等

- [ ] **成本优化**
  - Prompt Caching：稳定 CLAUDE.md 提升缓存命中率（目标 > 40%）
  - 使用 `/cost` 监控缓存命中率
  - 小模型用于简单任务（Explore subagent 用 Haiku）
  - 批量 API 用于大规模任务（节省高达 95%）
  - 上下文管理：避免无意义的长对话

- [ ] **团队协作模式**
  - Managed Settings：组织级统一配置
  - Plugins：打包 Skills/Hooks/Subagents/MCP 供团队复用
  - 共享的 Prompt 模板库
  - 代码审查标准统一（通过 Skill 实现）
  - Onboarding 新成员：用 `/init` + CLAUDE.md

- [ ] **安全合规**
  - OWASP 扫描集成
  - 敏感数据不进入上下文
  - Git hook 防止密钥提交
  - 审计日志：记录 Claude 的操作
  - 合规检查：HIPAA / SOC2 / PCI-DSS 相关控制

- [ ] **迁移与落地策略**
  - 从个人使用到团队推广的渐进路径
  - 衡量 ROI：开发效率提升指标
  - 常见阻力与应对（"Claude 写的代码不可靠"等）
  - 最佳实践内部培训材料

### 🔨 实战练习

- [ ] 为你的团队项目创建一份完整的项目级 CLAUDE.md
- [ ] 配置 GitHub Actions 工作流：PR 创建时自动用 Claude 做代码审查
- [ ] 设置权限模型：为敏感仓库配置 minimal 模式 + disallowedTools
- [ ] 使用 `/cost` 分析一周的实际使用，优化缓存命中率至 40%+
- [ ] 创建一个 Plugin 打包团队的 Skills 和 Hooks，供其他项目复用
- [ ] 配置安全 Hook：检测并阻止潜在密钥提交

### 参考资料

- [Claude Code 设置文档](https://docs.anthropic.com/zh-CN/docs/claude-code/setup)
- [第三方最佳实践合集](https://claudecodeguides.com/best-practices/)
- [团队设置指南](https://docs.anthropic.com/zh-CN/docs/claude-code/settings)

---

## 复习计划

| 时间 | 内容 | 方式 |
|------|------|------|
| 完成 Phase 1 后 | CLAUDE.md 设计模式、上下文管理命令 | 间隔重复（/review） |
| 完成 Phase 2 后 | 需求分析流程、Plan Mode 使用 | 实战模拟 |
| 完成 Phase 3 后 | Bug 修复、重构、测试工作流 | 代码审查练习 |
| 完成 Phase 4 后 | Subagents/MCP/Skills/Hooks 对比选择 | 场景匹配测试 |
| 完成 Phase 5 后 | 团队落地全链路 | 综合项目 |
| 全部完成后 | 全课程综合复习 | /progress + /review |

---

## 综合实战项目

完成所有阶段后，进行一个综合项目，模拟真实团队场景：

**项目：为一个中型 Web 应用引入完整的 Claude Code 工程体系**

1. 编写项目级 CLAUDE.md（含架构说明、开发规范、已知问题）
2. 用 Plan Mode 规划一个新功能（涉及前端 + 后端 + 数据库迁移）
3. 配置 MCP 连接数据库，让 Claude 直接查询数据模型
4. 创建自定义 Skills：部署检查清单、代码审查清单
5. 配置 Hooks：提交前 lint + 测试 + 密钥检测
6. 设置 CI/CD：PR 自动审查 + 文档更新
7. 优化成本：将缓存命中率提升至 40%+
8. 编写团队落地指南（如何让新成员快速上手）

---

## 附加资源

- [Claude Code 官方文档首页](https://docs.anthropic.com/zh-CN/docs/claude-code/overview)
- [Claude Code 快速开始](https://docs.anthropic.com/zh-CN/docs/claude-code/quickstart)
- [Claude API 文档](https://docs.anthropic.com/zh-CN/home)
- [Claude Code 最佳实践（英文）](https://code.claude.com/docs/en/best-practices)
- [Claude Code 教程合集](https://codingnomads.com/claude-code-common-development-workflows)
- [Claude Code 第三方指南](https://claudecodeguides.com/best-practices/)
