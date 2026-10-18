# goit-rdb-hw-05
**Виконавець:** Мазуренко Микола

## Датасет

**Olist Brazilian E-commerce Public Dataset**

Офіційне джерело:
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

У роботі використано повний Olist dataset без додаткового семплювання.

## Отримання файлів

Notebook використовує `kagglehub`:

```python
dataset_dir = kagglehub.dataset_download('olistbr/brazilian-ecommerce')
```

CSV-файли знаходяться рекурсивно за назвою, тому notebook не залежить від конкретної структури Kaggle cache.

## Raw-рівень

- `olist_customers_raw`
- `olist_orders_raw`
- `olist_order_items_raw`
- `olist_products_raw`
- `olist_sellers_raw`

## Typed-рівень

- `olist_customers`
- `olist_orders`
- `olist_order_items`
- `olist_products`
- `olist_sellers`

Typed-схема містить PK, FK, CHECK та NOT NULL там, де це доречно.

## CTE stages

- `reference_date` - стабільна reference date для історичного dataset;
- `stage1_orders_total` - order-level total та status;
- `stage2_rfm` - recency, frequency, monetary;
- `stage3_cohort` - cohort quarter;
- `stage4_rolling_aov` - rolling 30-day AOV та latest-order rank;
- `stage5_cancel_rate` - cancellation rate;
- `stage6_category_revenue` - revenue за customer та category;
- `stage7_preferred_category` - top-1 category за revenue.

## Recursive CTE

Створюється синтетична `hw5_referrals`. Recursive CTE має anchor через `NOT EXISTS`, `depth`, `path_ids`, текстовий path, cycle protection і depth guard `< 50`.

## Materialized View

Повний pipeline матеріалізується як `mv_olist_customer_features`. На `customer_id` створюється `UNIQUE INDEX`, після чого виконується:

```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_olist_customer_features;
```

## EXPLAIN

Notebook зберігає:

1. `EXPLAIN (ANALYZE, BUFFERS)` повного feature pipeline;
2. `EXPLAIN (ANALYZE, BUFFERS)` lookup із Materialized View.

Reflection використовує фактичні execution times поточного запуску.

## Запуск

1. Відкрийте `notebooks/HW_Topic_5_Olist_CTE_MV_UA.ipynb` у Google Colab.
2. Якщо потрібно для `pgserver`, використайте Python 3.12.
3. Виконайте `Runtime -> Restart session`.
4. Виконайте `Run all`.