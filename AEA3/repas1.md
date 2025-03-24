# Repàs

## Personatges valorant

```mermaid
graph TD;

    AGENT[AGENT]
    ABILITY[ABILITY]
    ABILITY_TYPE[ABILITY_TYPE]

    AGENT -->|1:N| ABILITY
    ABILITY -->|N:1| ABILITY_TYPE

    AGENT_ID[+id : int<br>name : string<br>role : string<br>country : string]
    ABILITY_ID[+id : int<br>name : string<br>description : text<br>agent_id : int (FK)<br>type_id : int (FK)]
    ABILITY_TYPE_ID[+id : int<br>name : string]

    AGENT --> AGENT_ID
    ABILITY --> ABILITY_ID
    ABILITY_TYPE --> ABILITY_TYPE_ID

```