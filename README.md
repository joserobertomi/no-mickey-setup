# No Mickey Setup

Um handbook vivo para construir um ambiente de engenharia de software **terminal-first**, orientado a agentes e operável integralmente por teclado.

> “No Mickey” é um trocadilho com Mickey Mouse: significa *sem depender do mouse*, não automatizar decisões sem supervisão.

## Objetivo

Substituir as superfícies do VS Code por ferramentas pequenas e combináveis, preservando:

- navegação rápida pelo projeto;
- leitura e edição de código com LSP;
- busca global e abertura rápida;
- revisão visual de diffs e operação de Git;
- execução e observação de testes, serviços e infraestrutura;
- coordenação de vários agentes sem disputa pelo mesmo diretório;
- sessões persistentes e recuperáveis;
- uma experiência coerente, exclusivamente pelo teclado.

## Arquitetura proposta

```text
WezTerm (renderização, teclado, clipboard)
└── Herdr (workspaces, tabs, panes, persistência, estado dos agentes)
    ├── agente A ── worktree A
    ├── agente B ── worktree B
    ├── Neovim ─── leitura, edição e LSP
    ├── lazygit ── revisão e operações Git
    └── shell ──── busca, navegação, tarefas e observabilidade
```

A regra mais importante é manter **um único dono para cada responsabilidade**. Em especial, não aninhar tmux/Zellij nem usar tabs do WezTerm como uma segunda camada de multiplexação.

## Documentação

1. [Princípios e critérios](docs/01-principios.md)
2. [Mapa de problemas e ferramentas](docs/02-mapa-de-ferramentas.md)
3. [Arquitetura e fluxo de trabalho](docs/03-arquitetura-e-workflow.md)
4. [Roadmap de adoção](docs/04-roadmap.md)
5. [Fontes da pesquisa](docs/05-fontes.md)
6. [Perfil e stack inicial](docs/06-perfil-e-stack-inicial.md)
7. [Decisões de arquitetura](docs/decisions/)

## Estado atual

| Componente | Papel | Estado |
|---|---|---|
| WezTerm | emulador de terminal | adotado |
| Herdr | multiplexador e runtime dos agentes | adotado |
| Neovim + LazyVim | editor/revisor terminal | teste proposto |
| Zsh + Oh My Zsh | shell interativo | manter inicialmente |
| Yazi | explorador de arquivos | testar |
| lazygit | UI de Git e revisão | testar |
| fzf + ripgrep + fd | abertura, busca e seleção | testar |
| zoxide | navegação entre projetos | testar |
| delta + difftastic | leitura de diffs | testar |
| Worktrunk | worktrees para agentes paralelos | testar |
| mise + direnv + just | runtime, ambiente e tarefas | testar |
| chezmoi | distribuição dos dotfiles | instalado; confirmar adoção |

“Testar” não significa instalar tudo de uma vez. Cada ferramenta deve passar pelo critério de saída descrito no roadmap.

## Perfil confirmado

O primeiro alvo é Ubuntu/Linux com Zsh + Oh My Zsh, Python/FastAPI, TypeScript/Next.js e os agentes Pi, Codex, Claude Code e Kimi Code. Como ainda não há experiência com editores/TUIs modais, LazyVim será testado como ponte inicial. Detalhes e limites estão no [perfil da stack](docs/06-perfil-e-stack-inicial.md).

Próximas decisões:

- política de permissões/sandbox dos agentes;
- frequência de uso de Docker, Kubernetes e bancos SQL;
- desenvolvimento remoto e devcontainers;
- estratégia de dotfiles públicos e secrets;
- aceitar ou rejeitar LazyVim após tarefas reais em Python e TypeScript.

## Como registrar mudanças

- Ferramenta experimental entra no [mapa](docs/02-mapa-de-ferramentas.md).
- Ferramenta estrutural aceita ou rejeitada ganha um ADR em `docs/decisions/`.
- Configurações reproduzíveis irão para `config/` somente depois do teste manual.
- Toda adoção deve remover uma dor concreta; popularidade não é justificativa suficiente.
