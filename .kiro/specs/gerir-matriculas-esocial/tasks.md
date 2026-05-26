# Plano de Implementação: Gerir Matrículas eSocial

## Visão Geral

Implementação da funcionalidade **Gerir Matrículas eSocial** no protótipo single-file `index.html` (vanilla JS/CSS). A implementação segue o padrão já estabelecido no SIGrh: entrada no array `menu`, função de ação, renderização via `showPage()` e funções auxiliares em `<script>` no final do `<body>`.

## Tarefas

- [x] 1. Estrutura de dados e dados iniciais em memória
  - Declarar o array `var matriculasEsocial = [...]` com ao menos 3 registros de exemplo cobrindo os dois tipos de tratamento (`ficticia` e `datas_ajustadas`) e situações variadas (`Ativa`, `Vigência finalizada`)
  - Declarar as variáveis de controle: `var meSelectedIdx = -1`, `var mePageAtual = 1`, `var meTamanhoPagina = 10`, `var meRegistrosFiltrados = []`
  - Inserir o item de menu `{ label: 'Gerir Matriculas eSocial', action: 'gerirMatriculasEsocial' }` dentro do submódulo `eSocial > Geracao/Transmissao` no array `menu`, condicionado ao perfil NUGESP em `buildMenu()`
  - _Requirements: 1.1, 2.1, 11.1, 11.2, 11.3_

- [x] 2. Controle de acesso e função principal de entrada
  - [x] 2.1 Implementar `showGerirMatriculasEsocial()`
    - Verificar `usuarioAtual.perfis.indexOf('NUGESP') >= 0`; se ausente, chamar `showPage()` com mensagem de acesso negado e retornar
    - Caso contrário, chamar `showPage()` com o HTML da página principal (título + área de filtros + barra de ações + `#me-grid-area` + `#me-form-area` vazio)
    - Chamar `listarMatriculasEsocial()` ao final para popular a grade inicial
    - _Requirements: 1.1, 1.2_

  - [ ]* 2.2 Escrever teste de propriedade — Property 1: visibilidade do menu por perfil
    - **Property 1: Visibilidade do menu controlada pelo perfil**
    - Para qualquer array de perfis, o item de menu deve aparecer se e somente se `'NUGESP'` está presente
    - **Validates: Requirements 1.1**

- [x] 3. Listagem, filtros e paginação
  - [x] 3.1 Implementar `listarMatriculasEsocial()`
    - Ler os valores dos 10 campos de filtro e aplicar filtragem sobre `matriculasEsocial`
    - Armazenar resultado em `meRegistrosFiltrados`
    - Calcular paginação e renderizar `table.grid` com as 14 colunas especificadas dentro de `#me-grid-area`
    - Renderizar controles de paginação (anterior / página X de Y / próxima)
    - Ao clicar em uma linha, adicionar classe `selected`, atualizar `meSelectedIdx` e habilitar botões de ação
    - _Requirements: 2.1, 2.2, 2.3, 3.1, 3.2, 3.3, 10.4_

  - [ ]* 3.2 Escrever teste de propriedade — Property 2: grade com 14 colunas
    - **Property 2: Grade renderiza todas as 14 colunas obrigatórias**
    - Para qualquer array `matriculasEsocial` (incluindo vazio), o HTML gerado deve conter exatamente os 14 cabeçalhos especificados
    - **Validates: Requirements 2.1**

  - [ ]* 3.3 Escrever teste de propriedade — Property 3: paginação consistente
    - **Property 3: Paginação é consistente com o tamanho do array**
    - Para N registros filtrados e página P, número de páginas = `Math.ceil(N/P)` e página atual exibe no máximo P registros
    - **Validates: Requirements 2.2**

  - [ ]* 3.4 Escrever teste de propriedade — Property 4: seleção de linha habilita ações
    - **Property 4: Seleção de linha habilita ações**
    - Para qualquer índice válido, clicar na linha deve adicionar `selected` e atualizar `meSelectedIdx`
    - **Validates: Requirements 2.3**

  - [x] 3.5 Implementar `filtrarMatriculasEsocial()` e `limparFiltrosME()`
    - `filtrarMatriculasEsocial()`: aplicar todos os filtros (texto e período) e chamar `listarMatriculasEsocial()`
    - `limparFiltrosME()`: zerar todos os inputs/selects de filtro e chamar `listarMatriculasEsocial()`
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [ ]* 3.6 Escrever teste de propriedade — Property 5: interseção de vigência no filtro
    - **Property 5: Lógica de interseção de vigência**
    - Para qualquer par de datas de filtro `[A, B]`, retornar exatamente os registros onde `dataFimVigencia >= A` E `dataIniVigencia <= B` (fim nulo = vigência aberta)
    - **Validates: Requirements 3.2, 9.5**

  - [ ]* 3.7 Escrever teste de propriedade — Property 6: limpar filtros é round-trip
    - **Property 6: Limpar filtros é operação de round-trip**
    - Após `limparFiltrosME()`, todos os campos de filtro devem estar vazios/padrão e a grade deve exibir todos os registros
    - **Validates: Requirements 3.4**

- [ ] 4. Checkpoint — Verificar listagem, filtros e paginação
  - Garantir que todos os testes passam; tirar dúvidas com o usuário se necessário.

- [x] 5. Formulário de cadastro de nova regra
  - [x] 5.1 Implementar `abrirFormNovaRegra()`
    - Renderizar o formulário inline em `#me-form-area` com as três seções: "Dados do Servidor", "Parametrização eSocial" e "Auditoria"
    - Incluir todos os campos especificados no design (CPF + botão buscar, Nome readonly, Vínculo select, Matrícula original readonly, Tipo de tratamento select, Matrícula eSocial/fictícia, Categoria eSocial, datas de vigência e ajustadas, Forçar consideração, Situação, Justificativa)
    - Ao selecionar "Tipo de tratamento", chamar lógica que habilita/desabilita e torna obrigatório o campo Matrícula eSocial/fictícia
    - _Requirements: 4.1, 4.2, 4.3_

  - [ ]* 5.2 Escrever teste de propriedade — Property 7: estado do campo matrícula fictícia
    - **Property 7: Estado do campo matrícula fictícia segue o tipo de tratamento**
    - Para qualquer valor de "Tipo de tratamento", o campo deve ser obrigatório/habilitado sse tipo = `'ficticia'`
    - **Validates: Requirements 4.2, 4.3**

  - [x] 5.3 Implementar `detectarSobreposicao(cpf, vinculo, iniNova, fimNova, idxExcluir)`
    - Percorrer `matriculasEsocial` e retornar `true` se existir registro com mesmo CPF + vínculo (excluindo `idxExcluir`) cujo intervalo intersecta `[iniNova, fimNova]`
    - Usar a regra: `dataFimVigencia >= iniNova` E `dataIniVigencia <= fimNova` (fim nulo = infinito)
    - _Requirements: 4.5_

  - [ ]* 5.4 Escrever teste de propriedade — Property 9: detecção de sobreposição
    - **Property 9: Detecção de sobreposição de vigência**
    - Para qualquer par de intervalos `[A1,B1]` e `[A2,B2]`, `detectarSobreposicao()` retorna `true` sse `B1 >= A2` E `A1 <= B2`
    - **Validates: Requirements 4.5**

  - [x] 5.5 Implementar `salvarNovaRegra()`
    - Validar campos obrigatórios; exibir `alert()` com campos pendentes se inválido
    - Chamar `detectarSobreposicao()` e exibir `alert()` de conflito se houver sobreposição
    - Criar novo objeto com `id` auto-incremento, campos do formulário, `usuarioCadastro = usuarioAtual.nome`, `dataCadastro` com data/hora atual, `situacao = 'Ativa'`
    - Fazer push em `matriculasEsocial`, fechar formulário e chamar `listarMatriculasEsocial()`
    - _Requirements: 4.4, 4.5, 4.6, 4.7, 10.1_

  - [ ]* 5.6 Escrever teste de propriedade — Property 8: validação rejeita campos obrigatórios ausentes
    - **Property 8: Validação rejeita qualquer combinação com campo obrigatório ausente**
    - Para qualquer estado de formulário com ao menos um campo obrigatório vazio, a validação deve retornar `false` e nenhum registro deve ser adicionado
    - **Validates: Requirements 4.4, 5.3, 6.2, 7.2**

  - [ ]* 5.7 Escrever teste de propriedade — Property 10: campos de auditoria preenchidos ao salvar
    - **Property 10: Campos de auditoria são sempre preenchidos ao salvar**
    - Para qualquer operação válida de criação, o registro deve ter `usuarioCadastro`, `dataCadastro` e `justificativaCriacao` preenchidos
    - **Validates: Requirements 4.6, 10.1**

- [x] 6. Formulário de alteração de regra
  - [x] 6.1 Implementar `abrirFormAlterarRegra(idx)`
    - Verificar se `meSelectedIdx >= 0`; caso contrário, exibir `alert('Selecione um registro na lista.')`
    - Renderizar o formulário inline em `#me-form-area` com os dados do registro em `matriculasEsocial[idx]`
    - Campos editáveis conforme Requirement 5.2; CPF, Nome e Matrícula original em modo readonly
    - _Requirements: 5.1, 5.2_

  - [x] 6.2 Implementar `salvarAlteracaoRegra(idx)`
    - Validar campos obrigatórios; exibir `alert()` se inválido
    - Atualizar o registro em `matriculasEsocial[idx]` com os novos valores
    - Registrar `usuarioAlteracao = usuarioAtual.nome` e `dataAlteracao` com data/hora atual
    - Fechar formulário e chamar `listarMatriculasEsocial()`
    - _Requirements: 5.3, 5.4, 10.2_

- [x] 7. Modal "Alterar Matrícula eSocial" (menu + Opções)
  - [x] 7.1 Implementar `abrirModalAlterarMatricula(idx)`
    - Verificar seleção; exibir `alert()` se nenhum registro selecionado
    - Renderizar `.modal-overlay` com `.modal-box` (560px) contendo os campos: Matrícula eSocial/fictícia, Categoria eSocial, datas de vigência e ajustadas, Forçar consideração, Justificativa da alteração (obrigatório)
    - _Requirements: 6.1_

  - [x] 7.2 Implementar `salvarAlteracaoMatricula(idx)`
    - Validar Justificativa da alteração; exibir `alert()` se vazia
    - Atualizar campos correspondentes em `matriculasEsocial[idx]`
    - Registrar `usuarioAlteracao` e `dataAlteracao`; fechar modal e chamar `listarMatriculasEsocial()`
    - _Requirements: 6.2, 6.3, 10.2_

- [ ] 8. Modal "Finalizar Vigência" (menu + Opções)
  - [x] 8.1 Implementar `abrirModalFinalizarVigencia(idx)`
    - Verificar seleção; exibir `alert()` se nenhum registro selecionado
    - Renderizar `.modal-overlay` com `.modal-box` (460px) contendo: Data fim da vigência (obrigatório) e Justificativa da finalização (obrigatório)
    - _Requirements: 7.1_

  - [x] 8.2 Implementar `confirmarFinalizarVigencia(idx)`
    - Validar ambos os campos; exibir `alert()` se algum estiver vazio
    - Atualizar `dataFimVigencia`, `situacao = 'Vigência finalizada'`, `usuarioFinalizacao`, `dataFinalizacao`, `justificativaFinalizacao`
    - Fechar modal e chamar `listarMatriculasEsocial()`
    - _Requirements: 7.2, 7.3, 10.3_

  - [ ]* 8.3 Escrever teste de propriedade — Property 11: finalizar vigência altera situação
    - **Property 11: Finalizar vigência altera situação para "Vigência finalizada"**
    - Para qualquer registro com situação "Ativa", após `confirmarFinalizarVigencia()` com dados válidos, situação deve ser `'Vigência finalizada'` e `dataFimVigencia` deve ser a data informada
    - **Validates: Requirements 7.3**

- [ ] 9. Checkpoint — Verificar CRUD completo e modais
  - Garantir que todos os testes passam; tirar dúvidas com o usuário se necessário.

- [ ] 10. Função de aplicação de regras eSocial
  - [ ] 10.1 Implementar `aplicarRegraEsocial(vinculo, periodoIni, periodoFim)`
    - Buscar em `matriculasEsocial` a regra ativa com `vinculoOriginal === vinculo` e vigência que intersecta `[periodoIni, periodoFim]`
    - Retornar `{ matricula: matriculaEsocial, datas: null }` se tipo = `'ficticia'`
    - Retornar `{ matricula: matriculaOriginal, datas: { ini: dataIniEsocialAjustada, fim: dataFimEsocialAjustada } }` se tipo = `'datas_ajustadas'`
    - Retornar comportamento padrão (matrícula original, sem ajuste) se nenhuma regra ativa encontrada
    - Considerar `forcarConsideracao` no resultado retornado
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5_

  - [ ]* 10.2 Escrever teste de propriedade — Property 12: aplicação de regra para qualquer vínculo e período
    - **Property 12: Aplicação de regra retorna resultado correto para qualquer vínculo e período**
    - Para qualquer vínculo e período, `aplicarRegraEsocial()` deve retornar matrícula fictícia, original com datas ajustadas, ou padrão conforme a regra ativa
    - **Validates: Requirements 9.1, 9.2, 9.3, 9.4**

- [ ] 11. Modal de impressão
  - [ ] 11.1 Implementar `abrirModalImprimir()`
    - Renderizar `.modal-overlay` com `.modal-box` (560px) reutilizando o padrão do `modal-imprimir-ecp` já existente
    - Campos: Título do relatório, Disposição (Retrato/Paisagem), Formato (PDF/Excel/HTML)
    - _Requirements: 8.1_

  - [ ] 11.2 Implementar geração do relatório de impressão
    - Ao confirmar, usar `meRegistrosFiltrados` como fonte de dados (respeitar filtros aplicados)
    - Exibir `alert()` confirmando geração e fechar modal
    - _Requirements: 8.2_

  - [ ]* 11.3 Escrever teste de propriedade — Property 13: impressão respeita filtros aplicados
    - **Property 13: Impressão respeita os filtros aplicados**
    - Para qualquer estado de filtro, os registros passados para o relatório devem ser idênticos a `meRegistrosFiltrados` no momento do acionamento
    - **Validates: Requirements 8.2**

- [ ] 12. Integração final e wiring
  - [ ] 12.1 Registrar `gerirMatriculasEsocial` no dispatcher `runAction(a)`
    - Adicionar `if (a === 'gerirMatriculasEsocial') showGerirMatriculasEsocial();` na função `runAction`
    - _Requirements: 1.1_

  - [ ] 12.2 Conectar todos os botões e eventos da página
    - Botão [Novo] → `abrirFormNovaRegra()`
    - Botão [Alterar] → `abrirFormAlterarRegra(meSelectedIdx)`
    - Botão [Imprimir] → `abrirModalImprimir()`
    - Dropdown "+ Opções" → toggle do `.dropdown-menu`
    - Item "Alterar Matrícula eSocial" → `abrirModalAlterarMatricula(meSelectedIdx)`
    - Item "Finalizar Vigência" → `abrirModalFinalizarVigencia(meSelectedIdx)`
    - Botão [Pesquisar] → `filtrarMatriculasEsocial()`
    - Botão [Limpar] → `limparFiltrosME()`
    - _Requirements: 2.3, 3.1, 3.4, 4.1, 5.1, 6.1, 7.1, 8.1_

  - [ ] 12.3 Verificar integridade dos dados de origem
    - Confirmar que nenhuma função modifica os arrays `servidores` ou dados de vínculo funcional
    - _Requirements: 11.1, 11.2, 11.3_

- [ ] 13. Checkpoint final — Garantir que todos os testes passam
  - Garantir que todos os testes passam; tirar dúvidas com o usuário se necessário.

## Notas

- Tarefas marcadas com `*` são opcionais e podem ser puladas para um MVP mais rápido
- Cada tarefa referencia os requisitos específicos para rastreabilidade
- Os testes de propriedade usam [fast-check](https://github.com/dubzzz/fast-check) via CDN, mínimo 100 iterações cada
- Testes unitários e de propriedade são complementares — ambos devem ser implementados quando não marcados como opcionais
- A implementação segue o padrão single-file `index.html`: todo código JS vai em `<script>` no final do `<body>`
