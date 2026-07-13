# DBT-BI references（四件套规范）

同步自飞书文档《DBT-BI工作流：指标字典 + schema + analyses + PBI》。Agent 建立/维护四件套时 **Read 后严格执行**。

| 文件 | 内容 | 何时读 |
| --- | --- | --- |
| [00-overview.md](00-overview.md) | 工作流结构、Skill 功能明细 | 建仓开头 |
| [01-metrics-dictionary.md](01-metrics-dictionary.md) | 指标字典字段、特殊类型、示例 | 写/改字典 |
| [02-schema-yml.md](02-schema-yml.md) | schema.yml；可选粒度登记/维度目录 | 写/改 model YAML |
| [03-analyses.md](03-analyses.md) | 验数 SQL 模板 | 写 analyses |
| [04-pbi-pbip.md](04-pbi-pbip.md) | 落地原则、AI prompt、DAX、exposures | 落 PBI/PBIP |
| [05-release-checklist.md](05-release-checklist.md) | 发版检查 | 发布前 |
| [06-cheatsheet.md](06-cheatsheet.md) | 总结/口诀 | 随时 |
| [agents-snippet.md](agents-snippet.md) | 项目 AGENTS.md 片段 | 初始化仓库 |
| [templates/](templates/) | spec requirements/design/tasks | 开 spec |

**与 SKILL.md**：SKILL.md = MUST 规则与流程；本目录 = 字段/模板级规范。
