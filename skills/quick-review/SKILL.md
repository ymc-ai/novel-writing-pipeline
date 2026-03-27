---
name: quick-review
description: |
  快速检查流程。检查章节质量和一致性。
  用户说"检查章节"、"质量报告"时使用。
---

# 快速检查流程

## 输入

- 章节编号或章节内容
- 检查类型：full（全面）/ quick（快速）

## 流程

### Step 1: 加载章节

```
调用 subagent（world-agent）加载：
→ 章节内容
→ 世界状态
→ 伏笔状态
```

### Step 2: 质量检查

```
调用 subagent（quality-agent）：

检测项：
- 节奏模式
- 禁用词
- 字数
- 钩子
```

### Step 3: 一致性检查

```
调用 subagent（plot-agent）：

检测项：
- 世界一致性
- 伏笔合规
- 逻辑自洽
```

### Step 4: 汇总报告

## 输出格式

```json
{
  "chapter_id": 45,
  "quality": {
    "score": 8.0,
    "mode": "高潮期",
    "word_count": 3200,
    "forbidden_words": [],
    "has_hook": true
  },
  "validation": {
    "result": "pass",
    "issues": []
  },
  "recommendations": [
    "建议在第3章后回收伏笔thread_001"
  ]
}
```

## 检查级别

| 级别 | 检查项 | 时间 |
|------|--------|------|
| quick | 节奏+禁用词 | 1分钟 |
| full | 全面检查 | 5分钟 |
