# Fluxograma Geral do Sistema

## Componentes principais

```mermaid
flowchart LR
    Player[Player]
    Game[Godot Client]
    API[ASP.NET Core API]
    Realtime[SignalR]
    Application[Application Layer]
    Domain[Domain Layer]
    Infrastructure[Infrastructure Layer]
    PostgreSQL[(PostgreSQL)]
    Redis[(Redis)]

    Player --> Game

    Game -->|REST| API
    Game <-->|Real-time communication| Realtime

    API --> Application
    Realtime --> Application

    Application --> Domain
    Application --> Infrastructure

    Infrastructure --> PostgreSQL
    Infrastructure --> Redis
```

## Regra de autoridade

O cliente envia intenções.

O servidor valida regras, estado, permissões e resultados.

O cliente apresenta somente informações autorizadas pelo servidor.

## Fluxo de uma ação crítica

```mermaid
sequenceDiagram
    participant Player
    participant Game as Godot Client
    participant Server as Authoritative Server
    participant Domain as Domain Rules
    participant Database as PostgreSQL
    participant Cache as Redis

    Player->>Game: Selects action
    Game->>Server: Sends action request
    Server->>Domain: Validates rules and state
    Domain-->>Server: Returns authorized result
    Server->>Database: Persists permanent state
    Server->>Cache: Updates temporary state
    Server-->>Game: Returns official result
    Game-->>Player: Displays feedback
```
