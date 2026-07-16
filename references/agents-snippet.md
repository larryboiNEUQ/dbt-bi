# 建议写入 dbt 仓库根 AGENTS.md

## DBT-BI

- 口径对齐走四件套：指标字典 + schema.yml + analyses + **独立 PBIP 仓**（报表层）。
- dbt 仓用 `exposures.yml` 登记下游：`url`（最终看板）+ `meta.pbip_repo_url`（PBIP 仓），勿把 `.pbip` 工程放进 dbt 根目录。
- 业务逻辑变更或 spec 驱动需求，先显式调用 [$setup-matt-pocock-skills](/Users/larry/.agents/skills/setup-matt-pocock-skills/SKILL.md)，按其 Explore → Present findings and ask → Confirm and edit → Write 流程配置仓库。
- 不要把 `references/templates/` 当作独立的 DBT-BI spec workflow；实现与验证遵循仓库已配置的 engineering skills。
- 每完成一次相关修改立即 commit，message 含 spec id（若适用）。
- 字典含 PBI 语义建模列；PBIP 仓只按字典/schema 落关系与 DAX，禁止看板私改口径。
- 数据就绪：可物化则连 marts；未物化可用 dbt 逻辑模型编译 SQL 取数/验数，禁止 stg/raw 当官方数。
- Agent 必须加载并遵循 skill：`dbt-bi`（对外名称 **DBT-BI**）。
