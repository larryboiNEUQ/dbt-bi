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

#### 1）四件套（建仓 / 补齐时）

| 工件 | 作用 |
| --- | --- |
| 指标字典 | 业务口径 + PBI 建模列（权威） |
| schema.yml | 表 grain、主键、字段、维键 |
| analyses | 可运行验数 SQL |
| 独立 PBIP 仓 + exposures 链接 | 按字典 1:1 交互实现 |

Agent 发现缺件 → 先补四件套缺口，再大改业务或看板。

#### 2）spec 变更（改 dbt 逻辑时）

一次变更一个 `spec/<id>_<name>/`：

| 文件 | 作用 |
| --- | --- |
| `requirements.md` | 要什么、为什么 |
| `design.md` | 怎么改 dbt |
| `tasks.md` | 有序实现 + 验证 |

可选：大对账加 `validation.md`。
```plaintext
建 spec → requirements → design → tasks
    ↓
先 commit spec → 按 tasks 实现（小步 commit）
    ↓
spec 不对先改 spec → compile/build/test/对账
    ↓
回写四件套 → 若影响看板则更新 PBIP
```

**规则摘要**：NEEDS CLARIFICATION 禁止猜；先 spec 后 SQL；窄 selector 测试；禁止只在 SQL 注释改口径。

#### 3）落到 PBIP（字典已填时）

按字典 + schema 生成/更新语义模型与 DAX，**禁止改口径**。细节与可复制 prompt 见 **§4.2**。**Agent 对用户一句话时**：

| 用户说 | 走哪段 |
| --- | --- |
| 建四件套 / 补字典 schema analyses | Part A |
| 写 spec / 改 dbt 逻辑 | Part B |
| 进 PBIP / 按字典写 DAX | Part C（§4.2） |
| 端到端做完 | A→B→C 按缺口推进 |
