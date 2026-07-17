# Infraestrutura Local

## Objetivo

Definir o ambiente local de persistência e cache usado por Umbra Blackvale.

## Serviços

- PostgreSQL para persistência permanente.
- Redis para cache, sessões, matchmaking e ranking.
- Docker Compose para orquestração local.

## Regras

- PostgreSQL e Redis serão executados em containers Linux.
- Os dados persistentes utilizarão volumes nomeados.
- Os serviços compartilharão uma rede Docker interna.
- Portas locais serão vinculadas somente ao endereço 127.0.0.1.
- Credenciais serão carregadas por variáveis de ambiente.
- Arquivos com segredos reais nunca serão versionados.
- Cada serviço deverá possuir healthcheck.
- O backend não poderá iniciar dependências críticas sem validar a disponibilidade dos serviços.

## Arquivos previstos

- compose.yaml
- .env.example
- Arquivos de configuração do backend em etapa posterior.

## Serviços locais

| Serviço | Porta padrão | Persistência |
|---|---:|---|
| PostgreSQL | 5432 | Volume nomeado |
| Redis | 6379 | Volume nomeado |
