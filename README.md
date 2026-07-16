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

- `SKILL.md` — MUST 规则与流程（Part 0 setup / Part A 四件套 / B 工程变更入口 / C PBIP）
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
4. setup 阶段完全遵循该 skill 当前版本的规则配置 issue tracker、可选标签映射和 domain docs；DBT-BI 不复制其内部条件。如果仓库没有 `AGENTS.md`/`CLAUDE.md`，按 skill 要求先询问要创建哪一个。
5. setup 完成并回读 `docs/agents/` 配置后，再继续 DBT-BI 的四件套、dbt 实现和 PBIP 流程。
6. setup 完成后，Coding Agent MUST 在自己的当前环境中动态发现已安装且可用的 Matt Pocock engineering skills，读取候选 skill 的名称、描述与触发条件后按任务意图路由。DBT-BI 不维护或枚举固定的下游 skill 列表；所需 skill 缺失时，提示用户安装并暂停对应流程。

### 已有 DBT 项目的工程变更

提出 spec 驱动需求或修改 dbt model、macro、source、test 时，先检查 Part 0 的 setup 配置是否已完成：未完成才调用 `$setup-matt-pocock-skills`；已完成则不重复运行 setup，直接由 Coding Agent 按当前环境动态发现并路由 Matt Pocock engineering skills。DBT-BI 不再维护并行的 requirements/design/tasks spec workflow，`references/templates/` 仅保留兼容性跳转。
