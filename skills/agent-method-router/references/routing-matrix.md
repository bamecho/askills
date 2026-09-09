# Routing Matrix

任务特征到方法论的映射规则。帮助快速识别任务类型并选择适用方法。

## 任务分类维度

### 1. 按工作类型

| 任务类型 | 典型表述 | 主要方法 | 次要方法 | 原因 |
|---------|---------|---------|---------|------|
| **问题诊断** | "修复 X"、"为什么 Y 不工作"、"调试 Z" | First Principles | Critical Thinking | 先确认问题真实存在和根因 |
| **功能开发** | "实现 X 功能"、"添加 Y" | Occam's Razor | Adversarial Review | 从最简版本开始，上线前审查 |
| **架构设计** | "设计 X 系统"、"重构 Y 模块" | High Cohesion Low Coupling | Occam's Razor | 清晰边界优先，避免过度设计 |
| **质量审查** | "检查代码"、"审查设计"、"验证方案" | Adversarial Review | Critical Thinking | 独立视角找问题 |
| **效果验证** | "X 有用吗"、"是否需要 Y"、"优化有效吗" | Ablation Experiment | Critical Thinking | 对比实验证明因果 |
| **需求分析** | "如何实现 X"、"需要做什么" | First Principles | List Uncertainties | 理清真实需求和约束 |
| **研究探索** | "调研 X"、"分析 Y 可行性" | List Uncertainties | Independent Thinking | 显式标注已知/未知 |

### 2. 按信息完整度

| 信息状态 | 信号 | 必选方法 | 说明 |
|---------|------|---------|------|
| **模糊不清** | "有点慢"、"不太对"、"可能是 X" | First Principles | 先明确问题定义 |
| **缺少上下文** | 没有复现步骤、无基线数据 | First Principles + List Uncertainties | 列出需要补充的信息 |
| **假设过多** | "应该是 X 导致的" | Critical Thinking | 检查推理链和证据 |
| **信息充足** | 有日志、错误信息、复现步骤 | 跳过 First Principles | 直接进入解决阶段 |

### 3. 按风险等级

| 风险等级 | 场景 | 必选方法 | 可选方法 |
|---------|------|---------|---------|
| **高风险** | 生产环境、安全、数据、金融 | Adversarial Review + Critical Thinking | List Uncertainties |
| **中风险** | 功能变更、API 修改、重构 | Adversarial Review 或 Critical Thinking | - |
| **低风险** | 内部工具、实验性功能、文档 | Occam's Razor | - |

### 4. 按复杂度

| 复杂度 | 特征 | 推荐方法 | 原因 |
|--------|------|---------|------|
| **高复杂** | 多模块、多依赖、长链路 | High Cohesion Low Coupling → Ablation Experiment | 先理清边界，再逐步验证 |
| **中复杂** | 单模块但逻辑复杂 | List Uncertainties + Critical Thinking | 显式推理过程 |
| **低复杂** | 明确输入输出、单一职责 | Occam's Razor | 快速实现 |

## 特殊场景路由

### Multi-Agent 协作

**触发信号：**
- 明确说"多个 agent"
- 需要不同视角（如：实施 + 审查）
- 防止群体思维

**方法组合：**
```
Independent Thinking (各自独立分析)
    ↓
汇总结果
    ↓
Adversarial Review (交叉审查)
    ↓
Critical Thinking (基于证据达成共识)
```

### 优化类任务

**触发信号：**
- "提升性能"、"减少延迟"、"降低成本"
- "优化"、"改进"

**方法组合：**
```
First Principles (建立基线、识别瓶颈)
    ↓
Occam's Razor (最小改动优化)
    ↓
Ablation Experiment (验证每个优化的实际效果)
```

### 遗留系统处理

**触发信号：**
- "不知道为什么这样写"
- "不敢动"、"复杂历史"

**方法组合：**
```
First Principles (理解当前行为和约束)
    ↓
Ablation Experiment (逐步移除/修改，观察影响)
    ↓
High Cohesion Low Coupling (重构时建立清晰边界)
```

### 快速验证 Prototype

**触发信号：**
- "试试看"、"快速验证"
- "POC"、"demo"

**方法组合：**
```
Occam's Razor (最简实现)
    ↓
First Principles (验证是否解决核心问题)
    ↓
(如果成功) List Uncertainties (标注生产化前需要解决的问题)
```

## 路由决策树

```
用户任务输入
    ↓
问题定义清晰吗？
    ├─ 否 → First Principles
    └─ 是 ↓
        ↓
是新开发还是修改现有？
    ├─ 新开发 → Occam's Razor
    └─ 修改/调试 ↓
        ↓
问题复现了吗/根因明确吗？
    ├─ 否 → First Principles
    └─ 是 ↓
        ↓
是否高风险/关键决策？
    ├─ 是 → Adversarial Review + Critical Thinking
    └─ 否 ↓
        ↓
需要验证某个组件的必要性吗？
    ├─ 是 → Ablation Experiment
    └─ 否 ↓
        ↓
是否涉及多模块/架构变更？
    ├─ 是 → High Cohesion Low Coupling
    └─ 否 → 直接执行
```

## 自动路由触发词

基于用户输入中的关键词自动推荐方法：

| 关键词 | 推荐方法 | 优先级 |
|--------|---------|--------|
| 修复、bug、错误、不工作 | First Principles | P0 |
| 为什么、原因、导致 | First Principles + Critical Thinking | P0 |
| 实现、添加、新功能 | Occam's Razor | P0 |
| 设计、架构、重构 | High Cohesion Low Coupling | P0 |
| 审查、检查、验证 | Adversarial Review | P0 |
| 有用吗、需要吗、是否必要 | Ablation Experiment | P0 |
| 不确定、可能、应该 | List Uncertainties | P1 |
| 多个 agent、协作、团队 | Independent Thinking | P0 |
| 优化、性能、改进 | Ablation Experiment + First Principles | P0 |
| 安全、生产、关键 | Adversarial Review + Critical Thinking | P0 |

## 方法论互斥性检查

某些方法不应同时作为主要方法（可以是主次关系）：

- ❌ Occam's Razor + High Cohesion Low Coupling（矛盾：简化 vs 完整架构）
  - ✅ 应该：先 Occam's Razor（快速验证）→ 后 High Cohesion Low Coupling（生产化重构）

- ❌ Independent Thinking + First Principles（重复：都是"先独立判断"）
  - ✅ 应该：First Principles 用于问题定义，Independent Thinking 用于方案评估

## 输出格式模板

```markdown
## 任务分析
[1-2 句话总结任务类型和关键特征]

## 选择的方法论
1. **[方法名]** (主要)
   - 原因：[为什么适用]
   - 应用：[具体怎么做]

2. **[方法名]** (次要)
   - 原因：[为什么适用]
   - 应用：[具体怎么做]

## 工作流程
1. [应用方法 1 的步骤]
2. [应用方法 2 的步骤]
3. [验证标准]

## 需要明确的点
- [列出任何不确定性或需要用户确认的假设]
```

## 示例映射

### 示例 1: "登录页面加载慢"

```
任务特征：
- 类型：问题诊断 + 性能优化
- 信息：模糊（"慢"无具体数据）
- 风险：中（影响用户体验）

路由结果：
1. First Principles（主）：
   - 建立基线：慢多少？哪个环节慢？
   - 复现：是否稳定复现？特定条件？
   
2. Critical Thinking（次）：
   - 验证：修改后是否真的变快？
   - 证据：性能指标改善的数据
```

### 示例 2: "设计权限系统"

```
任务特征：
- 类型：架构设计
- 风险：高（安全敏感）
- 复杂度：中高

路由结果：
1. Occam's Razor（主）：
   - 先实现基础 RBAC（角色-权限）
   - 不要过早设计 ABAC、动态权限等
   
2. High Cohesion Low Coupling（次）：
   - 权限检查、角色管理、用户分配 分离
   
3. Adversarial Review（必需）：
   - 独立审查：绕过权限的可能？边界条件？
```

### 示例 3: "这段缓存代码是否必要？"

```
任务特征：
- 类型：效果验证
- 明确目标：判断必要性

路由结果：
1. Ablation Experiment（主）：
   - 测量：有缓存时的响应时间、命中率
   - 移除：删除缓存，重新测量
   - 对比：量化影响
   
2. Critical Thinking（次）：
   - 检查：缓存一致性策略是否合理？
   - 证据：缓存带来的收益 vs 复杂度成本
```

## 路由器自我检查

在输出路由结果前，验证：

- [ ] 是否选择了 1-3 个方法（不要全选）
- [ ] 主要方法直接对应任务类型
- [ ] 如果选择 First Principles，是否真的存在问题定义不清？
- [ ] 如果选择 Adversarial Review，是否是高风险或需要独立验证的场景？
- [ ] 方法之间是否有清晰的先后顺序或互补关系？
- [ ] 是否列出了应用每个方法的具体动作？
