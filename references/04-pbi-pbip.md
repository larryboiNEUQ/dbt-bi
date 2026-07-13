# PBI / PBIP 落地规范

> 来源：文档 §4。含 AI prompt 与 DAX 提示。

## PBI DAX / PBIP（交互实现）

字典（含建模列）+ schema 就绪后，**按字典行 1:1 落度量**；报表只消费认证 Dataset。

### 4.1 端到端流程
```plaintext
1. AE：逻辑模型 + schema.yml + 指标字典（口径列 + PBI 建模列）+ analyses
2. PR：业务签口径，DE 签可物化
3. DE：物化到约定 schema（如 marts）
4. 用 analyses SQL 对数（总数 / 抽样维度）
5. PBI：连 marts → 按 schema/维度目录建关系 → 按字典 pbi_measure/dax_hint 落 DAX
6. 发布 Dataset → 报表只绑数据集
7. exposure 补 PBI 链接；breaking 变更发说明

```

### 4.2 落地原则

1. PBI 只连 marts（或约定 schema），不连 stg/raw
1. 正式指标以字典为准，DAX 名 = `pbi_measure`
1. 一张认证 Dataset，报表不私建同名度量副本
1. 粒度变更 = breaking，先通知下游再合并
1. 每个正式看板有 exposure + 负责人
1. 发布前必须 SQL vs PBI 对数

### 4.3 按字典 + schema 生成 PBI 模型（给 AI）

字典与 schema 已填好时，**不要让 AI 重新发明口径**；把下面整段当 prompt 用（替换 `<>` 占位即可）。
```plaintext
按 DBT-BI skill 执行。根据本仓库已有的指标字典与 schema，生成/更新 Power BI / PBIP 语义模型（表/关系/隐藏列/度量）与 DAX，禁止改口径。

输入（只读）：
- 指标字典：<docs/metrics.md 或 metrics 表路径>
- schema / 维度目录：<models/**/schema.yml 路径>
- marts 连接信息：<库.schema 或已有 .pbix/.pbir 路径>
- 范围：仅 status=certified 的指标（或列出 metric_id 列表：<...>）

任务：
1. 进模表：收集范围内 grain_ref 与维度目录中的维表；只连 marts，不连 stg/raw。
2. 关系：按维度目录键建 维 1→* 事实；只保留 allowed_dims 需要的边；default_time_dim 标为默认日期关系；多时间角色（如支付日/下单日）分开，禁止混用。
3. 隐藏列：schema 中的代理键、ETL/中间列隐藏；业务属性与金额原子列保留。
4. 度量：每个指标一行 → pbi_measure 为名；优先用 dax_hint；必须落实 filters 与 compute_layer（dbt+pbi 用 SUM 类；pbi 用 DISTINCTCOUNT/DIVIDE/时间智能等）；display_folder 有则归组。
5. 约束：遵守 non_additive_note、forbidden_dims；agg grain 不可当成明细下钻；不得私建与字典冲突的同名度量。
6. 对数清单：输出 analysis_sql 列表及建议筛选（默认时间 + filters），供发版 SQL=卡片核对。

输出：
- 表清单 + 关系说明（键、基数、默认时间）
- 隐藏列清单
- 度量清单（metric_id / pbi_measure / 最终 DAX / folder）
- 与字典不一致或 NEEDS CLARIFICATION 的问题（有则停，勿猜）

规则：字典与 schema 冲突时以字典口径 + schema grain 为准并标出冲突；禁止扩大 scope 到未登记指标。

```

### 4.4 DAX 落点提示（对照字典 compute_layer）

| 类型 | 典型写法 | 字典侧 |
| --- | --- | --- |
| 可加总金额 | `SUM(fct[pay_amount])` | compute_layer=dbt+pbi；filters 一致 |
| 去重人数 | `DISTINCTCOUNT(fct[user_id])` | compute_layer=pbi；non_additive_note |
| 比率 | `DIVIDE([分子], [分母])` | compute_layer=pbi；分子分母 grain 对齐 |
| 同比/环比 | 时间智能 + 默认时间关系 | default_time_dim 写死 |

### 4.5 exposures.yml 示例
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
