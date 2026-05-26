# Plano de Implementação: Central de Chamados

## Visão Geral

Implementação do módulo Central de Chamados no `index.html` do SIGrh. Todo o código (dados, estado, renderização e lógica) é adicionado como um novo bloco `<script>` ao final do `<body>`, reutilizando 100% dos componentes CSS existentes. A integração ao menu lateral segue o padrão já estabelecido no sistema.

A implementação é feita em JavaScript vanilla (ES5/ES6), sem dependências externas adicionais.

---

## Tarefas

- [ ] 1. Integrar entrada no menu e criar estrutura base do módulo
  - Adicionar ao array `menu` a entrada `{ label:'Central de Chamados', icon:'fa-headset', sub:[{ label:'Central de Chamados', sub:[{ label:'Central de Chamados', action:'centralChamados' }] }] }`
  - Adicionar o bloco `if(a==='centralChamados') showCentralChamados()` na função `runAction(a)`
  - Criar o bloco `<script>` ao final do `<body>` com as variáveis de estado: `var ccPerfilAtivo = 'solicitante'` e `var ccChamadoAtual = null`
  - Criar o esqueleto vazio das funções: `showCentralChamados`, `ccRenderPerfil`, `ccSetPerfil`, `ccRenderSolicitante`, `ccRenderSuporteAdmin`
  - _Requisitos: 9.1, 9.2, 9.3_

- [ ] 2. Implementar dados simulados em memória
  - [ ] 2.1 Criar array `chamadosData` com os 6 chamados pré-populados (CHM-001 a CHM-006) cobrindo todos os status: Novo, Em análise, Em tratamento, Aguardando validação, Finalizado e Cancelado
    - Cada chamado deve ter: id, tipo, urgencia, area, solicitante, responsavel, status, descricao, dataAbertura, dataAtualizacao, historico (array de HistoricoEntry), anexos
    - CHM-002 deve conter ao menos um comentário interno e um público no histórico
    - Incluir variações de urgência (Baixa, Média, Alta, Crítica), área e tipo de solicitação
    - _Requisitos: 10.1, 10.2, 10.3, 10.4_
  - [ ] 2.2 Criar array `notificacoesData` com notificações simuladas correspondentes aos chamados pré-populados
    - Cada notificação deve ter: id, tipo, chamadoId, dataHora, lida
    - _Requisitos: 5.1, 5.2_
  - [ ] 2.3 Criar variável `ccNotifIdCounter` para auto-incremento de IDs de notificação
    - _Requisitos: 5.2_

- [ ] 3. Implementar funções de lógica pura (sem renderização)
  - [ ] 3.1 Implementar `ccValidarFormulario(campos)` — retorna `true` se todos os campos obrigatórios (tipo, urgencia, area, descricao) estão preenchidos; `false` caso contrário
    - _Requisitos: 2.3_
  - [ ]* 3.2 Escrever property test para `ccValidarFormulario` — Property 2: Formulário com campo obrigatório vazio não cria chamado
    - **Property 2: Formulário com campo obrigatório vazio não cria chamado**
    - **Valida: Requisito 2.3**
  - [ ] 3.3 Implementar `ccCorStatus(status)` — retorna um `<span>` com cor inline diferenciada para cada status (Novo: azul, Em análise: amarelo, Em tratamento: laranja, Aguardando validação: roxo, Finalizado: verde, Cancelado: cinza)
    - _Requisitos: 3.2_
  - [ ]* 3.4 Escrever property test para `ccCorStatus` — Property 5: Função de cor de status retorna valores distintos para cada status
    - **Property 5: Função de cor de status retorna valores distintos para cada status**
    - **Valida: Requisito 3.2**
  - [ ] 3.5 Implementar `ccOrdenarPorData(chamados)` — retorna array de chamados ordenado por `dataAbertura` decrescente
    - _Requisitos: 3.4_
  - [ ]* 3.6 Escrever property test para `ccOrdenarPorData` — Property 6: Lista de chamados do Solicitante está sempre em ordem decrescente de data
    - **Property 6: Lista de chamados do Solicitante está sempre em ordem decrescente de data**
    - **Valida: Requisito 3.4**
  - [ ] 3.7 Implementar `ccFiltrarChamados(chamados, filtros)` — retorna subarray com chamados que satisfazem todos os critérios de filtro ativos (status, urgencia, area, solicitante, responsavel)
    - _Requisitos: 6.3_
  - [ ]* 3.8 Escrever property test para `ccFiltrarChamados` — Property 7: Filtros da fila retornam apenas chamados que satisfazem todos os critérios
    - **Property 7: Filtros da fila retornam apenas chamados que satisfazem todos os critérios**
    - **Valida: Requisito 6.3**
  - [ ]* 3.9 Escrever property test para `ccFiltrarChamados` com filtros zerados — Property 8: Limpar filtros restaura todos os chamados na fila
    - **Property 8: Limpar filtros restaura todos os chamados na fila**
    - **Valida: Requisito 6.4**
  - [ ] 3.10 Implementar `ccContarPorStatus(chamados)` — retorna objeto `{ status: count }` com contagem de chamados por status
    - _Requisitos: 6.5_
  - [ ]* 3.11 Escrever property test para `ccContarPorStatus` — Property 9: Contadores do painel administrativo somam o total de chamados
    - **Property 9: Contadores do painel administrativo somam o total de chamados**
    - **Valida: Requisito 6.5**
  - [ ] 3.12 Implementar `ccGerarNotificacao(tipo, chamadoId)` — cria e adiciona entrada em `notificacoesData` com id auto-incrementado, tipo, chamadoId, dataHora atual e `lida: false`
    - _Requisitos: 2.4, 5.2_
  - [ ]* 3.13 Escrever property test para `ccGerarNotificacao` — Property 3: Criação de chamado gera notificação correspondente
    - **Property 3: Criação de chamado gera notificação correspondente**
    - **Valida: Requisitos 2.4, 5.2**
  - [ ] 3.14 Implementar `ccMarcarLidas()` — atualiza `lida: true` em todas as entradas de `notificacoesData`
    - _Requisitos: 5.4_
  - [ ]* 3.15 Escrever property test para `ccMarcarLidas` — Property 14: Visualizar painel de notificações zera o contador de não lidas
    - **Property 14: Visualizar painel de notificações zera o contador de não lidas**
    - **Valida: Requisito 5.4**

- [ ] 4. Checkpoint — Verificar lógica pura
  - Garantir que todas as funções de lógica pura retornam os valores esperados. Perguntar ao usuário se houver dúvidas antes de prosseguir.

- [ ] 5. Implementar Seletor de Perfil e estrutura de abas
  - [ ] 5.1 Implementar `showCentralChamados()` — chama `showPage('Central de Chamados', html)` com o container principal que inclui o seletor de perfil (`.alt-section`) e a área de conteúdo dinâmico `#cc-content`
    - Inicializar com `ccPerfilAtivo = 'solicitante'` e chamar `ccRenderPerfil()` ao final
    - _Requisitos: 1.1, 1.5, 9.2, 9.3_
  - [ ] 5.2 Implementar `ccSetPerfil(perfil)` — atualiza `ccPerfilAtivo`, atualiza destaque visual dos botões do seletor e chama `ccRenderPerfil()`
    - _Requisitos: 1.2, 1.3, 1.4_
  - [ ] 5.3 Implementar `ccRenderPerfil()` — despacha para `ccRenderSolicitante()` ou `ccRenderSuporteAdmin()` conforme `ccPerfilAtivo`, injetando o HTML em `#cc-content`
    - _Requisitos: 1.2, 1.3_

- [ ] 6. Implementar visão Solicitante — Formulário de abertura
  - [ ] 6.1 Implementar `ccRenderSolicitante()` — renderiza estrutura de abas (`.tabs`) com as abas: "Abrir Chamado", "Meus Chamados" e "Notificações"; ativa a aba "Abrir Chamado" por padrão
    - _Requisitos: 2.1, 3.1, 5.1_
  - [ ] 6.2 Implementar o formulário de abertura dentro da aba "Abrir Chamado" com os campos: Tipo da Solicitação (select), Nível de Urgência (select), Área do Usuário (select), Descrição (textarea) e Anexos (input file); usar `.frow`/`.fg` e labels com classe `.req` nos obrigatórios
    - _Requisitos: 2.1_
  - [ ] 6.3 Implementar `ccAbrirChamado()` — lê os campos do formulário, chama `ccValidarFormulario`, destaca campos inválidos com `border-color:#c00` e mensagem de erro em `<span>` quando inválido; quando válido: cria objeto chamado com id `CHM-NNN`, status `'Novo'`, dataAbertura atual, histórico inicial com `acao:'Chamado aberto'`, adiciona a `chamadosData`, chama `ccGerarNotificacao('Chamado criado', id)` e exibe mensagem de confirmação
    - _Requisitos: 2.2, 2.3, 2.4, 2.5_
  - [ ]* 6.4 Escrever property test para `ccAbrirChamado` — Property 1: Criação de chamado válido sempre resulta em status "Novo" com histórico inicial
    - **Property 1: Criação de chamado válido sempre resulta em status "Novo" com histórico inicial**
    - **Valida: Requisitos 2.2, 2.5**

- [ ] 7. Implementar visão Solicitante — Lista e detalhe de chamados
  - [ ] 7.1 Implementar `ccRenderListaChamados()` — filtra `chamadosData` pelo solicitante simulado ("João Silva"), ordena por `ccOrdenarPorData`, renderiza `table.grid` com colunas: Número, Tipo, Urgência, Área, Status (usando `ccCorStatus`), Data de Abertura e Última Atualização; cada linha com `onclick="ccRenderDetalhe(id,'solicitante')"`
    - _Requisitos: 3.1, 3.2, 3.3, 3.4_
  - [ ] 7.2 Implementar `ccRenderDetalhe(id, perfil)` — busca chamado em `chamadosData` por id, armazena em `ccChamadoAtual`; renderiza seção de informações (Número, Tipo, Urgência, Área, Status, Data de abertura, Responsável, Descrição) e histórico como linha do tempo
    - Na visão `'solicitante'`: exibir apenas entradas com `visibilidade === 'publico'` no histórico; exibir botão "Voltar" que chama `ccRenderSolicitante()`
    - Na visão `'suporte'`: exibir histórico completo (públicos + internos) com diferenciação visual; exibir barra de ações
    - _Requisitos: 4.1, 4.2, 4.3, 4.4, 8.1, 8.2, 8.3, 8.4_
  - [ ]* 7.3 Escrever property test para `ccRenderDetalhe` na visão solicitante — Property 4: Visão Solicitante nunca exibe comentários internos
    - **Property 4: Visão Solicitante nunca exibe comentários internos**
    - **Valida: Requisito 4.3**
  - [ ]* 7.4 Escrever property test para renderização do histórico — Property 12: Comentários internos e públicos recebem classes CSS distintas no histórico
    - **Property 12: Comentários internos e públicos recebem classes CSS distintas no histórico**
    - **Valida: Requisito 8.2**
  - [ ]* 7.5 Escrever property test para ordenação do histórico — Property 13: Histórico é sempre renderizado em ordem cronológica crescente
    - **Property 13: Histórico é sempre renderizado em ordem cronológica crescente**
    - **Valida: Requisito 8.3**

- [ ] 8. Implementar visão Solicitante — Notificações
  - [ ] 8.1 Implementar `ccRenderNotificacoes()` — renderiza lista de `notificacoesData` com tipo do evento, número do chamado e data/hora; exibe contador de não lidas; ao renderizar, chama `ccMarcarLidas()` e atualiza o contador para 0
    - _Requisitos: 5.1, 5.2, 5.3, 5.4_

- [ ] 9. Checkpoint — Verificar visão Solicitante completa
  - Garantir que as três abas da visão Solicitante funcionam: abertura de chamado (com validação), lista de chamados (ordenada, com cores de status) e notificações (com marcação de lidas). Perguntar ao usuário se houver dúvidas.

- [ ] 10. Implementar visão Suporte — Painel e fila de chamados
  - [ ] 10.1 Implementar `ccRenderSuporteAdmin()` — renderiza estrutura de abas com: "Painel", "Fila de Chamados" e aba dinâmica "Detalhe" (oculta até um chamado ser selecionado); ativa "Painel" por padrão
    - _Requisitos: 6.1, 6.5_
  - [ ] 10.2 Implementar `ccRenderPainelAdmin()` — chama `ccContarPorStatus(chamadosData)` e renderiza cards de contagem por status em `.frow` dentro da aba "Painel"
    - _Requisitos: 6.5_
  - [ ] 10.3 Implementar `ccRenderFila()` — renderiza linha de filtros (`.frow`/`.fg`) com: Status (select multiple), Urgência (select multiple), Área (select), Solicitante (input text), Responsável (select com equipe + "Sem responsável"); abaixo, renderiza `table.grid` com colunas: Número, Tipo, Urgência, Área, Solicitante, Responsável, Status e Data de Abertura; cada linha com `onclick="ccRenderDetalhe(id,'suporte')"`
    - _Requisitos: 6.1, 6.2_
  - [ ] 10.4 Implementar `ccAplicarFiltros()` — lê os controles de filtro do DOM, chama `ccFiltrarChamados` com os critérios lidos e re-renderiza apenas a tabela da fila
    - _Requisitos: 6.3_
  - [ ] 10.5 Implementar `ccLimparFiltros()` — reseta todos os controles de filtro para o estado padrão e re-renderiza a tabela com todos os chamados
    - _Requisitos: 6.4_

- [ ] 11. Implementar ações do Suporte — Modais e dispatcher
  - [ ] 11.1 Adicionar ao HTML os modais necessários após `#layout`: `modal-cc-assumir`, `modal-cc-status`, `modal-cc-comentario`, `modal-cc-finalizar`, `modal-cc-cancelar`; cada modal usa `.modal-overlay` / `.modal-box` / `.modal-title` / `.modal-body` / `.modal-footer` conforme padrão existente
    - _Requisitos: 7.1, 7.2, 7.3, 7.5, 7.6_
  - [ ] 11.2 Implementar `ccAcaoSuporte(tipo, id)` — dispatcher que abre o modal correto conforme `tipo` ('assumir', 'status', 'comentario', 'finalizar', 'cancelar') ou executa ação direta para 'encaminhar'; popula o modal com dados do chamado atual
    - _Requisitos: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6_
  - [ ] 11.3 Implementar `ccConfirmarAcao(tipo, id, dados)` — aplica a ação em `chamadosData`: atualiza `responsavel`, `status`, `dataAtualizacao` e adiciona entrada ao `historico`; chama `ccGerarNotificacao` com o tipo correspondente; fecha o modal; re-renderiza o detalhe do chamado
    - Validar inline no modal: responsável obrigatório em "Assumir", texto obrigatório em "Comentário"
    - _Requisitos: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7_
  - [ ]* 11.4 Escrever property test para `ccConfirmarAcao('assumir', ...)` — Property 10: Ação "Assumir chamado" atualiza responsável, status e histórico
    - **Property 10: Ação "Assumir chamado" atualiza responsável, status e histórico**
    - **Valida: Requisito 7.1**
  - [ ]* 11.5 Escrever property test para `ccConfirmarAcao` (qualquer ação) — Property 11: Qualquer ação do Suporte gera notificação para o Solicitante
    - **Property 11: Qualquer ação do Suporte gera notificação para o Solicitante**
    - **Valida: Requisitos 7.7, 5.2**

- [ ] 12. Checkpoint final — Verificar módulo completo
  - Garantir que todas as funcionalidades estão integradas: menu lateral abre a Central de Chamados, seletor de perfil alterna as visões, formulário cria chamados com validação, lista exibe chamados ordenados, detalhe exibe histórico correto por perfil, fila com filtros funciona, ações do Suporte atualizam dados e geram notificações. Perguntar ao usuário se houver dúvidas.

---

## Notas

- Tarefas marcadas com `*` são opcionais e podem ser puladas para uma entrega mais rápida
- Os testes de propriedade (PBT) requerem `fast-check` + `vitest` (`npm install --save-dev fast-check vitest`) — aplicável apenas se o ambiente de testes for configurado separadamente do `index.html`
- Cada tarefa referencia os requisitos específicos para rastreabilidade
- Toda a implementação ocorre em `PROJETOS KIRO ESIG/index.html` — nenhum arquivo adicional é necessário para o protótipo funcionar
- Os dados simulados vivem apenas em memória; não há persistência entre recarregamentos (comportamento esperado do protótipo)
