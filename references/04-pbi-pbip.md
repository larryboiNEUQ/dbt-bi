# PBI / PBIP 落地规范

> 来源：文档 §4。含 AI prompt 与 DAX 提示。数据就绪含 **物化** 与 **逻辑模型 SQL** 两路径。

## PBI DAX / PBIP（交互实现）

字典（含建模列）+ schema 就绪后，**按字典行 1:1 落度量**；正式报表优先消费认证 Dataset。

### 端到端流程

```plaintext
1. AE：逻辑模型 + schema.yml + 指标字典 + analyses
2. PR：业务签口径；（若要物化）DE 签可物化
3. 数据就绪：
   Path A · 已可物化：DE 物化到 marts（等约定 schema）
   Path B · 尚未物化：用 dbt 逻辑模型 SQL 取数
           （compile / 预览 / ephemeral 编译 SQL；analyses 同）
4. analyses 对数
5. PBI/PBIP：Path A 连 marts 落 DAX；Path B 用逻辑模型 SQL 验数/试点，物化后切 A
6. 发布认证 Dataset（正式）
7. exposure + breaking 说明
```

**Path B**：逻辑未到可物化时不阻塞口径与验数；禁止 stg/raw 当官方数。

### 落地原则

1. **正式看板**：只连 marts（或约定 schema），不连 stg/raw  
2. **未物化**：dbt 逻辑模型编译 SQL 取数/验数；口径仍字典 + schema  
3. DAX 名 = `pbi_measure`；一张认证 Dataset  
4. 粒度变更 = breaking  
5. 发布前 SQL = PBI（Path B 的 SQL 来自逻辑模型编译）  

### 给 AI 的 prompt

```text
按 DBT-BI skill 执行。根据指标字典与 schema 生成/更新 PBIP 与 DAX，禁止改口径。

输入：
- 指标字典 / schema 路径
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

### exposures 示例

见团队文档或项目 `models/exposures.yml`；depends_on 使用逻辑模型 `ref`，物化后连接指向 marts。
