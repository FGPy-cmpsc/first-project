# ER‑диаграмма и описание схемы базы данных `cottage_rentals_database`

```mermaid
erDiagram
    COTTAGE {
        INT cottage_id PK
        VARCHAR address
        INT floors_cnt
        INT rooms_cnt
        INT total_area
        INT price_for_day
        BOOL available
    }

    INTERIOR_ITEM_CATEGORIES {
        INT item_category_id PK
        VARCHAR category_name
    }

    INTERIOR {
        INT item_id PK
        INT related_cottage_id FK
        INT item_category_id FK
        VARCHAR item_name
        INT floor
    }

    CLIENTS {
        INT client_id PK
        VARCHAR client_name
        VARCHAR client_surname
        VARCHAR client_patronymic
        VARCHAR passport
        VARCHAR phone
        VARCHAR email
    }

    RENT_ORDERS {
        INT rent_id PK
        INT cottage_id FK
        INT client_id  FK
        DATE date_from
        DATE date_to
        INT total_cost
        BOOL paid
    }

    %% Relationships
    COTTAGE ||--o{ INTERIOR                 : has_interior
    INTERIOR_ITEM_CATEGORIES ||--o{ INTERIOR : categorises
    COTTAGE ||--o{ RENT_ORDERS              : rented_in
    CLIENTS ||--|{ RENT_ORDERS              : places
```

## Описание сущностей

### `cottage`
* **Назначение:** сведения о коттеджах, доступных для аренды.  
* **Поля:** `cottage_id` (PK), `address`, `floors_cnt`, `rooms_cnt`, `total_area`, `price_for_day`, `available`.  
* **Связи:**  
  * 0..N `interior` — коттедж содержит множество предметов интерьера.  
  * 0..N `rent_orders` — коттедж упоминается в заказах.

### `interior_item_categories`
* **Назначение:** справочник категорий предметов интерьера.  
* **Поля:** `item_category_id` (PK), `category_name`.  
* **Связь:** 1 → N `interior`.

### `interior`
* **Назначение:** отдельные предметы внутри коттеджей.  
* **Поля:** `item_id` (PK), `related_cottage_id` (FK), `item_category_id` (FK), `item_name`, `floor`.  
* **Связи:** принадлежит одному коттеджу и одной категории.

### `clients`
* **Назначение:** персональные и контактные данные клиентов.  
* **Поля:** `client_id` (PK), `client_name`, `client_surname`, `client_patronymic`, `passport` (UQ), `phone` (UQ), `email` (UQ).  
* **Связь:** 1 → N `rent_orders`.

### `rent_orders`
* **Назначение:** факты бронирования коттеджей.  
* **Поля:** `rent_id` (PK), `cottage_id` (FK, nullable), `client_id` (FK), `date_from`, `date_to`, `total_cost`, `paid`.  
  Уникальные пары (`cottage_id`, `date_from`) и (`cottage_id`, `date_to`) не дают пересекаться броням.  
* **Связи:** ссылка на коттедж и обязательная ссылка на клиента (`CASCADE`).

## Таблица связей

| От | К | Кратность | Семантика |
|---|---|---|---|
| `cottage` | `interior` | 0..N | Коттедж содержит предметы |
| `interior_item_categories` | `interior` | 1 — N | Категория группирует предметы |
| `cottage` | `rent_orders` | 0..N | Коттедж фигурирует в заказах |
| `clients` | `rent_orders` | 1 — N | Клиент оформляет заказы |
