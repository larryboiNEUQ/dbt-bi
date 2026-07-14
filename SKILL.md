---
name: dbt-bi
description: >
  DBT-BI end-to-end Analytics Engineer workflow: establish and maintain the four-piece
  alignment stack (metrics dictionary + schema.yml + analyses + PBI/PBIP DAX), drive
  spec-based dbt changes (requirements/design/tasks), and implement into Power BI Project
  (PBIP). Use when the user mentions DBT-BI, dbt-bi, 四件套, 指标字典, schema.yml, analyses,
  PBIP, .pbip, semantic model, DAX from dictionary, spec/, requirements.md, or runs /dbt-bi.
---

# DBT-BI

本 skill 是 **agent 强制规则**（与项目 `AGENTS.md` 同级）：覆盖 **口径四件套 → dbt 变更（spec）→ PBIP 落地**，不是「只写 SQL」或「只贴 prompt」。

## When to use

- 从 0 建立或补齐：指标字典 / schema.yml / analyses / PBI 度量
- dbt 业务逻辑变更（model、macro、source、test）
- 把已填字典 + schema 落到 **PBIP**（表、关系、隐藏列、DAX）
- 用户说「按 DBT-BI / dbt-bi 做」「四件套」「进 PBIP」「按 spec 改」

## 总览：两段式 + 一条变更链

```text
【建立/维护四件套】
  指标字典（口径 + PBI 语义建模列）
      + schema.yml（grain / 字段 / 维键）
      + analyses（验数 SQL）
      + PBI/PBIP（按字典落 DAX）
           │
           ▼
【单次工程变更】spec 三件套 → 实现 dbt → 回写四件套 → 更新 PBIP → 对数
```

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

## Part B — Spec 驱动 dbt 变更（MUST）

改 dbt 业务逻辑时 MUST 走 spec，不得跳过（用户明确 hotfix 豁免除外，仍须最小改动并说明风险）。

### B.1 强制规则

1. **MUST** 一次变更一个 `spec/<NNN>_<short_name>/`。
2. **MUST** 含 `requirements.md`、`design.md`、`tasks.md`（大对账可加 `validation.md`）。
3. **MUST** 先 commit spec，再改 SQL。
4. **MUST** 未知写 `NEEDS CLARIFICATION`，禁止猜。
5. **MUST** 每完成一次相关修改：`git status` → 只提交相关文件 → message 含 spec id。
6. **MUST** 实现与 spec 冲突：先改 spec 并 commit，再改代码。
7. **MUST** 窄 selector：`dbt compile/build/test --select <changed+>`。
8. **MUST** 涉及 grain/指标/验数时 **回写四件套**，禁止只改 SQL。
9. **NEVER** 扩大未写入 requirements 的 scope；**NEVER** `--no-verify` / 跳 hooks / 主分支 force-push。

### B.2 目录

```text
spec/
  _template/   # 可从本 skill references/templates 拷贝
  001_name/
    requirements.md
    design.md
    tasks.md
```

### B.3 阶段（不可跳）

| Phase | 动作 | 停点 |
| --- | --- | --- |
| 0 定位 | 项目根、`spec/` 下一 id、读相关 model/YAML |  |
| 1 Requirements | 只写 requirements | 有 NEEDS CLARIFICATION 或待确认 → **停** |
| 2 Design | 读现状写 design（含测试策略） | 确认前不改业务 SQL |
| 3 Tasks + commit | 拆 tasks，**只 commit spec** |  |
| 4 Implement | 按 tasks 实现；小步 commit |  |
| 5 Verify | compile/build/test/对账，记 Validation Notes |  |
| 6 四件套/PBIP | 回写字典/schema/analyses；若影响看板则更新 PBIP |  |

### B.4 用户一句话时

| 用户意图 | 行为 |
| --- | --- |
| 写 spec / requirements | Phase 0–1，停确认 |
| 出 design | Phase 2，停确认 |
| 按 tasks 实现 | Phase 4+ |
| 端到端做完 | 全流程，澄清则暂停 |
| 建立四件套 / 进 PBIP | Part A / Part C，可与 spec 并行规划 |

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
| [references/templates/](references/templates/) | spec：`requirements` / `design` / `tasks` |
