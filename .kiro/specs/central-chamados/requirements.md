# Requirements Document — Central de Chamados

## Introduction

A Central de Chamados é uma nova funcionalidade do SIGrh que centraliza o registro, acompanhamento e resolução de solicitações de suporte dos servidores. O módulo é implementado como protótipo single-file HTML integrado ao `index.html` existente, seguindo os padrões visuais e de navegação já estabelecidos no sistema.

O protótipo simula dois perfis de uso — **Solicitante** e **Suporte / Pessoa Central** — por meio de um seletor de perfil fixo na tela, exclusivo do protótipo. Não representa autenticação real. A equipe de suporte simulada é composta por: Evelen Silva, Matheus Neto, Raiane Rocha e Evelyn Fernandes.

---

## Glossary

- **Central_de_Chamados**: módulo do SIGrh responsável pelo ciclo de vida dos chamados de suporte.
- **Chamado**: registro de uma solicitação de suporte criada por um Solicitante.
- **Solicitante**: servidor que abre e acompanha chamados.
- **Suporte**: membro da equipe de atendimento que analisa, trata e finaliza chamados.
- **Pessoa_Central**: perfil administrativo com visão completa da fila e capacidade de gerenciar todos os chamados.
- **Seletor_de_Perfil**: controle fixo no protótipo que alterna a visão entre Solicitante e Suporte/Pessoa Central.
- **Status**: estado atual de um chamado. Valores possíveis: Novo, Em análise, Em tratamento, Aguardando validação, Finalizado, Cancelado.
- **Histórico**: linha do tempo imutável de todas as ações realizadas em um chamado.
- **Comentário_Público**: comentário visível tanto para o Solicitante quanto para o Suporte.
- **Comentário_Interno**: comentário visível apenas para o Suporte/Pessoa Central.
- **Notificação**: alerta simulado gerado automaticamente por eventos do ciclo de vida do chamado.
- **Fila_de_Chamados**: lista consolidada de todos os chamados visível ao Suporte/Pessoa Central.
- **Urgência**: nível de prioridade do chamado. Valores: Baixa, Média, Alta, Crítica.
- **Área**: setor/departamento do Solicitante.

---

## Requirements

### Requirement 1: Seletor de Perfil do Protótipo

**User Story:** Como desenvolvedor/avaliador do protótipo, quero alternar entre os perfis Solicitante e Suporte/Pessoa Central, para que eu possa navegar e validar as diferentes visões do sistema sem precisar de autenticação real.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir um seletor fixo no topo da página com o rótulo "Visualizar como:" e as opções "Solicitante" e "Suporte / Pessoa Central".
2. WHEN o usuário seleciona "Solicitante" no Seletor_de_Perfil, THE Central_de_Chamados SHALL renderizar exclusivamente a visão do Solicitante.
3. WHEN o usuário seleciona "Suporte / Pessoa Central" no Seletor_de_Perfil, THE Central_de_Chamados SHALL renderizar exclusivamente a visão do Suporte/Pessoa Central.
4. THE Seletor_de_Perfil SHALL manter o perfil selecionado visível e destacado enquanto a página da Central de Chamados estiver ativa.
5. THE Central_de_Chamados SHALL inicializar com o perfil "Solicitante" selecionado por padrão.

---

### Requirement 2: Abertura de Chamado (Visão Solicitante)

**User Story:** Como Solicitante, quero abrir um novo chamado informando o tipo, urgência, área, descrição e anexos, para que minha solicitação seja registrada e encaminhada à equipe de suporte.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir, na visão Solicitante, um formulário de abertura de chamado com os campos: Tipo da Solicitação (select obrigatório), Nível de Urgência (select obrigatório: Baixa, Média, Alta, Crítica), Área do Usuário (select obrigatório), Descrição (textarea obrigatório) e Anexos (input file, opcional).
2. WHEN o Solicitante submete o formulário com todos os campos obrigatórios preenchidos, THE Central_de_Chamados SHALL criar o chamado com Status "Novo", registrar data/hora de criação e exibir mensagem de confirmação.
3. IF o Solicitante submete o formulário com algum campo obrigatório vazio, THEN THE Central_de_Chamados SHALL destacar os campos inválidos e exibir mensagem de erro sem criar o chamado.
4. WHEN um chamado é criado com sucesso, THE Central_de_Chamados SHALL gerar uma Notificação do tipo "Chamado criado" e adicioná-la ao painel de notificações do Solicitante.
5. WHEN um chamado é criado com sucesso, THE Central_de_Chamados SHALL adicionar uma entrada no Histórico do chamado com: data/hora, ação "Chamado aberto" e nome do Solicitante.

---

### Requirement 3: Lista de Chamados do Solicitante

**User Story:** Como Solicitante, quero visualizar a lista dos meus chamados com seus status atuais, para que eu possa acompanhar o andamento de cada solicitação.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir, na visão Solicitante, uma tabela com os chamados do próprio usuário contendo as colunas: Número, Tipo, Urgência, Área, Status, Data de Abertura e Última Atualização.
2. THE Central_de_Chamados SHALL aplicar cor de destaque diferenciada por Status na tabela de chamados do Solicitante (ex.: "Novo" em azul, "Em análise" em amarelo, "Finalizado" em verde, "Cancelado" em cinza).
3. WHEN o Solicitante clica em um chamado na lista, THE Central_de_Chamados SHALL exibir a tela de detalhe daquele chamado.
4. THE Central_de_Chamados SHALL exibir a lista de chamados ordenada por data de abertura decrescente por padrão.

---

### Requirement 4: Detalhe do Chamado (Visão Solicitante)

**User Story:** Como Solicitante, quero visualizar os detalhes do meu chamado, incluindo status atual, histórico de ações e comentários públicos do suporte, para que eu me mantenha informado sobre o andamento da minha solicitação.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir, na tela de detalhe do chamado (visão Solicitante), as informações: Número do chamado, Tipo, Urgência, Área, Status atual, Data de abertura, Responsável atual e Descrição original.
2. THE Central_de_Chamados SHALL exibir o Histórico do chamado como linha do tempo com: data/hora, nome do usuário responsável pela ação, descrição da ação e mudança de Status (quando aplicável).
3. THE Central_de_Chamados SHALL exibir apenas os Comentários_Públicos na visão Solicitante; comentários internos NÃO devem ser visíveis ao Solicitante.
4. THE Central_de_Chamados SHALL exibir um botão "Voltar" que retorna o Solicitante à lista de chamados.

---

### Requirement 5: Notificações do Solicitante

**User Story:** Como Solicitante, quero receber notificações sobre atualizações nos meus chamados, para que eu seja alertado sem precisar verificar manualmente o status.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir um painel de notificações na visão Solicitante com os eventos: Chamado criado, Chamado assumido por suporte, Mudança de status, Comentário público adicionado e Chamado finalizado.
2. WHEN uma notificação é gerada, THE Central_de_Chamados SHALL exibir: tipo do evento, número do chamado relacionado e data/hora do evento.
3. THE Central_de_Chamados SHALL exibir o contador de notificações não lidas no painel de notificações.
4. WHEN o Solicitante visualiza o painel de notificações, THE Central_de_Chamados SHALL marcar as notificações exibidas como lidas e atualizar o contador.

---

### Requirement 6: Painel Administrativo e Fila de Chamados (Visão Suporte)

**User Story:** Como Suporte/Pessoa Central, quero visualizar o painel administrativo com a fila geral de chamados e aplicar filtros, para que eu possa gerenciar e priorizar o atendimento de forma eficiente.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir, na visão Suporte/Pessoa Central, um painel administrativo com a Fila_de_Chamados contendo todos os chamados do sistema nas colunas: Número, Tipo, Urgência, Área, Solicitante, Responsável, Status e Data de Abertura.
2. THE Central_de_Chamados SHALL disponibilizar filtros na Fila_de_Chamados para os campos: Status (multiselect), Urgência (multiselect), Área (select), Solicitante (texto livre) e Responsável (select com os nomes da equipe de suporte mais opção "Sem responsável").
3. WHEN o Suporte aplica um ou mais filtros, THE Central_de_Chamados SHALL atualizar a Fila_de_Chamados exibindo apenas os chamados que atendem a todos os critérios selecionados.
4. WHEN o Suporte limpa os filtros, THE Central_de_Chamados SHALL restaurar a Fila_de_Chamados com todos os chamados.
5. THE Central_de_Chamados SHALL exibir contadores de resumo no painel administrativo com o total de chamados por Status.

---

### Requirement 7: Ações sobre Chamados (Visão Suporte)

**User Story:** Como Suporte/Pessoa Central, quero executar ações sobre os chamados (assumir, alterar status, comentar, encaminhar, finalizar), para que eu possa conduzir o atendimento do início ao fim.

#### Acceptance Criteria

1. WHEN o Suporte seleciona um chamado na Fila_de_Chamados e aciona "Assumir chamado", THE Central_de_Chamados SHALL exibir um seletor com os nomes da equipe de suporte (Evelen Silva, Matheus Neto, Raiane Rocha, Evelyn Fernandes) e, após confirmação, atribuir o responsável selecionado ao chamado, alterar o Status para "Em análise" e registrar a ação no Histórico.
2. WHEN o Suporte aciona "Alterar status" em um chamado, THE Central_de_Chamados SHALL exibir um seletor com os Status disponíveis (exceto o Status atual) e, após confirmação, atualizar o Status do chamado e registrar a mudança no Histórico com data/hora e nome do Suporte.
3. WHEN o Suporte aciona "Adicionar comentário" em um chamado, THE Central_de_Chamados SHALL exibir um formulário com campo de texto e seletor de visibilidade (Público / Interno) e, após confirmação, registrar o comentário no Histórico com data/hora, nome do Suporte e indicação de visibilidade.
4. WHEN o Suporte aciona "Encaminhar para validação" em um chamado, THE Central_de_Chamados SHALL alterar o Status do chamado para "Aguardando validação" e registrar a ação no Histórico.
5. WHEN o Suporte aciona "Finalizar chamado", THE Central_de_Chamados SHALL alterar o Status do chamado para "Finalizado", registrar a ação no Histórico e gerar Notificação do tipo "Chamado finalizado" para o Solicitante.
6. WHEN o Suporte aciona "Cancelar chamado", THE Central_de_Chamados SHALL alterar o Status do chamado para "Cancelado" e registrar a ação no Histórico.
7. WHEN qualquer ação é executada pelo Suporte sobre um chamado, THE Central_de_Chamados SHALL gerar a Notificação correspondente (conforme Requirement 5.1) para o Solicitante do chamado.

---

### Requirement 8: Histórico Completo do Chamado (Visão Suporte)

**User Story:** Como Suporte/Pessoa Central, quero visualizar o histórico completo de um chamado incluindo comentários internos, para que eu tenha contexto total do atendimento.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL exibir, na tela de detalhe do chamado (visão Suporte), o Histórico completo com: data/hora, nome do usuário responsável, ação realizada, mudança de Status (quando aplicável) e todos os comentários (públicos e internos).
2. THE Central_de_Chamados SHALL diferenciar visualmente os Comentários_Internos dos Comentários_Públicos no Histórico da visão Suporte (ex.: cor de fundo ou badge de identificação).
3. THE Central_de_Chamados SHALL exibir o Histórico em ordem cronológica crescente (mais antigo primeiro).
4. THE Central_de_Chamados SHALL exibir todas as ações disponíveis (Assumir, Alterar status, Comentar, Encaminhar, Finalizar, Cancelar) na tela de detalhe do chamado da visão Suporte.

---

### Requirement 9: Integração com o Menu do SIGrh

**User Story:** Como usuário do SIGrh, quero acessar a Central de Chamados pelo menu lateral do sistema, para que a funcionalidade esteja integrada à navegação padrão do SIGrh.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL ser adicionada ao array `menu` do `index.html` como um novo módulo com ícone representativo e subitem "Central de Chamados" com `action: 'centralChamados'`.
2. WHEN o usuário clica em "Central de Chamados" no menu lateral, THE Central_de_Chamados SHALL chamar `showPage()` e renderizar a página da Central de Chamados no `#content`.
3. THE Central_de_Chamados SHALL seguir os padrões visuais do SIGrh: `.page-title`, `.page-body`, `.frow`, `.fg`, `table.grid`, `.btn`, `.alt-section`, `.tabs/.tab/.tab-body`.

---

### Requirement 10: Dados Simulados do Protótipo

**User Story:** Como avaliador do protótipo, quero que a Central de Chamados seja pré-populada com chamados simulados em diferentes status, para que eu possa avaliar todas as telas e fluxos sem precisar criar dados manualmente.

#### Acceptance Criteria

1. THE Central_de_Chamados SHALL inicializar com ao menos 6 chamados simulados distribuídos entre os Status: Novo, Em análise, Em tratamento, Aguardando validação, Finalizado e Cancelado.
2. THE Central_de_Chamados SHALL incluir nos chamados simulados variações de Urgência (Baixa, Média, Alta, Crítica), Área e Tipo de Solicitação.
3. THE Central_de_Chamados SHALL incluir nos chamados simulados entradas de Histórico pré-populadas que demonstrem o fluxo completo de atendimento.
4. THE Central_de_Chamados SHALL incluir nos chamados simulados ao menos um Comentário_Interno e um Comentário_Público para demonstrar a diferença de visibilidade entre os perfis.
