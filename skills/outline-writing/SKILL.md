---
name: outline-writing
description: |
  大纲生成流程。生成总纲、卷纲、章纲的完整工作流。
  用户说"生成大纲"、"规划大纲"、"写章纲"时使用。
---

# 大纲生成流程

## 核心原则

> **质量优先，分批执行，稳步推进**
> 
> 每批生成章纲数量：5-10章
> 每批生成后必须检查，不合格则重写

---

## 三层循环结构

```
┌─────────────────────────────────────────────────────────────┐
│  第一循环：总纲生成（一次性）                                  │
│  调用 subagent（outline-agent）→ 世界状态 → 伏笔规划 → 输出总纲 │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│  第二循环：卷纲生成（每卷一次）                                │
│  调用 subagent（outline-agent）→ 总纲读取 → 卷纲设计 → 输出卷纲 │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│  第三循环：章纲生成（批量执行，每批5-10章）                    │
│  调用 subagent（outline-agent）→ 伏笔读取 → 章纲编写 → 质量检查 → 伏笔植入 │
└─────────────────────────────────────────────────────────────┘
```

---

## 第一：总纲生成

### 流程步骤

```
a) 调用 subagent（outline-agent）     → 生成总纲框架
b) 调用 subagent（world-agent）       → 确认世界状态
c) 调用 subagent（outline-agent）     → 规划主线伏笔（≥10个）
d) 调用 subagent（outline-agent）     → 定义节奏分布
e) 调用 subagent（outline-agent）     → 输出完整总纲
f) 调用 subagent（world-agent）       → 同步主线伏笔到 threads.json
```

### 总纲必须包含

```yaml
# data/outline/outline.yaml

title: "小说标题"
genre: "题材"
target_chapters: 目标章节数

three_act_structure:
  act_one:
    chapters: "1-333"
    percentage: "33%"
  act_two_part_a:
    chapters: "334-500"
    percentage: "17%"
  act_two_part_b:
    chapters: "501-666"
    percentage: "17%"
  act_three:
    chapters: "667-1000"
    percentage: "33%"

main_threads: []              # ≥10个主线伏笔
pacing_targets: {}            # 节奏分布
```

### 检查清单

- [ ] 三幕结构完整
- [ ] 核心主题定义清晰
- [ ] 主线伏笔 ≥10个
- [ ] 节奏目标合理

---

## 第二：卷纲生成（循环）

### 流程步骤

```
a) 调用 subagent（outline-agent）     → 获取总纲中的本卷规划
b) 调用 subagent（outline-agent）     → 确定本卷主题和范围
c) 调用 subagent（outline-agent）     → 设置里程碑（每25章一个）
d) 调用 subagent（outline-agent）     → 规划伏笔分布
e) 调用 subagent（outline-agent）     → 设计角色弧光
f) 调用 subagent（outline-agent）     → 输出卷纲
g) 调用 subagent（world-agent）       → 同步本卷伏笔到 threads.json
```

### 卷纲模板

```yaml
# data/outline/arcs/arc_001.yaml

arc_id: 1
arc_title: "卷标题"
chapters: "1-100"
theme: "本卷主题"

milestones:
  - chapter: 25
    event: "里程碑1"
    type: "钩子"
  - chapter: 50
    event: "里程碑2"
    type: "高潮"
  - chapter: 75
    event: "里程碑3"
    type: "危机"
  - chapter: 100
    event: "里程碑4"
    type: "收尾"

thread_distribution: []
character_arcs: []
```

---

## 第三：章纲生成（循环）

### 核心原则

> **每批5-10章，质量过关后再生成下一批**

### 流程步骤（单批次5-10章）

```
a) 调用 subagent（outline-agent）     → 获取下一批次章号
b) 调用 subagent（outline-agent）     → 获取待植入伏笔列表
c) 调用 subagent（outline-agent）     → 编写本章纲（每章单独输出）
d) 调用 subagent（outline-agent）     → 质量自检（逐章检查）
e) 调用 subagent（plot-agent）        → 一致性校验
f) 调用 subagent（outline-agent）     → 修复问题（如有）
g) 调用 subagent（world-agent）       → 同步伏笔到 threads.json
h) 调用 subagent（outline-agent）     → 输出检查报告
```

### 章纲模板

```yaml
# data/outline/chapters/chapter_001.yaml

chapter_id: 1
title: "章节标题"

summary:
  one_line: "单句概要（≤20字）"
  three_sentences:
    - "事件1"
    - "事件2"
    - "事件3"

plot_purpose:
  advances_main_thread: "thread_XXX"
  advances_sub_threads: ["thread_XXX"]
  character_development: "角色发展"

scene_structure:
  - scene_id: 1
    location: "地点"
    time: "时间"
    characters: ["角色"]
    type: "dialogue|narration|action"
    tension: 1-10

ending_hook:
  type: "cliffhanger|question|mystery"
  content: "钩子内容"

character_states:
  protagonist:
    status: "状态"
    emotional_state: "情绪"

pacing:
  type: "action|narrative|transition"
  tension_level: 1-10

hooks_planted:
  - hook_id: "hook_001_01"
    type: "foreshadow"
    content: "伏笔内容"
    recovery_plan:
      expected_chapter: 章节
```

---

## 批量生成策略

### 分批规则

| 批次 | 章节范围 | 数量 | 每批检查 |
|------|----------|------|----------|
| 第1批 | 1-10章 | **5-10章** | 逐章质量检查 |
| 第2批 | 11-20章 | 5-10章 | 逐章质量检查 |
| ... | ... | ... | ... |
| 每批间隔 | 休息/审查 | - | 确认质量后再继续 |

### 质量检查（每批次）

```
subagent（outline-agent）自检：
├── 单句概要 ≤ 20字
├── 三个关键事件覆盖情节点
├── 场景数 ≤ 4个
├── 紧张度设置合理
├── 伏笔植入位置明确
└── 结尾钩子类型正确

subagent（plot-agent）校验：
├── 与上一章衔接正确
├── 与总纲/卷纲一致
├── 伏笔跨度 ≤ 150章
└── 无逻辑矛盾
```

### 不合格处理

```
如果某章质量不合格：
1. 标记问题章节
2. 重新生成问题章节
3. 再次检查
4. 合格后继续下一批
```

---

## 伏笔管理系统

### 伏笔生成规则

```
伏笔跨度 = 回收章节 - 引入章节
跨度 ≤ 150章

权重公式：W = P × T
W ≥ 20 → 必须排期回收
```

### 伏笔分布模板

每10章应包含：
- 1-2个新伏笔引入
- 1个伏笔推进/深化
- 0-1个伏笔回收

### 伏笔优先级

| 优先级 | 类型 | 数量上限 | 跨度 |
|--------|------|----------|------|
| P10 | 核心主线 | 3-5个 | 全文 |
| P8-9 | 重要支线 | 5-10个 | 50-200章 |
| P6-7 | 角色相关 | 10-20个 | 20-100章 |
| P4-5 | 细节伏笔 | 不限 | ≤50章 |

---

## 同步到 threads.json

### 同步时机

> **每批次生成并检查合格后，同步伏笔到 threads.json**

### 同步格式

```json
{
  "threads": [
    {
      "id": "thread_001",
      "title": "伏笔标题",
      "chapter_introduced": 1,
      "priority": 10,
      "status": "active",
      "key_moments": [
        {"chapter": 1, "event": "伏笔首次出现"}
      ],
      "resolution_plan": {
        "expected_chapter": 100
      }
    }
  ]
}
```

---

## 质量标准

### 章纲质量标准

| 指标 | 标准 | 检查方式 |
|------|------|----------|
| 单句概要 | ≤20字 | 自检 |
| 关键事件 | 3个，覆盖主要情节点 | 自检 |
| 场景数 | ≤4个 | 自检 |
| 紧张度 | 1-10 | 自检 |
| 伏笔植入 | 每章0-2个新伏笔 | 自检 |
| 结尾钩子 | 必须有，类型正确 | 自检 |
| 伏笔跨度 | ≤150章 | subagent（plot-agent） |
| 章节衔接 | 承接上一章 | subagent（plot-agent） |
| 逻辑一致 | 无矛盾 | subagent（plot-agent） |

### 检查报告模板

```yaml
# 每批次检查报告

batch_info:
  batch_number: 1
  chapters: "1-10"
  total_chapters: 10

quality_check:
  outline_agent_self_check:
    - chapter_001: pass
    - chapter_002: pass
    - chapter_003: fail  # 问题描述
    - chapter_004: pass
    - chapter_005: pass
    
plot_agent_validation:
  - chapter_001: pass
  - chapter_002: pass
  - chapter_003: fail  # 问题描述
  - chapter_004: pass
  - chapter_005: pass

issues:
  - chapter: 3
    issue: "问题描述"
    fix_required: true
    status: "pending"  # pending | fixed

summary:
  passed: 9
  failed: 1
  next_action: "重新生成第3章"
```

---

## 总结

```
生成流程：
1. 总纲生成（一次性）
2. 卷纲生成（每卷一次）
3. 章纲生成（每批5-10章）
   ├── a) 调用 subagent（outline-agent）获取待生成章节
   ├── b) 调用 subagent（outline-agent）获取伏笔列表
   ├── c) 调用 subagent（outline-agent）逐章编写章纲
   ├── d) 调用 subagent（outline-agent）逐章自检
   ├── e) 调用 subagent（plot-agent）一致性校验
   ├── f) 修复问题（如有）
   ├── g) 调用 subagent（world-agent）同步伏笔
   └── h) 输出检查报告

质量原则：
- 每批5-10章，不可贪多
- 逐章质量检查
- 不合格则重写
- 合格后才生成下一批
```
