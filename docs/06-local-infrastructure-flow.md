# Fluxo da Infraestrutura Local

## Visão geral

```mermaid
flowchart LR
    Developer[Developer]
    Backend[ASP.NET Core Backend]
    PostgreSQL[(PostgreSQL)]
    Redis[(Redis)]
    Docker[Docker Compose]
    PostgresVolume[(PostgreSQL Volume)]
    RedisVolume[(Redis Volume)]

    Developer --> Docker
    Docker --> Backend
    Docker --> PostgreSQL
    Docker --> Redis

    Backend -->|Persistent data| PostgreSQL
    Backend -->|Cache and temporary state| Redis

    PostgreSQL --> PostgresVolume
    Redis --> RedisVolume
```

## Inicialização dos serviços

```mermaid
sequenceDiagram
    participant Developer
    participant Docker as Docker Compose
    participant PostgreSQL
    participant Redis
    participant Backend

    Developer->>Docker: Starts local infrastructure
    Docker->>PostgreSQL: Creates and starts container
    Docker->>Redis: Creates and starts container

    PostgreSQL-->>Docker: Healthcheck successful
    Redis-->>Docker: Healthcheck successful

    Docker->>Backend: Allows backend startup
    Backend->>PostgreSQL: Validates database connection
    Backend->>Redis: Validates cache connection

    PostgreSQL-->>Backend: Connection available
    Redis-->>Backend: Connection available
```

## Regra de inicialização

O backend somente poderá utilizar PostgreSQL e Redis depois que os respectivos healthchecks indicarem que os serviços estão disponíveis.

## Responsabilidades

### PostgreSQL

Armazena dados permanentes, como:

- contas;
- Comandantes;
- Cartas de Invocação;
- inventários;
- progresso;
- economia;
- histórico de transações;
- resultados persistentes.

### Redis

Armazena dados temporários ou de acesso rápido, como:

- sessões;
- cache;
- matchmaking;
- estado temporário de partidas;
- notificações;
- filas;
- ranking de leitura rápida.

### Docker Compose

Responsável por:

- criar os containers;
- configurar a rede interna;
- criar volumes;
- aplicar variáveis de ambiente;
- verificar a saúde dos serviços;
- permitir inicialização reproduzível.