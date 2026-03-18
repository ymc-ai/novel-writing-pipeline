# Novel Writer Agent - Agent 协作指南

## 项目概述

基于 Claude Code Agent Skills 架构的长篇小说创作系统，通过专业化 Agent 协作创作 1000+ 章节长篇网络小说。

**核心目标**：
- **信息熵增对抗**：三层记忆架构 + 世界状态管理
- **叙事疲劳对抗**：节奏监控 + 语调约束

---

## Agent = 专家

Agent 定义"是什么"，负责执行具体任务。

### Agent 列表

| Agent | 职责 | 核心能力 |
|-------|------|----------|
| `world-agent` | 世界状态管理 | 状态维护、伏笔追踪、记忆管理 |
| `quality-agent` | 叙事质量 | 节奏监控、语调约束、爽感管理 |
| `chapter-agent` | 章节创作 | 场景规划、对话设计、伏笔植入 |
| `character-agent` | 角色设计 | 角色档案、角色弧线 |
| `outline-agent` | 大纲规划 | 总纲、卷纲、章纲 |
| `plot-agent` | 情节校验 | 世界一致性、伏笔合规、记忆管理 |
| `polish-agent` | 风格润色 | AI味消除、表达优化 |

---

## Skill = 工作流

Skill 定义"怎么做"，负责编排 Agent 协作流程。

### Skill 列表

| Skill | 用途 | 调用方式 |
|-------|------|----------|
| `novel-writing` | 完整创作流程 | 写小说、创建项目 |
| `chapter-writing` | 单章创作流程 | 写第X章 |
| `quick-review` | 快速检查 | 检查章节 |

---

## 调用流程

### novel-writing（完整创作流程）

```
阶段一：初始化
  world-agent → 初始化项目

阶段二：角色设定
  character-agent → 创建角色

阶段三：构建世界
  world-agent → 世界设定

阶段四：生成大纲
  outline-agent → 总纲 + 卷纲 + 章纲

阶段五：章节创作（循环）
  chapter-agent → 创作
  ↓
  plot-agent + quality-agent → 检查
  ↓
  polish-agent → 润色
  ↓
  world-agent → 更新状态
```

### chapter-writing（单章创作流程）

```
1. world-agent → 获取世界状态
2. outline-agent → 获取章纲
3. quality-agent → 获取质量要求
4. chapter-agent → 创作章节
5. plot-agent → 一致性校验
6. polish-agent → 润色
7. world-agent → 更新状态
```

### quick-review（快速检查）

```
1. quality-agent → 节奏 + 禁用词
2. plot-agent → 一致性检查
3. 输出报告
```

---

## 数据规范

### world.json
```json
{
  "project": {"title": "", "genre": "", "target_chapters": 1000},
  "variables": {"角色": {}, "势力": {}, "道具": {}},
  "delta_log": [],
  "memory": {"L1": [], "L2": [], "L3": []}
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
      "status": "active|resolved|orphaned"
    }
  ]
}
```

### 伏笔优先级
```
W = P × T
W ≥ 20 → 必须回收
W < 20 → 可选回收
```

---

## 质量标准

| 指标 | 标准 |
|------|------|
| 字数 | 2000-4000 字/章 |
| 节奏 | Action_Density > 0.08 |
| 伏笔跨度 | ≤ 150 章 |
| 禁用词 | 零容忍 |

### 节奏模式

| 模式 | 特征 | 持续 |
|------|------|------|
| 高潮期 | 战斗、突破 | 3-5章 |
| 平缓期 | 文戏、日常 | 5-8章 |
| 过渡期 | 铺垫 | 5-10章 |
| 危机期 | 悬念 | 3-5章 |

---

## 修辞阶段

| 章节 | 文风 | 示例 |
|------|------|------|
| 1-10 | 市井白话 | 这厮、瞅着 |
| 11-50 | 江湖气息 | 好汉、在下 |
| 51-200 | 史诗叙事 | 彼时、乃至 |
| 200+ | 大道至简 | 极简白描 |

---

## 工作约定

1. **先查询再操作**：修改数据前先读取当前状态
2. **记录变更**：所有操作必须记录 delta_log
3. **伏笔必答**：must_resolve 伏笔必须排期回收
4. **质量优先**：章节必须通过 quality-agent 检查
5. **风格统一**：遵守修辞阶段规范
