# Changelog

Todas as alterações relevantes do projeto serão registradas neste arquivo.

## Unreleased

### Added

- Estrutura inicial do monorepo.
- README principal.
- Fundação documental.
- Fluxograma geral do sistema.
- Regras de desenvolvimento.
- Estrutura oficial do repositório.
- Ambiente de desenvolvimento documentado.
- .NET SDK 9.0.316 definido como versão-base.
- Godot Engine .NET 4.7.1 validada.
- WSL 2 e Docker Desktop validados para o ambiente local.
- Documentação da infraestrutura local.
- Fluxograma da infraestrutura local.
- Regras de segurança e persistência da infraestrutura.
- Docker Compose configurado para PostgreSQL e Redis.
- PostgreSQL 17.10 Alpine configurado com volume persistente e healthcheck.
- Redis 8.6.4 Alpine configurado com persistência e healthcheck.
- Rede Docker dedicada para os serviços locais.
- Arquivo `.env.example` adicionado para configuração segura do ambiente.
- Portas dos serviços limitadas ao endereço local `127.0.0.1`.
- Conexões reais com PostgreSQL e Redis validadas.
- Persistência dos volumes validada após recriação dos containers.
- Resultado dos testes da infraestrutura registrado na documentação.
