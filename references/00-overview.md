# 工作流总览与落盘

> 来源：团队口径对齐文档 §0（不含 skill 安装说明）。

## 工作流总览

口径对齐不是「写一堆 DAX」或「写一堆 SQL」的二选一，而是同一套定义在四层工件上闭环：
```plaintext
① 指标字典          → 业务口径 + PBI 建模列（一个表写清）
        ↓
② schema.yml        → 表粒度、主键、字段、维度键
        ↓
③ analyses SQL      → 可运行验收（对数基准）
        ↓
④ PBI DAX           → 按字典 1:1 落度量 → 认证 Dataset → 报表
```

| 角色 | 负责 |
| --- | --- |
| **AE（主责）** | 业务分析、逻辑模型（dim/fct）、**指标字典（含 PBI 建模列）**、schema.yml、analyses 验数、参与/主导 DAX 落地与对数 |
| DE | 物化（就绪时）、调度、权限、性能；未物化不阻塞 AE 用逻辑模型 SQL 验数 |
| 业务 | 确认官方口径 |

**角色定位**：AE 不是「扶着别人做 PBI」的旁观者。PBI 能做对，靠的是 **字典 + schema 已经写满**（grain、可切维度、度量名、dax_hint）；DAX 只是把字典落到筛选上下文。谁写最终 DAX 可以协作，**字典与 schema 必须由 AE 先交付**。

口诀：

> **定义与建模意图都放字典，形状放 schema，验收放 analyses，交互放 PBI。**

### 仓库落盘建议

四件套在 dbt 仓库中的对应位置（总览一览）：
```plaintext
docs/metrics.md          # 或 metrics.csv / 飞书多维表同步  → 指标字典
models/.../schema.yml    # grain + 字段 + tests
analyses/metrics/
  gmv.sql
  paid_users.sql
  ...
models/exposures.yml     # 正式 PBI 看板依赖

```
