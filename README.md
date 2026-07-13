# DBT-BI Skill

Analytics Engineer end-to-end skill: **四件套**（指标字典 + schema.yml + analyses + PBI/PBIP）+ **spec 驱动 dbt 变更** + **落地 PBIP**。

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

- `SKILL.md` — MUST 规则与流程（Part A 四件套 / B spec / C PBIP）
- `references/` — **文档级规范**（字段表、模板、检查清单）
  - 见 [references/README.md](references/README.md)
- `references/templates/` — spec 三件套模板

## 用法

对 Agent 说：`按 DBT-BI 做 <你的任务>`。建四件套时 Agent 应 Read `references/01`–`04` 再写文件。
