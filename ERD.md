# Database Schema — Ticket QR Code Generator Worker

## Entity Relationship Diagram

```mermaid
erDiagram
    USER ||--o{ TICKET : creates
    TICKET ||--|| QR_CODE : has

    USER {
        UUID id PK
        VARCHAR_100 name
        VARCHAR_255 email UK
        VARCHAR_50 role
        TIMESTAMP createdAt
        TIMESTAMP updatedAt
    }

    TICKET {
        UUID id PK
        VARCHAR_50 ticketNumber UK
        VARCHAR_100 customerName
        VARCHAR_30 status
        UUID createdBy FK
        TIMESTAMP createdAt
        TIMESTAMP updatedAt
    }

    QR_CODE {
        UUID id PK
        UUID ticketId FK
        TEXT qrValue
        VARCHAR_30 status
        TIMESTAMP createdAt
        TIMESTAMP updatedAt
    }```

### User

| Field     | Type         | Constraints      |
| --------- | ------------ | ---------------- |
| id        | UUID         | Primary Key      |
| name      | VARCHAR(100) | Required         |
| email     | VARCHAR(255) | Required, Unique |
| role      | VARCHAR(50)  | Required         |
| createdAt | TIMESTAMP    | Required         |
| updatedAt | TIMESTAMP    | Required         |

### Ticket

| Field        | Type         | Constraints           |
| ------------ | ------------ | --------------------- |
| id           | UUID         | Primary Key           |
| ticketNumber | VARCHAR(50)  | Required, Unique      |
| customerName | VARCHAR(100) | Required              |
| status       | VARCHAR(30)  | Required              |
| createdBy    | UUID         | Foreign Key → User.id |
| createdAt    | TIMESTAMP    | Required              |
| updatedAt    | TIMESTAMP    | Required              |

### QR Code

| Field     | Type        | Constraints                     |
| --------- | ----------- | ------------------------------- |
| id        | UUID        | Primary Key                     |
| ticketId  | UUID        | Foreign Key → Ticket.id, Unique |
| qrValue   | TEXT        | Required                        |
| status    | VARCHAR(30) | Required                        |
| createdAt | TIMESTAMP   | Required                        |
| updatedAt | TIMESTAMP   | Required                        |

## Relationships

```text
User 1 ─────────── N Ticket

Ticket 1 ───────── 1 QRCode
```

## Design Notes

* UUIDs are used as primary identifiers.
* Ticket numbers are unique.
* Each ticket can have one associated QR code.
* A ticket records which staff member created it.
* Timestamps are maintained for auditing and operational tracking.
* No real customer or staff data should be committed to source control.
