# Fluxo de Persistência

## Objetivo

Definir como o cliente, o backend, o PostgreSQL e o Redis participam das operações de leitura, escrita, economia, partidas e auditoria em Umbra Blackvale.

## Visão geral

```mermaid
flowchart LR
    Client["Godot Client"]
    Api["ASP.NET Core API"]
    Application["Application Layer"]
    Domain["Domain Layer"]
    Infrastructure["Infrastructure Layer"]
    PostgreSQL[("PostgreSQL")]
    Redis[("Redis")]

    Client -->|"Requests actions"| Api
    Api --> Application
    Application --> Domain
    Domain --> Application
    Application --> Infrastructure

    Infrastructure -->|"Permanent data"| PostgreSQL
    Infrastructure -->|"Temporary data and cache"| Redis

    PostgreSQL --> Infrastructure
    Redis --> Infrastructure
    Infrastructure --> Application
    Application --> Api
    Api -->|"Authoritative response"| Client
```

## Regra de autoridade

O cliente solicita ações.

O backend valida:

- identidade;
- autorização;
- estado atual;
- regras do domínio;
- disponibilidade dos recursos;
- integridade da operação.

Somente o servidor confirma:

- dano;
- crítico;
- loot;
- experiência;
- recompensas;
- propriedade;
- economia;
- resultados;
- progressão.

## Fluxo de leitura

```mermaid
sequenceDiagram
    participant Client
    participant Backend
    participant Redis
    participant PostgreSQL

    Client->>Backend: Requests data
    Backend->>Backend: Validates identity and permission
    Backend->>Redis: Checks cached data

    alt Cache found
        Redis-->>Backend: Returns cached data
    else Cache not found
        Backend->>PostgreSQL: Queries permanent data
        PostgreSQL-->>Backend: Returns permanent data
        Backend->>Redis: Stores temporary cache with expiration
    end

    Backend-->>Client: Returns authorized response
```

## Regras do fluxo de leitura

- O cache nunca substitui a fonte permanente.
- Dados sensíveis deverão ser filtrados antes da resposta.
- O jogador somente poderá acessar dados autorizados.
- Dados armazenados no Redis deverão possuir expiração quando aplicável.
- A ausência do Redis não poderá apagar ou invalidar dados permanentes.
- Consultas frequentes deverão ser avaliadas para cache somente quando houver benefício real.

## Fluxo de escrita permanente

```mermaid
sequenceDiagram
    participant Client
    participant Backend
    participant PostgreSQL
    participant Redis

    Client->>Backend: Requests state change
    Backend->>Backend: Authenticates and authorizes
    Backend->>Backend: Validates domain rules
    Backend->>PostgreSQL: Begins transaction
    Backend->>PostgreSQL: Reads and locks required state
    Backend->>PostgreSQL: Persists changes
    Backend->>PostgreSQL: Persists audit record
    Backend->>PostgreSQL: Commits transaction
    PostgreSQL-->>Backend: Confirms success
    Backend->>Redis: Invalidates or updates related cache
    Backend-->>Client: Returns authoritative result
```

## Regras do fluxo de escrita

- Toda alteração permanente deverá passar pelo backend.
- Operações críticas deverão utilizar transação.
- Dados relacionados deverão ser alterados como uma única unidade.
- Falhas antes do commit deverão cancelar toda a operação.
- A resposta ao cliente somente deverá confirmar sucesso após o commit.
- O cache relacionado deverá ser invalidado ou atualizado após a persistência.
- A falha do Redis não poderá desfazer silenciosamente uma operação já confirmada no PostgreSQL.
- Operações repetíveis deverão utilizar mecanismos de idempotência.

## Fluxo econômico

```mermaid
sequenceDiagram
    participant Client
    participant Backend
    participant PostgreSQL
    participant Redis

    Client->>Backend: Requests purchase, sale or transfer
    Backend->>Backend: Validates identity and idempotency
    Backend->>PostgreSQL: Begins transaction
    Backend->>PostgreSQL: Locks balances, items and ownership
    Backend->>PostgreSQL: Validates current economic state

    alt Operation valid
        Backend->>PostgreSQL: Applies debit and credit
        Backend->>PostgreSQL: Transfers ownership
        Backend->>PostgreSQL: Creates transaction history
        Backend->>PostgreSQL: Creates audit record
        Backend->>PostgreSQL: Commits transaction
        Backend->>Redis: Invalidates related cache
        Backend-->>Client: Confirms authoritative result
    else Operation invalid
        Backend->>PostgreSQL: Rolls back transaction
        Backend-->>Client: Returns rejected operation
    end
```

## Regras do fluxo econômico

- Débito, crédito e transferência de propriedade deverão ocorrer na mesma transação.
- Nenhum item poderá possuir dois proprietários simultaneamente.
- Nenhum saldo poderá ser alterado sem histórico correspondente.
- Operações duplicadas deverão ser detectadas.
- Valores monetários não utilizarão ponto flutuante.
- Toda movimentação relevante deverá possuir origem, destino, motivo e data.
- Falhas parciais não poderão gerar duplicação ou perda de patrimônio.

## Fluxo de salas e partidas

```mermaid
flowchart LR
    ClientA["Player A"]
    ClientB["Player B"]
    SignalR["SignalR"]
    GameServer["Authoritative Game Server"]
    Redis[("Redis")]
    PostgreSQL[("PostgreSQL")]

    ClientA --> SignalR
    ClientB --> SignalR
    SignalR --> GameServer

    GameServer -->|"Room and temporary match state"| Redis
    Redis --> GameServer

    GameServer -->|"Final result, rewards and progression"| PostgreSQL
    PostgreSQL --> GameServer

    GameServer --> SignalR
    SignalR --> ClientA
    SignalR --> ClientB
```

## Regras do fluxo de partidas

- Redis poderá armazenar o estado temporário da sala e da partida.
- O servidor continuará sendo responsável pelo cálculo autoritativo.
- O cliente não poderá enviar resultados finais.
- Resultados relevantes deverão ser persistidos no PostgreSQL.
- Recompensas somente serão concedidas após validação do resultado.
- Desconexões não deverão permitir duplicação de recompensas.
- O encerramento da partida deverá possuir uma operação idempotente.
- Estados temporários abandonados deverão possuir expiração.

## Fluxo de ranking

```mermaid
sequenceDiagram
    participant Backend
    participant PostgreSQL
    participant Redis
    participant Client

    Backend->>PostgreSQL: Persists ranking-affecting result
    PostgreSQL-->>Backend: Confirms permanent result
    Backend->>Redis: Updates or invalidates ranking cache
    Client->>Backend: Requests ranking
    Backend->>Redis: Reads fast ranking view

    alt Ranking cache available
        Redis-->>Backend: Returns cached ranking
    else Ranking cache unavailable
        Backend->>PostgreSQL: Rebuilds ranking view
        PostgreSQL-->>Backend: Returns permanent ranking data
        Backend->>Redis: Recreates ranking cache
    end

    Backend-->>Client: Returns ranking
```

## Fluxo de auditoria

Operações críticas deverão produzir registros de auditoria dentro da mesma transação dos dados alterados sempre que a consistência exigir.

```mermaid
flowchart LR
    Request["Critical Request"]
    Validation["Authorization and Domain Validation"]
    Transaction["Database Transaction"]
    StateChange["Permanent State Change"]
    AuditRecord["Audit Record"]
    Commit["Commit"]
    Response["Authoritative Response"]

    Request --> Validation
    Validation --> Transaction
    Transaction --> StateChange
    Transaction --> AuditRecord
    StateChange --> Commit
    AuditRecord --> Commit
    Commit --> Response
```

## Tratamento de falhas

### Falha antes do commit

- A transação deverá ser revertida.
- Nenhum resultado de sucesso será enviado ao cliente.
- Nenhuma recompensa será considerada concedida.
- A mesma solicitação poderá ser repetida com segurança quando houver idempotência.

### Falha depois do commit

- O dado permanente continuará válido.
- O sistema não deverá repetir a operação econômica.
- Atualizações de cache ou notificações poderão ser refeitas.
- O backend deverá recuperar o resultado pela chave de idempotência quando aplicável.

### Falha do Redis

- O PostgreSQL continuará sendo a fonte de verdade.
- O cache poderá ser reconstruído.
- Estados temporários poderão expirar.
- O progresso permanente não poderá ser perdido.
- Recursos dependentes do Redis poderão ficar temporariamente indisponíveis sem corromper os dados permanentes.

### Falha do PostgreSQL

- Operações permanentes não serão confirmadas.
- Economia, propriedade, progressão e recompensas deverão permanecer bloqueadas.
- O backend não poderá utilizar o Redis como substituto permanente.
- O cliente receberá uma resposta controlada de indisponibilidade.

## Critério de conclusão

O fluxo de persistência será considerado documentado quando:

1. Leituras diferenciarem cache de fonte permanente.
2. Escritas permanentes utilizarem autoridade do servidor.
3. Operações econômicas utilizarem transações.
4. Auditoria estiver vinculada às operações críticas.
5. Redis puder falhar sem apagar progresso permanente.
6. Resultados de partidas forem persistidos pelo servidor.
7. Operações repetidas não gerarem recompensas ou transações duplicadas.