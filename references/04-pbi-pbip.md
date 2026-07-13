# PBI / PBIP 落地规范

> 来源：文档 §4。总流程见 00-overview；本节为落地原则与 AI prompt。

## PBI DAX / PBIP（交互实现）

字典（含建模列）+ schema 就绪后，**按字典行 1:1 落度量**；正式报表优先消费认证 Dataset。总流程见 §0；本节只补 **落地原则** 与 **按字典生成模型**。

### 4.1 落地原则

1. **正式看板（Path A）**：PBI 只连 marts（或约定 schema），不连 stg/raw
1. **未物化（Path B）**：用 dbt 逻辑模型编译 SQL 取数/验数；口径仍以字典 + schema 为准；**禁止** stg/raw 当官方数；物化后切 Path A
1. 正式指标以字典为准，DAX 名 = `pbi_measure`
1. 一张认证 Dataset，报表不私建同名度量副本
1. 粒度变更 = breaking，先通知下游再合并
1. 每个正式看板有 exposure + 负责人
1. 发布前必须 SQL vs PBI 对数（Path B 时 SQL 来自逻辑模型编译结果）

### 4.2 按字典 + schema 生成 PBI 模型（给 AI）

字典与 schema 已填好时，**不要让 AI 重新发明口径**；把下面整段当 prompt 用（替换 `<>` 占位即可）。
```plaintext
按 DBT-BI skill 执行。根据本仓库已有的指标字典与 schema，生成/更新 Power BI / PBIP 语义模型（表/关系/隐藏列/度量）与 DAX，禁止改口径。

输入（只读）：
- 指标字典：<docs/metrics.md 或 metrics 表路径>
- schema.yml（维度目录可选，从 schema 整理）：<models/**/schema.yml 路径>
- 数据就绪方式：
  - Path A 物化：marts 连接 <库.schema 或 .pbip 路径>
  - Path B 未物化：dbt 项目路径；用逻辑模型 compile SQL 取数（勿连 stg/raw）
- 范围：仅 status=certified 的指标（或列出 metric_id 列表：<...>）

任务：
1. 进模表：收集 grain_ref 与 schema 中的 dim 表。
   Path A：只连 marts；Path B：标明基于哪些逻辑模型 ref，并给出 compile 取数方式。
2. 关系：维 1→* 事实；allowed_dims；default_time_dim 为默认日期关系；多时间角色分开。
3. 隐藏列：代理键/ETL 列隐藏。
4. 度量：pbi_measure + dax_hint；落实 filters 与 compute_layer。
5. 约束：non_additive_note、forbidden_dims；agg 不可当明细下钻。
6. 对数清单：analysis_sql + 默认时间/filters；注明 SQL 来自 marts 还是逻辑模型编译。

输出：表/关系/隐藏列/度量 DAX/对数清单/数据就绪路径（A 或 B）；NEEDS CLARIFICATION 则停。
```

### 4.3 DAX 落点提示（对照字典 compute_layer）

| 类型 | 典型写法 | 字典侧 |
| --- | --- | --- |
| 可加总金额 | `SUM(fct[pay_amount])` | compute_layer=dbt+pbi；filters 一致 |
| 去重人数 | `DISTINCTCOUNT(fct[user_id])` | compute_layer=pbi；non_additive_note |
| 比率 | `DIVIDE([分子], [分母])` | compute_layer=pbi；分子分母 grain 对齐 |
| 同比/环比 | 时间智能 + 默认时间关系 | default_time_dim 写死 |

### 4.4 exposures.yml 示例
```plaintext
exposures:
  - name: pbi_sales_dashboard
    type: dashboard
    owner:
      name: analytics_team
      email: ae@company.com
    depends_on:
      - ref('fct_orders')
      - ref('dim_customer')
      - ref('dim_date')
    url: "https://app.powerbi.com/..."
    description: 经营分析看板；指标以 docs/metrics 为准

```
