# 指标字典规范

> 来源：文档 §1。建立/填写指标字典时必读。

## 指标字典（定义 + PBI 建模意图）

指标字典是**唯一权威表**：业务口径与 PBI 建模意图写在**同一张表**，靠多几列解决，不另开「语义建模」大章。

- **改口径** → 先改业务列
- **进 PBI** → 读同一行的建模列 + schema 的 grain/键（落地见 references/04-pbi-pbip.md）
- **不是** MetricFlow YAML，也不是 analyses 注释凑目录

### 1.1 三个概念：粒度 · 维度 · 指标

| 概念 | 回答的问题 | 主责位置 |
| --- | --- | --- |
| 粒度 grain | 一行代表什么？ | **事实表 schema.yml**（主定义）；字典用 `grain_ref` 引用 |
| 维度 | 按什么切、看什么属性？ | **schema.yml / dim 模型**（可选整理「维度目录」）+ 字典 `allowed_dims` |
| 指标 | 在哪个 grain 上如何聚合、在 PBI 叫什么？ | **指标字典**（口径列 + 建模列） |

原则：粒度详写在事实表，字典只引用 `grain_ref`；维度写 dim_id；一个指标一个 grain；切片不改变事实 grain。

### 1.2 约束规则（实用）

1. 指标 grain = 事实表 grain
1. 维度经事实表外键连接
1. **默认时间维度写死**（支付日 vs 下单日）
1. 从细到粗可以；从粗当细不可以
1. 明细指标与汇总表指标分开登记
1. 进 PBI 的正式指标必须有 `pbi_measure`；改口径先改字典再改 DAX

### 1.3 字典字段规范（主表）

字段分两组：**A 业务口径**（业务签）+ **B PBI 建模**（AE 写满，再交给 AI/人落 PBI）。

#### A. 业务口径列

| 字段 | 必填 | 含义 | 示例 |
| --- | --- | --- | --- |
| metric_id | 是 | 稳定英文 id | gmv |
| name | 是 | 业务名 | GMV |
| definition | 是 | 业务一句话 | 已支付订单实付金额合计 |
| grain_ref | 是 | 依赖事实/agg 表 | fct_orders |
| expression | 是 | 聚合表达式（逻辑） | sum(pay_amount) |
| filters | 是 | 默认过滤 | status = 'paid' |
| default_time_dim | 是 | 默认时间角色 | date_pay |
| allowed_dims | 是 | 允许切片 dim_id | date_pay, customer, product, channel |
| forbidden_dims | 否 | 禁止/慎用 | date_order（非官方时间） |
| non_additive_note | 否 | 不可加总说明 | 跨渠道人数不可直接相加 |
| owner | 否 | 口径负责人 | AE 姓名 |
| status | 否 | draft / certified | certified |

#### B. PBI 建模列（与口径同一行）

| 字段 | 必填 | 含义 | 示例 |
| --- | --- | --- | --- |
| pbi_measure | 是 | PBI 度量显示名（Dataset 内唯一） | [GMV] |
| compute_layer | 是 | 主要在哪算：`dbt` / `pbi` / `dbt+pbi` | dbt+pbi |
| dax_hint | 建议 | DAX 草稿，与 expression+filters 同向 | CALCULATE(SUM(...), status="paid") |
| display_folder | 否 | 度量文件夹 | 销售核心 |
| analysis_sql | 是 | 验数路径（对接 §3） | analyses/metrics/gmv.sql |

| compute_layer | 何时用 |
| --- | --- |
| `dbt` | 结果已在表内，PBI 几乎只展示 |
| `dbt+pbi` | 原子列在 dbt，PBI 做 SUM 等简单聚合 |
| `pbi` | 比率、同比、上下文去重等，算在 DAX |

#### 特殊类型怎么填（对照上面字段）

| 类型 | 容易错 | 关键列（示例） |
| --- | --- | --- |
| 可加总金额/件数 | 挂错表、过滤没进度量 | `gmv`：grain=fct_orders，sum(pay_amount)，status=paid，layer=dbt+pbi |
| 去重人数 | 各渠道人数相加 | `paid_users`：dcount(user_id)，layer=pbi，note=不可跨维加总 |
| 比率/转化率 | 分子分母 grain/过滤不一致 | `pay_rate`：DIVIDE(分子,分母)，layer=pbi，note=不可对比率 SUM |
| 时点库存 | 多天库存被 SUM | `inventory_qty`：快照 grain，layer=pbi，note=跨日不可 SUM |
| 预聚合指标 | 当明细下钻 | `gmv_store_day`：grain=agg_sales_day（日×店），allowed=date/store |

### 1.4 示例行

| metric_id | name | grain_ref | expression | filters | default_time | allowed_dims | compute_layer | pbi_measure | note |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| gmv | GMV | fct_orders | sum(pay_amount) | status=paid | date_pay | date_pay, customer, product, channel | dbt+pbi | [GMV] | 官方时间=支付日 |
| item_qty | 销售件数 | fct_order_items | sum(qty) | — | date_pay | date_pay, product, customer | dbt+pbi | [Item Qty] | 勿只挂订单头维 |
| paid_users | 支付用户数 | fct_orders | dcount(user_id) | status=paid | date_pay | date_pay, channel | pbi | [Paid Users] | 跨维不可直接加总 |
| gmv_store_day | 店日 GMV | agg_sales_day | sum(gmv) | — | date | date, store | dbt+pbi | [Store Day GMV] | 不能下钻订单 |

### 1.5 指标 × 维度细表（可选，口径易吵时再加）

| metric_id | dim_id | 关系 | 备注 |
| --- | --- | --- | --- |
| gmv | date_pay | 默认 | 官方时间 |
| gmv | date_order | 可选/探索 | 报表需标注非官方 |
| gmv | customer | 允许 |  |
| conversion_rate | product | 禁止 | 分子分母 grain 风险 |
| inventory_qty | date | 慎用 | 时点，不可跨日 SUM |

多数场景：**字段规范 + 示例行**即可。
