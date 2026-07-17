# Decisões Iniciais do Banco para o MVP

## Status

Aprovado provisoriamente para o MVP.

Estas decisões orientam o primeiro modelo lógico e físico do banco de dados.

Elas poderão ser revisadas futuramente, desde que:

1. exista uma necessidade real de produto ou arquitetura;
2. o impacto seja analisado;
3. a documentação seja atualizada antes do código;
4. migrations e dados existentes sejam considerados;
5. a alteração não comprometa integridade, economia ou progressão.

## Objetivo

Reduzir ambiguidades antes da criação das entidades, relacionamentos, constraints e migrations iniciais.

Estas decisões não representam limites permanentes do universo de Umbra Blackvale.

Representam o menor modelo sólido necessário para o MVP.

## Decisões aprovadas

### Account e Commander

Uma `Account` possuirá inicialmente exatamente um `Commander`.

Motivos:

- simplificar identidade jogável;
- impedir manipulação de ranking por personagens alternativos;
- reduzir abuso de recompensas;
- reduzir complexidade inicial de mercado;
- manter o escopo do MVP controlado.

A arquitetura deverá evitar acoplamentos que impeçam múltiplos Commanders no futuro.

### Inventory

Cada `Commander` possuirá um único `Inventory`.

O Inventory será responsável por organizar os ativos pertencentes ao Commander.

Itens individualizados, itens empilháveis e Summon Cards poderão possuir modelos internos diferentes.

### Summon Card

Cada `SummonCard` possuirá um proprietário atual do tipo `Commander`.

Mudanças de propriedade serão registradas separadamente em `SummonCardOwnershipHistory`.

O proprietário atual não substituirá o histórico de proprietários anteriores.

### Wallet

Cada `Account` possuirá uma única `Wallet`.

Os saldos econômicos pertencerão à Account.

O Commander poderá aparecer publicamente como comprador, vendedor ou participante de uma operação, mas a responsabilidade financeira permanecerá vinculada à Account.

Esta decisão reduz o risco de duplicação econômica caso múltiplos Commanders sejam permitidos futuramente.

### Ranking

O ranking pertencerá ao `Commander`.

Cada `RankingEntry` representará um Commander em uma temporada e categoria específicas.

A Account não aparecerá diretamente no ranking público.

### Participantes históricos

`DungeonParticipant` e `MatchParticipant` manterão referência ao Commander participante.

Também será preservado um snapshot mínimo das informações necessárias para leitura histórica.

O snapshot poderá incluir:

- nome público do Commander;
- título exibido;
- equipe ou lado;
- configuração relevante;
- versão das regras utilizadas.

Alterações futuras no perfil do Commander não deverão modificar silenciosamente registros históricos.

### Credenciais

Uma `Account` poderá possuir várias `AccountCredentials`.

Isso permitirá futuramente:

- autenticação local;
- provedores externos;
- recuperação de acesso;
- migração de método de login;
- múltiplas formas de autenticação autorizadas.

Nenhuma credencial armazenará senha em texto simples.

### Marketplace inicial

O primeiro Marketplace utilizará anúncios com preço fixo.

O modelo inicial deverá suportar:

- vendedor;
- ativo anunciado;
- preço;
- moeda;
- quantidade;
- estado;
- início;
- expiração;
- comprador;
- taxas;
- transação concluída.

Permanecem fora do primeiro modelo físico:

- leilões;
- lances;
- trocas diretas;
- ofertas parciais;
- contratos econômicos avançados.

Esses recursos somente serão adicionados após documentação própria.

### Sistemas fora do primeiro modelo físico

Não entrarão no primeiro modelo físico:

- guildas;
- guerras de guildas;
- chat persistente;
- rede social completa;
- múltiplos Commanders por Account;
- habitação;
- leilões;
- trocas diretas;
- eventos mundiais;
- múltiplas regiões de servidor.

A ausência desses sistemas no MVP não impede sua implementação futura.

### Divisão de propriedade dos dados

Pertencem à `Account`:

- autenticação;
- credenciais;
- segurança;
- restrições;
- sessões;
- Wallet;
- registros administrativos relacionados à conta.

Pertencem ao `Commander`:

- perfil público;
- progressão;
- Inventory;
- Summon Cards;
- equipamentos;
- participação em Dungeons;
- participação em Matches;
- Ranking Entries;
- reputação;
- fama;
- estado persistente de gameplay.

## Resumo de cardinalidades iniciais

```mermaid
erDiagram
    ACCOUNT ||--|| COMMANDER : controls
    ACCOUNT ||--|| WALLET : owns
    ACCOUNT ||--o{ ACCOUNT_CREDENTIAL : authenticates_with

    COMMANDER ||--|| INVENTORY : owns
    COMMANDER ||--o{ SUMMON_CARD : owns
    COMMANDER ||--o{ RANKING_ENTRY : receives
    COMMANDER ||--o{ DUNGEON_PARTICIPANT : participates
    COMMANDER ||--o{ MATCH_PARTICIPANT : participates

    SUMMON_CARD ||--o{ SUMMON_CARD_OWNERSHIP_HISTORY : records
