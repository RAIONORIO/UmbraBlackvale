# Estrutura do Repositório

```text
UmbraBlackvale/
├── src/
│   ├── game/
│   ├── server/
│   └── shared/
├── tests/
│   ├── game/
│   └── server/
├── tools/
├── docs/
├── assets/
│   ├── concept-art/
│   ├── models/
│   ├── animations/
│   ├── textures/
│   ├── audio/
│   ├── music/
│   └── ui/
├── README.md
├── .editorconfig
└── .gitignore
```

## Responsabilidades

### src/game

Cliente Godot responsável por:

- interface;
- entrada do jogador;
- apresentação visual;
- animações;
- áudio;
- feedback das ações autorizadas pelo servidor.

### src/server

Backend ASP.NET Core responsável por:

- autenticação;
- regras do jogo;
- combate;
- Dungeons;
- inventário;
- mercado;
- ranking;
- economia;
- persistência;
- comunicação em tempo real.

### src/shared

Contratos estritamente necessários para comunicação entre cliente e servidor.

O compartilhamento de código não deve expor regras internas do domínio ao cliente.

### tests

Testes automatizados do cliente, servidor, domínio, aplicação e integrações.

### tools

Ferramentas internas de desenvolvimento, importação, validação e automação.

### docs

Documentação técnica, fluxogramas, regras, decisões e histórico.

### assets

Arquivos visuais e sonoros organizados por responsabilidade.
