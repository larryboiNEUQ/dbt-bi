# DBT-BI Skill

Analytics Engineer end-to-end skill: **四件套**（指标字典 + schema.yml + analyses + PBI/PBIP）+ **工程变更入口** + **落地 PBIP**。

对外名称：**DBT-BI** · skill 目录名：`dbt-bi`

## 安装（给 Agent）

```text
请帮我安装 GitHub 公开 skill：https://github.com/larryboiNEUQ/dbt-bi
安装到本机 Agent skills 目录（按你当前环境选路径）：
- Claude: ~/.claude/skills/dbt-bi/
- Agents 通用: ~/.agents/skills/dbt-bi/
- Grok: ~/.grok/skills/dbt-bi/
若目录已存在则 git pull 更新。装完确认 SKILL.md 存在，并告诉我如何调用（说「按 DBT-BI 做 xxx」）。
```

## 结构

- `SKILL.md` — MUST 规则与流程（Part A 四件套 / B 工程变更入口 / C PBIP）
- `references/` — **文档级规范**（字段表、兼容入口、检查清单）
  - 见 [references/README.md](references/README.md)
- `references/templates/` — 兼容性跳转（规范入口见 setup skill）

## 用法

对 Agent 说：`按 DBT-BI 做 <你的任务>`。建四件套时 Agent 应 Read `references/01`–`04` 再写文件。

## 工程变更入口

涉及 dbt model、macro、source、test 的工程变更，或用户提出 spec 驱动需求时，先显式调用 [$setup-matt-pocock-skills](/Users/larry/.agents/skills/setup-matt-pocock-skills/SKILL.md)。
该 skill 按 Explore → Present findings and ask → Confirm and edit → Write 配置仓库级工程工作流；建议 GitHub 仓库使用 GitHub Issues，并默认采用 single-context。
DBT-BI 不再维护一套并行的 requirements/design/tasks spec workflow；`references/templates/` 仅保留兼容性跳转。
