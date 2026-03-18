# Novel Writer Agent

长篇小说创作系统，基于 Claude Code Agent Skills 架构。

## 系统架构

```
┌─────────────────────────────────────────────┐
│                  Skill (工作流)               │
│  novel-writing / chapter-writing / quick-review │
└─────────────────────────────────────────────┘
                    ↓ 调用
┌─────────────────────────────────────────────┐
│                 Agent (专家)                  │
│  world | quality | chapter | character      │
│  outline | plot | polish                     │
└─────────────────────────────────────────────┘
```

## Agent = 专家

| Agent | 职责 |
|-------|------|
| `world-agent` | 世界状态、伏笔、记忆 |
| `quality-agent` | 节奏、语调、爽感 |
| `chapter-agent` | 章节创作 |
| `character-agent` | 角色设计 |
| `outline-agent` | 大纲规划 |
| `plot-agent` | 情节校验 |
| `polish-agent` | 风格润色 |

## Skill = 工作流

| Skill | 用途 |
|-------|------|
| `novel-writing` | 完整创作流程 |
| `chapter-writing` | 单章创作流程 |
| `quick-review` | 快速检查 |

## 目录结构

```
├── agents/           # Agent 定义
│   ├── world-agent.md
│   ├── quality-agent.md
│   └── ...
├── skills/           # Skill 定义
│   ├── novel-writing/
│   ├── chapter-writing/
│   └── quick-review/
├── data/             # 数据存储
│   ├── world/
│   ├── threads/
│   ├── chapters/
│   └── outline/
└── README.md
```

## 使用方式

### 加载 Skill
```
/skill novel-writing
```

### 创作流程

```
用户请求 → Skill 编排 → Agent 执行 → 输出
```

详见 [AGENTS.md](./AGENTS.md)
