# Mapa de problemas e ferramentas

Esta é uma shortlist, não uma lista de instalação. Estados: **adotado**, **testar**, **alternativa** e **condicional**.

## Núcleo

| Problema | Escolha | Estado | Por quê | Limite |
|---|---|---:|---|---|
| emular o terminal | [WezTerm](https://wezterm.org/) | adotado | multiplataforma, configurável em Lua, bom suporte de teclado, SSH e protocolos modernos | usar como terminal, não como um segundo mux |
| persistir e coordenar panes/agentes | [Herdr](https://herdr.dev/) | adotado | sessões persistentes, status working/blocked/idle, integrações e API para agentes | centraliza toda multiplexação |
| editar, navegar por símbolos e usar LSP | [Neovim](https://neovim.io/) | testar | ecossistema amplo, modal e extensível; substitui a superfície central do editor | configuração pode virar um projeto paralelo |
| explorar arquivos visualmente | [Yazi](https://yazi-rs.github.io/) | testar | preview, seleção, operações de arquivo e integração com `fd`, `rg`, `fzf` e `zoxide` | não duplicar a árvore de arquivos do editor |
| inspecionar e operar Git | [lazygit](https://github.com/jesseduffield/lazygit) | testar | torna status, staging, commits, branches, rebase e conflitos legíveis por teclado | operações raras continuam na CLI |

### Editor: decisão de teste

Começar com **Neovim + [LazyVim](https://www.lazyvim.org/)** como ponte para quem ainda não usou editor modal, ativando apenas suporte a Python e TypeScript. `kickstart.nvim` é o fallback caso as convenções e plugins prontos dificultem entender ou manter o editor. [Helix](https://helix-editor.com/) permanece como alternativa caso o custo do ecossistema Neovim supere o valor de sua extensibilidade. A justificativa e o experimento estão no [ADR 0003](decisions/0003-lazyvim-como-editor-inicial.md).

O editor deve oferecer, no mínimo: LSP, diagnósticos, rename, go-to-definition, references, formatação, Treesitter, quickfix e integração com o clipboard. Terminal, file tree e Git UI embutidos não são requisitos, pois já têm donos externos.

## Primitivas de navegação e leitura

| Problema | Escolha | Estado | Observação |
|---|---|---:|---|
| localizar texto | [ripgrep (`rg`)](https://github.com/BurntSushi/ripgrep) | testar | respeita `.gitignore`; base para buscas próprias e de outras TUIs |
| localizar arquivos | [fd](https://github.com/sharkdp/fd) | testar | interface mais simples que `find`; não substitui `find` em scripts portáveis |
| escolher qualquer item | [fzf](https://junegunn.github.io/fzf/) | testar | cola universal para arquivos, branches, histórico e processos |
| saltar entre diretórios | [zoxide](https://github.com/ajeetdsouza/zoxide) | testar | aprende diretórios frequentes e reduz digitação de caminhos |
| ler arquivos com destaque | [bat](https://github.com/sharkdp/bat) | testar | pager e preview para fzf/Yazi; scripts continuam usando `cat` |
| listar diretórios interativamente | [Yazi](https://yazi-rs.github.io/) | testar | usar quando a visão espacial for melhor que `fd | fzf` |
| histórico do shell | [Atuin](https://atuin.sh/) | condicional | busca estruturada, histórico local e sync opcional; exige política para comandos sensíveis |
| documentação curta | [`tldr`/tealdeer](https://github.com/dbrgn/tealdeer) | condicional | exemplos rápidos antes de recorrer ao `man` |
| Markdown | [Glow](https://github.com/charmbracelet/glow) | disponível | útil para README, planos e relatórios dos agentes |

## Git e revisão humana

| Problema | Escolha | Estado | Observação |
|---|---|---:|---|
| fluxo Git visual | [lazygit](https://github.com/jesseduffield/lazygit) | testar | candidato principal em vez de GitUI pela abrangência do workflow |
| pager de diff | [delta](https://dandavison.github.io/delta/) | testar | bom default para `git diff`, `show`, `blame` e saídas unificadas |
| diff semântico | [difftastic](https://difftastic.wilfred.me.uk/) | testar | usar sob demanda em mudanças estruturais; complementa, não substitui, delta |
| GitHub | [GitHub CLI (`gh`)](https://cli.github.com/) | disponível | issues, PRs, checks, releases e autenticação sem sair do terminal |
| isolamento por tarefa | [Worktrunk (`wt`)](https://worktrunk.dev/) | testar | ergonomia sobre Git worktrees, desenhado para workflows paralelos com agentes |

Regra: agentes podem produzir commits, mas a integração ocorre apenas depois de `lazygit`/`git diff`, checks locais e, quando houver, CI.

## Ambiente reproduzível

| Problema | Escolha | Estado | Observação |
|---|---|---:|---|
| versões de runtimes e ferramentas | [mise](https://mise.jdx.dev/) | testar | uma interface para versões por projeto; também suporta env e tasks |
| ativar env por diretório | [direnv](https://direnv.net/) | testar | explícito e shell-native; `.envrc` precisa ser revisado antes de `allow` |
| catálogo de comandos do projeto | [just](https://just.systems/) | testar | cria comandos memorizáveis para humano, CI e agentes |
| dotfiles | [chezmoi](https://www.chezmoi.io/) | confirmar | já está instalado nesta máquina; verificar se será a fonte de verdade |
| segredos em arquivos | [SOPS](https://getsops.io/) + [age](https://age-encryption.org/) | condicional | somente se dotfiles/configs precisarem carregar segredos criptografados |

`mise`, `direnv` e `just` têm alguma sobreposição. Limite proposto: mise gerencia **versões**, direnv **ativação local**, just **comandos do repositório**.

## Shell e ergonomia

Manter **Zsh + Oh My Zsh** durante as primeiras fases para não combinar a migração de shell com o aprendizado de editor e TUIs. Revisar plugins, medir startup e integrar as primitivas adotadas. Scripts do projeto continuam em Bash/POSIX.

Fish pode ser reavaliado no futuro por seus defaults interativos; Bash permanece o fallback universal. [Starship](https://starship.rs/) é candidato condicional para um prompt consistente. O prompt deve permanecer rápido e mostrar apenas contexto acionável: diretório, branch/estado Git, runtime e exit code.

## Ferramentas por domínio — instalar apenas quando necessário

| Domínio | Candidato | Problema resolvido |
|---|---|---|
| HTTP/API | [xh](https://github.com/ducaale/xh) | requests legíveis e scriptáveis |
| HTTP exploratório | [Posting](https://posting.sh/) | coleção e exploração de APIs em TUI |
| JSON | [jq](https://jqlang.org/) + [jnv](https://github.com/ynqa/jnv) | transformação e construção interativa de filtros |
| YAML/TOML/XML | [yq](https://mikefarah.gitbook.io/yq/) | consulta e transformação estruturada |
| bancos SQL | [Rainfrog](https://github.com/achristmascarl/rainfrog) | exploração e queries em TUI |
| Docker | [lazydocker](https://github.com/jesseduffield/lazydocker) | containers, logs, métricas e compose |
| imagem Docker | [dive](https://github.com/wagoodman/dive) | investigar tamanho e camadas |
| Kubernetes | [k9s](https://k9scli.io/) | navegação, logs e operação de recursos |
| processos/recursos | [btop](https://github.com/aristocratos/btop) | CPU, memória, disco, rede e processos |
| múltiplos processos locais | [mprocs](https://github.com/pvolok/mprocs) | executar e observar serviços de desenvolvimento |
| repetir checks | [watchexec](https://watchexec.github.io/) | refazer teste/lint/build após mudanças |
| arquivos grandes | [dust](https://github.com/bootandy/dust) | uso de disco legível |
| automação interativa | [gum](https://github.com/charmbracelet/gum) | prompts e seletores em scripts shell |

## Agentes CLI

Herdr não deve impor um único agente; ele hospeda os já usados. As integrações oficiais documentadas incluem Pi, Claude Code, Codex, GitHub Copilot CLI, OpenCode e outros.

Candidatos como [Aider](https://aider.chat/), [OpenCode](https://opencode.ai/) e [Goose](https://block.github.io/goose/) só devem ser comparados após definir modelos, orçamento, privacidade, permissões e necessidade de extensões. Adicionar agentes demais antes disso apenas fragmenta contexto e atalhos.

## O que não adotar agora

- tmux ou Zellij: duplicam o Herdr;
- uma distribuição grande de dotfiles de terceiros: esconde decisões importantes;
- dezenas de substitutos de comandos POSIX (`ls`, `ps`, `du`) no primeiro ciclo;
- plugins de Neovim para encapsular todas as TUIs externas;
- sincronização cloud do histórico antes de uma revisão de segurança;
- TUIs de Kubernetes, banco ou Docker sem uso diário comprovado.
