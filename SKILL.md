---
name: dbt-bi
description: >
  DBT-BI end-to-end Analytics Engineer workflow: establish and maintain the four-piece
  alignment stack (metrics dictionary + schema.yml + analyses + PBI/PBIP DAX), route
  spec-driven repository changes through the explicit setup-matt-pocock-skills workflow,
  and implement into Power BI Project (PBIP). Use when the user mentions DBT-BI, dbt-bi,
  四件套, 指标字典, schema.yml, analyses, PBIP, .pbip, semantic model, DAX from dictionary,
  spec 驱动, setup-matt-pocock-skills, or runs /dbt-bi.
---

# DBT-BI

本 skill 是 **agent 强制规则**（与项目 `AGENTS.md` 同级）：覆盖 **口径四件套 → 工程变更入口（调用 setup-matt-pocock-skills）→ PBIP 落地**，不是「只写 SQL」或「只贴 prompt」。

## When to use

- 从 0 建立或补齐：指标字典 / schema.yml / analyses / PBI 度量
- dbt 业务逻辑变更（model、macro、source、test）
- 把已填字典 + schema 落到 **PBIP**（表、关系、隐藏列、DAX）
- 用户说「按 DBT-BI / dbt-bi 做」「四件套」「进 PBIP」「按 spec 改」

## 总览：初始化 + 两段式 + 一条变更链

```text
【DBT 项目搭建 / 首次使用】
  检查当前 Agent 是否安装 `$setup-matt-pocock-skills`
  未安装 → 提示安装并暂停
  已安装 → 显式调用 `$setup-matt-pocock-skills`
  Explore → Present findings and ask → Confirm and edit → Write
           │
           ▼
【建立/维护四件套】
  指标字典（口径 + PBI 语义建模列）
      + schema.yml（grain / 字段 / 维键）
      + analyses（验数 SQL）
      + PBI/PBIP（按字典落 DAX）
           │
           ▼
【单次工程变更】
  使用已配置的 issue tracker / domain docs / engineering skills
  dbt 实现 → 回写四件套 → 更新 PBIP → 对数
```

---

## Part 0 — DBT 项目初始化：配置 setup skill（MUST）

新建 dbt 项目、初始化已有 dbt 仓库，或首次按 DBT-BI 开始工程工作时，MUST 先完成本节。`$setup-matt-pocock-skills` 的 frontmatter 标记为 `disable-model-invocation: true`，必须显式调用。

### 0.1 检查与安装依赖

- 通过当前 Agent 的技能名解析 `$setup-matt-pocock-skills`；不要在仓库文档中写任何具体用户目录或本机绝对路径。
- 如果当前 Agent 未安装该 skill，MUST 暂停，不得继续创建 spec、修改 dbt 业务逻辑或写入 setup 配置。
- 向用户提示：`未检测到 $setup-matt-pocock-skills，请按当前 Agent 平台的安装方式把它安装到当前机器的 skills 目录，安装后重新运行 DBT-BI。`
- 安装完成后重新检查并显式调用该 skill；不能把另一台机器上的 skill 路径当作替代。

### 0.2 按 setup skill 配置仓库

调用后严格遵循它的停点，不要自行简化成一次性写文件：

1. **Explore**：读取 `git remote -v`、`.git/config`、`AGENTS.md`/`CLAUDE.md`、上下文文档、ADR、`docs/agents/`、`.scratch/`、triage skill 和 monorepo 信号。
2. **Present findings and ask**：先报告发现，再按顺序确认 issue tracker；仅在 triage skill 已安装时确认 triage labels；domain docs 默认 single-context。
3. **Confirm and edit**：展示待写入的 `## Agent skills` block 和 `docs/agents/*.md` 草稿，等待用户确认或修改。如果既没有 `CLAUDE.md` 也没有 `AGENTS.md`，先询问创建哪一个。
4. **Write**：按确认结果更新选定的 Agent 指令文件，并写入 `docs/agents/issue-tracker.md`、`docs/agents/domain.md`，以及仅在需要时写入 `docs/agents/triage-labels.md`。
5. **Done**：回读配置，说明后续 engineering skills 将从这些文件读取仓库约定。

setup 未完成或停在澄清点时，DBT-BI 也必须停下；不得猜测 issue tracker、triage 标签、上下文布局或直接启动旧模板。

---

## Part A — 推动建立四件套（MUST）

口径权威不在看板私有度量里。Agent 协助建仓/补齐时 MUST 按下列工件推进，缺一则标缺口，禁止用「只写 DAX」或「只写 SQL」冒充闭环。

| 工件 | 落盘（建议） | MUST 包含 | 详细规范 |
| --- | --- | --- | --- |
| **指标字典** | `docs/metrics.md` 或 metrics.csv / 飞书表 | 业务列 + PBI 语义建模列：`grain_ref`、`expression`、`filters`、`default_time_dim`、`allowed_dims`、`pbi_measure`、`compute_layer`、`dax_hint`、`analysis_sql` | [01-metrics-dictionary.md](references/01-metrics-dictionary.md) |
| **schema.yml** | `models/**/schema.yml` | 事实 grain/pk、字段含义、tests；维度键可支撑关系 | [02-schema-yml.md](references/02-schema-yml.md) |
| **analyses** | `analyses/metrics/{metric_id}.sql` | 与字典 filters/默认时间一致的可运行验数 | [03-analyses.md](references/03-analyses.md) |
| **PBI/PBIP** | **独立 PBIP 仓库**（报表层，与 dbt 仓分离）+ 认证 Dataset | 度量名 = `pbi_measure`；关系与 `allowed_dims` / `default_time_dim` 一致；dbt 仓用 `exposures.yml` 挂 **看板 URL + PBIP 仓 URL** | [04-pbi-pbip.md](references/04-pbi-pbip.md) |

总览与落盘：[00-overview.md](references/00-overview.md) · 索引：[references/README.md](references/README.md) · 发版：[05-release-checklist.md](references/05-release-checklist.md) · 口诀：[06-cheatsheet.md](references/06-cheatsheet.md)

**建立/补齐四件套时**：MUST **Read** 对应 `references/*.md` 后再写仓库文件；字段名、示例、模板以 references 为准。

### A.1 字典与 schema

- **MUST** 改口径先改字典业务列，再改 analyses 与 DAX。
- **MUST** grain 主定义在 schema 事实表；字典只 `grain_ref` 引用。
- **MUST** `compute_layer` 标明 dbt / dbt+pbi / pbi（定义仍在字典，算在哪层另写）。
- **NEVER** 只在 SQL 注释或报表筛选里改官方口径。

### A.2 analyses

- 每个 certified 核心指标至少 1 条 `analyses/metrics/{metric_id}.sql`。
- 验收：同日同过滤，SQL 结果 = PBI 卡片。

### A.3 数据就绪：物化 vs 逻辑模型 SQL

数据有两条路径（正式看板优先 Path A）：

| 路径 | 何时 | 怎么取数 |
| --- | --- | --- |
| **Path A · 物化** | DE 已物化到约定 schema（如 marts） | PBIP/PBI **只连 marts**，不连 stg/raw |
| **Path B · 逻辑模型 SQL** | 逻辑尚未到可物化/可调度 | 用 dbt **逻辑模型**（`ref` 链）`compile`/预览 SQL 直查仓库；analyses 同样基于逻辑模型编译后跑。**禁止**把 stg/raw 当官方数 |

- **MUST** 未物化不阻塞口径与验数：Path B 仍以字典 + schema + analyses 为准。
- **MUST** 物化完成后，对数与正式 Dataset **切到 marts**（Path A）。
- 粒度变更 = breaking；Path A 先对齐物化再改关系/度量。

---

## Part B — 工程变更入口：调用 setup-matt-pocock-skills（MUST）

涉及 dbt model、macro、source、test 的工程变更，或用户提出 spec 驱动需求时，MUST 先显式调用 `$setup-matt-pocock-skills`。

如果尚未完成 Part 0，必须先回到 Part 0；不能直接创建 spec 文件或开始工程实现。

该 skill 的 frontmatter 标记为 `disable-model-invocation: true`，因此必须显式调用，不能假定它会被隐式加载。它负责仓库级工程技能配置；DBT-BI 不再维护一套并行的 requirements/design/tasks spec workflow。

### B.1 setup handoff

按 setup skill 的顺序执行，不得跳过确认停点：

1. **Explore**：检查 remote、AGENTS/CLAUDE、现有 `docs/agents/`、上下文文档、ADR、`.scratch/`、triage skill 和 monorepo 信号。
2. **Present findings and ask**：呈现发现，并按顺序确认 issue tracker、triage labels（仅在安装 triage 时）和 domain docs。
3. **Confirm and edit**：展示 `## Agent skills` 及 `docs/agents/*.md` 草稿，等待确认或修改。
4. **Write**：写入选定的 `AGENTS.md`/`CLAUDE.md` 与 `docs/agents/` 配置。

建议使用 GitHub issue tracker（仓库 remote 指向 GitHub 时）和 single-context；triage labels 只在 triage skill 已安装且用户确认后写入。

### B.2 DBT-BI handoff

1. setup 完成后，读取其写入的 `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 以及存在的 triage 配置。
2. 继续按本 skill 的 Part A 建立/回写四件套；涉及 PBIP 时按 Part C 执行。
3. 工程实现与验证遵循仓库已配置的 engineering skills 和 issue tracker，不再复制 `references/templates/` 来启动一套 DBT-BI 私有 spec 流程。
4. 若 setup 的 Explore 或确认阶段发现缺失信息，保留 `NEEDS CLARIFICATION` 并停在对应停点，不要猜测。

### B.3 用户一句话时

| 用户说 | 行为 |
| --- | --- |
| 写 spec / requirements / design / tasks | 先调用 setup skill；按其 Explore → Confirm and edit → Write 流程推进，确认停点暂停 |
| 改 dbt 逻辑 | 先确保 setup 已完成，再遵循仓库已配置的 engineering skills 实现并验证 |
| 建立四件套 / 进 PBIP | 直接走 Part A / Part C；若同时涉及工程变更，先走 setup skill |
| 端到端做完 | setup → Part A → 工程实现与验证 → Part C，按缺口推进 |

---

## Part C — 实施到独立 PBIP 仓库（MUST）

字典 + schema 已就绪时，在 **独立的 PBIP/报表仓库**（与 dbt 仓分离）落地语义模型与 DAX；dbt 仓只登记依赖与链接，不把报表工程塞进 dbt 根目录。

详细流程、AI prompt、DAX 提示、exposures：**MUST Read** [references/04-pbi-pbip.md](references/04-pbi-pbip.md)。

### C.1 强制

- **MUST** PBIP 作为报表层 **独立仓库**；dbt 仓通过 `models/exposures.yml`（或约定路径）链接下游。
- **MUST** 每个正式看板 exposure 至少含：`url`（最终看板/Service 链接）+ **`meta.pbip_repo_url`**（PBIP 仓地址）；可选 `meta.dataset_url` / `meta.pbip_path`。
- **MUST** 只读字典/schema/analyses，**禁止改口径**；冲突标出并停，勿猜。
- **MUST** 度量显示名 = `pbi_measure`；优先 `dax_hint`；落实 `filters` 与 `compute_layer`。
- **MUST** 关系：维 1→* 事实；默认时间 = `default_time_dim`；多时间角色分开。
- **MUST** 隐藏代理键/ETL 列；遵守 `non_additive_note` / `forbidden_dims`。
- **MUST** 发布前用 `analysis_sql` 对数（同日同过滤 SQL = 卡片）。
- **NEVER** 报表私建与字典冲突的同名度量副本；**NEVER** 连 stg/raw。

### C.2 执行清单（可当内部 checklist）

1. 进模表：`grain_ref` ∪ schema 中的 dim 表（可选维度目录仅作索引）→ Path A 连 marts；Path B 基于逻辑模型 compile SQL 取数。  
2. 关系：schema 维键（可选维度目录协助角色映射）+ `allowed_dims`；标默认日期关系。  
3. 隐藏列：schema 代理键/中间列。  
4. 度量：逐行字典 → DAX；`display_folder` 归组。  
5. 对数清单：`analysis_sql` + 建议筛选。  
6. 输出：表清单、关系说明、隐藏列、度量清单（metric_id / pbi_measure / DAX）、开放问题。

### C.3 给用户的可复制 prompt（路径替换后使用）

```text
按 DBT-BI skill 执行。字典与 schema 已填好，生成/更新 PBIP 语义模型与 DAX，禁止改口径。

输入（只读）：
- dbt 仓：指标字典 + schema.yml（维度目录可选）
- PBIP 独立仓路径：<path 或 git URL>
- 数据就绪：Path A marts <库.schema>；或 Path B dbt 逻辑模型 SQL（未物化）
- 范围：certified 或 metric_id 列表 <...>

在 PBIP 仓生成/更新模型与 DAX；在 dbt 仓写/更新 exposures（url=看板链接，meta.pbip_repo_url=PBIP 仓）。
输出表/关系/隐藏列/度量 DAX/对数清单/exposures 草稿；有 NEEDS CLARIFICATION 则停。
```

---

## 常用 dbt 命令

```powershell
dbt compile --select <model_or_model_plus>
dbt build --select <model_or_model_plus>
dbt test --select <model_or_source>
```

PowerShell 下先 `Set-Location` 到 dbt 项目根，再跑相对路径命令。

## 项目 AGENTS.md 建议

见 `references/agents-snippet.md`。

## references 与 templates

| 路径 | 用途 |
| --- | --- |
| [references/README.md](references/README.md) | 规范索引 |
| [references/00-overview.md](references/00-overview.md) … [06-cheatsheet.md](references/06-cheatsheet.md) | 四件套字段/模板级规范（来自团队文档） |
| [references/templates/](references/templates/) | 历史 spec 链接的兼容性跳转 | 不作为独立 workflow 使用 |
