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

1. 由 setup skill 按其当前版本探索仓库；DBT-BI 不复制其内部检查项。
2. 由 setup skill 按当前版本呈现发现并完成所需确认。
3. 展示 `## Agent skills` 与 `docs/agents/*.md` 草稿并等待确认，按 setup skill 规则选择 Agent 指令文件。
4. 写入确认后的 Agent 指令和 `docs/agents/` 配置，回读确认后再进入四件套或 dbt 实现。

#### 1）四件套（建仓 / 补齐时）

| 工件 | 作用 |
| --- | --- |
| 指标字典 | 业务口径 + PBI 建模列（权威） |
| schema.yml | 表 grain、主键、字段、维键 |
| analyses | 可运行验数 SQL |
| 独立 PBIP 仓 + exposures 链接 | 按字典 1:1 交互实现 |

Agent 发现缺件 → 先补四件套缺口，再大改业务或看板。

#### 2）工程变更入口（运行时发现）

改 dbt 逻辑或提出 spec 驱动工程变更时，先确认 setup 已完成；未完成才调用 `$setup-matt-pocock-skills`。setup 已完成后，Coding Agent 在自己的当前环境中动态发现可用的 Matt Pocock engineering skills，读取元数据和完整指令后按任务路由。

DBT-BI 不维护固定的下游 skill 名称或列表；所需能力缺失时提示安装并暂停。工程实现与验证继续遵循 DBT-BI 的四件套、dbt 验证和 PBIP 规则。

#### 3）落到 PBIP（字典已填时）

按字典 + schema 生成/更新语义模型与 DAX，**禁止改口径**。细节与可复制 prompt 见 **§4.2**。**Agent 对用户一句话时**：

| 用户说 | 走哪段 |
| --- | --- |
| 建四件套 / 补字典 schema analyses | Part A |
| 写 spec / 改 dbt 逻辑 | 确认 setup 已完成，再由 Coding Agent 动态发现并路由当前环境中的 Matt Pocock skills |
| 进 PBIP / 按字典写 DAX | Part C（§4.2） |
| 端到端做完 | A→B→C 按缺口推进 |
