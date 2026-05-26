# Design Document — Gerir Matrículas eSocial

## Overview

A funcionalidade **Gerir Matrículas eSocial** é implementada como uma nova página do protótipo SIGrh (single-file `index.html`, vanilla JS/CSS). Ela permite que usuários com perfil NUGESP cadastrem, alterem e finalizem **Regras de Matrícula**, que parametrizam como cada vínculo de servidor é enviado ao eSocial — substituindo lógica hardcoded em SQL por configuração gerenciável na interface.

A funcionalidade não altera dados de Pessoa nem de Vínculo funcional; opera exclusivamente sobre o array `var matriculasEsocial = [...]` em memória.

---

## Architecture

O sistema segue o padrão já estabelecido no `index.html`:

```
menu (array JS)
  └─ eSocial > Geracao/Transmissao
       └─ { label: 'Gerir Matriculas eSocial', action: 'gerirMatriculasEsocial' }

runAction(a)
  └─ if (a === 'gerirMatriculasEsocial') showGerirMatriculasEsocial()

showGerirMatriculasEsocial()
  └─ verifica perfil NUGESP
  └─ chama showPage(htmlString)

Funções auxiliares (todas em <script> no final do <body>):
  ├─ listarMatriculasEsocial()          — renderiza a grade com filtros aplicados
  ├─ abrirFormNovaRegra()               — exibe seção de cadastro
  ├─ abrirFormAlterarRegra(idx)         — exibe seção de edição
  ├─ abrirModalAlterarMatricula(idx)    — abre modal "+ Opções > Alterar Matrícula eSocial"
  ├─ abrirModalFinalizarVigencia(idx)   — abre modal "+ Opções > Finalizar Vigência"
  ├─ abrirModalImprimir()               — abre modal de impressão
  ├─ salvarNovaRegra()                  — valida e persiste novo registro
  ├─ salvarAlteracaoRegra()             — valida e persiste alteração
  ├─ salvarAlteracaoMatricula()         — valida e persiste alteração via modal
  ├─ confirmarFinalizarVigencia()       — valida e finaliza vigência
  ├─ filtrarMatriculasEsocial()         — aplica filtros ao array
  ├─ limparFiltrosME()                  — limpa campos de filtro
  ├─ detectarSobreposicao(cpf, vinculo, iniNova, fimNova, idxExcluir)
  └─ aplicarRegraEsocial(vinculo, periodoIni, periodoFim)
```

### Controle de Perfil

O protótipo usa a variável global `var usuarioAtual = { nome: 'PESSOA CENTRAL', perfis: ['NUGESP'] }`. A função `showGerirMatriculasEsocial()` verifica `usuarioAtual.perfis.indexOf('NUGESP') >= 0` antes de renderizar a página. O item de menu só é inserido no array `menu` quando o perfil está presente (verificação em `buildMenu()`).

---

## Components and Interfaces

### 1. Entrada no Menu

```js
// Inserido dentro de: { label: 'eSocial', sub: [{ label: 'Geracao/Transmissao', sub: [...] }] }
{ label: 'Gerir Matriculas eSocial', action: 'gerirMatriculasEsocial' }
```

### 2. Página Principal — Listagem

Estrutura HTML gerada por `showGerirMatriculasEsocial()`:

```
.page-title          "Gerir Matrículas eSocial"
.page-body
  .alt-section       "Filtros de Pesquisa"
    .alt-body
      .frow           linha 1: Nome, CPF, Vínculo original, Matrícula original, Matrícula eSocial/fictícia
      .frow           linha 2: Categoria eSocial, Tipo de tratamento, Situação, Data início vigência, Data fim vigência
      .action-bar     [Pesquisar] [Limpar]
  .action-bar         [Novo] [Alterar] [Imprimir] [+ Opções ▼]
    .dropdown-wrap
      .dropdown-menu  "Alterar Matrícula eSocial" | "Finalizar Vigência"
  #me-grid-area       table.grid (14 colunas) + .pagination
  #me-form-area       (vazio inicialmente; recebe formulário de cadastro/edição)
```

### 3. Grade — 14 Colunas

| # | Coluna |
|---|--------|
| 1 | Nome do servidor |
| 2 | CPF |
| 3 | Vínculo original |
| 4 | Matrícula original |
| 5 | Matrícula eSocial/fictícia |
| 6 | Tipo de tratamento |
| 7 | Categoria eSocial |
| 8 | Data início da vigência |
| 9 | Data fim da vigência |
| 10 | Situação |
| 11 | Usuário de cadastro |
| 12 | Data de cadastro |
| 13 | Usuário de alteração |
| 14 | Data de alteração |

### 4. Formulário de Cadastro / Edição

Renderizado em `#me-form-area` (inline, não modal), usando `.alt-section` / `.alt-body`:

**Seção "Dados do Servidor"** (somente leitura após busca por CPF):
- CPF (input + botão buscar), Nome do servidor (readonly), Vínculo original (select), Matrícula original (readonly, preenchida ao selecionar vínculo)

**Seção "Parametrização eSocial"**:
- Tipo de tratamento (select: "Considerar matrícula fictícia" | "Considerar matrícula original com datas ajustadas")
- Matrícula eSocial/fictícia (input, obrigatório/desabilitado conforme tipo)
- Categoria eSocial (input numérico)
- Data início da vigência (date, obrigatório)
- Data fim da vigência (date, opcional)
- Data início eSocial ajustada (date)
- Data fim eSocial ajustada (date)
- Forçar consideração no eSocial (checkbox)
- Situação (select: Ativa | Vigência finalizada | Inativa)

**Seção "Auditoria"**:
- Justificativa da criação/alteração (textarea, obrigatório)

**Rodapé**: `.alt-actions` com [Salvar] [Cancelar]

### 5. Modal — Alterar Matrícula eSocial

`.modal-overlay` com `.modal-box` (largura 560px):
- Campos: Matrícula eSocial/fictícia, Categoria eSocial, Data início vigência, Data fim vigência, Data início eSocial ajustada, Data fim eSocial ajustada, Forçar consideração, Justificativa da alteração (obrigatório)
- Rodapé: [Salvar] [Cancelar]

### 6. Modal — Finalizar Vigência

`.modal-overlay` com `.modal-box` (largura 460px):
- Campos: Data fim da vigência (obrigatório), Justificativa da finalização (obrigatório)
- Rodapé: [Confirmar] [Cancelar]

### 7. Modal — Impressão

Reutiliza o padrão já existente no `index.html` (ver `modal-imprimir-ecp`):
- Título do relatório, Disposição (Retrato/Paisagem), Formato (PDF/Excel/HTML)
- Rodapé: [Imprimir] [Cancelar]

---

## Data Models

### Array principal em memória

```js
var matriculasEsocial = [
  {
    // Identificação
    id: 1,                                    // int, auto-incremento
    cpf: '000.000.000-00',
    nomeServidor: 'ÉRICO AMORIM CUMARU',
    vinculoOriginal: '742339',
    matriculaOriginal: '0974566-1-01',

    // Parametrização eSocial
    tipoTratamento: 'ficticia',               // 'ficticia' | 'datas_ajustadas'
    matriculaEsocial: '0974566-1-02',         // null quando tipoTratamento === 'datas_ajustadas'
    categoriaEsocial: '410',
    dataIniVigencia: '2022-04-02',            // ISO date string
    dataFimVigencia: '2023-09-30',            // null = em aberto
    dataIniEsocialAjustada: null,
    dataFimEsocialAjustada: null,
    forcarConsideracao: false,
    situacao: 'Ativa',                        // 'Ativa' | 'Vigência finalizada' | 'Inativa'

    // Auditoria — criação
    usuarioCadastro: 'PESSOA CENTRAL',
    dataCadastro: '02/04/2022 10:30',
    justificativaCriacao: 'Cadastro inicial da regra para vínculo cedido.',

    // Auditoria — última alteração
    usuarioAlteracao: null,
    dataAlteracao: null,
    justificativaAlteracao: null,

    // Auditoria — finalização
    usuarioFinalizacao: null,
    dataFinalizacao: null,
    justificativaFinalizacao: null
  }
];
```

### Variável de controle de seleção

```js
var meSelectedIdx = -1;   // índice do registro selecionado na grade (-1 = nenhum)
var mePageAtual = 1;
var meTamanhoPagina = 10;
var meRegistrosFiltrados = []; // cópia filtrada usada para paginação e impressão
```

### Dados de referência (leitura)

- `var servidores = [...]` — já existente no `index.html`; usado para busca por CPF
- Vínculos simulados inline na função de busca (array local por CPF)

---

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system — essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Visibilidade do menu controlada pelo perfil

*Para qualquer* valor do array `usuarioAtual.perfis`, o item "Gerir Matrículas eSocial" deve aparecer no menu se e somente se `perfis` contém `'NUGESP'`.

**Validates: Requirements 1.1**

---

### Property 2: Grade renderiza todas as 14 colunas obrigatórias

*Para qualquer* array `matriculasEsocial` (incluindo vazio), o HTML gerado por `listarMatriculasEsocial()` deve conter exatamente os 14 cabeçalhos de coluna especificados.

**Validates: Requirements 2.1**

---

### Property 3: Paginação é consistente com o tamanho do array

*Para qualquer* array de N registros filtrados e tamanho de página P, o número de páginas exibido deve ser `Math.ceil(N / P)`, e a página atual deve exibir no máximo P registros.

**Validates: Requirements 2.2**

---

### Property 4: Seleção de linha habilita ações

*Para qualquer* índice de linha válido na grade, clicar nessa linha deve adicionar a classe `selected` à linha e atualizar `meSelectedIdx` para o índice correspondente.

**Validates: Requirements 2.3**

---

### Property 5: Lógica de interseção de vigência

*Para qualquer* array de regras e qualquer par de datas de filtro `[A, B]`, a função `filtrarMatriculasEsocial()` deve retornar exatamente os registros onde `dataFimVigencia >= A` E `dataIniVigencia <= B` (registros com `dataFimVigencia` nula são considerados como vigência aberta e sempre satisfazem a condição de fim).

**Validates: Requirements 3.2, 9.5**

---

### Property 6: Limpar filtros é operação de round-trip

*Para qualquer* estado dos campos de filtro (preenchidos com quaisquer valores), chamar `limparFiltrosME()` deve resultar em todos os inputs e selects de filtro com valor vazio/padrão, e a grade deve exibir todos os registros.

**Validates: Requirements 3.4**

---

### Property 7: Estado do campo matrícula fictícia segue o tipo de tratamento

*Para qualquer* valor selecionado em "Tipo de tratamento", o campo "Matrícula eSocial/fictícia" deve ser obrigatório e habilitado se e somente se o tipo for `'ficticia'`; deve ser desabilitado e não obrigatório se o tipo for `'datas_ajustadas'`.

**Validates: Requirements 4.2, 4.3**

---

### Property 8: Validação rejeita qualquer combinação com campo obrigatório ausente

*Para qualquer* estado de formulário onde pelo menos um campo obrigatório está vazio (CPF, Vínculo original, Matrícula original, Tipo de tratamento, Data início da vigência, Categoria eSocial, Justificativa), a função de validação deve retornar `false` e nenhum registro deve ser adicionado/alterado em `matriculasEsocial`.

**Validates: Requirements 4.4, 5.3, 6.2, 7.2**

---

### Property 9: Detecção de sobreposição de vigência

*Para qualquer* par de intervalos `[A1, B1]` e `[A2, B2]` (onde B pode ser nulo = infinito), `detectarSobreposicao()` deve retornar `true` se e somente se `B1 >= A2` E `A1 <= B2` (usando a mesma regra de interseção do Requirement 3.2).

**Validates: Requirements 4.5**

---

### Property 10: Campos de auditoria são sempre preenchidos ao salvar

*Para qualquer* operação válida de criação, alteração ou finalização, o registro resultante em `matriculasEsocial` deve ter os campos de auditoria correspondentes (`usuarioCadastro`/`dataAlteracao`/`dataFinalizacao` e respectiva justificativa) preenchidos com o usuário atual e a data/hora da operação.

**Validates: Requirements 4.6, 5.4, 6.3, 7.3, 10.1, 10.2, 10.3**

---

### Property 11: Finalizar vigência altera situação para "Vigência finalizada"

*Para qualquer* registro com situação "Ativa", após `confirmarFinalizarVigencia()` com dados válidos, a situação do registro deve ser `'Vigência finalizada'` e `dataFimVigencia` deve ser a data informada.

**Validates: Requirements 7.3**

---

### Property 12: Aplicação de regra retorna resultado correto para qualquer vínculo e período

*Para qualquer* vínculo e período consultado, `aplicarRegraEsocial(vinculo, periodoIni, periodoFim)` deve retornar:
- a `matriculaEsocial` (fictícia) quando existe regra ativa do tipo `'ficticia'` com vigência que intersecta o período;
- a `matriculaOriginal` com datas ajustadas quando existe regra ativa do tipo `'datas_ajustadas'`;
- o comportamento padrão (matrícula original, sem ajuste) quando não existe regra ativa para o período.

**Validates: Requirements 9.1, 9.2, 9.3, 9.4**

---

### Property 13: Impressão respeita os filtros aplicados

*Para qualquer* estado de filtro, os registros passados para a função de geração do relatório devem ser idênticos ao array `meRegistrosFiltrados` no momento em que o botão "Imprimir" é acionado.

**Validates: Requirements 8.2**

---

## Error Handling

| Situação | Comportamento |
|---|---|
| Campos obrigatórios ausentes | `alert()` listando os campos pendentes; formulário permanece aberto |
| Sobreposição de vigência detectada | `alert()` com mensagem de conflito; registro não é salvo |
| Nenhum registro selecionado ao acionar "Alterar" / "+ Opções" | `alert('Selecione um registro na lista.')` |
| Acesso sem perfil NUGESP | `showPage()` com mensagem de acesso negado; sem renderização do formulário |
| CPF não encontrado na busca | Mensagem inline abaixo do campo CPF: "Servidor não encontrado." |
| Vínculo sem matrícula original | Campo matrícula original exibe "—"; salvar é bloqueado pela validação |

Todos os erros são comunicados via `alert()` ou mensagem inline, seguindo o padrão já adotado no protótipo (sem biblioteca de toast/notificação).

---

## Testing Strategy

### Abordagem dual

A feature combina testes baseados em exemplos (para renderização de UI e casos específicos) com testes baseados em propriedades (para lógica de filtragem, validação, detecção de sobreposição e aplicação de regras).

**Biblioteca PBT recomendada**: [fast-check](https://github.com/dubzzz/fast-check) (JavaScript, sem dependências de build — pode ser carregada via CDN para testes).

Cada teste de propriedade deve rodar mínimo **100 iterações**.

### Testes de exemplo (unit)

- Renderização da página com perfil NUGESP vs. sem perfil (Req 1.1, 1.2)
- Presença dos 14 cabeçalhos de coluna na grade (Req 2.1)
- Presença dos 10 campos de filtro (Req 3.1)
- Presença de todos os campos do formulário de cadastro (Req 4.1)
- Dados de origem (servidores/vínculos) não são modificados após salvar (Req 11.1–11.3)

### Testes de propriedade (PBT)

Cada propriedade abaixo corresponde a uma Correctness Property da seção anterior:

| Tag | Propriedade | Gerador |
|---|---|---|
| `Feature: gerir-matriculas-esocial, Property 1: menu visibility` | Visibilidade do menu por perfil | `fc.array(fc.string())` para perfis |
| `Feature: gerir-matriculas-esocial, Property 3: pagination` | Paginação consistente | `fc.array(fc.record({...}))` para registros |
| `Feature: gerir-matriculas-esocial, Property 5: date range filter` | Interseção de vigência | `fc.record({dataIni, dataFim})` + filtro aleatório |
| `Feature: gerir-matriculas-esocial, Property 6: clear filters round-trip` | Limpar filtros | `fc.record({...})` para estado de filtros |
| `Feature: gerir-matriculas-esocial, Property 7: field state by tipo` | Estado do campo fictícia | `fc.constantFrom('ficticia', 'datas_ajustadas')` |
| `Feature: gerir-matriculas-esocial, Property 8: required field validation` | Validação de campos obrigatórios | `fc.record({...})` com campos aleatoriamente omitidos |
| `Feature: gerir-matriculas-esocial, Property 9: overlap detection` | Detecção de sobreposição | `fc.tuple(fc.date(), fc.date())` × 2 |
| `Feature: gerir-matriculas-esocial, Property 10: audit fields` | Campos de auditoria | `fc.record({...})` para regras válidas |
| `Feature: gerir-matriculas-esocial, Property 12: rule application` | Aplicação de regra | `fc.record({vinculo, periodoIni, periodoFim})` |

### Testes de integração / smoke

- Fluxo completo: cadastrar → alterar → finalizar vigência → verificar situação (smoke, 1 execução)
- Verificar que `servidores` e `vinculos` não são mutados após qualquer operação (exemplo)
