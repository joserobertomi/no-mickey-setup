# ADR 0002 — Uma worktree por tarefa concorrente

- **Status:** proposto
- **Data:** 2026-08-17

## Contexto

Vários agentes no mesmo checkout podem editar os mesmos arquivos, trocar a branch ativa, sobrescrever mudanças e tornar impossível atribuir um diff a uma tarefa. O multiplexador separa terminais, mas não separa o filesystem.

## Decisão proposta

Cada tarefa concorrente terá sua própria branch e Git worktree. O pane/tab do Herdr deve iniciar dentro dessa worktree. Worktrunk será testado como camada ergonômica, mantendo `git worktree` como mecanismo e fallback.

## Consequências positivas

- filesystem e index separados por tarefa;
- branch e diff atribuíveis a um agente/objetivo;
- checks podem rodar em paralelo;
- fácil descartar uma tentativa inteira;
- revisão e integração seguem primitives do Git.

## Consequências negativas

- maior uso de disco, especialmente com dependências e builds por worktree;
- serviços precisam de portas, bancos ou nomes isolados;
- arquivos ignorados e secrets locais podem exigir bootstrap em cada worktree;
- branches dependentes ainda exigem coordenação de ordem e rebase.

## Guardrails

- nunca compartilhar uma worktree gravável entre agentes simultâneos;
- não remover worktree com mudanças não revisadas;
- usar comandos de cleanup explícitos;
- distinguir tarefas independentes de tarefas que deveriam ser sequenciais;
- documentar como dependências, env e portas são preparados por worktree.

## Validação

O ADR passa para **aceito** após o experimento da Fase 3: duas tarefas reais em paralelo, revisão individual, integração/remoção e cleanup sem perda de trabalho.
