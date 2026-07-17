# Regras de Desenvolvimento

## Ordem obrigatória

Todo sistema deve seguir:

1. Documentação.
2. Fluxograma.
3. Regras.
4. Banco de Dados.
5. Backend.
6. Frontend.
7. Testes.
8. Refatoração.
9. Atualização da documentação.

Nenhuma etapa pode ser ignorada sem justificativa técnica registrada.

## Arquitetura

- Clean Architecture.
- SOLID.
- Baixo acoplamento.
- Alta coesão.
- Separação clara de responsabilidades.
- Domínio independente de frameworks e infraestrutura.

## Nomenclatura

Todo código será escrito em inglês.

Exemplos:

- Commander
- Dungeon
- SummonCard
- Marketplace
- Inventory
- Guild

Nomes técnicos não devem misturar português e inglês.

## Servidor autoritativo

O cliente nunca decide:

- Damage.
- Critical hit.
- Loot.
- Experience.
- Rewards.
- Economy.
- Match results.
- Card ownership.
- Summon transfer.
- Ranking position.

## Segurança

- Toda entrada do cliente deve ser validada.
- O estado enviado pelo cliente nunca é considerado confiável.
- Regras críticas pertencem ao domínio do servidor.
- Informações sensíveis não devem ser expostas desnecessariamente.
- Operações econômicas devem ser atômicas e auditáveis.

## Critério para novas funcionalidades

Uma funcionalidade só pode entrar quando:

- resolver um problema;
- enriquecer uma mecânica existente;
- ou fortalecer a identidade de Umbra Blackvale.
