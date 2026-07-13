# PBI / PBIP 落地规范

> 来源：文档 §4。总流程见 00-overview / SKILL 总览；本节不重复端到端编号流程。

## PBI DAX / PBIP（交互实现）

字典（含建模列）+ schema 就绪后，**按字典行 1:1 落度量**；正式报表优先消费认证 Dataset。

### 落地原则

1. **正式看板（Path A）**：只连 marts（或约定 schema），不连 stg/raw  
2. **未物化（Path B）**：dbt 逻辑模型编译 SQL 取数/验数；口径仍字典 + schema；禁止 stg/raw 当官方数；物化后切 Path A  
3. DAX 名 = `pbi_measure`；一张认证 Dataset  
4. 粒度变更 = breaking  
5. 发布前 SQL = PBI（Path B 的 SQL 来自逻辑模型编译）  

### 给 AI 的 prompt（文档 §4.2）

```text
按 DBT-BI skill 执行。根据指标字典与 schema 生成/更新 PBIP 与 DAX，禁止改口径。

输入：
- 指标字典 / schema.yml（维度目录可选，从 schema 整理）
- 数据就绪：Path A marts <...> 或 Path B dbt 逻辑模型 SQL（未物化）
- 范围：certified 或 metric_id 列表

任务：表/关系/隐藏/度量/对数；注明 SQL 来自 marts 还是逻辑模型编译；NEEDS CLARIFICATION 则停。
```

### DAX 落点提示

| 类型 | 典型写法 | 字典侧 |
| --- | --- | --- |
| 可加总金额 | SUM(...) | compute_layer=dbt+pbi |
| 去重人数 | DISTINCTCOUNT(...) | compute_layer=pbi；non_additive_note |
| 比率 | DIVIDE(...) | compute_layer=pbi |
| 同比/环比 | 时间智能 | default_time_dim 写死 |

### exposures

见项目 `models/exposures.yml`；depends_on 使用逻辑模型 `ref`，正式连接指向 marts。
