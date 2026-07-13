# DBT-BI references（四件套规范）

本目录是飞书「口径对齐工作流」文档的规范正文，供 Agent 在建立/维护四件套时 **Read 后严格执行**。

| 文件 | 内容 | 何时读 |
| --- | --- | --- |
| [00-overview.md](00-overview.md) | 工作流总览、仓库落盘 | 建仓开头 |
| [01-metrics-dictionary.md](01-metrics-dictionary.md) | 指标字典字段、特殊类型、示例 | 写/改字典 |
| [02-schema-yml.md](02-schema-yml.md) | grain、schema.yml、可选维度目录（从 schema 整理） | 写/改 model YAML |
| [03-analyses.md](03-analyses.md) | 验数 SQL 模板与标准 | 写 analyses |
| [04-pbi-pbip.md](04-pbi-pbip.md) | PBIP/DAX 流程、AI prompt、exposures | 落 PBI |
| [05-release-checklist.md](05-release-checklist.md) | 发版检查 | 发布前 |
| [06-cheatsheet.md](06-cheatsheet.md) | 口诀 | 随时 |
| [agents-snippet.md](agents-snippet.md) | 项目 AGENTS.md 片段 | 初始化仓库 |
| [templates/](templates/) | spec requirements/design/tasks 模板 | 开 spec |

**与 SKILL.md 关系**：SKILL.md = MUST 规则与流程；本目录 = 字段/模板级规范。冲突时以 SKILL MUST 为准，字段细节以本目录为准。
