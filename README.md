# 📦 Cottage Rentals Database — Entities & Relationships

## Entity‑Relationship (ER) diagram

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
        INT related_cottage_id FK ➜ COTTAGE.cottage_id
        INT item_category_id FK ➜ INTERIOR_ITEM_CATEGORIES.item_category_id
        VARCHAR item_name
        INT floor
    }

    CLIENTS {
        INT client_id PK
        VARCHAR client_name
        VARCHAR client_surname
        VARCHAR client_patronymic
        VARCHAR passport  (unique)
        VARCHAR phone     (unique)
        VARCHAR email     (unique)
    }

    RENT_ORDERS {
        INT rent_id PK
        INT cottage_id   FK ➜ COTTAGE.cottage_id   (nullable)
        INT client_id    FK ➜ CLIENTS.client_id
        DATE date_from
        DATE date_to
        INT total_cost
        BOOL paid
    }

    %% Relationships
    COTTAGE           ||--o{ INTERIOR             : "has interior"
    INTERIOR_ITEM_CATEGORIES ||--o{ INTERIOR      : "categorises"
    COTTAGE           |o--o{ RENT_ORDERS          : "is rented in"
    CLIENTS           ||--o{ RENT_ORDERS          : "places"
