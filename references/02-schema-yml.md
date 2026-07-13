# schema.yml 规范

> 来源：文档 §2。事实粒度登记、维度目录均为可选，从 schema 整理。

## schema.yml（表粒度 / 字段）

逻辑模型（dim/fct）决定「数从哪来、一行是什么」；`schema.yml` 是粒度与字段含义的**可执行合同**（含 tests）。

### 2.1 schema.yml 写法示例
```plaintext
models:
  - name: fct_orders
    description: >
      粒度：一笔已支付订单（order_id 唯一）。
      非订单行；退款不在此表（见 fct_refunds）。
    columns:
      - name: order_id
        description: 订单主键
        tests: [unique, not_null]
      - name: pay_amount
        description: 实付金额（元），含税，已扣券，不含运费
      - name: pay_date_key
        description: 支付日期键，关联 dim_date（角色 date_pay）

```

### 2.2 事实粒度登记（可选，从schema 整理）

| table | grain（一行是什么） | pk | 备注 |
| --- | --- | --- | --- |
| fct_orders | 一笔已支付订单 | order_id | 非订单行；退款见 fct_refunds |
| fct_order_items | 一行订单明细 | order_item_id |  |
| agg_sales_day | 一天 × 一店铺 | date_key + store_id | 不可下钻到订单 |

### 2.3 维度目录（可选，从schema 整理）

| dim_id | 名称 | 来源表 | 键 / 关联 | 层级或常用属性 | 备注 |
| --- | --- | --- | --- | --- | --- |
| date_pay | 支付日 | dim_date | fct.pay_date_key → date_key | 日→周→月→年 | GMV 默认时间 |
| date_order | 下单日 | dim_date | fct.order_date_key → date_key | 同上 | 角色扮演维，勿与支付日混用 |
| customer | 客户 | dim_customer | customer_key | 客户→城市→区域 |  |
| product | 商品 | dim_product | product_key | SKU→品类 | 多用于明细事实 |
| channel | 渠道 | dim 或 fct 属性 | channel_id |  |  |

说明：属性字段在维表 / schema.yml 维护；指标只引用 dim_id。
