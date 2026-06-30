---
name: loop-engineering
description: Iterative self-review engineering loop for code quality. Invoke when user asks for 'loop engineering', 'iterative debugging', 'self-review iteration', or wants to continuously improve code by finding and fixing bugs across multiple dimensions. Must coordinate with coding-standards, self-introspection, content-reviewing, and prompt-writing skills per the 联动技能矩阵.
---

# Loop Engineering — 自动多轮迭代

> 核心循环：**审查 → 写提示词 → 迭代 → 下一轮审查**（单轮内流程，**轮内逻辑不可变**）
> 关键改进：**轮间自动推进**，R1-R6 之间无需用户手动确认；任一轮收敛后自动进入下一轮
> 默认范式：**自动**（除非用户指令明确要求"先停一下"等）

## 强制约束

1. **每轮先写提示词再执行**：每轮迭代开始时，必须先用 `prompt-writing` skill 设计本轮 5 层结构 prompt（角色-任务-规则-示例-异常），然后**严格按 prompt** 执行后续修复
2. **多 Skill 必调**：每轮迭代必须按顺序调用以下联动 skill（通过 Skill 工具 invoke），不能跳过：
   - [2] 写本轮提示词 → `prompt-writing`：设计 5 层结构 prompt 驱动本轮执行
   - [4] 修复后 → `coding-standards`：复查新代码（中文注释、≤20 行、命名一致）
   - [7] 每轮结束 → `self-introspection`：用五问法（5-Whys）做根因分析
   - [8] 报告输出后 → `content-reviewing`：审查修复报告的准确性/逻辑性/可读性
   - [Check] 经验沉淀 → `self-introspection`：提炼 Bug 模式，写入 project_memory.md
3. **维度有序**：6 轮顺序固定——核心逻辑 → 状态管理 → 交互一致性 → UI 一致性 → 性能 → 极端场景
4. **验证锚点**：修复含公式/算法的代码时，必须先列 3 组以上具体数值验证公式方向正确
5. **SSOT 强制**：版本号、常量、配置等元数据只声明 1 次，其他位置必须引用，不允许硬编码副本
6. **经验沉淀**：每轮发现的 Bug 模式必须更新到对应项目的 `project_memory.md`，否则不算完成
7. **终止条件（按优先级）**：
   - **P0 主动信号**：用户明确说"停"、"退出"、"跳过 R_N" → 立即终止
   - **P1 6 维度扫描完毕**：R1-R6 全部跑完 → 终止
   - **P2 连续无新问题**：连续 1 轮未发现新问题 → 终止
   - **P3 修复饱和**：本轮发现的修复数 = 0 且本轮报告无新建议 → 进入下一轮
8. **轮间自动推进**：R1-R6 顺序执行，**每轮 [Check] 经验沉淀后自动进入下一轮 [1] 审查**，**不询问用户**；仅在 P0 主动信号时停下
9. **用户控制权（始终保留）**：
   - "停" / "暂停" → 立即停
   - "跳到 R_N" / "只跑 R_N" → 直接跳
   - "回滚 R_N" / "重跑 R_N" → 回到 R_N
   - "继续" → 继续当前轮剩余步骤

## 反例（不允许的做法）

- 不调用 `prompt-writing` 直接开始改代码（失去指令驱动，修复方向发散）
- 只调用 `loop-engineering` 一个 skill 走完全流程
- 修复完代码不验证，直接进入下一轮
- 修复报告只写"修复了 X"，不写症状→根因→修复→预防
- Bug 模式只在本轮使用，不写入 project_memory
- 6 轮里只跑 1-2 轮就声称"已修复"
- 轮间等待用户确认（**默认范式为自动**，仅在 P0 主动信号时停下）

## 联动 skill 详细职责

| 联动 skill | 触发步骤 | 必做动作 |
|------------|----------|----------|
| `prompt-writing` | [2] 写本轮提示词 | 输出 5 层结构 prompt（角色-任务-规则-示例-异常），然后严格按 prompt 执行 |
| `coding-standards` | [4] 修复后 | 复查：函数 ≤20 行、注释解释"为什么"、命名风格一致 |
| `self-introspection` | [7] 每轮结束 | 对本轮每个修复做五问法：现象→为什么→为什么→根因→预防 |
| `self-introspection` | [Check] 经验沉淀 | 提炼新 Bug 模式，更新 project_memory.md 的"Bug 模式库" |
| `content-reviewing` | [8] 报告输出后 | 审查修复报告：准确性/逻辑性/完整性/可读性 |

## 每轮执行模板

```
[Plan] 制定本轮维度（从 6 个维度中选 1）
[1] Read/Grep 审查现状
[2] → invoke skill: prompt-writing（写本轮 5 层结构 prompt）
[3] 按 prompt 迭代执行 Edit
[4] → invoke skill: coding-standards（修复后规范复审）
[5] 浏览器实测（web 项目；小程序跳过）
[6] 控制台错误检查
[7] → invoke skill: self-introspection（五问法根因）
[8] → invoke skill: content-reviewing（报告质量审查）
[Check] → invoke skill: self-introspection（更新 project_memory.md）
[收敛判断] 是否进入下一轮？
  ├─ P0 主动信号（用户说停/跳）→ 终止
  ├─ P1 6 维度已扫描完毕 → 终止
  ├─ P2 连续无新问题 → 终止
  └─ 其他 → 自动进入 [下一轮] → 回到 [1] 审查
```

## 失败案例参考

- 2026-06-25 CoachEngine 6 轮 loop engineering：6 轮只调用了 1 个 skill（loop-engineering），未在 [2] 用 prompt-writing 驱动、未在 [4][7][8] 调用联动 skill，导致"流程缺陷本身没被发现"，由用户外部发现并指出。**本规则即为对此失败案例的修复**。
- 2026-06-30 默认范式误设：流程假设"用户确认驱动"导致频繁打断 → 改为"轮间自动推进"，仅保留 P0 主动信号为停止条件。
