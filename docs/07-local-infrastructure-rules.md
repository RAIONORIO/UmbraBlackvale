# Regras da Infraestrutura Local

## Segurança

- Credenciais reais nunca serão versionadas.
- O arquivo `.env` permanecerá ignorado pelo Git.
- O repositório conterá apenas `.env.example`, sem valores sensíveis.
- PostgreSQL e Redis serão expostos somente em `127.0.0.1`.
- O backend nunca confiará em dados enviados diretamente pelo cliente.
- Operações econômicas deverão ser atômicas e auditáveis.

## PostgreSQL

- Será utilizado para armazenar dados permanentes.
- Utilizará volume Docker nomeado.
- O schema será gerenciado pelo Entity Framework Core.
- Alterações estruturais serão realizadas somente por migrations versionadas.
- O banco não será alterado manualmente para corrigir regras de negócio.
- Identificadores internos utilizarão UUID quando apropriado.
- Datas serão armazenadas em UTC.
- Exclusões lógicas serão avaliadas para entidades com histórico relevante.

## Redis

- Será utilizado somente para dados temporários, derivados ou de acesso rápido.
- Redis não será a única fonte de verdade de informações permanentes.
- A perda do conteúdo do Redis não poderá apagar o progresso do jogador.
- As chaves deverão seguir uma nomenclatura padronizada.
- Dados temporários deverão possuir tempo de expiração quando aplicável.
- Rankings persistentes deverão possuir respaldo no PostgreSQL.

## Docker Compose

- Cada serviço deverá possuir nome claro e estável.
- Cada serviço deverá possuir healthcheck.
- Serviços dependentes deverão aguardar a saúde das dependências.
- Volumes e redes deverão possuir nomes identificáveis.
- As versões das imagens deverão ser fixadas.
- A tag `latest` não será utilizada.
- Containers não deverão executar com privilégios desnecessários.

## Portas locais

- PostgreSQL: `127.0.0.1:5432`.
- Redis: `127.0.0.1:6379`.
- As portas poderão ser alteradas por variáveis de ambiente quando houver conflito local.

## Inicialização

A infraestrutura deverá permitir:

```bash
docker compose up -d