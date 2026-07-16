# 工作流总览

> 来源：文档 §0（安装 TLDR 见仓库 README；此处保留结构与 Skill 功能明细）。

## 工作流总览

### 工作流结构

口径对齐不是「写一堆 DAX」或「写一堆 SQL」的二选一，而是同一套定义在四层工件上闭环：
```plaintext
① 指标字典          → 业务口径 + PBI 建模列（一个表写清）
        ↓
② schema.yml        → 表粒度、主键、字段、维度键
        ↓
③ analyses SQL      → 可运行验收（对数基准）
        ↓
④ PBI DAX           → 按字典 1:1 落度量 → 认证 Dataset → 报表
```

总结：

> **定义与建模意图都放字典，形状放 schema，验收放 analyses，交互放 PBI。**

四件套在 dbt 仓库中的对应位置（总览一览）：
```plaintext
docs/metrics.md          # 或 metrics.csv / 飞书多维表同步  → 指标字典
models/.../schema.yml    # grain + 字段 + tests
analyses/metrics/
  gmv.sql
  paid_users.sql
  ...
models/exposures.yml     # 正式 PBI 看板依赖
```

### Skill 功能明细（DBT-BI）

完整规则见 skill **dbt-bi**。文档这里只记功能。

#### 0）DBT 项目初始化：setup skill

新建或首次接管 dbt 仓库时，先检查当前 Agent 是否安装 `$setup-matt-pocock-skills`。未安装时暂停并提示用户按当前 Agent 平台的安装方式安装到本机 skills 目录，安装后重新运行；仓库文档不引用任何具体机器的绝对路径。

已安装后显式调用 `$setup-matt-pocock-skills`，严格走 Explore → Present findings and ask → Confirm and edit → Write：

1. 探索 remote、Agent 指令、上下文文档、ADR、`docs/agents/`、`.scratch/`、triage 和 monorepo 信号。
2. 呈现发现并确认 issue tracker；triage 未安装则跳过标签确认；domain docs 默认 single-context。
3. 展示 `## Agent skills` 与 `docs/agents/*.md` 草稿并等待确认；没有 `AGENTS.md`/`CLAUDE.md` 时先询问创建哪一个。
4. 写入确认后的 Agent 指令和 `docs/agents/` 配置，回读确认后再进入四件套或 dbt 实现。

#### 1）四件套（建仓 / 补齐时）

| 工件 | 作用 |
| --- | --- |
| 指标字典 | 业务口径 + PBI 建模列（权威） |
| schema.yml | 表 grain、主键、字段、维键 |
| analyses | 可运行验数 SQL |
| 独立 PBIP 仓 + exposures 链接 | 按字典 1:1 交互实现 |

Agent 发现缺件 → 先补四件套缺口，再大改业务或看板。

#### 2）工程变更入口（setup skill）

改 dbt 逻辑或提出 spec 驱动工程变更时，先显式调用 `$setup-matt-pocock-skills`。
DBT-BI 不再维护独立的 requirements/design/tasks spec workflow；该目录中的旧模板只用于兼容历史链接。

按 setup skill 的四步推进：

1. **Explore**：读取仓库 remote、Agent 指令、上下文文档、ADR、既有 `docs/agents/`、`.scratch/` 与 monorepo/triage 信号。
2. **Present findings and ask**：先报告发现，再按顺序确认 issue tracker、triage labels 和 domain docs。
3. **Confirm and edit**：确认 `## Agent skills` 和 `docs/agents/*.md` 草稿。
4. **Write**：写入选定的 Agent 指令文件和 `docs/agents/` 配置。

setup 完成后，回到 DBT-BI 的 Part A / Part C；工程实现与验证遵循该仓库已配置的 engineering skills。

#### 3）落到 PBIP（字典已填时）

按字典 + schema 生成/更新语义模型与 DAX，**禁止改口径**。细节与可复制 prompt 见 **§4.2**。**Agent 对用户一句话时**：

| 用户说 | 走哪段 |
| --- | --- |
| 建四件套 / 补字典 schema analyses | Part A |
| 写 spec / 改 dbt 逻辑 | 先调用 setup skill，再遵循仓库配置的工程 workflow |
| 进 PBIP / 按字典写 DAX | Part C（§4.2） |
| 端到端做完 | A→B→C 按缺口推进 |
