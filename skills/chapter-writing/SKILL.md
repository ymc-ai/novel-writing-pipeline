---
name: chapter-writing
description: |
  单章创作流程。创作单个章节的完整流程。
  用户说"写第X章"、"创作章节"时使用。
---

# 单章创作流程

## 输入

- 章节编号：chapter_id
- 章节大纲：调用 subagent（outline-agent）获取
- 世界状态：调用 subagent（world-agent）获取

## 流程

### Step 1: 准备

```
a) 调用 subagent（world-agent）
   → 获取当前世界状态
   → 获取上一章结尾情节点（previous_chapter_ending）**必须获取**
   → 获取活跃伏笔列表
   → 获取相关角色状态

b) 调用 subagent（outline-agent）
   → 获取章纲
   → 获取待触发伏笔节点

c) 调用 subagent（quality-agent）
   → 获取当前节奏模式
   → 获取修辞阶段要求
   → 获取禁用词表
```

### Step 2: 创作

```
调用 subagent（chapter-agent）创作章节：

输入：
- chapter_id
- previous_chapter_ending（上一章结尾情节点）**必须使用**
- outline（章纲）
- world_context（世界状态）
- character_status（角色状态）
- active_threads（待触发伏笔）
- rhetoric_stage（修辞阶段）

要求：
- 章节开头必须自然承接上一章结尾的情节
- 不能跳跃或割裂，必须有情节上的连续性

输出：
- chapter_content
- delta_log
- hooks_planted
```

### Step 3: 检查

```
a) 调用 subagent（plot-agent）校验
   → 世界一致性
   → 伏笔合规性
   → 逻辑自洽

b) 调用 subagent（quality-agent）检查
   → 节奏评估
   → 禁用词检查
```

### Step 4: 润色

```
调用 subagent（polish-agent）润色：
→ 消除AI味
→ 统一风格
→ 增强表现力
```

### Step 5: 保存

```
a) 保存章节到 data/chapters/chapter_{id}.md
b) 调用 subagent（world-agent）更新状态
   → 更新 delta_log
   → 更新伏笔状态
   → 更新 L1_Endings（记录本章结尾情节点）
c) 调用 subagent（outline-agent）更新章纲状态
```

## 输出

```yaml
chapter_id: 45
title: "云霄惊变"
content: |
  章节全文...

word_count: 3200
delta_log: [...]
hooks_planted: [...]
ending_hook: "悬念结尾..."

quality_report:
  score: 8.5
  mode: "高潮期"
  issues: []

plot_validation:
  result: "pass"
  issues: []
```

## 质量标准

- 字数：2000-4000
- 节奏：Action_Density > 0.08
- 禁用词：零容忍
- 钩子：必须有
