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

DBT-BI 依赖当前 Agent 环境中的 `$setup-matt-pocock-skills`。仓库只按技能名调用，不写死任何机器的绝对路径。

### DBT 项目搭建时

1. 检查当前 Agent 是否已安装 `$setup-matt-pocock-skills`。
2. 如果未安装，暂停当前流程并提示用户：请按当前 Agent 平台的技能安装方式安装该 skill 到这台机器的 skills 目录；安装完成后重新运行 DBT-BI。不要用其他机器的绝对路径替代。
3. 如果已安装，显式调用 `$setup-matt-pocock-skills`，并按它的 Explore → Present findings and ask → Confirm and edit → Write 流程完成仓库配置。
4. setup 阶段按 skill 规则配置 issue tracker、triage labels（仅在 triage skill 已安装时）和 domain docs；如果仓库没有 `AGENTS.md`/`CLAUDE.md`，按 skill 要求先询问要创建哪一个。
5. setup 完成并回读 `docs/agents/` 配置后，再继续 DBT-BI 的四件套、dbt 实现和 PBIP 流程。

### 已有 DBT 项目的工程变更

提出 spec 驱动需求或修改 dbt model、macro、source、test 时，同样先显式调用 `$setup-matt-pocock-skills`；DBT-BI 不再维护并行的 requirements/design/tasks spec workflow，`references/templates/` 仅保留兼容性跳转。
