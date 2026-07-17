# Ambiente de Desenvolvimento

## Status

Ambiente-base validado para inicialização técnica.

## Sistema operacional

- Plataforma: Windows x64.
- Terminal principal: Git Bash.
- IDE oficial: Visual Studio Code.

## Ferramentas validadas

| Ferramenta | Versão | Uso |
|---|---:|---|
| Git | 2.53.0 | Versionamento |
| GitHub CLI | 2.95.0 | Integração com GitHub |
| .NET SDK | 9.0.316 | Backend ASP.NET Core e suporte C# |
| Godot Engine .NET | 4.7.1 stable mono | Cliente do jogo em C# |

## Comandos da Godot

Os comandos abaixo estão disponíveis no Git Bash:

- `godot --version`
- `godot_console --version`

Os atalhos locais estão armazenados em:

- `~/.local/bin/godot`
- `~/.local/bin/godot_console`

## Política de versões

- O backend utiliza .NET 9.
- O cliente utiliza Godot .NET 4.5 ou versão superior previamente aprovada.
- Atualizações de versão devem passar por validação de compatibilidade.
- Nenhuma versão deve ser alterada silenciosamente.
- Alterações de engine ou SDK devem ser registradas na documentação e no Changelog.

## Ferramentas pendentes

- Docker Desktop.
- PostgreSQL executado por container.
- Redis executado por container.

PostgreSQL e Redis não serão instalados como serviços locais do Windows. O ambiente de desenvolvimento utilizará containers para reduzir divergências entre máquinas.
## Infraestrutura local validada

- WSL: 2.7.10.0.
- Docker Desktop: 4.82.0.
- Docker Engine: 29.6.1.
- Docker Compose: 5.3.0.
- Backend de containers: Linux via WSL 2.
- Teste oficial: container hello-world executado com sucesso.
