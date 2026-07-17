# Validação da Infraestrutura Local

## Data da validação

17 de julho de 2026.

## Ambiente validado

- Docker Desktop com backend WSL 2.
- Docker Compose.
- PostgreSQL 17.10 Alpine.
- Redis 8.6.4 Alpine.
- Containers executados em rede Docker dedicada.
- Volumes nomeados para persistência.

## Serviços

| Serviço | Container | Endereço local | Status |
|---|---|---|---|
| PostgreSQL | `umbra-blackvale-postgres` | `127.0.0.1:5433` | Healthy |
| Redis | `umbra-blackvale-redis` | `127.0.0.1:6379` | Healthy |

## Observação sobre a porta do PostgreSQL

A porta padrão `5432` estava indisponível na máquina de desenvolvimento.

O arquivo local `.env` utiliza:

```env
POSTGRES_PORT=5433