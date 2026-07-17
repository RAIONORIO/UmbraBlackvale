# Regras do Banco de Dados

## Objetivo

Definir as regras obrigatórias de persistência, integridade, concorrência, segurança, auditoria e evolução do banco de dados de Umbra Blackvale.

Estas regras deverão orientar:

- modelo conceitual;
- modelo lógico;
- entidades de domínio;
- configurações do Entity Framework Core;
- migrations;
- repositórios;
- serviços de aplicação;
- operações econômicas;
- testes de integração;
- manutenção futura.

Nenhuma implementação poderá contrariar estas regras sem atualização prévia da documentação.

## Autoridade dos dados

O servidor é autoritativo.

O cliente nunca poderá definir diretamente:

- dano;
- acerto crítico;
- loot;
- experiência;
- progressão;
- recompensas;
- resultados;
- moedas;
- propriedade;
- ranking;
- evolução de Cartas;
- estado econômico;
- transferências entre jogadores.

O cliente somente poderá enviar solicitações e intenções.

O backend deverá validar a operação e persistir o resultado confirmado pelo domínio.

## Fonte permanente de verdade

O PostgreSQL será a fonte permanente de verdade para:

- contas;
- Comandantes;
- Cartas;
- Ecos;
- inventários;
- equipamentos;
- progressão;
- propriedade;
- moedas;
- economia;
- Dungeons;
- resultados;
- temporadas;
- ranking consolidado;
- histórico;
- auditoria.

Nenhuma informação permanente poderá existir exclusivamente no Redis.

## Uso do Redis

O Redis poderá armazenar:

- sessões;
- cache;
- presença online;
- matchmaking;
- filas;
- convites temporários;
- estado temporário de salas;
- estado temporário de partidas;
- notificações;
- bloqueios distribuídos;
- controle de requisições;
- visualizações rápidas de ranking.

A perda completa do Redis não poderá causar perda de:

- progresso;
- inventário;
- propriedade;
- moedas;
- histórico;
- recompensas concedidas;
- resultados permanentes;
- vínculos com Invocações.

Dados do Redis deverão ser descartáveis, reconstruíveis ou temporários.

## Nomenclatura

Todo nome utilizado em código e banco será escrito em inglês.

Isso inclui:

- entidades;
- propriedades;
- tabelas;
- colunas;
- índices;
- constraints;
- migrations;
- chaves do Redis;
- eventos;
- comandos;
- consultas.

Não será permitido misturar português e inglês em nomes técnicos.

As convenções físicas de nomes de tabelas e colunas serão definidas antes da criação da primeira migration.

## Identificadores

Entidades com identidade própria utilizarão identificadores únicos.

UUID deverá ser considerado para entidades expostas externamente ou distribuídas, como:

- accounts;
- commanders;
- summon cards;
- dungeon runs;
- matches;
- marketplace listings;
- transactions;
- audit records.

Identificadores sequenciais não deverão ser expostos ao cliente quando isso facilitar enumeração ou descoberta indevida de dados.

Identificadores enviados pelo cliente nunca serão considerados suficientes para comprovar propriedade ou autorização.

## Datas e horários

Datas e horários permanentes serão armazenados em UTC.

O banco não armazenará horários locais como referência principal.

Conversões para o fuso do jogador serão realizadas na apresentação.

Entidades relevantes deverão possuir, quando aplicável:

- criação;
- última atualização;
- início;
- encerramento;
- expiração;
- cancelamento.

Datas não poderão ser geradas pelo cliente para validar operações críticas.

## Integridade referencial

Relacionamentos permanentes deverão utilizar chaves estrangeiras quando aplicável.

O banco deverá impedir estados inválidos mesmo quando uma falha ocorrer fora da aplicação.

Serão utilizadas constraints para proteger regras estruturais, como:

- valores obrigatórios;
- valores positivos;
- combinações únicas;
- estados permitidos;
- propriedade exclusiva;
- relacionamentos válidos;
- limites que possam ser garantidos estruturalmente.

Validações do domínio não substituem constraints do banco quando a regra puder ser protegida pelas duas camadas.

## Valores monetários

Moedas e valores econômicos nunca utilizarão tipos de ponto flutuante.

Valores inteiros serão preferidos quando a moeda não possuir casas fracionárias.

Quando valores fracionários forem realmente necessários, será utilizado tipo decimal com precisão explicitamente definida.

Nenhum saldo poderá:

- ser alterado sem motivo;
- ser alterado sem histórico;
- ficar negativo quando a regra econômica não permitir;
- ser calculado apenas no cliente;
- ser atualizado por incremento não auditado.

## Transações

Operações que alterem múltiplos dados relacionados deverão utilizar transação.

Exemplos obrigatórios:

- compra;
- venda;
- troca;
- transferência de propriedade;
- concessão de recompensa;
- consumo de recurso;
- evolução de Carta;
- alteração de vínculo;
- encerramento de Dungeon;
- encerramento de partida;
- recompensa de temporada.

Uma operação somente será considerada concluída após o commit.

Em caso de falha, todas as alterações relacionadas deverão ser revertidas.

## Economia e propriedade

Toda operação econômica deverá preservar consistência entre:

- saldo;
- item;
- proprietário;
- anúncio;
- transação;
- taxa;
- histórico;
- auditoria.

Débito e crédito deverão ocorrer na mesma transação.

Transferência de propriedade e pagamento deverão ocorrer na mesma transação.

Nenhum item poderá possuir dois proprietários atuais.

Nenhuma moeda poderá ser criada ou destruída sem uma origem econômica registrada.

Toda movimentação deverá possuir, quando aplicável:

- origem;
- destino;
- valor;
- ativo transferido;
- motivo;
- operação responsável;
- data;
- estado;
- identificador de idempotência.

## Idempotência

Operações críticas deverão suportar repetição segura.

Uma mesma solicitação não poderá:

- conceder recompensa duas vezes;
- cobrar duas vezes;
- transferir um item duas vezes;
- concluir uma partida duas vezes;
- registrar duas compras;
- aplicar experiência repetida;
- duplicar loot.

Chaves de idempotência deverão ser associadas à operação e ao contexto autorizado.

O resultado de uma operação já concluída deverá poder ser recuperado sem executar novamente seus efeitos.

## Concorrência

Alterações concorrentes deverão ser tratadas explicitamente.

Não será permitido confiar apenas no valor lido anteriormente pela aplicação.

Operações críticas deverão utilizar, conforme o caso:

- controle otimista de concorrência;
- versionamento da entidade;
- locks transacionais;
- constraints únicas;
- atualização condicional;
- bloqueios distribuídos temporários.

Conflitos deverão produzir falha controlada, nunca sobrescrita silenciosa.

## Cartas de Invocação e Ecos

A definição de um tipo de Eco será separada da instância individual de uma Carta pertencente a um jogador.

Cada Carta individual deverá poder preservar:

- identidade;
- proprietário atual;
- origem;
- raridade;
- evolução;
- vínculo;
- estado;
- restrições;
- histórico de transferência.

O avatar principal de um jogador nunca poderá ser representado como uma Carta transferível.

Transferências deverão respeitar as regras de vínculo e consentimento definidas pelo GDD e pela Lore Oficial. :contentReference[oaicite:2]{index=2}

Consentimento não poderá ser representado apenas por uma informação enviada pelo comprador.

Quando exigido, o consentimento deverá ser registrado de forma verificável e associado à transferência correspondente.

## Inventário

Itens empilháveis e itens individualizados não deverão utilizar obrigatoriamente o mesmo modelo.

Itens com qualquer uma destas características deverão possuir instância própria:

- identidade única;
- evolução;
- durabilidade;
- vínculo;
- proprietário;
- atributos variáveis;
- histórico;
- restrição de negociação.

Quantidades de itens empilháveis deverão ser protegidas contra valores negativos.

Movimentações relevantes deverão possuir histórico ou operação de origem identificável.

O inventário não poderá ser atualizado diretamente pelo cliente.

## Dungeons

A definição de uma Dungeon será separada de cada execução realizada.

A definição poderá conter:

- identidade;
- versão;
- dificuldade;
- regras;
- requisitos;
- recompensas possíveis;
- ciclo;
- temporada;
- estado de disponibilidade.

Cada execução poderá registrar:

- participantes;
- configuração;
- início;
- encerramento;
- resultado;
- versão das regras;
- recompensas concedidas;
- estado de conclusão.

Alterações futuras na definição não poderão modificar silenciosamente o histórico de execuções já concluídas.

## Partidas e resultados

Resultados de partidas serão calculados e confirmados pelo servidor.

O cliente nunca poderá persistir diretamente:

- vencedor;
- derrota;
- pontuação;
- recompensa;
- experiência;
- ranking;
- estatísticas finais.

O encerramento de uma partida deverá ser idempotente.

Uma desconexão ou repetição de mensagem não poderá gerar recompensas duplicadas.

O sistema deverá preservar dados suficientes para:

- auditoria;
- suporte;
- investigação de fraude;
- correção de inconsistências;
- resolução de disputas.

Não será obrigatório armazenar cada quadro de animação ou efeito visual do cliente.

## Recompensas

Toda recompensa deverá possuir origem identificável.

Exemplos:

- Dungeon;
- partida;
- missão;
- temporada;
- evento;
- compensação administrativa;
- conquista;
- promoção autorizada.

Recompensas não poderão ser concedidas apenas porque o cliente informou ter concluído uma ação.

A concessão deverá registrar:

- destinatário;
- origem;
- conteúdo;
- data;
- operação;
- resultado;
- identificador de idempotência.

## Ranking e temporadas

O PostgreSQL manterá:

- pontuação consolidada;
- temporada;
- histórico;
- posição final;
- critérios utilizados;
- recompensas;
- encerramento.

O Redis poderá manter uma visualização rápida do ranking atual.

A perda do ranking em Redis deverá permitir reconstrução por meio dos dados permanentes.

Uma temporada encerrada não poderá ter seus resultados alterados silenciosamente.

Correções administrativas deverão possuir justificativa e auditoria.

## Auditoria

Operações críticas deverão gerar registros de auditoria.

A auditoria deverá registrar, quando aplicável:

- responsável;
- operação;
- entidade afetada;
- identificador da entidade;
- estado anterior relevante;
- estado posterior relevante;
- motivo;
- data;
- origem da solicitação;
- correlação;
- resultado.

Registros de auditoria não poderão ser editados ou excluídos por fluxos normais da aplicação.

Informações sensíveis, como senhas e tokens, nunca serão copiadas para registros de auditoria.

## Exclusão de dados

Exclusão física não será utilizada automaticamente.

Dados com valor histórico, econômico, administrativo ou de auditoria deverão ser preservados.

Exclusão lógica poderá ser utilizada quando a entidade precisar deixar de aparecer sem perder seu histórico.

Relacionamentos em cascata serão usados apenas quando a remoção automática for comprovadamente segura.

Não será permitido apagar, por cascata acidental:

- transações;
- propriedade histórica;
- resultados;
- recompensas;
- auditoria;
- temporadas;
- movimentações econômicas.

Regras legais de privacidade e retenção serão documentadas antes da operação comercial.

## Segurança

Senhas nunca serão armazenadas em texto simples.

Tokens de autenticação, segredos e credenciais não deverão ser persistidos sem proteção adequada.

Consultas deverão ser sempre parametrizadas.

O Entity Framework Core será utilizado sem montagem insegura de SQL por concatenação.

Toda consulta deverá considerar:

- identidade;
- autorização;
- propriedade;
- escopo;
- estado da conta;
- visibilidade do dado.

Possuir o identificador de uma entidade não concede acesso a ela.

## Privacidade

Dados pessoais deverão ser limitados ao necessário para operação do produto.

Informações privadas não deverão aparecer em:

- ranking público;
- logs;
- auditoria exposta;
- mensagens de erro;
- respostas destinadas a outros jogadores.

A separação entre dados públicos do Comandante e dados privados da conta deverá ser preservada.

## Índices

Índices deverão ser criados com base em consultas reais e constraints necessárias.

Índices serão avaliados para:

- chaves estrangeiras;
- identificadores públicos;
- propriedade;
- status;
- datas;
- temporadas;
- buscas frequentes;
- combinações únicas;
- filas e estados operacionais.

Não serão criados índices indiscriminadamente.

Cada índice possui custo de armazenamento e escrita e deverá resolver uma necessidade conhecida.

## Consultas e desempenho

Consultas deverão retornar apenas os campos necessários.

Listagens deverão utilizar paginação.

Não será permitido carregar coleções ilimitadas em memória.

Problemas de consultas repetidas deverão ser identificados por testes e observabilidade.

Agregações pesadas poderão utilizar:

- consultas especializadas;
- projeções;
- cache;
- processamento assíncrono;
- visualizações persistidas futuramente, quando justificadas.

Desnormalização somente será adotada após medição e documentação do problema.

## Entity Framework Core

Entidades de domínio não deverão depender diretamente de detalhes do PostgreSQL.

Configurações de persistência serão mantidas na camada de Infrastructure.

Mapeamentos importantes serão explícitos.

O modelo não dependerá exclusivamente de convenções implícitas para decisões críticas.

As configurações deverão definir, quando aplicável:

- chave primária;
- relacionamentos;
- obrigatoriedade;
- tamanho máximo;
- precisão decimal;
- índices;
- constraints;
- conversões;
- concorrência;
- comportamento de exclusão.

Entidades não deverão expor setters públicos indiscriminadamente apenas para satisfazer o ORM.

## Migrations

Toda mudança estrutural deverá utilizar migration versionada.

Migrations já aplicadas em ambientes compartilhados não serão modificadas.

Correções serão realizadas por novas migrations.

Cada migration deverá possuir:

- nome descritivo;
- escopo limitado;
- revisão;
- teste local;
- estratégia de aplicação;
- avaliação de risco.

Mudanças destrutivas deverão possuir plano de recuperação.

Dados de produção nunca serão corrigidos por alteração manual sem registro.

## Dados iniciais

Seeds serão utilizados apenas para dados técnicos ou de catálogo realmente necessários.

Seeds não deverão criar dados pessoais, credenciais reais ou economia fictícia em produção.

Conteúdo de gameplay deverá possuir estratégia própria de versionamento quando o volume e a operação do jogo exigirem.

## Chaves do Redis

Chaves do Redis deverão possuir nomenclatura padronizada.

A convenção detalhada será definida antes da implementação.

Cada chave deverá identificar claramente:

- módulo;
- finalidade;
- entidade;
- versão quando necessária.

Dados temporários deverão possuir expiração quando aplicável.

Chaves abandonadas não poderão permanecer indefinidamente por descuido.

Dados sensíveis deverão ser minimizados.

## Cache

O cache será uma otimização, não uma fonte de regras.

O sistema deverá funcionar corretamente mesmo quando ocorrer:

- cache vazio;
- cache expirado;
- cache indisponível;
- invalidação;
- reconstrução.

Alterações permanentes deverão invalidar ou atualizar o cache relacionado somente depois do commit.

O cache não poderá confirmar uma operação econômica ainda não persistida.

## Logs e observabilidade

Falhas de banco deverão possuir logs com contexto suficiente para investigação.

Logs não poderão expor:

- senhas;
- tokens;
- segredos;
- dados pessoais desnecessários;
- conteúdo completo de credenciais.

Operações críticas deverão possuir identificador de correlação.

Métricas futuras deverão permitir observar:

- tempo de consulta;
- falhas;
- deadlocks;
- conflitos de concorrência;
- uso de conexões;
- tempo de transação;
- falhas de cache;
- operações idempotentes repetidas.

## Backups e recuperação

Antes do ambiente de produção serão documentados:

- política de backup;
- frequência;
- retenção;
- criptografia;
- armazenamento;
- restauração;
- testes de recuperação;
- objetivos de perda aceitável;
- objetivos de tempo de recuperação.

Backup sem teste de restauração não será considerado estratégia de recuperação.

## Proibições

Não será permitido:

- usar Redis como fonte permanente;
- armazenar senhas em texto simples;
- usar ponto flutuante para valores econômicos;
- confirmar resultados enviados pelo cliente;
- alterar saldo sem histórico;
- conceder recompensa sem origem;
- executar transferência sem transação;
- apagar auditoria por fluxo normal;
- alterar migrations já compartilhadas;
- corrigir produção sem registro;
- criar tabelas genéricas para sistemas ainda indefinidos;
- adicionar colunas temporárias sem documentação;
- duplicar a mesma regra em múltiplas fontes de verdade;
- ignorar concorrência em operações críticas;
- permitir acesso apenas porque o cliente conhece um identificador.

## Decisões ainda pendentes

Este documento não define:

- nomes físicos finais das tabelas;
- convenção final entre `snake_case` e outros formatos;
- entidades completas;
- colunas;
- tipos detalhados;
- precisão das moedas;
- limites de inventário;
- atributos de combate;
- raridades;
- moedas oficiais;
- regras completas do mercado;
- fórmula do ranking;
- estratégia de particionamento;
- política final de retenção;
- política final de backup.

Essas decisões deverão ser documentadas antes da criação do modelo físico correspondente.

## Critério de conclusão

As regras do banco de dados serão consideradas documentadas quando:

1. PostgreSQL estiver definido como fonte permanente.
2. Redis estiver limitado a dados temporários ou derivados.
3. Operações críticas estiverem sob autoridade do servidor.
4. Economia utilizar transações, histórico e idempotência.
5. Propriedade estiver protegida contra duplicação.
6. Concorrência estiver prevista.
7. Auditoria estiver definida para operações críticas.
8. Migrations possuírem regras de versionamento.
9. Dados sensíveis possuírem proteção definida.
10. Decisões ainda pendentes não forem inventadas no modelo físico.
