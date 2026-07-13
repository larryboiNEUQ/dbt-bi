# 建议写入 dbt / 分析仓库根 AGENTS.md

## DBT-BI

- 口径对齐走四件套：指标字典 + schema.yml + analyses + PBI/PBIP DAX。
- 业务逻辑变更必须走 `spec/<id>_*/` 三件套（requirements / design / tasks）；实现前先 commit spec。
- 每完成一次相关修改立即 commit，message 含 spec id（若适用）。
- 字典含 PBI 建模列；落地 PBIP 时只按字典/schema 生成表关系与 DAX，禁止看板私改口径。
- 数据就绪：可物化则连 marts；未物化可用 dbt 逻辑模型编译 SQL 取数/验数，禁止 stg/raw 当官方数。
- Agent 必须加载并遵循 skill：`dbt-bi`（对外名称 **DBT-BI**）。
