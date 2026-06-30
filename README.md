# RELOO

> **当前版本：RELLO V1.0.2**（2026-06-30）

> AI 驱动的多轮代码迭代自审工程框架 — 让 AI 按工程化流程系统性打磨代码质量，而非凭直觉改代码。

## 这是什么

RELOO指的是 **R**eview, **E**valuate, **L**oop, **O**ptimize, **O**rchestrator（审查-评估-循环-优化-编排器），这是一套面向 AI 编程助手（如 Trae IDE）的 **Skill 与 Rules 配置体系**。定义了 **Loop Engineering** 的迭代自审流程：让 AI 对代码进行 6 轮有序的审查—修复—验证循环，每轮聚焦一个质量维度，配合 5 个联动 Skill 协同工作，逐步消除逻辑错误、状态问题、交互缺陷和性能隐患。

**解决的问题**：AI 改代码时容易"凭感觉一把梭"——方向发散、遗漏边界、修复不验证。RELOO把代码质量加固变成一个有章可循的工程流程。

## 核心概念：Loop Engineering

```
审查现状 → 写提示词 → 迭代修复 → 规范复审 → 实测验证 → 根因分析 → 经验沉淀 → 下一轮
```

### 6 轮维度矩阵

| 轮次 | 维度 | 检查重点 |
|------|------|----------|
| R1 | 核心逻辑正确性 | 算法、公式来源、边界值覆盖 |
| R2 | 状态管理与持久化 | 数据跨会话/跨操作丢失、编辑态回退 |
| R3 | 交互一致性 | 事件冒泡、按钮重复触发、输入失焦 |
| R4 | UI 一致性与边界 | 视觉一致性、空状态、极端数据展示 |
| R5 | 性能与可访问性 | 局部更新 vs 整页重渲染、CSS 变量有效性 |
| R6 | 极端边界场景 | 跨年跨月、超大数据量、零值负值 |

### 终止条件

- 连续一轮未发现新问题
- 所有 6 个维度已扫描完毕

## 项目结构

```
skills/
├── loop-engineering/
│   └── SKILL.md               # 核心调度器：6 轮迭代框架 + 联动 Skill 矩阵
├── prompt-writing/
│   └── SKILL.md               # 提示词编写：5 层结构 prompt（角色-任务-规则-示例-异常）
├── coding-standards/
│   └── SKILL.md               # 代码规范：风格、命名、架构、性能、安全
├── self-introspection/
│   └── SKILL.md               # 自我反思：五问法根因分析 + Bug 模式沉淀
└── content-reviewing/
    └── SKILL.md               # 内容审查：准确性/逻辑性/专业性/安全性
```

## 5 个核心 Skill

### 1. loop-engineering（总调度器）
定义 6 轮迭代框架，编排联动 Skill 的调用顺序。每轮：审查 → 写提示词 → 修复 → 复审 → 验证 → 根因分析 → 经验沉淀。

### 2. prompt-writing（指令驱动）
每轮迭代开始前，设计 5 层结构 prompt（角色-任务-规则-示例-异常），将"AI 自由发挥"转化为"按预设指令执行"，避免修复方向发散。

### 3. coding-standards（规范复审）
修复后复查代码是否合规：函数不超过 20 行、注释解释"为什么"而非"做什么"、命名风格一致。

### 4. self-introspection（根因分析）
用五问法（5-Whys）追溯每个 Bug 的根本原因，并将新发现的 Bug 模式沉淀到 `project_memory.md` 的模式库中。

### 5. content-reviewing（质量审查）
审查修复报告的准确性、逻辑性、完整性和可读性，确保修复描述完整（症状→根因→修复→预防）。

## 联动关系

Loop Engineering 不是单一 skill，而是多 skill 协同。每轮迭代必须在指定步骤调用对应 skill：

| 步骤 | 联动 Skill | 目的 |
|------|-----------|------|
| [2] 写提示词 | prompt-writing | 设计 5 层结构 prompt 驱动本轮执行 |
| [4] 修复后复审 | coding-standards | 复查代码规范（行数、注释、命名） |
| [7] 每轮根因 | self-introspection | 五问法根因分析 |
| [8] 报告质量 | content-reviewing | 审查修复报告质量 |
| [Check] 经验沉淀 | self-introspection | 提炼 Bug 模式写入 project_memory |

## Rules（规则）

> 待补充：用户级规则（编码规范 + Loop Engineering 流程规则）后续将以 `rules/user_rules.md` 形式发布。

## 快速开始

1. 将 `skills/` 目录下的 5 个 Skill 配置到你的 AI 编程助手（如 Trae IDE）中
2. 在项目中创建 `project_memory.md` 用于积累 Bug 模式
3. 对目标代码说：**"执行 loop engineering"**，AI 将自动进入 6 轮迭代自审流程

## 适用场景

- 大型功能开发完成后的全面质量加固
- 代码能运行但存在已知边界 Bug 需要系统性排查
- 需要建立可持续的代码审查与经验沉淀机制
- 团队希望 AI 按工程化流程而非随意方式改代码

## 仓库

[https://github.com/kipozannaki/RELOO](https://github.com/kipozannaki/RELOO)

## 许可

MIT
