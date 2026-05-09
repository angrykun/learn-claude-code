# Phase 1: 上下文管理与 CLAUDE.md 精通

## 概念 1: Claude Code 工作原理

### 1. Agent 循环（Agentic Loop）
Claude Code 的核心是一个**自主循环**，而非简单的问答：
```
用户输入 → Claude 分析任务，选择工具（Read/Grep/Bash/Write...） → 执行工具 → 获得结果 → 继续推理 → 选择下一个工具 → 循环直到任务完成
```

关键：Claude 可以**主动调用工具多次**，而不只是回答一次。

### 2. 内置工具集

| 工具 | 用途 |
|------|------|
| **Read** | 读取文件 |
| **Edit** | 精确字符串替换 |
| **Write** | 写入新文件 |
| **Bash** | 执行 shell 命令 |
| **Grep** | ripgrep 搜索 |
| **Glob** | 文件模式匹配 |
| **WebFetch** | 抓取网页 |
| **WebSearch** | 搜索引擎 |
| **Agent** | 启动子智能体 |
| **Skill** | 执行自定义技能 |
| **TaskCreate/TaskUpdate** | 任务追踪 |

### 3. 上下文窗口机制

- 标准上下文：**200K tokens**（Opus 4.7 / Sonnet 4.6 / Haiku 4.5）
- 扩展上下文：**1M tokens**（Opus 4.6 / Sonnet 4.6）
- **性能随填充度下降** — 上下文越满，输出质量越低
- **Prompt Caching** — CLAUDE.md 和对话前缀会被缓存

### 4. 系统提示词结构
1. 角色指令
2. 工具定义
3. 行为规则
4. CLAUDE.md
5. Memory（前 200 行）
6. 对话历史

---

## 概念 2: CLAUDE.md 四层架构

CLAUDE.md 有四层优先级结构（越往下优先级越高）：

| 层级 | 路径 | 作用 | 是否提交到 git |
|------|------|------|----------------|
| 1 | `~/.claude/CLAUDE.md` | 全局规则，所有项目共享 | 否 |
| 2 | `./CLAUDE.md` | 项目级规则，团队共享 | 是 |
| 3 | `./CLAUDE.md.local` | 个人偏好 | 否（加入 .gitignore） |
| 4 | `src/subdirectory/CLAUDE.md` | 目录级规则，懒加载 | 通常否 |

### 优先级规则
加载顺序：后面的覆盖前面的。
- 全局 → 项目级 → 个人 → 目录级

---

## 概念 3: 上下文管理最佳实践

### 核心原则
上下文越精简，输出越精准。

### 实践清单
1. **精简 CLAUDE.md（< 500 tokens）**
   - 只写"改变行为的规则"
   - 不写项目介绍（放 README）
   - 不写 API 文档（Claude 能读代码）

2. **稳定 CLAUDE.md，提升缓存命中率**
   - 每周最多编辑一次
   - 用 `/cost` 监控，目标 > 40%

3. **使用 `.claudeignore`**
   ```text
   node_modules/
   dist/
   build/
   .next/
   *.min.js
   ```

4. **会话管理命令**
   | 命令 | 作用 |
   |------|------|
   | `/context` | 查看 token 分布 |
   | `/compact` | 压缩对话历史 |
   | `/clear` | 清空会话 |
   | `/cost` | 查看费用和缓存命中率 |

5. **用对工具**
   - 找函数定义 → 用 Grep，不要 Read 整个文件
   - 查错误原因 → 粘贴关键几行，不要贴整个日志

---

## 概念 4: CLAUDE.md 五种设计模式

### 模式对比

| 模式 | 适用场景 | 核心优势 |
|------|----------|----------|
| **Minimal** | 小项目，< 10 行 | 极简，无浪费 |
| **Sectioned** | 中型项目，30-100 行 | 结构清晰 |
| **Task-Focused** | 临时任务，10-30 行 | 临时聚焦 |
| **Hierarchical** | 大型/Monorepo | 按需加载 |
| **Context Index** | 有大量文档的项目 | 文档按需读 |

### 各模式示例

**Minimal（小项目）**
```markdown
# 项目规则
- 用 pnpm，不用 npm
- 测试命令：pnpm test
- 提交格式：Conventional Commits
```

**Sectioned（中型项目）**
```markdown
# 项目规则
## 命令
- 安装：pnpm install
- 开发：pnpm dev
## 架构约束
- 前端：Vue 3 + TypeScript
## 代码风格
- 组件用 PascalCase
## 已知坑
- 不要用 uuid v3，用 v4
```

**Task-Focused（特定会话）**
```markdown
# 今日任务
## 目标
重构 src/auth/ 模块，将 JWT 换成 Session + Redis
## 要动的文件
- src/auth/jwt.ts
- src/middleware/auth.ts
## 约束
- 不改现有 API 接口签名
```

**Hierarchical（大型项目）**
```
my-monorepo/
├── CLAUDE.md              ← 根目录全局
├── frontend/
│   └── CLAUDE.md          ← 前端专属
└── backend/
    └── CLAUDE.md          ← 后端专属
```

**Context Index（文档引用）**
```markdown
# 项目规则
## 详细文档（按需读取）
- 架构设计：docs/architecture.md
- API 接口：docs/api-reference.md
```

---

## 概念 5: Auto Memory（2026 新特性）

### 位置
```
~/.claude/projects/<项目路径>/memory/MEMORY.md
```

### 加载规则
- 每次会话开始时，**前 200 行**自动加载到上下文
- 优先级：CLAUDE.md > Memory

### Claude 会记录什么？
| 类型 | 内容示例 |
|------|----------|
| **user** | 编码偏好、技术栈偏好 |
| **feedback** | 你纠正过的行为、确认过的方法 |
| **project** | 项目状态、技术决策 |
| **reference** | 外部文档位置 |

### 关键命令
- `/memory` — 查看 Memory 内容
- `/memory edit` — 编辑 Memory

### 记忆 vs CLAUDE.md
| | CLAUDE.md | Memory |
|--|-----------|--------|
| 加载时机 | 每次会话开始 | 每次会话开始（前 200 行） |
| 内容性质 | 硬性规则 | 参考信息 |
| 稳定性 | 应该稳定 | 可以频繁更新 |

---

## Phase 1 实战练习

1. 运行 `/init` 生成 CLAUDE.md，精简至 200 行以内
2. 配置 `.claudeignore` 排除 node_modules/、dist/ 等
3. 设置全局 `~/.claude/CLAUDE.md`（5-10 条核心规则）
4. 创建目录级 CLAUDE.md 测试懒加载
5. 使用 `/context` 和 `/cost` 监控开发会话