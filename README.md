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
    COTTAGE ||--o{ INTERIOR             : has_interior
    INTERIOR_ITEM_CATEGORIES ||--o{ INTERIOR : categorises
    COTTAGE |o--o{ RENT_ORDERS          : rented_in
    CLIENTS ||--o{ RENT_ORDERS          : places```


## Описание схемы словами

### 1. `cottage`
* **Назначение:** хранит сведения о коттеджах, предлагаемых в аренду.  
* **Ключевые поля:**  
  * `cottage_id` — первичный ключ (PK, `serial`).  
  * `address` — полный адрес.  
  * `floors_cnt`, `rooms_cnt`, `total_area` — числовые характеристики здания, каждая > 0.  
  * `price_for_day` — стоимость суток аренды > 0.  
  * `available` — флаг доступности (по умолчанию `true`).  
* **Связи:**  
  * «1:N» с `interior` (`cottage_id` → `related_cottage_id`) — коттедж содержит множество предметов интерьера.  
  * «0..N» с `rent_orders` (`cottage_id`) — коттедж может фигурировать в нескольких заказах аренды; при удалении коттеджа ссылка в заказах обнуляется (`ON DELETE SET NULL`).

---

### 2. `interior_item_categories`
* **Назначение:** справочник категорий предметов интерьера (мебель, техника и т.д.).  
* **Ключевые поля:**  
  * `item_category_id` — PK.  
  * `category_name` — название категории.  
* **Связи:**  
  * «1:N» с `interior` (`item_category_id`) — одна категория включает множество предметов.

---

### 3. `interior`
* **Назначение:** описывает каждый предмет внутри коттеджа.  
* **Ключевые поля:**  
  * `item_id` — PK (целое, не `serial` — подразумевается внешняя генерация).  
  * `related_cottage_id` — FK на `cottage`.  
  * `item_category_id` — FK на `interior_item_categories`.  
  * `item_name` — наименование предмета.  
  * `floor` — этаж (> 0), где расположен предмет.  
* **Связи:**  
  * Каждый предмет принадлежит ровно одному коттеджу и одной категории.

---

### 4. `clients`
* **Назначение:** хранит персональные и контактные данные клиентов.  
* **Ключевые поля:**  
  * `client_id` — PK.  
  * `client_name`, `client_surname`, `client_patronymic` — ФИО.  
  * `passport` — серия+номер паспорта (уникально).  
  * `phone`, `email` — контакты (уникальны).  
* **Связи:**  
  * «1:N» с `rent_orders` — один клиент может оформить несколько заказов; при удалении клиента все его заказы удаляются каскадно (`ON DELETE CASCADE`).

---

### 5. `rent_orders`
* **Назначение:** фиксирует факты бронирования коттеджей.  
* **Ключевые поля:**  
  * `rent_id` — PK.  
  * `cottage_id` — FK на `cottage` (допускается `NULL`).  
  * `client_id` — FK на `clients` (не `NULL`).  
  * `date_from`, `date_to` — период аренды (`date_to` > `date_from`).  
  * `total_cost` — суммарная стоимость периода (> 0).  
  * `paid` — оплачен / не оплачен.  
  * Уникальные пары `(cottage_id, date_from)` и `(cottage_id, date_to)` предотвращают пересечение бронирований в один день.  
* **Связи:**  
  * Ссылается на коттедж (может обнулиться) и на клиента (обязательна).  

---

### Общее представление связей
| От сущности | К сущности | Тип связи | Семантика |
|-------------|-----------|-----------|-----------|
| `cottage` | `interior` | 1 — N | Коттедж содержит предметы интерьера |
| `interior_item_categories` | `interior` | 1 — N | Категория объединяет множество предметов |
| `cottage` | `rent_orders` | 0..N | Коттедж может иметь много заказов, но запись заказа возможна даже при удалённом коттедже |
| `clients` | `rent_orders` | 1 — N | Клиент оформляет заказы; при удалении клиента заказы удаляются |

Эта текстовая сводка дополняет Mermaid‑диаграмму: прочитав оба раздела, можно быстро понять структуру базы и бизнес‑логику сервиса краткосрочной аренды коттеджей.
