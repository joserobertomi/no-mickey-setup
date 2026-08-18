# Princípios e critérios

## 1. Agent-first, não agent-only

Agentes executam exploração, alteração e validação. O engenheiro continua responsável por formular intenção, definir restrições, revisar o diff e aceitar o resultado.

A interface humana deve ser otimizada principalmente para:

1. distribuir trabalho;
2. perceber bloqueios;
3. ler código e documentação;
4. inspecionar mudanças;
5. executar verificações;
6. integrar ou rejeitar alterações.

## 2. Um dono por responsabilidade

| Responsabilidade | Dono esperado |
|---|---|
| renderização, fonte, teclado e clipboard | WezTerm |
| panes, tabs, workspaces, persistência e estado de agente | Herdr |
| edição, LSP e navegação semântica | Neovim |
| estado e operações Git | lazygit + Git CLI |
| isolamento de tarefas concorrentes | Git worktrees + Worktrunk |
| runtimes e versões por projeto | mise |
| variáveis locais por diretório | direnv |
| comandos do projeto | just |
| dotfiles entre máquinas | chezmoi |

Evitar:

- tmux ou Zellij dentro do Herdr;
- panes/tabs do WezTerm competindo com panes/tabs do Herdr;
- tree explorer, Git UI e terminal embutido diferentes em cada editor;
- aliases que escondem operações destrutivas;
- dois atalhos globais para a mesma ação.

## 3. Composição antes de “super-app”

Preferir CLIs que:

- recebem e emitem texto;
- respeitam `stdin`, `stdout`, exit codes e `$EDITOR`/`$PAGER`;
- possuem configuração versionável;
- funcionam localmente e por SSH;
- não exigem daemon ou conta sem uma razão forte;
- podem ser substituídas sem reconstruir o ambiente inteiro.

TUIs são bem-vindas quando tornam estado complexo mais legível, como diffs, containers ou recursos Kubernetes.

## 4. O teclado precisa ter uma gramática

Namespaces sugeridos:

| Camada | Namespace |
|---|---|
| WezTerm/SO | `Ctrl+Shift+…` para janela, fonte e clipboard |
| Herdr | prefixo `Ctrl+b`, seguido da ação |
| Neovim | `<Space>` como leader e movimentos modais |
| shell | atalhos de linha e histórico |
| aplicações | atalhos locais, preferencialmente `j/k`, `/`, `?`, `q` |

Não capturar no WezTerm uma tecla necessária em uma camada inferior. Todo TUI deve oferecer ajuda por `?` quando configurável.

## 5. Concorrência exige isolamento

Dois agentes não devem editar a mesma worktree ao mesmo tempo. Cada tarefa paralela recebe:

- uma branch;
- uma worktree;
- um pane ou tab identificável no Herdr;
- um comando de validação determinístico;
- um diff revisável antes da integração.

O repositório e o Git são a fronteira de coordenação; o histórico da conversa do agente não é a fonte de verdade.

## 6. Automação precisa ser observável e reversível

- comandos comuns devem existir em `justfile`, scripts ou documentação do projeto;
- agentes devem executar os mesmos checks disponíveis ao humano;
- operações destrutivas precisam de confirmação;
- mudanças devem ser pequenas e revisáveis;
- segredos nunca entram em prompts, histórico do shell ou dotfiles públicos;
- o setup deve poder ser reconstruído em uma máquina limpa.

## 7. Critério para adotar uma ferramenta

Uma ferramenta só passa de **teste** para **adotada** se:

1. resolve uma dor frequente e nomeada;
2. é utilizável sem mouse;
3. integra-se ao fluxo por pipes, Git ou arquivos;
4. possui manutenção e documentação adequadas;
5. não duplica uma responsabilidade já atendida;
6. tem custo aceitável de inicialização e configuração;
7. funciona nos sistemas operacionais necessários;
8. é removível sem aprisionar dados.

## 8. Métricas práticas

Durante os testes, observar:

- tempo entre abrir o terminal e chegar ao projeto correto;
- teclas necessárias para achar arquivo, símbolo, erro e diff;
- tempo para identificar qual agente está bloqueado;
- frequência de conflitos entre agentes;
- tempo para revisar e integrar uma tarefa;
- quantidade de configuração customizada que precisa ser mantida;
- ocasiões em que ainda foi necessário abrir o VS Code.

O objetivo não é atingir zero uso de GUI por dogma, mas remover a **dependência** dela no fluxo de engenharia.
