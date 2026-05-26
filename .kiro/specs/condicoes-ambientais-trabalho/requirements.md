# Requirements Document

## Introduction

A funcionalidade **Manter Condições Ambientais do Trabalho** permite que o setor de Saúde do Servidor cadastre, altere e gerencie as Condições Ambientais do Trabalho (CAT) dos servidores públicos. Essa funcionalidade serve como base de dados para o evento S-2240 do eSocial, registrando os ambientes de trabalho, atividades desempenhadas, agentes nocivos, equipamentos de proteção (EPC/EPI) e responsáveis técnicos pelo registro.

A funcionalidade **não** gera XML, **não** envia dados ao eSocial e **não** controla status de envio — seu escopo é exclusivamente o cadastro e manutenção dos dados que subsidiarão o S-2240.

O sistema está implementado como protótipo single-file HTML (index.html) com vanilla JS/CSS, seguindo os padrões visuais e de interação já estabelecidos no SIGrh. O caminho no menu é: Saúde do Servidor > Riscos/Insalubridade > Manter Condições Ambientais do Trabalho (action: `mantCondicoesAmbientais`).

---

## Glossary

- **CAT**: Condição Ambiental do Trabalho — registro que descreve o ambiente, atividades e agentes nocivos aos quais um servidor está exposto.
- **Sistema**: o módulo Manter Condições Ambientais do Trabalho do SIGrh.
- **Gestor_CAT**: usuário autenticado com acesso ao módulo Saúde do Servidor que opera o Sistema.
- **Ambiente_de_Trabalho**: local físico onde o servidor desempenha suas atividades, podendo ser do próprio empregador ou de terceiros.
- **Agente_Nocivo**: fator de risco ambiental ao qual o servidor está exposto (físico, químico, biológico, ergonômico ou mecânico), conforme Tabela 24 do eSocial.
- **EPC**: Equipamento de Proteção Coletiva — medida de proteção coletiva implementada no ambiente de trabalho.
- **EPI**: Equipamento de Proteção Individual — equipamento fornecido ao servidor para proteção contra agentes nocivos.
- **Responsável_Técnico**: profissional de saúde (médico, engenheiro, etc.) responsável pelo registro da condição ambiental, identificado por CPF e órgão de classe.
- **Situação_Cadastral**: estado atual da CAT — Rascunho, Ativa, Anulada ou Inativa.
- **S-2240**: evento do eSocial que reporta as condições ambientais do trabalho; esta funcionalidade fornece os dados base para esse evento, sem gerá-lo ou transmiti-lo.
- **Tabela_24**: tabela oficial do eSocial que lista os códigos de agentes nocivos reconhecidos.
- **tpAval**: tipo de avaliação do agente nocivo — 1 (Quantitativo) ou 2 (Qualitativo).
- **localAmb**: tipo de ambiente de trabalho — 1 (Próprio empregador) ou 2 (Terceiros).
- **tpInsc**: tipo de inscrição do local de trabalho — 1 (CNPJ), 3 (CAEPF) ou 4 (CNO).
- **ideOC**: identificador do órgão de classe do responsável técnico — 1 (CRM), 4 (CREA) ou 9 (Outros).
- **Agente_Ausência**: código 09.01.001 da Tabela_24, que representa ausência de agente nocivo; quando selecionado, é exclusivo (não pode coexistir com outros agentes) e desabilita o bloco EPC/EPI.
- **Vinculação_Servidor**: associação entre um servidor e uma CAT, com período de vigência e situação próprios.

---

## Requirements

### Requirement 1: Listagem de Condições Ambientais

**User Story:** Como Gestor_CAT, quero visualizar todas as Condições Ambientais do Trabalho cadastradas em formato de grade com filtros, para que eu possa consultar e gerenciar os registros existentes.

#### Acceptance Criteria

1. THE Sistema SHALL exibir uma grade com as colunas: Condição ambiental, Tipo de ambiente, Setor, Atividade desempenhada, Agente nocivo principal, Situação cadastral, Responsável técnico, Última alteração.
2. THE Sistema SHALL disponibilizar os seguintes filtros antes da listagem: Condição ambiental (texto), Tipo de ambiente (select), Descrição do setor (texto), Agente nocivo (select da Tabela_24), Situação cadastral (select: Rascunho/Ativa/Anulada/Inativa), Responsável técnico (texto/CPF), Período de cadastro/alteração (data início e data fim).
3. WHEN o Gestor_CAT aciona o botão "Listar", THE Sistema SHALL retornar os registros que atendam a todos os filtros preenchidos.
4. WHEN o Gestor_CAT aciona o botão "Listar" sem preencher nenhum filtro, THE Sistema SHALL retornar todos os registros cadastrados.
5. THE Sistema SHALL exibir os botões de ação: Novo, Listar, Imprimir e "+Opções" (habilitado após selecionar um registro na grade).
6. THE Sistema SHALL NÃO exibir colunas ou informações relacionadas a status eSocial, recibo, protocolo, XML ou retorno de envio.
7. WHEN o Gestor_CAT seleciona um registro na grade, THE Sistema SHALL habilitar o menu "+Opções" com as ações: Informações da condição, Ambiente de trabalho, Atividade desempenhada, Agentes nocivos, Responsável pelo registro, Servidores vinculados, Histórico de alterações.

---

### Requirement 2: Informações da Condição Ambiental

**User Story:** Como Gestor_CAT, quero cadastrar e alterar as informações principais de uma Condição Ambiental do Trabalho, para que os dados básicos de identificação e vigência estejam registrados corretamente.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar um formulário com os campos: Nome/Descrição da condição ambiental (texto, obrigatório), Data de início — dtIniCondicao (data, obrigatório), Data fim — dtFimCondicao (data, opcional), Situação cadastral (select: Rascunho/Ativa/Anulada/Inativa, obrigatório).
2. THE Sistema SHALL exibir a tag técnica ao lado de cada campo, no formato: "Data de início (dtIniCondicao)".
3. IF o Gestor_CAT informar uma dtIniCondicao anterior à data de obrigatoriedade do evento S-2240, THEN THE Sistema SHALL exibir mensagem de validação e não salvar o registro.
4. IF o Gestor_CAT informar uma dtIniCondicao posterior a 30 dias da data atual, THEN THE Sistema SHALL exibir mensagem de validação e não salvar o registro.
5. IF o Gestor_CAT informar uma dtFimCondicao anterior à dtIniCondicao, THEN THE Sistema SHALL exibir mensagem de validação e não salvar o registro.
6. WHEN o Gestor_CAT salva um cadastro válido, THE Sistema SHALL registrar automaticamente o usuário autenticado e a data/hora da operação.

---

### Requirement 3: Ambiente de Trabalho

**User Story:** Como Gestor_CAT, quero cadastrar múltiplos ambientes de trabalho vinculados a uma CAT, para que todos os locais onde o servidor está exposto a riscos sejam documentados.

#### Acceptance Criteria

1. THE Sistema SHALL exibir um grid de ambientes de trabalho com as colunas: Tipo de ambiente, Descrição do setor, Tipo de inscrição, Número de inscrição, Ações (Alterar/Remover).
2. THE Sistema SHALL disponibilizar um formulário de inclusão/alteração de ambiente com os campos: Tipo de ambiente — localAmb (select: 1-Próprio empregador / 2-Terceiros, obrigatório), Descrição do setor — dscSetor (texto, máx. 100 caracteres, obrigatório), Tipo de inscrição — tpInsc (select: 1-CNPJ / 3-CAEPF / 4-CNO, obrigatório), Número de inscrição — nrInsc (texto, obrigatório).
3. IF o Gestor_CAT tentar adicionar um 10º ambiente de trabalho, THEN THE Sistema SHALL exibir mensagem informando que o limite máximo de 9 ambientes foi atingido e não permitir a inclusão.
4. WHEN o Gestor_CAT aciona "Remover" em um ambiente, THE Sistema SHALL solicitar confirmação antes de excluir o registro do grid.
5. THE Sistema SHALL exibir a tag técnica ao lado de cada campo do formulário de ambiente.

---

### Requirement 4: Atividade Desempenhada

**User Story:** Como Gestor_CAT, quero registrar a descrição da atividade desempenhada pelo servidor na condição ambiental, para que fique documentado o contexto de exposição ao risco.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar um campo de texto único: Descrição da atividade — dscAtivDes (textarea, máx. 999 caracteres, obrigatório).
2. THE Sistema SHALL exibir a tag técnica ao lado do campo: "Descrição da atividade (dscAtivDes)".
3. THE Sistema SHALL exibir um contador de caracteres restantes ao lado do campo dscAtivDes.
4. IF o Gestor_CAT tentar salvar a CAT sem preencher o campo dscAtivDes, THEN THE Sistema SHALL exibir mensagem de validação e não salvar o registro.

---

### Requirement 5: Agentes Nocivos

**User Story:** Como Gestor_CAT, quero cadastrar múltiplos agentes nocivos vinculados a uma CAT, para que todos os fatores de risco aos quais o servidor está exposto sejam documentados conforme a Tabela 24 do eSocial.

#### Acceptance Criteria

1. THE Sistema SHALL exibir um grid de agentes nocivos com as colunas: Código, Descrição, Tipo de avaliação, Unidade de medida, Possui EPC/EPI, Ações (Alterar/Remover).
2. THE Sistema SHALL disponibilizar um formulário de inclusão/alteração de agente com os campos: Código do agente — codAgNoc (select da Tabela_24, obrigatório), Descrição — dscAgNoc (texto, máx. 100 caracteres, obrigatório), Tipo de avaliação — tpAval (select: 1-Quantitativo / 2-Qualitativo), Intensidade/Concentração/Dose — intConc (texto), Limite de tolerância — limTol (texto), Unidade de medida — unMed (texto), Técnica de medição — tecMedicao (texto, máx. 40 caracteres), Número processo judicial — nrProcJud (texto).
3. IF o Gestor_CAT selecionar o código 09.01.001 (ausência de agente nocivo) e já existir outro agente no grid, THEN THE Sistema SHALL exibir mensagem informando que o código 09.01.001 é exclusivo e não permitir a inclusão.
4. IF o Gestor_CAT tentar adicionar qualquer agente diferente de 09.01.001 quando o código 09.01.001 já estiver no grid, THEN THE Sistema SHALL exibir mensagem de exclusividade e não permitir a inclusão.
5. IF o codAgNoc selecionado for diferente de 09.01.001, THEN THE Sistema SHALL tornar o campo tpAval obrigatório.
6. IF o tpAval selecionado for 1 (Quantitativo), THEN THE Sistema SHALL tornar os campos intConc, unMed e tecMedicao obrigatórios.
7. IF o tpAval for 1 (Quantitativo) E o codAgNoc for 01.18.001 ou 02.01.014, THEN THE Sistema SHALL tornar o campo limTol obrigatório.
8. IF o codAgNoc selecionado for 05.01.001, THEN THE Sistema SHALL tornar o campo nrProcJud obrigatório.
9. THE Sistema SHALL exibir a tag técnica ao lado de cada campo do formulário de agente.

---

### Requirement 6: EPC/EPI por Agente Nocivo

**User Story:** Como Gestor_CAT, quero registrar as informações de EPC e EPI para cada agente nocivo cadastrado, para que as medidas de proteção adotadas fiquem documentadas conforme exigido pelo S-2240.

#### Acceptance Criteria

1. THE Sistema SHALL vincular um bloco EPC/EPI a cada agente nocivo cadastrado no grid.
2. WHILE o agente nocivo associado possuir o código 09.01.001, THE Sistema SHALL manter o bloco EPC/EPI desabilitado para aquele agente.
3. THE Sistema SHALL disponibilizar os campos do bloco EPC/EPI: Utilização de EPC — utilizEPC (select: 0-Não se aplica / 1-Não implementa / 2-Implementa), Eficácia do EPC — eficEpc (select: S-Sim / N-Não), Utilização de EPI — utilizEPI (select: 0-Não se aplica / 1-Não utilizado / 2-Utilizado), Eficácia do EPI — eficEpi (select: S-Sim / N-Não), Documento de avaliação — docAval (texto, máx. 255 caracteres).
4. IF o utilizEPC selecionado for 2 (Implementa), THEN THE Sistema SHALL tornar o campo eficEpc obrigatório.
5. IF o utilizEPI selecionado for 2 (Utilizado), THEN THE Sistema SHALL tornar os campos eficEpi e docAval obrigatórios.
6. IF o utilizEPI selecionado for 2 (Utilizado), THEN THE Sistema SHALL exibir o questionário EPI com os campos (todos S/N obrigatórios): Medida de proteção — medProtecao, Condição de funcionamento — condFuncto, Uso ininterrupto — usoInint, Prazo de validade — przValid, Periodicidade de troca — periodicTroca, Higienização — higienizacao.
7. WHEN o utilizEPI for alterado para valor diferente de 2, THE Sistema SHALL ocultar e limpar os campos do questionário EPI.
8. THE Sistema SHALL exibir a tag técnica ao lado de cada campo do bloco EPC/EPI.

---

### Requirement 7: Responsável pelo Registro

**User Story:** Como Gestor_CAT, quero registrar o responsável técnico pela condição ambiental, para que haja identificação do profissional que atesta as informações de saúde ocupacional.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar um formulário com os campos: CPF do responsável — cpfResp (texto, obrigatório), Órgão de classe — ideOC (select: 1-CRM / 4-CREA / 9-Outros), Descrição do órgão — dscOC (texto, máx. 20 caracteres), Número de inscrição — nrOC (texto, máx. 14 caracteres), UF — ufOC (select de UFs brasileiras).
2. IF a CAT possuir pelo menos um agente nocivo com código diferente de 09.01.001, THEN THE Sistema SHALL tornar os campos ideOC, nrOC e ufOC obrigatórios.
3. IF o ideOC selecionado for 9 (Outros), THEN THE Sistema SHALL tornar o campo dscOC obrigatório.
4. THE Sistema SHALL exibir a tag técnica ao lado de cada campo do formulário de responsável.

---

### Requirement 8: Observações

**User Story:** Como Gestor_CAT, quero registrar observações complementares sobre a condição ambiental, para que informações adicionais relevantes fiquem documentadas no registro.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar um campo de texto: Observações complementares — obsCompl (textarea, máx. 999 caracteres, opcional).
2. THE Sistema SHALL exibir a tag técnica ao lado do campo: "Observações complementares (obsCompl)".
3. THE Sistema SHALL exibir um contador de caracteres restantes ao lado do campo obsCompl.

---

### Requirement 9: Servidores Vinculados

**User Story:** Como Gestor_CAT, quero vincular servidores a uma Condição Ambiental do Trabalho individualmente ou em lote, para que fique registrado quais servidores estão expostos às condições documentadas.

#### Acceptance Criteria

1. THE Sistema SHALL exibir um grid de servidores vinculados com as colunas: Servidor, CPF, Matrícula, Categoria eSocial, Vínculo, Unidade, Cargo/Função, Data início da condição — dtIniCondicao, Data fim da condição — dtFimCondicao, Situação da vinculação.
2. THE Sistema SHALL disponibilizar a opção de vinculação individual de servidor, com os campos: Servidor (busca por nome/CPF/matrícula), dtIniCondicao (data, obrigatório), dtFimCondicao (data, opcional), Situação da vinculação.
3. THE Sistema SHALL disponibilizar a opção de vinculação em lote, com filtros de seleção (unidade, cargo/função, vínculo) e exibição da lista de servidores encontrados para revisão antes de confirmar a vinculação.
4. WHEN o Gestor_CAT confirma a vinculação em lote, THE Sistema SHALL exibir um resumo dos servidores que serão vinculados e solicitar confirmação antes de efetivar a operação.
5. IF a dtFimCondicao da vinculação for informada e for anterior à dtIniCondicao da vinculação, THEN THE Sistema SHALL exibir mensagem de validação e não salvar a vinculação.

---

### Requirement 10: Situação Cadastral

**User Story:** Como Gestor_CAT, quero controlar a situação cadastral de uma Condição Ambiental do Trabalho, para que o ciclo de vida do registro seja gerenciado adequadamente.

#### Acceptance Criteria

1. THE Sistema SHALL suportar as seguintes situações cadastrais: Rascunho, Ativa, Anulada, Inativa.
2. THE Sistema SHALL NÃO controlar ou exibir status de envio ao eSocial, recibo, protocolo ou XML em nenhuma situação cadastral.
3. WHEN o Gestor_CAT altera a situação de uma CAT para "Anulada", THE Sistema SHALL registrar o usuário autenticado, a data/hora da anulação e exigir justificativa.
4. WHEN o Gestor_CAT altera a situação de uma CAT de "Anulada" ou "Inativa" para "Ativa", THE Sistema SHALL registrar o usuário autenticado e a data/hora da reativação.

---

### Requirement 11: Histórico de Alterações

**User Story:** Como Gestor_CAT, quero consultar o histórico completo de alterações de uma Condição Ambiental do Trabalho, para que haja rastreabilidade de todas as modificações realizadas no registro.

#### Acceptance Criteria

1. THE Sistema SHALL registrar e exibir no histórico: usuário e data/hora de cadastro, usuário e data/hora da última alteração, registros de anulação e reativação com usuário, data/hora e justificativa.
2. THE Sistema SHALL registrar no histórico as alterações realizadas em: dados dos servidores vinculados, agentes nocivos, bloco EPC/EPI e responsável técnico.
3. THE Sistema SHALL exibir o histórico em ordem cronológica decrescente (alteração mais recente primeiro).
4. THE Sistema SHALL disponibilizar o histórico de alterações como opção no menu "+Opções" da listagem, abrindo uma seção ou modal com os registros de auditoria da CAT selecionada.

---

### Requirement 12: Impressão

**User Story:** Como Gestor_CAT, quero imprimir a listagem de Condições Ambientais do Trabalho, para que eu tenha um relatório físico ou em PDF dos registros.

#### Acceptance Criteria

1. WHEN o Gestor_CAT aciona o botão "Imprimir", THE Sistema SHALL exibir um modal de configuração de impressão com opções de título do relatório, disposição (retrato/paisagem) e formato (PDF/Excel/HTML).
2. WHEN o Gestor_CAT confirma a impressão, THE Sistema SHALL gerar o relatório com os registros atualmente exibidos na listagem, respeitando os filtros aplicados.
3. THE Sistema SHALL NÃO incluir no relatório campos relacionados a status eSocial, recibo, protocolo ou XML.

---

### Requirement 13: Padrão Visual e Navegação

**User Story:** Como Gestor_CAT, quero que a funcionalidade siga os padrões visuais e de navegação do SIGrh, para que a experiência de uso seja consistente com o restante do sistema.

#### Acceptance Criteria

1. THE Sistema SHALL utilizar os componentes visuais padrão do SIGrh: `.page-title`, `.page-body`, `.frow`, `.fg`, `table.grid`, `.btn`, `.alt-section`, `.tabs`/`.tab`/`.tab-body`.
2. THE Sistema SHALL organizar o formulário de cadastro/alteração em abas ou seções nomeadas: Informações da Condição, Ambiente de Trabalho, Atividade Desempenhada, Agentes Nocivos, EPC/EPI, Responsável pelo Registro, Observações.
3. THE Sistema SHALL exibir a tag técnica ao lado de cada campo do formulário, no formato: "Nome do campo (nomeVariavel)".
4. THE Sistema SHALL adicionar a entrada de menu no array `menu` do JavaScript, no caminho: Saúde do Servidor > Riscos/Insalubridade > Manter Condições Ambientais do Trabalho, com `action: 'mantCondicoesAmbientais'`.
5. THE Sistema SHALL implementar a funcionalidade como uma função JavaScript `mantCondicoesAmbientais()` que chama `showPage(title, htmlContent)` para renderizar o conteúdo no `#content` div.
