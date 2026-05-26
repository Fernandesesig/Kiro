# Design Técnico — Central de Chamados

## Overview

A Central de Chamados é um novo módulo do SIGrh que implementa o ciclo de vida completo de chamados de suporte. O módulo é integrado ao `index.html` existente seguindo todos os padrões visuais e de navegação já estabelecidos no sistema.

O protótipo simula dois perfis de uso — **Solicitante** e **Suporte / Pessoa Central** — por meio de um seletor de perfil fixo no topo da página. Toda a lógica, dados e renderização vivem em blocos `<script>` no final do `<body>` do `index.html`, sem dependências externas adicionais.

### Objetivos do Design

- Integrar ao menu lateral existente via `action: 'centralChamados'`
- Reutilizar 100% dos componentes CSS já definidos (`.page-title`, `.page-body`, `.frow`, `.fg`, `table.grid`, `.btn`, `.alt-section`, `.tabs`, `.modal-overlay`, etc.)
- Manter todos os dados em arrays JS em memória (sem backend, sem localStorage)
- Suportar navegação interna entre sub-telas (lista → detalhe → voltar) sem recarregar a página

---

## Architecture

O módulo segue o padrão arquitetural já estabelecido no `index.html`:

```
index.html
├── <style>          ← CSS existente (sem adições necessárias, exceto estilos específicos do módulo)
├── var menu = [...]  ← Adicionar entrada { label:'Central de Chamados', icon:'fa-headset', sub:[...] }
├── function runAction(a) ← Adicionar: if(a==='centralChamados') showCentralChamados()
└── <script> (novo bloco ao final do body)
    ├── Dados em memória
    │   ├── var chamadosData = [...]     ← 6+ chamados simulados
    │   └── var notificacoesData = [...] ← notificações simuladas
    ├── Estado da UI
    │   ├── var ccPerfilAtivo = 'solicitante'
    │   └── var ccChamadoAtual = null
    └── Funções de renderização
        ├── showCentralChamados()        ← entry point, chamado por runAction
        ├── ccRenderPerfil()             ← re-renderiza a área de conteúdo conforme perfil
        ├── ccRenderSolicitante()        ← visão completa do Solicitante (tabs)
        ├── ccRenderSuporteAdmin()       ← visão completa do Suporte (tabs)
        ├── ccRenderListaChamados()      ← tabela de chamados do Solicitante
        ├── ccRenderDetalhe(id, perfil)  ← tela de detalhe (compartilhada, comportamento difere por perfil)
        ├── ccRenderFila()              ← fila de chamados do Suporte com filtros
        ├── ccRenderNotificacoes()       ← painel de notificações
        ├── ccAbrirChamado()            ← submissão do formulário de abertura
        ├── ccAcaoSuporte(tipo, id)     ← dispatcher de ações do Suporte
        └── ccGerarNotificacao(tipo, chamadoId) ← cria entrada em notificacoesData
```

### Fluxo de Navegação

```
showCentralChamados()
    │
    ├─ Seletor de Perfil (fixo no topo)
    │
    ├─ [Perfil: Solicitante]
    │   ├─ Tab "Abrir Chamado"   → formulário de abertura
    │   ├─ Tab "Meus Chamados"   → lista com tabela.grid → clique → ccRenderDetalhe(id, 'solicitante')
    │   └─ Tab "Notificações"    → painel de notificações
    │
    └─ [Perfil: Suporte / Pessoa Central]
        ├─ Tab "Painel"          → contadores por status
        ├─ Tab "Fila de Chamados" → tabela com filtros → clique → ccRenderDetalhe(id, 'suporte')
        └─ Tab "Detalhe" (dinâmica, aparece ao abrir um chamado)
```

---

## Components and Interfaces

### 1. Seletor de Perfil

Renderizado como uma `.alt-section` no topo da página, acima das tabs. Dois botões `.btn` que alternam `ccPerfilAtivo` e chamam `ccRenderPerfil()`.

```html
<!-- Estrutura conceitual -->
<div class="alt-section" style="margin-bottom:10px">
  <div class="alt-body" style="display:flex;align-items:center;gap:10px">
    <span>Visualizar como:</span>
    <button class="btn btn-blue" onclick="ccSetPerfil('solicitante')">Solicitante</button>
    <button class="btn btn-gray" onclick="ccSetPerfil('suporte')">Suporte / Pessoa Central</button>
  </div>
</div>
```

**Interface:**
- `ccSetPerfil(perfil: 'solicitante' | 'suporte')` — atualiza `ccPerfilAtivo` e re-renderiza

### 2. Formulário de Abertura de Chamado

Renderizado dentro de `.tab-body` da aba "Abrir Chamado". Usa `.frow`/`.fg` para layout dos campos.

**Campos:**
| Campo | Tipo | Obrigatório | Valores |
|---|---|---|---|
| Tipo da Solicitação | `<select>` | Sim | Acesso ao sistema, Dúvida, Erro/Bug, Solicitação de cadastro, Outros |
| Nível de Urgência | `<select>` | Sim | Baixa, Média, Alta, Crítica |
| Área do Usuário | `<select>` | Sim | NUGESP, NUGEP, NUGED, NUGEF, DIRAD, SEAD |
| Descrição | `<textarea>` | Sim | Texto livre |
| Anexos | `<input type="file">` | Não | — |

**Interface:**
- `ccAbrirChamado()` — lê os campos, valida, cria objeto chamado, adiciona a `chamadosData`, gera notificação e histórico

### 3. Tabela de Chamados (Visão Solicitante)

`table.grid` com as colunas: Número, Tipo, Urgência, Área, Status, Data de Abertura, Última Atualização.

Cada linha tem `onclick="ccRenderDetalhe(id, 'solicitante')"`. A coluna Status usa uma função `ccCorStatus(status)` que retorna um `<span>` com cor inline.

**Interface:**
- `ccRenderListaChamados()` — filtra `chamadosData` pelo solicitante simulado, ordena por data decrescente, renderiza tabela

### 4. Tela de Detalhe do Chamado

Compartilhada entre os dois perfis, com comportamento diferente:

- **Visão Solicitante:** exibe campos de informação + histórico (apenas comentários públicos) + botão Voltar
- **Visão Suporte:** exibe campos de informação + histórico completo (públicos + internos) + barra de ações

**Interface:**
- `ccRenderDetalhe(id, perfil)` — busca chamado em `chamadosData`, renderiza conforme perfil

### 5. Painel de Notificações

Lista de notificações do solicitante simulado. Exibe contador de não lidas. Ao abrir o painel, marca todas como lidas.

**Interface:**
- `ccRenderNotificacoes()` — renderiza lista de `notificacoesData`, chama `ccMarcarLidas()`
- `ccMarcarLidas()` — atualiza `lida: true` em todas as notificações

### 6. Painel Administrativo (Visão Suporte)

Contadores de resumo por status, renderizados como cards em `.frow`.

**Interface:**
- `ccRenderPainelAdmin()` — agrupa `chamadosData` por status e exibe contadores

### 7. Fila de Chamados com Filtros

`table.grid` com todas as colunas + linha de filtros acima. Filtros em `.frow`/`.fg`.

**Filtros disponíveis:**
| Filtro | Tipo | Valores |
|---|---|---|
| Status | `<select multiple>` | Todos os status |
| Urgência | `<select multiple>` | Baixa, Média, Alta, Crítica |
| Área | `<select>` | Todas as áreas |
| Solicitante | `<input text>` | Texto livre |
| Responsável | `<select>` | Equipe + "Sem responsável" |

**Interface:**
- `ccAplicarFiltros()` — lê os controles de filtro, filtra `chamadosData`, re-renderiza tabela
- `ccLimparFiltros()` — reseta controles e re-renderiza com todos os chamados

### 8. Ações do Suporte (Modais)

Cada ação abre um `.modal-overlay` específico:

| Ação | Modal | Comportamento |
|---|---|---|
| Assumir chamado | `modal-cc-assumir` | Seletor de responsável (equipe) + confirmar |
| Alterar status | `modal-cc-status` | Seletor de status (exceto atual) + confirmar |
| Adicionar comentário | `modal-cc-comentario` | Textarea + seletor Público/Interno + confirmar |
| Encaminhar para validação | — | Ação direta (sem modal), muda status |
| Finalizar chamado | `modal-cc-finalizar` | Confirmação simples |
| Cancelar chamado | `modal-cc-cancelar` | Confirmação simples |

**Interface:**
- `ccAcaoSuporte(tipo, id)` — dispatcher que abre o modal correto ou executa ação direta
- `ccConfirmarAcao(tipo, id, dados)` — aplica a ação em `chamadosData`, registra histórico, gera notificação, fecha modal

---

## Data Models

Todos os dados vivem em variáveis JS globais no escopo do bloco `<script>`.

### Chamado

```javascript
{
  id: 'CHM-001',                    // string, formato CHM-NNN
  tipo: 'Acesso ao sistema',        // string
  urgencia: 'Alta',                 // 'Baixa' | 'Média' | 'Alta' | 'Crítica'
  area: 'NUGESP',                   // string
  solicitante: 'João Silva',        // string (nome do solicitante simulado)
  responsavel: 'Evelen Silva',      // string | null
  status: 'Novo',                   // 'Novo' | 'Em análise' | 'Em tratamento' | 'Aguardando validação' | 'Finalizado' | 'Cancelado'
  descricao: 'Texto da solicitação',// string
  dataAbertura: '2025-01-15T09:30:00', // ISO 8601
  dataAtualizacao: '2025-01-15T09:30:00', // ISO 8601
  historico: [HistoricoEntry],      // array, ordem cronológica crescente
  anexos: []                        // array de nomes de arquivo (simulado)
}
```

### HistoricoEntry

```javascript
{
  dataHora: '2025-01-15T09:30:00',  // ISO 8601
  usuario: 'João Silva',            // string
  acao: 'Chamado aberto',           // string descritiva
  statusAnterior: null,             // string | null
  statusNovo: 'Novo',               // string | null
  visibilidade: 'publico',          // 'publico' | 'interno'
  comentario: null                  // string | null (apenas para ação 'Comentário adicionado')
}
```

### Notificacao

```javascript
{
  id: 1,                            // number, auto-incremento
  tipo: 'Chamado criado',           // string (ver tipos abaixo)
  chamadoId: 'CHM-001',             // string
  dataHora: '2025-01-15T09:30:00',  // ISO 8601
  lida: false                       // boolean
}
```

**Tipos de notificação:** `'Chamado criado'`, `'Chamado assumido'`, `'Status alterado'`, `'Comentário adicionado'`, `'Chamado finalizado'`, `'Chamado cancelado'`

### Dados Simulados (chamadosData)

6 chamados pré-populados cobrindo todos os status:

| ID | Tipo | Urgência | Área | Solicitante | Responsável | Status |
|---|---|---|---|---|---|---|
| CHM-001 | Acesso ao sistema | Alta | NUGESP | João Silva | Evelen Silva | Em análise |
| CHM-002 | Erro/Bug | Crítica | NUGEP | Maria Santos | Matheus Neto | Em tratamento |
| CHM-003 | Dúvida | Baixa | NUGED | Carlos Oliveira | null | Novo |
| CHM-004 | Solicitação de cadastro | Média | NUGEF | Ana Costa | Raiane Rocha | Aguardando validação |
| CHM-005 | Outros | Média | DIRAD | Pedro Lima | Evelyn Fernandes | Finalizado |
| CHM-006 | Acesso ao sistema | Alta | SEAD | João Silva | Evelen Silva | Cancelado |

Cada chamado terá histórico pré-populado demonstrando o fluxo de atendimento. CHM-002 terá um comentário interno e um público para demonstrar a diferença de visibilidade.

---

## Correctness Properties

*Uma propriedade é uma característica ou comportamento que deve ser verdadeiro em todas as execuções válidas do sistema — essencialmente, uma declaração formal sobre o que o sistema deve fazer. Propriedades servem como ponte entre especificações legíveis por humanos e garantias de correção verificáveis por máquina.*

### Property 1: Criação de chamado válido sempre resulta em status "Novo" com histórico inicial

*Para qualquer* combinação válida de (tipo, urgência, área, descrição), submeter o formulário de abertura deve criar um chamado com `status === 'Novo'`, `dataAbertura` preenchida, e `historico` contendo exatamente uma entrada com `acao === 'Chamado aberto'`.

**Validates: Requirements 2.2, 2.5**

---

### Property 2: Formulário com campo obrigatório vazio não cria chamado

*Para qualquer* combinação de campos onde pelo menos um campo obrigatório (tipo, urgência, área ou descrição) está vazio ou em branco, a submissão do formulário não deve adicionar nenhum item ao array `chamadosData`.

**Validates: Requirements 2.3**

---

### Property 3: Criação de chamado gera notificação correspondente

*Para qualquer* chamado criado com sucesso, o array `notificacoesData` deve conter uma entrada com `tipo === 'Chamado criado'`, `chamadoId` igual ao ID do chamado criado, e `dataHora` preenchida.

**Validates: Requirements 2.4, 5.2**

---

### Property 4: Visão Solicitante nunca exibe comentários internos

*Para qualquer* chamado com qualquer combinação de entradas de histórico (comentários públicos e internos), a função de renderização do detalhe na visão Solicitante não deve incluir no HTML resultante nenhuma entrada com `visibilidade === 'interno'`.

**Validates: Requirements 4.3**

---

### Property 5: Função de cor de status retorna valores distintos para cada status

*Para qualquer* par de status distintos válidos (Novo, Em análise, Em tratamento, Aguardando validação, Finalizado, Cancelado), a função `ccCorStatus` deve retornar valores diferentes, garantindo diferenciação visual.

**Validates: Requirements 3.2**

---

### Property 6: Lista de chamados do Solicitante está sempre em ordem decrescente de data

*Para qualquer* array de chamados com datas de abertura variadas, a lista renderizada na visão Solicitante deve apresentar os chamados em ordem decrescente de `dataAbertura` (mais recente primeiro).

**Validates: Requirements 3.4**

---

### Property 7: Filtros da fila retornam apenas chamados que satisfazem todos os critérios

*Para qualquer* combinação de filtros aplicados (status, urgência, área, solicitante, responsável), todos os chamados exibidos na fila devem satisfazer simultaneamente todos os critérios de filtro ativos.

**Validates: Requirements 6.3**

---

### Property 8: Limpar filtros restaura todos os chamados na fila

*Para qualquer* estado de filtros aplicados, após executar `ccLimparFiltros()`, o número de chamados exibidos na fila deve ser igual ao total de chamados em `chamadosData`.

**Validates: Requirements 6.4**

---

### Property 9: Contadores do painel administrativo somam o total de chamados

*Para qualquer* estado do array `chamadosData`, a soma de todos os contadores por status exibidos no painel administrativo deve ser igual a `chamadosData.length`.

**Validates: Requirements 6.5**

---

### Property 10: Ação "Assumir chamado" atualiza responsável, status e histórico

*Para qualquer* chamado e qualquer membro da equipe de suporte selecionado, após executar a ação "Assumir", o chamado deve ter `responsavel` igual ao membro selecionado, `status === 'Em análise'`, e o `historico` deve conter uma nova entrada registrando a ação.

**Validates: Requirements 7.1**

---

### Property 11: Qualquer ação do Suporte gera notificação para o Solicitante

*Para qualquer* ação executada pelo Suporte (assumir, alterar status, comentar, encaminhar, finalizar, cancelar) em qualquer chamado, o array `notificacoesData` deve conter uma nova entrada com o `chamadoId` correto e `dataHora` preenchida após a execução da ação.

**Validates: Requirements 7.7, 5.2**

---

### Property 12: Comentários internos e públicos recebem classes CSS distintas no histórico

*Para qualquer* entrada de histórico com `visibilidade === 'interno'` e qualquer entrada com `visibilidade === 'publico'`, a função de renderização do histórico deve aplicar classes CSS diferentes às duas, garantindo diferenciação visual.

**Validates: Requirements 8.2**

---

### Property 13: Histórico é sempre renderizado em ordem cronológica crescente

*Para qualquer* array de entradas de histórico com `dataHora` variadas, a renderização do histórico deve apresentar as entradas em ordem crescente de `dataHora` (mais antiga primeiro).

**Validates: Requirements 8.3**

---

### Property 14: Visualizar painel de notificações zera o contador de não lidas

*Para qualquer* número N de notificações com `lida === false`, após chamar `ccMarcarLidas()`, todas as notificações devem ter `lida === true` e o contador exibido deve ser 0.

**Validates: Requirements 5.4**

---

## Error Handling

### Validação do Formulário de Abertura

- Campos obrigatórios vazios: adicionar classe `border-color: #c00` ao campo e exibir mensagem de erro em `<span style="color:#c00;font-size:11px">` abaixo do campo
- Nenhum chamado é criado enquanto houver campos inválidos
- Ao corrigir um campo, o destaque de erro é removido no próximo submit

### Ações do Suporte

- Confirmar ação sem selecionar responsável (modal "Assumir"): exibir alerta inline no modal
- Confirmar comentário com texto vazio: exibir alerta inline no modal
- Todas as ações são executadas de forma síncrona (sem async/await), sem possibilidade de falha de rede

### Dados Simulados

- O array `chamadosData` é inicializado com dados fixos ao carregar o script
- Não há persistência entre recarregamentos da página (comportamento esperado do protótipo)

---

## Testing Strategy

### Avaliação de Aplicabilidade de PBT

Este módulo é adequado para property-based testing nas funções de lógica pura:
- Validação de formulário (`ccValidarFormulario`)
- Filtragem da fila (`ccFiltrarChamados`)
- Ordenação de listas (`ccOrdenarPorData`)
- Renderização de histórico (`ccRenderHistoricoEntrada`)
- Geração de notificações (`ccGerarNotificacao`)
- Cálculo de contadores (`ccContarPorStatus`)

Funções de renderização de UI completa (que produzem HTML para `showPage`) são mais adequadas para testes de exemplo/snapshot.

### Biblioteca de PBT Recomendada

**[fast-check](https://github.com/dubzzz/fast-check)** — biblioteca JavaScript de property-based testing, compatível com vanilla JS e ambientes de teste como Jest ou Vitest.

```bash
npm install --save-dev fast-check vitest
```

Cada teste de propriedade deve ser configurado com mínimo de **100 iterações** (`numRuns: 100`).

### Testes de Propriedade (PBT)

Cada propriedade do design deve ser implementada como um único teste de propriedade:

```javascript
// Exemplo de estrutura — Property 2
import fc from 'fast-check';
import { test } from 'vitest';

// Feature: central-chamados, Property 2: Formulário com campo obrigatório vazio não cria chamado
test('Property 2: formulário inválido não cria chamado', () => {
  fc.assert(fc.property(
    fc.record({
      tipo: fc.oneof(fc.constant(''), fc.string()),
      urgencia: fc.oneof(fc.constant(''), fc.string()),
      area: fc.oneof(fc.constant(''), fc.string()),
      descricao: fc.oneof(fc.constant(''), fc.string())
    }).filter(campos => !campos.tipo || !campos.urgencia || !campos.area || !campos.descricao),
    (campos) => {
      const antes = chamadosData.length;
      ccValidarFormulario(campos); // deve retornar false
      return chamadosData.length === antes;
    }
  ), { numRuns: 100 });
});
```

**Tag format:** `// Feature: central-chamados, Property N: <texto da propriedade>`

### Testes de Exemplo (Unit Tests)

Para critérios classificados como EXAMPLE:

- Renderização do seletor de perfil (Req 1.1)
- Alternância de perfil (Req 1.2, 1.3, 1.4, 1.5)
- Presença de campos no formulário (Req 2.1)
- Colunas da tabela de chamados (Req 3.1)
- Clique em chamado abre detalhe (Req 3.3)
- Campos exibidos no detalhe (Req 4.1, 4.2, 4.4)
- Painel de notificações (Req 5.1, 5.3)
- Fila com colunas corretas (Req 6.1)
- Controles de filtro presentes (Req 6.2)
- Histórico completo na visão Suporte (Req 8.1)
- Botões de ação na visão Suporte (Req 8.4)

### Testes de Smoke

Para verificação de configuração e dados iniciais:

- Entrada no array `menu` com `action: 'centralChamados'` (Req 9.1)
- `chamadosData` tem >= 6 itens cobrindo todos os 6 status (Req 10.1)
- Dados simulados cobrem todas as urgências (Req 10.2)
- Cada chamado simulado tem histórico pré-populado (Req 10.3)
- Existe pelo menos um comentário interno e um público nos dados (Req 10.4)

### Testes de Integração

- Clicar em "Central de Chamados" no menu chama `showPage()` e renderiza o módulo (Req 9.2)

### Cobertura Esperada

| Tipo | Quantidade | Requisitos Cobertos |
|---|---|---|
| Propriedades (PBT) | 14 | 2.2–2.5, 3.2, 3.4, 4.3, 5.2, 5.4, 6.3–6.5, 7.1, 7.7, 8.2, 8.3 |
| Exemplos (unit) | ~15 | 1.1–1.5, 2.1, 3.1, 3.3, 4.1, 4.2, 4.4, 5.1, 5.3, 6.1, 6.2, 8.1, 8.4 |
| Smoke | 5 | 9.1, 10.1–10.4 |
| Integração | 1 | 9.2 |
