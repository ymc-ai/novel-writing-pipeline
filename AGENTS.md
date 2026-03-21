# Novel Writer Agent - Agent 协作指南

## 项目概述

基于 AI Agent Skills 架构的长篇小说创作系统，通过专业化 Agent 协作创作 1000+ 章节长篇网络小说。

**核心目标**：
- **信息熵增对抗**：三层记忆架构 + 世界状态管理
- **叙事疲劳对抗**：节奏监控 + 语调约束

---

## Agent = 专家

Agent 定义"是什么"，负责执行具体任务。**每个 Agent 有明确的边界约束**。

### Agent 列表

| Agent | 职责 | 数据范围 |
|-------|------|----------|
| `world-agent` | 世界状态管理 | `data/world/` `data/threads.json` |
| `quality-agent` | 叙事质量 | `data/chapters/` (只读) |
| `chapter-agent` | 章节创作 | `data/chapters/` |
| `character-agent` | 角色设计 | `data/world/characters/` |
| `outline-agent` | 大纲规划 | `data/outline/` |
| `plot-agent` | 情节校验 | `data/chapters/` `data/world/` (只读) |
| `polish-agent` | 风格润色 | `data/chapters/` |

详细边界约束见 `agents/` 目录下各 Agent 文件的 frontmatter。

### Agent 调用规则

1. **禁止越界调用**：Agent 不得直接调用同级 Agent
2. **通过 Skill 编排**：Agent 协作由 Skill 统一调度
3. **只读标记**：只读 Agent 不得修改任何数据文件

---

## Skill = 工作流

Skill 定义"怎么做"，负责编排 Agent 协作流程。

### Skill 列表

| Skill | 用途 | 调用方式 |
|-------|------|----------|
| `novel-writing` | 完整创作流程 | 写小说、创建项目 |
| `chapter-writing` | 单章创作流程 | 写第X章 |
| `outline-writing` | 大纲生成流程 | 生成大纲、规划情节 |
| `quick-review` | 快速检查 | 检查章节 |

---

## 调用流程

### novel-writing（完整创作流程）

```
阶段一：初始化
  world-agent → 初始化项目世界状态

阶段二：角色设定
  character-agent → 创建角色档案

阶段三：构建世界
  world-agent → 世界设定、势力关系

阶段四：生成大纲
  outline-agent → 总纲 + 卷纲 + 章纲

阶段五：章节创作（循环）
  chapter-agent → 创作章节
  ↓
  plot-agent + quality-agent → 一致性 + 质量检查
  ↓
  polish-agent → 风格润色
  ↓
  world-agent → 更新世界状态
```

### chapter-writing（单章创作流程）

```
1. world-agent     → 获取世界状态 + 上一章结尾情节点
2. outline-agent   → 获取章纲
3. quality-agent   → 获取质量要求
4. chapter-agent   → 创作章节（必须承接上一章结尾）
5. plot-agent      → 校验一致性
6. polish-agent    → 润色
7. world-agent     → 更新状态 + 记录本章结尾情节点
```

### outline-writing（大纲生成流程）

```
1. world-agent     → 获取世界设定
2. outline-agent  → 生成总纲
3. outline-agent  → 生成分卷大纲
4. outline-agent  → 生成章纲
5. world-agent    → 保存大纲到 data/outline/
```

### quick-review（快速检查）

```
1. quality-agent → 节奏 + 禁用词检查
2. plot-agent   → 一致性检查
3. 输出报告
```

---

## 数据规范

### world.json

```json
{
  "project": {
    "title": "小说标题",
    "genre": "题材类型",
    "target_chapters": 1000,
    "current_chapter": 1
  },
  "variables": {
    "角色": {},
    "势力": {},
    "道具": {}
  },
  "delta_log": [],
  "memory": {
    "L1": [],
    "L1_Endings": [
      {
        "chapter_id": 1,
        "ending_point": "上一章结尾情节点",
        "ending_type": "cliffhanger|suspense|normal"
      }
    ],
    "L2": [],
    "L3": []
  }
}
```

### threads.json

```json
{
  "threads": [
    {
      "id": "thread_001",
      "title": "伏笔标题",
      "chapter_introduced": 12,
      "priority": 4,
      "status": "active|resolved|orphaned",
      "must_resolve": true
    }
  ]
}
```

### 伏笔优先级计算

```
W = P × T

W = 权重
P = 优先级 (1-5)
T = 已过章节数

W ≥ 20 → 必须回收
W < 20 → 可选回收
```

### 三层记忆架构

| 层级 | 内容 | 用途 |
|------|------|------|
| L1 | 短期记忆 | 当前章节情节、角色状态 |
| L2 | 中期记忆 | 卷内情节推进、势力变化 |
| L3 | 长期记忆 | 世界观设定、核心人物弧线 |

---

## 质量标准

| 指标 | 标准 |
|------|------|
| 字数 | 2000-4000 字/章 |
| 节奏 | Action_Density > 0.08 |
| 伏笔跨度 | ≤ 150 章 |
| 禁用词 | 零容忍 |

### 节奏模式

| 模式 | 特征 | 持续章节 |
|------|------|----------|
| 高潮期 | 战斗、突破 | 3-5 章 |
| 平缓期 | 文戏、日常 | 5-8 章 |
| 过渡期 | 铺垫 | 5-10 章 |
| 危机期 | 悬念 | 3-5 章 |

### 修辞阶段

| 章节范围 | 文风 | 示例 |
|----------|------|------|
| 1-10 | 市井白话 | 这厮、瞅着 |
| 11-50 | 江湖气息 | 好汉、在下 |
| 51-200 | 史诗叙事 | 彼时、乃至 |
| 200+ | 大道至简 | 极简白描 |

---

## 工作约定

1. **边界约束**：每个 Agent 严格遵守自己的 scope 定义
2. **先查询再操作**：修改数据前先读取当前状态
3. **记录变更**：所有操作必须记录到 delta_log
4. **伏笔必答**：must_resolve 伏笔必须排期回收
5. **质量优先**：章节必须通过 quality-agent 检查
6. **风格统一**：遵守修辞阶段规范
7. **禁止越界**：Agent 不得调用同级 Agent 进行检查
8. **章节衔接**：创作时必须承接上一章结尾情节点
9. **Delta 日志**：每次状态变更记录格式：`[{timestamp, agent, action, before, after}]`

---

## 章节文件规范

章节文件存储于 `data/chapters/`，命名格式：`chapter_XXX.md`

```markdown
---
chapter_id: 1
title: 第一章标题
word_count: 2500
rhetoric_stage: 市井白话
ending_type: cliffhanger
plot_threads: [thread_001, thread_002]
created_at: 2024-01-01T00:00:00Z
---

# 第一章标题

章节正文...
```
