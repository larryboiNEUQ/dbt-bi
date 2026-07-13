# 发布检查清单

> 来源：文档 §5。

## 发布检查清单

- [ ] 事实表 grain / pk 已写进 schema.yml
- [ ] 新指标已写入字典：**口径列** + **建模列**（pbi_measure、compute_layer、dax_hint、analysis_sql）
- [ ] analyses/metrics/{id}.sql 可跑且与定义一致
- [ ] 关系按维度目录 + allowed_dims / default_time_dim 建好；隐藏列已处理
- [ ] PBI 度量名与 dax_hint 已按字典更新
- [ ] 同日同过滤：SQL = PBI
- [ ] exposure / 变更说明（若 breaking）已更新
- [ ] DE 物化与刷新窗口已对齐
