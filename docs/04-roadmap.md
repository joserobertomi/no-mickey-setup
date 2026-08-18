# Roadmap de adoção

Instalar uma camada por vez. Cada fase termina com uso real e uma decisão: **adotar**, **ajustar** ou **remover**.

## Fase 0 — baseline e mapa de teclas

**Objetivo:** estabilizar WezTerm + Herdr antes de adicionar aplicações.

- [ ] registrar SOs, shell, linguagens, agentes e ferramentas de infraestrutura usados;
- [ ] versionar a configuração atual do WezTerm e Herdr;
- [ ] garantir que Herdr seja o único multiplexador;
- [ ] instalar integrações Herdr apenas para os agentes ativos;
- [ ] praticar os cinco atalhos essenciais e `Ctrl+b ?`;
- [ ] validar attach/detach, restauração, copy mode e clipboard;
- [ ] listar conflitos de teclas.

**Saída:** trabalhar uma sessão inteira, inclusive após detach/attach, sem mouse e sem perder panes.

## Fase 1 — substituir navegação e Git do VS Code

**Objetivo:** encontrar, ler e revisar qualquer mudança pelo terminal.

Ordem sugerida:

1. `fzf`, `ripgrep`, `fd`, `bat`, `zoxide`;
2. Yazi;
3. lazygit e delta;
4. difftastic sob demanda;
5. Neovim + LazyVim, com somente extras de Python e TypeScript.

- [ ] localizar projeto com zoxide;
- [ ] localizar arquivo com fzf/fd;
- [ ] localizar conteúdo com ripgrep;
- [ ] navegar e fazer operações de arquivo com Yazi;
- [ ] stage, commit, branch, rebase e resolver conflito no lazygit;
- [ ] completar uma revisão real usando delta/Neovim;
- [ ] mapear LSP, diagnostics, definition, references, rename e quickfix no Neovim.

**Saída:** concluir uma pequena mudança e sua revisão sem abrir o VS Code.

## Fase 2 — ambiente reproduzível

**Objetivo:** humano e agente executarem os mesmos comandos.

- [ ] escolher o shell antes de customizar prompt e plugins;
- [ ] usar mise em um projeto piloto;
- [ ] usar direnv sem colocar secrets no repositório;
- [ ] criar um `justfile` com `setup`, `dev`, `test` e `check` no piloto;
- [ ] decidir se chezmoi será a fonte de verdade dos dotfiles;
- [ ] medir startup do shell e remover plugins sem valor;
- [ ] documentar bootstrap de máquina limpa.

**Saída:** clonar um projeto piloto, ativar suas versões e executar `just check` seguindo apenas documentação versionada.

## Fase 3 — agentes paralelos com worktrees

**Objetivo:** paralelismo sem conflitos de filesystem.

- [ ] dominar `git worktree` nativo antes ou durante o teste do Worktrunk;
- [ ] definir convenção de branches, worktrees e tabs Herdr;
- [ ] executar duas tarefas independentes em duas worktrees;
- [ ] revisar ambos os diffs sem misturar contexto;
- [ ] integrar uma tarefa e rejeitar/retrabalhar a outra;
- [ ] remover worktrees e branches ao final;
- [ ] documentar permissões e ações que exigem confirmação humana.

**Saída:** duas tarefas concorrentes sem edição compartilhada, conflito acidental ou dúvida sobre qual agente está bloqueado.

## Fase 4 — ferramentas específicas do trabalho

**Objetivo:** substituir extensões de VS Code realmente usadas, não hipotéticas.

Escolher somente as trilhas relevantes:

- [ ] HTTP: xh e, se necessário, Posting;
- [ ] dados: jq/jnv/yq;
- [ ] banco: Rainfrog;
- [ ] containers: lazydocker/dive;
- [ ] Kubernetes: k9s;
- [ ] processos: btop/mprocs/watchexec;
- [ ] debugger: CLI ou integração DAP mínima no Neovim;
- [ ] documentação: Glow, `man`, tldr.

**Saída:** para cada ferramenta instalada, uma extensão/GUI dispensada ou uma tarefa frequente comprovadamente mais rápida.

## Fase 5 — hardening e portabilidade

**Objetivo:** tornar o setup recuperável, seguro e pequeno.

- [ ] bootstrap idempotente em máquina limpa/VM;
- [ ] dotfiles sem tokens, chaves ou dados pessoais;
- [ ] backups e sync avaliados separadamente de configuração;
- [ ] plugins e ferramentas fixados quando mudanças quebráveis forem um risco;
- [ ] tempo de startup e comandos ausentes observáveis;
- [ ] fallback para SSH e terminais sem recursos gráficos;
- [ ] ADRs atualizados com o que foi rejeitado e por quê.

**Saída:** reconstrução documentada e ausência de dependência escondida do VS Code.

## Perfil respondido

- shell: Zsh com Oh My Zsh;
- sistema prioritário: Linux/Ubuntu;
- stack: Python/FastAPI e TypeScript/Next.js;
- agentes: Pi, Codex, Claude Code e Kimi Code;
- experiência com editor/TUI terminal: nenhuma;
- recursos insubstituíveis do VS Code: nenhum identificado.

A primeira recomendação derivada dessas respostas está em [Perfil e stack inicial](06-perfil-e-stack-inicial.md).

## Questões ainda abertas

1. Usa Docker, Kubernetes, banco SQL e cloud CLIs com que frequência?
2. Trabalha localmente, por SSH, em devcontainers ou em VMs?
3. Dotfiles podem ficar públicos? Há necessidade de segredos criptografados?
4. Que layout de teclado utiliza e quais atalhos já são memória muscular?
5. Quais permissões cada agente pode receber sem confirmação?

Essas respostas podem ser coletadas durante as fases relevantes; não bloqueiam o primeiro piloto.
