# Fundação do Banco de Dados

## Objetivo

Definir os princípios de persistência de Umbra Blackvale antes da criação de tabelas, entidades do Entity Framework Core, migrations ou repositórios.

O banco de dados deverá sustentar o MVP online por salas e Dungeons e permitir a expansão futura do jogo sem reescrita dos fundamentos de persistência.

## Tecnologias oficiais

- PostgreSQL como banco de dados relacional e fonte permanente de verdade.
- Entity Framework Core como ORM.
- Redis como cache e armazenamento temporário.
- Migrations versionadas para alterações estruturais.
- Docker Compose para execução local dos serviços.

## Autoridade dos dados

O servidor é autoritativo.

Somente o backend poderá confirmar ou alterar informações críticas, como:

- experiência;
- progressão;
- recompensas;
- loot;
- inventário;
- propriedade de Cartas;
- moedas;
- transações;
- ranking;
- resultados de Dungeons;
- resultados de partidas;
- transferências entre jogadores.

O cliente poderá solicitar ações, mas nunca determinar seus resultados.

## Fonte de verdade

### PostgreSQL

O PostgreSQL será a fonte de verdade para todos os dados permanentes.

A perda do Redis não poderá causar perda de:

- progresso;
- inventário;
- propriedade;
- moedas;
- ranking persistente;
- resultados;
- histórico econômico;
- vínculos com Invocações.

### Redis

O Redis será utilizado para informações temporárias, derivadas ou de leitura rápida, como:

- sessões;
- cache;
- matchmaking;
- filas;
- notificações temporárias;
- presença online;
- estado temporário de salas;
- estado temporário de partidas;
- ranking de leitura rápida;
- bloqueios distribuídos;
- controle temporário de requisições.

Dados armazenados apenas no Redis deverão poder ser reconstruídos ou descartados sem comprometer a integridade permanente do jogo.

## Domínios persistentes iniciais

### Accounts

Responsável por:

- identidade da conta;
- autenticação;
- credenciais protegidas;
- status da conta;
- permissões;
- datas de criação e acesso;
- medidas administrativas e de segurança.

### Commanders

Responsável por:

- identidade do Comandante;
- vínculo com a conta;
- nome público;
- progressão;
- fama;
- reputação;
- estado da Marca de Umbra;
- informações necessárias para ranking e gameplay.

Uma conta e um Comandante não deverão ser tratados como a mesma entidade.

### Summon Cards and Echoes

Responsável por:

- definição base de cada tipo de Eco;
- instância individual de cada Carta;
- proprietário atual;
- evolução;
- raridade;
- estado do vínculo;
- restrições de transferência;
- histórico de propriedade;
- consentimento quando exigido;
- capacidades e características persistentes.

O avatar principal de um jogador nunca poderá ser representado como uma Carta transferível.

### Inventories

Responsável por:

- Cartas pertencentes ao jogador;
- equipamentos;
- recursos;
- itens consumíveis;
- materiais;
- limites de armazenamento;
- movimentações de entrada e saída.

O inventário não será representado apenas por uma quantidade genérica. Itens com identidade, evolução, vínculo ou histórico deverão possuir instâncias próprias.

### Dungeons

Responsável por:

- definição das Dungeons;
- dificuldade;
- regras;
- condições de entrada;
- recompensas possíveis;
- chefes;
- ciclos;
- temporadas;
- execuções realizadas;
- participantes;
- resultados;
- recompensas concedidas.

A definição de uma Dungeon deverá permanecer separada de cada execução realizada pelos jogadores.

### Parties and Rooms

Responsável por:

- grupos;
- salas;
- participantes;
- convites;
- liderança;
- preparação;
- estado de entrada em Dungeon;
- associação com partidas ou execuções.

Estados transitórios poderão utilizar Redis, mas resultados relevantes deverão ser persistidos no PostgreSQL.

### Matches and Combat Results

Responsável por:

- participantes;
- configuração inicial;
- resultado final;
- duração;
- recompensas;
- penalidades;
- referência da temporada;
- dados necessários para auditoria e resolução de disputas.

O banco não precisa registrar cada animação executada pelo cliente, mas deverá preservar informações suficientes para validar o resultado autoritativo.

### Rankings and Seasons

Responsável por:

- temporadas;
- pontuações persistentes;
- posições consolidadas;
- histórico de classificação;
- recompensas de temporada;
- critérios utilizados;
- datas de início e encerramento.

O Redis poderá fornecer leitura rápida do ranking atual, mas o PostgreSQL manterá seu respaldo persistente.

### Marketplace and Economy

Responsável por:

- anúncios;
- ofertas;
- negociações;
- moedas;
- taxas;
- transferências;
- propriedade;
- histórico de transações;
- compensações;
- cancelamentos;
- auditoria econômica.

Toda operação econômica deverá ser atômica.

Nenhum item ou valor poderá ser removido de um jogador e concedido a outro em operações separadas sem proteção transacional.

### Audit

Responsável por registrar operações críticas, como:

- alterações administrativas;
- concessão de recompensas;
- movimentações econômicas;
- transferências de propriedade;
- mudanças de vínculo;
- punições;
- correções de dados;
- resultados contestáveis.

Registros de auditoria não deverão ser alterados silenciosamente.

## Categorias de dados

### Dados de catálogo

Definem conteúdos compartilhados por todos os jogadores, como:

- tipos de Carta;
- tipos de item;
- definições de Dungeon;
- habilidades;
- recompensas possíveis;
- regras de temporada.

### Dados de propriedade

Representam elementos pertencentes a uma conta ou Comandante, como:

- Cartas individuais;
- equipamentos;
- inventário;
- moedas;
- vínculos;
- progresso.

### Dados transacionais

Representam operações que alteram propriedade ou valor, como:

- compras;
- vendas;
- trocas;
- recompensas;
- consumo de recursos;
- transferências.

### Dados históricos

Preservam acontecimentos relevantes, como:

- resultados;
- posições em temporadas anteriores;
- mudanças de proprietário;
- punições;
- transações;
- alterações administrativas.

### Dados temporários

Representam estados que podem expirar ou ser reconstruídos, como:

- sessões;
- filas;
- presença;
- convites;
- cache;
- matchmaking;
- estado transitório de uma sala.

## Princípios de modelagem

- Todo código, entidade, tabela, coluna, índice e migration utilizará nomes em inglês.
- Identificadores utilizarão UUID quando apropriado.
- Datas e horários serão armazenados em UTC.
- Relacionamentos importantes possuirão chaves estrangeiras.
- Regras estruturais utilizarão constraints sempre que possível.
- Valores monetários nunca utilizarão tipos de ponto flutuante.
- Operações econômicas utilizarão transações.
- Alterações concorrentes deverão possuir controle de concorrência.
- Requisições críticas deverão ser idempotentes quando houver risco de repetição.
- Conteúdo de catálogo será separado de instâncias pertencentes aos jogadores.
- Exclusões físicas serão evitadas quando houver necessidade de histórico ou auditoria.
- Dados derivados não substituirão os dados originais que permitiram seu cálculo.
- Segredos, senhas e tokens nunca serão armazenados em texto simples.
- Migrations serão imutáveis depois de aplicadas em ambientes compartilhados.

## Escalabilidade

O modelo deverá permitir futuras expansões, incluindo:

- novas temporadas;
- novas Dungeons;
- novas Cartas;
- novas classes;
- novas facções;
- novas regiões;
- novos continentes;
- novas moedas;
- novos mercados;
- guildas;
- eventos mundiais;
- múltiplos servidores ou regiões.

A estrutura inicial não deverá criar tabelas genéricas para funcionalidades ainda indefinidas.

Expansibilidade não significa antecipar todas as tabelas possíveis. Significa evitar decisões que tornem futuras extensões incompatíveis com os fundamentos atuais.

## Decisões ainda pendentes

Esta etapa não define:

- atributos finais;
- classes oficiais;
- raridades finais;
- fórmulas de progressão;
- valores econômicos;
- moedas oficiais;
- limites de inventário;
- regras completas do mercado;
- regras completas de guildas;
- balanceamento;
- nomes finais de tabelas;
- relacionamentos detalhados;
- índices;
- estratégia de particionamento.

Essas decisões deverão ser documentadas nos módulos correspondentes antes de entrarem no modelo físico.

## Próximos documentos

Após esta fundação serão criados:

1. Fluxograma de persistência.
2. Regras detalhadas do banco de dados.
3. Modelo conceitual dos domínios.
4. Modelo lógico inicial.
5. Convenções do Entity Framework Core.
6. Estratégia de migrations.
7. Plano de testes do banco de dados.

## Critério de conclusão

A fundação do banco de dados será considerada documentada quando:

1. PostgreSQL e Redis possuírem responsabilidades claramente separadas.
2. Os domínios persistentes iniciais estiverem identificados.
3. Operações críticas estiverem protegidas por autoridade do servidor.
4. Economia e propriedade possuírem exigências de transação e auditoria.
5. Dados temporários não forem confundidos com dados permanentes.
6. Decisões ainda indefinidas permanecerem fora do modelo físico.