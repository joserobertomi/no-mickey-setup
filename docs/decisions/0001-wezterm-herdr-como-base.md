# ADR 0001 — WezTerm e Herdr como base do ambiente

- **Status:** aceito
- **Data:** 2026-08-17

## Contexto

O ambiente migrou do VS Code como superfície central para agentes executados no terminal. É necessário manter sessões persistentes, navegar entre vários agentes pelo teclado e enxergar quais precisam de atenção, sem acoplar o workflow a um agente específico.

## Decisão

- WezTerm será o emulador de terminal e dono de renderização, entrada, clipboard e janela do sistema.
- Herdr será o único multiplexador, dono de workspaces, tabs, panes, persistência e estado dos agentes.
- Tabs/panes nativos do WezTerm não serão usados para reconstruir uma segunda hierarquia de trabalho.
- tmux e Zellij não serão aninhados no Herdr.
- Integrações do Herdr serão habilitadas apenas para agentes em uso.

## Consequências positivas

- uma gramática de navegação única;
- sessões recuperáveis e acessíveis por attach/SSH;
- percepção de agentes working/blocked/idle;
- liberdade para trocar ou combinar agentes CLI;
- menor chance de conflitos de atalhos e multiplexação aninhada.

## Consequências negativas

- dependência operacional do Herdr para restaurar a sessão completa;
- necessidade de aprender e versionar seu keymap/configuração;
- recursos de multiplexação do WezTerm ficam deliberadamente sem uso;
- automações dependentes da API do Herdr precisarão de adaptação se o mux mudar.

## Alternativas consideradas

- **tmux:** maduro e onipresente, mas não oferece por padrão a semântica de estado e as integrações agent-native procuradas.
- **Zellij:** boa experiência e layouts, mas também duplicaria a responsabilidade assumida pelo Herdr.
- **somente WezTerm:** reduz componentes, porém não entrega o mesmo runtime persistente e awareness de agentes.
- **VS Code como mux:** experiência anterior funcional, mas mantém dependência da GUI e não atende ao objetivo terminal-first.

## Critérios para revisitar

Reavaliar se o Herdr deixar de oferecer persistência confiável, integração com os agentes escolhidos, operação por SSH ou keybindings configuráveis; ou se seu custo de manutenção superar os benefícios de awareness de agentes.
