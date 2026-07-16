# DBT-BI references（四件套规范）

同步自飞书文档《DBT-BI工作流：指标字典 + schema + analyses + PBI》。Agent 建立/维护四件套时 **Read 后严格执行**。

跨机器使用时，先确认当前 Agent 已安装 `$setup-matt-pocock-skills`；未安装就提示安装并暂停，不使用任何本机绝对路径。新建/初始化 dbt 仓库时，按 [00-overview.md](00-overview.md) 的 setup 流程先完成仓库配置。

涉及工程变更或 spec 驱动需求时，先显式调用 `$setup-matt-pocock-skills`；本目录不再提供独立的 spec workflow。

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
| [templates/](templates/) | 历史 spec 链接的兼容性跳转 | 遇旧链接时查看，不作为独立 workflow |

**与 SKILL.md**：SKILL.md = MUST 规则与流程；本目录 = 字段/模板级规范。
