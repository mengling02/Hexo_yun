---
title: "SMSR: Certified Defence Against Runtime Memory Poisoning in Persistent LLM Agent Systems"
date: 2026-06-12
tags: ["AI安全"]
---

# SMSR: Certified Defence Against Runtime Memory Poisoning in Persistent LLM Agent Systems

## 核心信息
- **论文ID**：2606.12703
- **作者**：Tarun Sharma
- **机构**：[从作者推断或查看论文]
- **发布时间**：2026-06-10
- **会议/期刊**：预印本 (cs.CR, cs.AI, cs.LG)
- **链接**：[论文](https://arxiv.org/abs/2606.12703) | [PDF](https://arxiv.org/pdf/2606.12703v1)
- **DOI**：10.48550/arXiv.2606.12703

## 摘要
RAG agents increasingly run with persistent memory that accumulates across user sessions. This creates a new attack surface: an adversary interacting only through normal channels can inject crafted memories that steer the agent responses for future users. We call this Multi-Session Memory Poisoning (MSMP). We present Signed Memory with Smoothed Retrieval ([[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]]), the first defence with a certified robustness bound. Component 1 adds HMAC-SHA256 provenance at write time. Component 2 applies randomised memory ablation with verdict-based majority voting. Across 15 enterprise scenarios, Component 1 cuts attack success from 93-100% to 0%. For authenticated adversary with single injection, Component 2 holds success to 8.0%.

## 摘要翻译
检索增强生成（RAG）智能体越来越多地运行在跨用户会话的持久化记忆之上。这创造了一个新的攻击面：对手仅通过正常渠道交互即可注入精心构造的记忆，一旦被检索，就会引导智能体对后续用户的回复，而无需触碰模型权重或代码。我们称之为**多会话记忆投毒（MSMP）**。本文提出 **签名记忆+平滑检索（[[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]]）**，首个对该场景具有认证鲁棒性界的防御方案。组件1在写入时添加 HMAC-SHA256 来源认证，阻断未签名注入；组件2在查询时使用随机化记忆消融+基于裁决的多数投票，限制已认证对手的影响。证明了无来源认证的检索时过滤器无法对抗自适应注入，为组件2推导了超几何认证，并形式化了"一致性少数效应"。

## 研究问题
持久化记忆 RAG 智能体面临多会话记忆投毒攻击，对手通过正常交互注入恶意记忆，影响后续用户。现有防御（RobustRAG、ReliabilityRAG）假设静态知识库，启发式过滤器可被流畅文本绕过。**如何为运行时记忆投毒提供可认证的防御？**

## 方法概述

### 核心方法

1. **组件1：签名记忆（Signed Memory）**
   - 写入时添加 HMAC-SHA256 来源签名
   - 阻断所有未签名的记忆注入
   - 攻击成功率从 93-100% 降至 0%

2. **组件2：平滑检索（Smoothed Retrieval）**
   - 查询时随机消融部分记忆
   - 使用基于裁决（verdict）的多数投票代替字符串匹配投票
   - 推导超几何分布认证界
   - 认证对手单次注入攻击成功率限制在 8.0%

3. **理论贡献**
   - 证明无来源认证的检索过滤器无法对抗自适应注入
   - 形式化"一致性少数效应"：一致的对抗答案在字符串投票中以少数胜出，但裁决投票消除此效应

### 数学公式
- 超几何认证：给定 $n$ 条记忆中 $k$ 条为对抗注入，消融 $m$ 条后检索到至少一条对抗记忆的概率
  $$P(\text{attack succeeds}) \leq 1 - \frac{\binom{n-k}{m}}{\binom{n}{m}}$$
- 裁决投票的认证界确保攻击成功率低于理论最坏情况

### 关键创新

1. **首个认证防御** - 首次为运行时记忆投毒提供可证明的鲁棒性界
2. **双重防御架构** - 签名认证（写入时）+ 随机消融（查询时），缺一不可
3. **一致性少数效应的形式化** - 揭示字符串投票的系统性缺陷并提出裁决投票替代

## 实验结果

### 数据集
- **企业场景**：15 个企业场景（3,150 次重复试验）
- **端到端攻击**：150 次试验（智能体自身写入毒药）

### 实验设置
- **攻击类型**：未签名注入、已认证单次注入、端到端查询攻击
- **评估指标**：攻击成功率（ASR）、干净查询效用

### 主要结果
| 攻击类型 | 无防御 | +Component 1 | +Both |
|----------|--------|-------------|-------|
| 未签名注入 | 93-100% | 0% | 0% |
| 已认证单次注入 | - | - | 8.0% (95% CI [5.8, 10.9]) |
| 端到端查询攻击 | 65.3% | - | 5.3% |
| 干净查询效用 | - | 90% | 85% |

## 深度分析

### 研究价值
- **理论贡献**：证明无来源认证的防御不可行，为 RAG 安全奠定理论基础
- **实际应用**：直接适用于企业 RAG 部署的安全加固
- **领域影响**：开辟"运行时记忆安全"新研究方向

### 优势
- 首个可认证的防御方案，超越启发式方法
- 双组件设计实用性强，可分阶段部署
- 理论保证与实验验证并重

### 局限性
- 已认证对手仍可达 8% 攻击成功率
- 干净查询效用从 90% 降至 85%（组合使用时）
- 仅评估单次注入场景，多次注入的安全性待验证
- HMAC 签名要求修改智能体架构，部署成本

### 适用场景
- 企业 RAG 智能体的安全加固
- 持久化记忆系统的安全审计
- 多用户共享智能体的安全设计

## 与相关论文对比

### [[RobustRAG]] - 静态知识库防御
- **差异**：RobustRAG 假设固定知识库，[[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]] 处理动态运行时记忆
- **改进**：[[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]] 首次提供认证鲁棒性界，不依赖静态假设

### [[ReliabilityRAG]] - RAG 可靠性
- **差异**：ReliabilityRAG 关注检索可靠性，未考虑恶意注入
- **改进**：[[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]] 明确建模对抗性注入并提供理论保证

### [[SMSR 之前的工作]] - 启发式过滤
- **差异**：启发式过滤器可被流畅文本绕过
- **改进**：[[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]] 的认证界不受对抗文本质量影响

## 技术路线定位

本文属于 **RAG 安全** 技术路线，主要关注 **运行时记忆投毒防御** 子方向，是首个提供认证鲁棒性界的工作。

## 未来工作建议

1. 扩展到多次注入场景的认证界推导
2. 探索更高效的签名方案（降低部署成本）
3. 结合 LLM 内在安全对齐与 [[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]] 外部防御
4. 在开源 RAG 框架（LlamaIndex、LangChain）中集成 [[Obsidian/Papers/AI安全/2026-06-12/SMSR_Certified_Defence_Against_Runtime_Memory_Poisoning_in_Persistent_LLM_Agent_Systems.md|SMSR]]

## 我的综合评价

### 价值评分
- **总体评分**：8.5/10
- **分项评分**：
  - 创新性：9/10
  - 技术质量：8/10
  - 实验充分性：8/10
  - 写作质量：8/10
  - 实用性：9/10

### 突出亮点
- "一致性少数效应"的形式化是重要理论贡献
- 双组件设计优雅且实用
- 端到端攻击评估贴近实际部署场景

### 重点关注
- 8% 的认证界是否足够低？对高风险场景可能需要更强保证
- HMAC 签名的部署开销

### 可借鉴点
- 签名+平滑的双重防御范式可推广到其他动态数据安全场景
- 裁决投票替代字符串投票的思路
- 超几何认证界的推导方法

### 批判性思考
- 单作者论文，实验规模是否足够？
- 仅评估企业场景，未覆盖个人助手等场景
- 对"智能体自身写入毒药"的端到端攻击防御仍有限

## 我的笔记

[用户阅读后手动补充的内容]

## 相关论文
- [[RobustRAG]] - 静态知识库防御
- [[ReliabilityRAG]] - RAG 可靠性

## 外部资源
- [论文链接](https://arxiv.org/abs/2606.12703)
- [PDF](https://arxiv.org/pdf/2606.12703v1)
- [HTML](https://arxiv.org/html/2606.12703v1)