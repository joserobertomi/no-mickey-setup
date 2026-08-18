# ADR 0003 — LazyVim como editor terminal inicial

- **Status:** proposto
- **Data:** 2026-08-17

## Contexto

O setup precisa substituir edição, navegação semântica, diagnostics e LSP do VS Code. Não há experiência anterior com editores modais ou TUIs, e as linguagens principais são Python/FastAPI e TypeScript/Next.js.

Construir Neovim do zero mistura duas curvas de aprendizado: interação modal e arquitetura/configuração de plugins. Uma distribuição excessivamente customizada, por outro lado, pode esconder comportamento e criar manutenção prematura.

## Decisão proposta

Testar Neovim com LazyVim como ponte inicial. Ativar apenas suporte necessário para Python e TypeScript/Next.js e manter responsabilidades externas fora do editor:

- Herdr possui terminais e panes;
- Yazi possui exploração visual de arquivos;
- lazygit possui Git UI;
- Neovim possui buffers, edição, LSP, diagnostics, quickfix e navegação semântica.

Plugins adicionais exigem uma dor repetida e registrada. A primeira semana prioriza aprender defaults e a ajuda de atalhos.

## Consequências positivas

- experiência próxima de IDE disponível rapidamente;
- atalhos descobríveis e ecossistema amplo;
- suporte maduro a Python, TypeScript, Treesitter e LSP;
- permite formar vocabulário modal antes de projetar configuração própria.

## Consequências negativas

- mais abstrações e plugins do que uma configuração mínima;
- atualizações podem alterar defaults;
- risco de copiar atalhos sem entender o modelo do Vim;
- alguma duplicação nativa pode precisar ser desativada ou simplesmente ignorada.

## Alternativas consideradas

- **kickstart.nvim:** base menor e educativa; candidato se as convenções do LazyVim atrapalharem.
- **Helix:** LSP e Treesitter com menos configuração; perde parte da extensibilidade e do material/ecossistema Neovim desejado.
- **Vim/Neovim sem plugins:** ótimo para fundamentos, insuficiente como substituição inicial do fluxo LSP do VS Code.
- **continuar no VS Code:** mantém produtividade imediata, mas não valida o objetivo terminal-first.

## Experimento de aceitação

Durante pelo menos uma tarefa real em Python e uma em TypeScript:

1. abrir e alternar arquivos sem mouse;
2. usar definition, references, rename e diagnostics;
3. formatar e executar checks externos;
4. revisar código sem abrir VS Code;
5. identificar quais plugins/defaults trouxeram valor ou confusão.

Aceitar o ADR se a operação ficar confiável e a manutenção compreensível. Trocar para kickstart.nvim se o usuário depender de comportamento que não consegue explicar ou depurar.
