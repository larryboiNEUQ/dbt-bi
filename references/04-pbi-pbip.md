# PBI / PBIP 落地规范

> 来源：文档 §4 + 团队约定。报表层 **独立 PBIP 仓库**；dbt 仓用 exposures 链接。

## PBI DAX / PBIP（交互实现）

字典（含建模列）+ schema 就绪后，在 **独立 PBIP 仓** 按字典 1:1 落度量；正式报表消费认证 Dataset。总流程见 §0 / `00-overview`。

### 仓库边界

| 仓库 | 放什么 | 不放什么 |
| --- | --- | --- |
| **dbt 仓** | 逻辑模型、schema.yml、指标字典、analyses、**exposures.yml** | `.pbip` 工程、报表页、DAX 终稿仓库 |
| **PBIP 仓**（独立） | 语义模型、关系、DAX、报表；连 marts 或约定 schema | 业务口径权威（以 dbt 字典为准） |

dbt → 报表层用 **exposures** 双向可追溯：既挂 **最终看板**，也挂 **PBIP 源码仓**。

### 4.1 落地原则

1. **PBIP 独立仓**（报表层），与 dbt 仓分离  
2. **正式看板（Path A）**：PBIP 只连 marts（或约定 schema），不连 stg/raw  
3. **未物化（Path B）**：用 dbt 逻辑模型编译 SQL 取数/验数；口径仍字典 + schema；物化后切 Path A  
4. DAX 名 = `pbi_measure`；一张认证 Dataset，报表不私建同名度量副本  
5. 每个正式看板：dbt `exposures.yml` 必填 **看板 url + pbip 仓链接**  
6. 粒度变更 = breaking；发布前 SQL = PBI 对数  

### 4.2 按字典 + schema 生成 PBI 模型（给 AI）

```text
按 DBT-BI skill 执行。字典与 schema 在 dbt 仓已填好；在独立 PBIP 仓生成/更新语义模型与 DAX，禁止改口径。

输入（只读）：
- dbt 仓：指标字典、schema.yml、analyses
- PBIP 独立仓：<git URL 或本地路径>
- 数据就绪：Path A marts <库.schema> 或 Path B 逻辑模型 compile SQL
- 范围：certified 或 metric_id 列表

任务：
1. 在 PBIP 仓：进模表/关系/隐藏列/度量（pbi_measure、dax_hint、filters、compute_layer）
2. 在 dbt 仓：写/更新 exposures.yml
   - url = 最终看板（Service）链接
   - meta.pbip_repo_url = PBIP 仓库 URL
   - meta.pbip_path / meta.dataset_url 可选
   - depends_on = 相关 dbt ref
3. 对数清单：analysis_sql；注明 SQL 来自 marts 还是逻辑模型编译

输出：PBIP 变更说明 + exposures 草稿；NEEDS CLARIFICATION 则停。
```

### 4.3 DAX 落点提示

| 类型 | 典型写法 | 字典侧 |
| --- | --- | --- |
| 可加总金额 | `SUM(fct[pay_amount])` | compute_layer=dbt+pbi；filters 一致 |
| 去重人数 | `DISTINCTCOUNT(...)` | compute_layer=pbi；non_additive_note |
| 比率 | `DIVIDE(...)` | compute_layer=pbi |
| 同比/环比 | 时间智能 | default_time_dim 写死 |

### 4.4 exposures.yml 示例（dbt 仓）

正式看板 **同时** 登记 Service 链接与 PBIP 仓链接：

```yaml
# models/exposures.yml  （位于 dbt 仓库，不在 PBIP 仓）
version: 2

exposures:
  - name: pbi_sales_dashboard
    label: 经营分析看板
    type: dashboard
    maturity: high
    url: "https://app.powerbi.com/groups/.../reports/..."   # 最终看板
    description: >
      经营分析看板；指标以 docs/metrics 为准。
      语义模型源码见 meta.pbip_repo_url。
    depends_on:
      - ref('fct_orders')
      - ref('dim_customer')
      - ref('dim_date')
    owner:
      name: analytics_team
      email: ae@company.com
    meta:
      pbip_repo_url: "https://github.com/<org>/<pricing-pbip>"  # PBIP 独立仓（必填）
      pbip_path: "Sales.SemanticModel"                           # 仓内路径（可选）
      dataset_url: "https://app.powerbi.com/.../datasets/..."  # 认证 Dataset（可选）
```

规则：

- **MUST** `url` = 最终看板（或 Service 入口）  
- **MUST** `meta.pbip_repo_url` = PBIP **独立仓库** URL  
- **MUST** `depends_on` 覆盖看板实际依赖的 marts/逻辑模型  
- 改口径仍只改 dbt 字典；改 DAX/布局在 PBIP 仓  
