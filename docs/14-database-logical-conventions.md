# Convenções do Modelo Lógico

## Status

Aprovado inicialmente para o MVP.

Estas convenções orientarão o modelo lógico, o Entity Framework Core e as migrations iniciais.

Poderão ser revisadas antes da primeira migration compartilhada. Depois disso, alterações deverão considerar compatibilidade e migração dos dados existentes.

## Objetivo

Definir um padrão único para:

- schemas;
- tabelas;
- colunas;
- identificadores;
- relacionamentos;
- datas;
- valores econômicos;
- estados;
- concorrência;
- exclusão;
- índices;
- constraints;
- dados históricos.

## Idioma

Todos os nomes técnicos serão escritos em inglês.

Isso inclui:

- schemas;
- tabelas;
- colunas;
- entidades;
- propriedades;
- índices;
- constraints;
- migrations;
- chaves do Redis.

Português poderá aparecer apenas em conteúdo apresentado ao jogador ou em documentação explicativa.

## Schemas do PostgreSQL

O banco será organizado inicialmente pelos seguintes schemas:

- `identity`;
- `gameplay`;
- `summons`;
- `inventory`;
- `dungeons`;
- `matches`;
- `competition`;
- `economy`;
- `audit`.

### Responsabilidades

`identity` armazenará contas, credenciais, restrições e dados privados de acesso.

`gameplay` armazenará Commanders, perfis e progressão geral.

`summons` armazenará definições, instâncias, evolução, vínculos e histórico das Summon Cards.

`inventory` armazenará inventários, definições de itens, pilhas, instâncias e movimentações.

`dungeons` armazenará definições, versões, execuções, participantes, resultados e recompensas relacionadas.

`matches` armazenará partidas, participantes e resultados permanentes.

`competition` armazenará temporadas, ranking e classificação histórica.

`economy` armazenará moedas, Wallets, movimentações, anúncios e transações.

`audit` armazenará auditoria e idempotência.

Novos schemas somente serão adicionados quando um novo domínio persistente justificar a separação.

## Nomes físicos

Tabelas utilizarão nomes plurais em `snake_case`.

Exemplos:

- `identity.accounts`;
- `gameplay.commanders`;
- `summons.summon_cards`;
- `dungeons.dungeon_runs`;
- `economy.marketplace_listings`.

Colunas utilizarão `snake_case`.

Exemplos:

- `created_at`;
- `commander_id`;
- `current_owner_id`;
- `idempotency_key`.

Classes e propriedades C# utilizarão `PascalCase`.

Exemplos:

- `Commander`;
- `SummonCard`;
- `CreatedAt`;
- `CurrentOwnerId`.

## Identificadores

Entidades persistentes com identidade própria utilizarão UUID.

A geração dos identificadores será responsabilidade do backend.

O cliente poderá receber identificadores, mas nunca poderá utilizá-los como prova de autorização ou propriedade.

A chave primária padrão será:

```text
id
