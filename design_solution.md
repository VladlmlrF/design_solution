# Проектирование информационной системы «Онлайн Магазин»
## UML-диаграмма классов

```postgres-sql
+----------------------------------+
|            Category              |
+----------------------------------+
| - id: int                        |
| - name: string                   |
| - parent: Category (nullable)    |
+----------------------------------+
| +getAllProducts(): List<Product> |
+----------------------------------+

               1           *
               ┌───────────┐
               │           │ 
               │           │
               v           v

        +-------------+   
        |   Product   | (abstract)
        +-------------+
        | - id: int   |
        | - name: str |
        | +getPrice(): float       |
        +-------------+   
               ^
       ┌───────┼───────────────────┐
       │                           │
       │                           │
+-------------------+       +----------------------+
|   SimpleProduct   |       |     ProductGroup     |
+-------------------+       +----------------------+
| - price: float    |       | - discountType: enum |
|                   |       | - discountValue: int |
+-------------------+       |                      |
| +getPrice():float |       | +getPrice(): float   |
+-------------------+       | productsInGroup:List<Product> 
                            +----------------------+
```
## ER-диаграмма для БД

```postgres-sql
  +------------------+
  |    Categories    |
  +------------------+
  | id (PK)          |
  | name             |
  | parent_id (FK->Categories.id, nullable)|
  +------------------+

  +------------------+
  |    Products      |
  +------------------+
  | id (PK)          |
  | type (enum: SIMPLE, GROUP)|
  | name             |
  | price (nullable для GROUP)|
  +------------------+

  +--------------------------------------+
  |           ProductGroups              |
  +--------------------------------------+
  | id (PK, FK->Products.id)             |
  | discount_type (enum: PERCENT, EVERY_NTH_FREE)|
  | discount_value (int)                 |
  +--------------------------------------+

  +-------------------------------------+
  |          Category_Products          |
  +-------------------------------------+
  | category_id (FK->Categories.id)     |
  | product_id  (FK->Products.id)       |
  | PRIMARY KEY(category_id, product_id)|
  +-------------------------------------+

  +--------------------------------------+
  |  Group_Product_Association           |
  +--------------------------------------+
  | group_id (FK->ProductGroups.id)      |
  | product_id (FK->Products.id)         |
  | PRIMARY KEY (group_id, product_id)   |
  +--------------------------------------+
```

## Обоснование решений

- Использование наследования упростит код и сделает модель товаров расширяемой
- Модель иерархии категорий через parent_id – понятный и широко применяемый подход
- Разделение логики на классы и отдельные сущности в БД позволит легко добавлять новые виды товаров и скидок
- Связь «многие-ко-многим» между категориями и товарами повышает гибкость и не ограничивает систему одним товаром в одной категории
- Чёткое разграничение типов скидок для групповых товаров обеспечит простоту добавления новых стратегий ценообразования

Таким образом, предложенная архитектура является расширяемой, понятной и удобной для дальнейшей разработки