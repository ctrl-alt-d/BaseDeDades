# Repàs

## Personatges valorant

```erDiagram
    AGENT ||--o{ ABILITY : has
    ABILITY }o--|| ABILITY_TYPE : is

    AGENT {
        int id PK
        string name
        string role
        string country
    }

    ABILITY {
        int id PK
        string name
        string description
        int agent_id FK
        int type_id FK
    }

    ABILITY_TYPE {
        int id PK
        string name
    }
```