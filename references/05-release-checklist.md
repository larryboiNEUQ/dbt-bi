# 发布检查清单

> 来源：文档 §5 + PBIP 独立仓约定。

## 发布检查清单

- [ ] 事实表 grain / pk 已写进 schema.yml
- [ ] 新指标已写入字典：口径列 + 语义建模列（pbi_measure、compute_layer、dax_hint、analysis_sql）
- [ ] analyses/metrics/{id}.sql 可跑且与定义一致（物化前基于逻辑模型编译 SQL 亦可）
- [ ] 数据就绪路径已明确：**Path A 物化 marts** 或 **Path B 逻辑模型 SQL 取数**
- [ ] **PBIP 在独立报表仓**更新；关系 + 度量与字典一致
- [ ] dbt `exposures.yml`：`url`（最终看板）+ **`meta.pbip_repo_url`（PBIP 仓）** + depends_on
- [ ] 同日同过滤：SQL = PBI
- [ ] breaking 变更说明已更新
- [ ] Path A：DE 物化与刷新窗口已对齐；Path B：已记录后续切 marts 的条件
