# Arquitetura e workflow

## Camadas

### WezTerm: borda do sistema

Responsável por renderização, fontes, cores, entrada de teclado, clipboard e abertura da conexão local/SSH. O domínio de tabs/panes fica desocupado para o Herdr.

Configuração desejada:

- uma fonte com Nerd Font apenas se os ícones trouxerem valor;
- protocolo de teclado moderno sem quebrar TUIs;
- clipboard por OSC 52 em sessões remotas;
- atalhos de janela que não conflitem com `Ctrl+b`, `<Space>` ou aplicações;
- configuração pequena e versionada.

### Herdr: runtime de trabalho

Responsável por workspaces, tabs, panes, persistência, attach/detach e status dos agentes. Os cinco atalhos iniciais documentados pelo Herdr são:

| Ação | Atalho padrão |
|---|---|
| nova tab | `Ctrl+b`, `c` |
| split horizontal/vertical | `Ctrl+b`, `v` / `Ctrl+b`, `-` |
| mover entre panes | `Ctrl+b`, `h/j/k/l` |
| navegar workspaces | `Ctrl+b`, `w` |
| detach | `Ctrl+b`, `q` |

`Ctrl+b`, `?` abre a ajuda dos atalhos ativos. Depois desses comandos, aprender zoom, resize, copy mode e navegação direta conforme a necessidade, não antecipadamente.

Instalar somente a integração dos agentes realmente usados, por exemplo `herdr integration install pi`. A integração fornece estado e/ou restauração de sessão mais confiáveis do que inferência pela saída do terminal.

### Aplicações: superfícies especializadas

Cada aplicação ocupa um pane temporário ou uma tab estável. Evitar abrir shells dentro do Neovim apenas para reconstruir um multiplexador dentro do editor.

## Layout inicial por repositório

Não fixar um dashboard complexo. Começar com quatro papéis:

```text
workspace: projeto
├── tab: control
│   ├── shell/just/watch
│   └── lazygit
├── tab: review
│   └── nvim
├── tab: task-123
│   └── agente na worktree task-123
└── tab: task-456
    └── agente na worktree task-456
```

Os panes de agentes podem surgir e desaparecer. `control` e `review` são superfícies humanas estáveis.

## Ciclo de uma tarefa

### 1. Formular

Definir antes de delegar:

- resultado observável;
- arquivos ou áreas permitidas, quando aplicável;
- restrições de arquitetura e compatibilidade;
- comando de teste/lint/typecheck;
- condições que obrigam o agente a parar e perguntar.

### 2. Isolar

Criar branch/worktree com Git ou Worktrunk. Convenção sugerida:

```text
branch:   agent/<ticket>-<slug>
worktree: ../<repo>.<ticket>-<slug>
herdr:    <ticket>-<slug>
```

Uma worktree pertence a uma tarefa ativa. Nunca iniciar dois agentes gravando no mesmo diretório.

### 3. Delegar

Abrir o agente dentro da worktree, entregar o contrato da tarefa e deixar o Herdr acompanhar `working`, `blocked` ou `idle`. O agente deve ler instruções versionadas no repositório e usar comandos de projeto, não comandos privados mantidos apenas no prompt.

### 4. Observar sem microgerenciar

Usar os estados do Herdr para decidir onde a atenção é necessária. Não alternar panes continuamente para “ver se está trabalhando”. Intervir quando:

- o agente está bloqueado;
- a execução extrapola o escopo;
- surgiu uma decisão irreversível;
- checks repetidamente falham sem novo diagnóstico.

### 5. Revisar

Ordem sugerida:

1. ler o resumo do agente, sem tratá-lo como evidência;
2. inspecionar `git status` e a lista de arquivos;
3. ler o diff no lazygit/delta;
4. usar difftastic quando a estrutura estiver difícil de enxergar;
5. abrir trechos críticos no Neovim e seguir referências via LSP;
6. executar checks pelo `just`;
7. verificar testes ausentes, segurança, compatibilidade e alterações incidentais.

### 6. Integrar e limpar

Depois da aprovação:

- atualizar/rebasear sobre a base adequada;
- repetir checks afetados;
- merge/cherry-pick conforme a política do repositório;
- remover worktree e branch temporária;
- fechar tab/pane;
- registrar decisões duradouras no repositório, não na conversa.

## Substituição das superfícies do VS Code

| VS Code | Setup terminal-first | Atalho mental |
|---|---|---|
| Explorer | Yazi; `fd | fzf` | navegar/selecionar |
| Quick Open | fzf ou picker do Neovim | procurar arquivo |
| Search | ripgrep + fzf/quickfix | procurar conteúdo |
| Outline/Symbols | LSP no Neovim | procurar símbolo |
| Editor | Neovim | ler/editar |
| Source Control | lazygit + Git CLI | revisar/integrar |
| Integrated Terminal | pane do Herdr | executar/observar |
| Tasks | just | comando nomeado |
| runtime manager | mise | reproduzir versão |
| env por workspace | direnv | ativar contexto |
| REST client | xh/Posting | testar API |
| Docker extension | lazydocker | observar containers |
| Kubernetes extension | k9s | operar cluster |
| Database extension | Rainfrog | consultar banco |
| Markdown preview | Glow | ler documentação |
| Problems panel | quickfix/diagnostics + saída do `just` | corrigir checks |
| Debug UI | debugger da linguagem/CLI; adapter no Neovim se necessário | depurar |

## Clipboard, seleção e URLs

Um fluxo sem mouse ainda precisa mover dados com segurança:

- usar copy mode do Herdr para scrollback;
- permitir OSC 52 no caminho Herdr → WezTerm quando local/remoto;
- manter atalhos explícitos de copiar/colar no WezTerm;
- usar keyboard selection/hints do WezTerm para URLs, se configurado;
- nunca copiar automaticamente saídas que possam conter secrets.

## Contrato mínimo de cada projeto

Para agentes e humanos compartilharem a mesma operação, cada repositório deveria convergir para:

```text
README.md / AGENTS.md   contexto e restrições
mise.toml               versões de ferramentas
.envrc                  ativação local revisável
justfile                setup, dev, test, lint, typecheck, check
.gitignore              artefatos e secrets locais
```

Nem todo projeto precisa de todos esses arquivos. O importante é haver um caminho documentado e não interativo para setup e validação.

Exemplo de interface, independente da implementação interna:

```bash
just setup
just dev
just test
just lint
just typecheck
just check       # conjunto exigido antes de integrar
```

## Guardrails para agentes

- permissões mínimas por padrão;
- sem acesso indiscriminado a `$HOME`, credenciais ou sockets;
- confirmação humana para deploy, publish, migrations destrutivas e force-push;
- logs e exit codes preservados;
- dependências novas justificadas no diff;
- nada de desabilitar checks para produzir uma execução verde;
- commits pequenos o bastante para revisão e reversão.
