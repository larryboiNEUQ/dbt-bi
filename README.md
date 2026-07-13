# DBT-BI Skill

Analytics Engineer end-to-end skill: **四件套**（指标字典 + schema.yml + analyses + PBI/PBIP）+ **spec 驱动 dbt 变更** + **落地 PBIP**。

对外名称：**DBT-BI** · skill 目录名：`dbt-bi`

## 安装（给 Agent）

把下面整段发给 Agent 即可：

```text
请帮我安装 GitHub 公开 skill：https://github.com/larryboiNEUQ/dbt-bi
安装到本机 Agent skills 目录（按你当前环境选路径）：
- Claude: ~/.claude/skills/dbt-bi/
- Agents 通用: ~/.agents/skills/dbt-bi/
- Grok: ~/.grok/skills/dbt-bi/
若目录已存在则 git pull 更新。装完确认 SKILL.md 存在，并告诉我如何调用（说「按 DBT-BI 做 xxx」）。
```

## 手动安装

```bash
# 示例：Agents 通用
git clone https://github.com/larryboiNEUQ/dbt-bi.git ~/.agents/skills/dbt-bi
```

Windows PowerShell：

```powershell
git clone https://github.com/larryboiNEUQ/dbt-bi.git "$env:USERPROFILE\.agents\skills\dbt-bi"
```

项目级（推荐跟仓库走）：

```text
<repo>/.agents/skills/dbt-bi/   # 拷贝或 submodule 本仓库内容
```

## 用法

对 Agent 说：`按 DBT-BI 做 <你的任务>`。

详见 `SKILL.md`。
