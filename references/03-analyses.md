# analyses 验数规范

> 来源：文档 §3。

## analyses（验数 SQL）

analyses 是**可运行验收**，不是指标目录。业务扫口径看字典；开发改口径必须先改字典，再改 analyses 与 DAX。

### 3.1 用途

- 每个 **核心指标** 至少 1 条标准验收 SQL
- 命名与 `metric_id` 对齐：`analyses/metrics/{metric_id}.sql`
- 注释写清：对应 PBI 度量名、默认时间、过滤条件
- **验收标准**：同一业务日、同一过滤下，<text bgcolor="yellow">SQL 结果 = PBI 卡片（通过PBI MCP获取）</text>

### 3.2 模板
```plaintext
-- analyses/metrics/gmv.sql
-- metric_id: gmv
-- pbi_measure: [GMV]
-- default_time: pay_date（date_pay）
-- filters: status = 'paid'
-- 用法：改日期后与 PBI 同筛选对数

select
    sum(pay_amount) as gmv
from {{ ref('fct_orders') }}
where pay_date = current_date - 1
  and status = 'paid';
```

去重人数示例：
```plaintext
-- analyses/metrics/paid_users.sql
select
    count(distinct user_id) as paid_users
from {{ ref('fct_orders') }}
where pay_date = current_date - 1
  and status = 'paid';
```

### 3.3 运行方式
```plaintext
# 编译查看 SQL
dbt compile --select analysis:gmv

# 仓库连到目标库后跑 analyses 产物，或用对数窗口执行编译后 SQL
```

---
