# PNX Load Board - Architecture Diagram

## System Architecture Overview

```mermaid
graph TB
    subgraph "Frontend Layer"
        UI[React TypeScript App]
        UI --> |HTTP/HTTPS| API
    end
    
    subgraph "Backend Layer"
        API[FastAPI Server]
        AUTH[JWT Authentication]
        WORKER[Background Workers]
        
        API --> AUTH
        API --> WORKER
    end
    
    subgraph "Data Layer"
        DB[(PostgreSQL Database)]
        CACHE[(Redis Cache)]
        
        API --> DB
        API --> CACHE
        WORKER --> DB
    end
    
    subgraph "External Services"
        LB1[DAT Load Board]
        LB2[Truckstop.com]        
        LB3[Other Load Boards]
        EMAIL[Email Service]
        
        WORKER --> LB1
        WORKER --> LB2
        WORKER --> LB3
        API --> EMAIL
    end
    
    subgraph "Local Infrastructure"
        SERVER[Local Server]
        BACKUP[Backup Storage]
        LOGS[Log Files]
        
        API --> LOGS
        DB --> BACKUP
    end
    
    style UI fill:#e1f5fe
    style API fill:#f3e5f5
    style DB fill:#e8f5e8
    style CACHE fill:#fff3e0
    style SERVER fill:#f5f5f5
```

## Database Schema (ERD)

```mermaid
erDiagram
    USERS {
        int id PK
        string username UK
        string email UK
        string password_hash
        string first_name
        string last_name
        string role
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }
      LOADS {
        int id PK
        string load_number UK
        boolean is_bid
        string origin_city
        string origin_state
        string origin_zip
        string origin_address
        string destination_city
        string destination_state
        string destination_zip
        string destination_address
        decimal rate
        string equipment_type
        decimal weight
        decimal distance
        text description
        timestamp pickup_date
        timestamp delivery_date
        timestamp posted_at
        string broker_name
        string broker_email
        string broker_phone
        string mc_number
        string load_board_source
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }
    
    BID_LOADS {
        int id PK
        int load_id FK
        int user_id FK
        decimal bid_amount
        text notes
        string status
        string user_name
        string user_email
        string user_phone
        timestamp created_at
        timestamp updated_at
    }
    
    USER_PREFERENCES {
        int id PK
        int user_id FK
        json filter_preferences
        json notification_settings
        timestamp created_at
        timestamp updated_at
    }
      NOTIFICATIONS {
        int id PK
        int user_id FK
        string type
        string title
        text message
        boolean is_read
        timestamp created_at
    }    USERS ||--o{ BID_LOADS : "places"
    USERS ||--o{ USER_PREFERENCES : "has"
    USERS ||--o{ NOTIFICATIONS : "receives"
    
    LOADS ||--o{ BID_LOADS : "receives"
```