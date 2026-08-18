# Perfil e stack inicial

Inventário realizado em **2026-08-17**. Versões são uma fotografia da máquina, não requisitos do setup.

## Perfil confirmado

| Área | Estado |
|---|---|
| sistema principal | Ubuntu 24.04.4 LTS, Linux x86_64 |
| shell | Zsh 5.9 com Oh My Zsh |
| Python | Python 3.12.3 do sistema; `uv` 0.12.3 instalado |
| JavaScript | Node 22.23.1 via NVM; npm 12.0.2; pnpm 10.4.1 |
| backend | Python + FastAPI |
| frontend | TypeScript + Next.js |
| agentes | Pi, Codex, Claude Code e Kimi Code instalados |
| terminal | WezTerm instalado via Flatpak; configuração em `~/.wezterm.lua` |
| multiplexador | Herdr 0.7.5; configuração em `~/.config/herdr` |
| dotfiles | chezmoi instalado e source directory presente |
| editor terminal | Neovim/Helix ainda não instalados/configurados |

O executável `wezterm` não aparece no `PATH` da sessão, pois a instalação detectada é Flatpak. Isso não impede o uso gráfico, mas scripts que chamem `wezterm` diretamente precisarão usar `flatpak run org.wezfurlong.wezterm` ou um wrapper explícito.

## Decisões específicas para este perfil

### Manter Zsh + Oh My Zsh inicialmente

Não migrar de shell enquanto editor, navegação e Git ainda estão sendo aprendidos. Trocar shell agora adicionaria uma variável sem resolver a principal dor.

Primeiro ciclo:

- manter Zsh;
- revisar plugins do Oh My Zsh e remover os sem uso;
- integrar `fzf`, `zoxide` e, opcionalmente depois, Atuin;
- medir tempo de startup;
- manter scripts de projeto em Bash/POSIX, nunca em sintaxe específica do Zsh.

Fish pode ser reavaliado no futuro, mas não faz parte do bootstrap.

### Testar LazyVim como ponte a partir do VS Code

Como não há experiência anterior com editores modais/TUIs, [LazyVim](https://www.lazyvim.org/) é recomendado para o primeiro piloto. Ele fornece uma superfície de IDE funcional, atalhos descobríveis e integração pronta com LSP sem exigir que a configuração seja construída antes de editar o primeiro arquivo.

Limites do teste:

- aprender movimentos e modos do Vim antes de customizar;
- ativar somente extras necessários para Python e TypeScript;
- não instalar file tree, terminal, Git UI e dashboards redundantes por impulso;
- usar Yazi, Herdr e lazygit como ferramentas externas;
- registrar todo plugin adicional e o problema que resolve.

Se a quantidade de convenções escondidas atrapalhar, o fallback será `kickstart.nvim`. Helix permanece alternativa caso a manutenção do ecossistema Neovim não compense.

### Python/FastAPI

Stack proposta:

| Responsabilidade | Ferramenta |
|---|---|
| Python e ambientes/dependências | `uv` |
| lint e formatação | Ruff, configurado no projeto |
| tipos/LSP | Pyright ou basedpyright, conforme o projeto |
| testes | pytest |
| servidor local | FastAPI CLI/Uvicorn por comando do projeto |
| requests manuais | xh; Posting somente se coleções interativas forem úteis |
| debug | debugpy somente quando houver caso real |

Não instalar ferramentas Python globalmente com `sudo pip`. Ferramentas pertencentes ao projeto entram no `pyproject.toml`; CLIs isoladas podem usar `uv tool` ou mise, com um único dono definido.

Interface desejada do projeto:

```bash
just setup       # uv sync
just dev         # uv run fastapi dev ...
just test        # uv run pytest
just lint        # uv run ruff check .
just format      # uv run ruff format .
just typecheck   # uv run pyright/basedpyright
just check       # lint + typecheck + tests
```

Os comandos exatos dependem de cada repositório; o contrato importa mais que a implementação.

### TypeScript/Next.js

Stack proposta:

| Responsabilidade | Ferramenta |
|---|---|
| versão do Node | NVM por enquanto; testar mise depois |
| package manager | pnpm |
| tipos/LSP | TypeScript do próprio workspace + typescript-language-server |
| lint | configuração ESLint do projeto |
| formatação | Prettier ou ferramenta já escolhida pelo projeto |
| testes | runner do projeto; Playwright para fluxos de browser quando aplicável |
| desenvolvimento | scripts de `package.json` chamados pelo just |
| debug | js-debug-adapter somente quando necessário |

Não migrar NVM para mise na mesma fase de adoção do editor. A migração só deve ocorrer em um projeto piloto e, se aceita, NVM deixa de ser o gerenciador ativo para evitar duas fontes de versão.

Interface desejada:

```bash
just setup       # pnpm install --frozen-lockfile
just dev         # pnpm dev
just test        # pnpm test
just lint        # pnpm lint
just typecheck   # pnpm exec tsc --noEmit
just check       # lint + typecheck + tests/build relevante
```

Biome não deve substituir ESLint/Prettier automaticamente; é uma decisão de cada projeto.

### Frontend e o limite do “100% terminal”

Código, testes, processos, logs e automação podem ser controlados inteiramente pelo terminal. A validação visual de uma interface Next.js continua exigindo um navegador renderizando a página. Isso não exige mouse: navegação por teclado, Playwright e agentes de browser reduzem a interação manual, mas screenshots e testes headless não substituem integralmente inspeção visual e acessibilidade humana.

O objetivo será **não depender de IDE gráfica**, sem fingir que uma aplicação gráfica pode ser validada apenas como texto.

## Integrações Herdr pendentes

O comando `herdr integration status` mostrou Pi, Claude, Codex e Kimi como **not installed**. Próxima ação proposta, após revisar o que cada integração altera:

```bash
herdr integration install pi
herdr integration install claude
herdr integration install codex
herdr integration install kimi
herdr integration status
```

Essas integrações melhoram sinalização de lifecycle e/ou restauração de sessão. Como escrevem nos diretórios de configuração de cada agente, sua instalação deve ser deliberada e suas mudanças devem ser inspecionadas.

## Primeira cesta de ferramentas

### Instalar/testar primeiro

1. Neovim + LazyVim;
2. fzf, fd, ripgrep, bat e zoxide;
3. Yazi;
4. lazygit e delta;
5. integrações Herdr dos quatro agentes.

### Segundo ciclo

1. difftastic;
2. just;
3. mise em um projeto piloto;
4. direnv;
5. Worktrunk depois de aprender o básico de `git worktree`.

### Somente mediante necessidade

- Posting e Rainfrog;
- lazydocker, dive e k9s;
- mprocs para subir FastAPI e Next.js simultaneamente;
- DAP/debug adapters;
- Atuin e sincronização de histórico;
- SOPS/age.

## Trilha mínima de aprendizado

Antes de plugins e atalhos personalizados:

1. shell: pipe, redirect, exit code, jobs e `Ctrl+r`;
2. Herdr: criar/split/mover/workspace/detach/ajuda;
3. Vim: modos normal/insert/visual, `hjkl`, palavra, linha, busca, salvar e sair;
4. fzf/rg/fd: selecionar arquivo e procurar conteúdo;
5. lazygit: status, stage, diff, commit, branch e conflito;
6. worktree: criar, listar e remover;
7. agentes: uma tarefa, uma worktree, um contrato e um diff.

Customização vem depois que uma fricção se repete; antes disso, os defaults são material de aprendizado.
