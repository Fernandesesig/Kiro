# Documento de Requisitos — SIGRH Protótipo

## Introdução

O SIGRH (Sistema Integrado de Gestão de Recursos Humanos) é uma plataforma governamental que centraliza a gestão de pessoas, vínculos funcionais, folha de pagamento, frequência, afastamentos, movimentações, benefícios, desenvolvimento e previdência dos servidores públicos. Este documento descreve os requisitos funcionais derivados do design do protótipo, cobrindo todos os módulos e submódulos identificados.

## Glossário

- **Sistema**: O SIGRH como um todo
- **Servidor**: Funcionário público com vínculo ativo no sistema
- **Órgão**: Unidade administrativa de nível superior na estrutura organizacional
- **Agrupamento**: Conjunto de órgãos sob mesma gestão de RH
- **Unidade Organizacional**: Subdivisão interna de um órgão
- **Vínculo**: Relação jurídica entre servidor e órgão (efetivo, comissionado, temporário)
- **Carreira**: Estrutura hierárquica de cargos e progressões funcionais
- **Folha de Pagamento**: Processamento mensal de remunerações e descontos
- **Frequência**: Registro e controle de presença e jornada de trabalho
- **Afastamento**: Ausência temporária ou definitiva do servidor do exercício do cargo
- **Recadastramento**: Processo periódico de confirmação de dados cadastrais do servidor
- **Ato Oficial**: Documento formal publicado em diário oficial com efeitos jurídicos
- **Contracheque**: Demonstrativo mensal de remuneração do servidor
- **ATS**: Adicional por Tempo de Serviço
- **eSocial**: Sistema de escrituração digital de obrigações fiscais, previdenciárias e trabalhistas
- **Lotação**: Alocação do servidor em determinada unidade organizacional
- **Quadro Lotacional**: Conjunto de vagas autorizadas por unidade organizacional
- **Processo Seletivo**: Concurso público ou seleção para ingresso de novos servidores
- **Usuário RH**: Servidor com perfil de gestão de recursos humanos no sistema
- **Gestor**: Servidor com perfil de chefia ou coordenação
- **Administrador**: Usuário com perfil de administração do sistema

## Requisitos


---

### Requisito 1: Manter Pessoas

**User Story:** Como usuário de RH, quero cadastrar e manter os dados pessoais dos servidores, para que as informações cadastrais estejam sempre atualizadas e disponíveis para os demais módulos do sistema.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir o cadastro de pessoas com campos obrigatórios: nome completo, CPF, data de nascimento, sexo e nacionalidade
2. WHEN um CPF já cadastrado for informado no formulário de inclusão, THE Sistema SHALL exibir mensagem de erro indicando duplicidade e impedir o cadastro
3. WHEN um registro de pessoa for salvo com sucesso, THE Sistema SHALL atribuir um identificador único e registrar data e hora da operação
4. WHEN o usuário pesquisar pessoas, THE Sistema SHALL permitir filtros por nome, CPF, matrícula e situação cadastral
5. WHEN um registro de pessoa for editado, THE Sistema SHALL manter histórico da alteração com usuário responsável e data/hora
6. IF um campo obrigatório não for preenchido, THEN THE Sistema SHALL destacar o campo e exibir mensagem de validação antes de permitir o salvamento
7. THE Sistema SHALL permitir o registro de dados de contato: endereço, telefone e e-mail
8. WHEN o usuário solicitar exclusão de uma pessoa com vínculos ativos, THE Sistema SHALL impedir a exclusão e exibir mensagem informativa

---

### Requisito 2: Corrida do Servidor — Baixar Inscrições

**User Story:** Como usuário de RH, quero baixar as inscrições da corrida do servidor, para que os dados dos participantes estejam disponíveis para controle e organização do evento.

#### Critérios de Aceitação

1. WHEN o usuário acessar a funcionalidade de baixar inscrições, THE Sistema SHALL listar os eventos de corrida disponíveis para seleção
2. WHEN o usuário selecionar um evento e solicitar o download, THE Sistema SHALL gerar arquivo com os dados dos inscritos no formato configurado (PDF ou planilha)
3. IF não houver inscrições para o evento selecionado, THEN THE Sistema SHALL exibir mensagem informativa ao usuário
4. THE Sistema SHALL exibir o total de inscritos por categoria antes de confirmar o download

---

### Requisito 3: Validar Recadastramento

**User Story:** Como usuário de RH, quero validar os recadastramentos enviados pelos servidores, para que apenas informações corretas e completas sejam confirmadas no sistema.

#### Critérios de Aceitação

1. WHEN o usuário acessar a validação de recadastramento, THE Sistema SHALL listar os recadastramentos pendentes de análise
2. WHEN o usuário selecionar um recadastramento, THE Sistema SHALL exibir os dados informados pelo servidor para conferência
3. WHEN o usuário aprovar um recadastramento, THE Sistema SHALL atualizar a situação para "Validado" e registrar o responsável e a data/hora da validação
4. WHEN o usuário rejeitar um recadastramento, THE Sistema SHALL solicitar o preenchimento do motivo da rejeição antes de confirmar
5. IF o prazo de recadastramento estiver vencido, THEN THE Sistema SHALL sinalizar o registro com indicador de prazo expirado
6. THE Sistema SHALL permitir filtrar recadastramentos por situação: pendente, validado e rejeitado

---

### Requisito 4: Manter Recadastramento

**User Story:** Como usuário de RH, quero manter os registros de recadastramento dos servidores, para que o processo de atualização cadastral periódica seja gerenciado de forma centralizada.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir a abertura de um período de recadastramento com data de início e data de encerramento
2. WHEN um período de recadastramento for aberto, THE Sistema SHALL notificar os servidores vinculados ao órgão
3. WHEN o servidor submeter o recadastramento, THE Sistema SHALL registrar data e hora do envio e alterar a situação para "Aguardando Validação"
4. IF um servidor não realizar o recadastramento dentro do prazo, THEN THE Sistema SHALL registrar a situação como "Não Recadastrado"
5. THE Sistema SHALL permitir consultar o histórico de recadastramentos por servidor


---

### Requisito 5: Analisar Solicitações de Frequência

**User Story:** Como usuário de RH, quero analisar as solicitações de frequência dos servidores, para que os registros sejam conferidos e aprovados antes de impactar a folha de pagamento.

#### Critérios de Aceitação

1. WHEN o usuário acessar a análise de solicitações, THE Sistema SHALL listar as solicitações pendentes com servidor, data, tipo e situação
2. WHEN o usuário selecionar uma solicitação, THE Sistema SHALL exibir os detalhes do registro e o histórico de justificativas
3. WHEN o usuário aprovar uma solicitação, THE Sistema SHALL alterar a situação para "Aprovada" e registrar o responsável e a data/hora
4. WHEN o usuário rejeitar uma solicitação, THE Sistema SHALL exigir o preenchimento do motivo antes de confirmar a rejeição
5. THE Sistema SHALL permitir filtrar solicitações por período, servidor, unidade organizacional e situação

---

### Requisito 6: Efetivar Registro de Ponto na Folha

**User Story:** Como usuário de RH, quero efetivar os registros de ponto aprovados na folha de pagamento, para que as ocorrências de frequência reflitam corretamente na remuneração do servidor.

#### Critérios de Aceitação

1. WHEN o usuário solicitar a efetivação, THE Sistema SHALL processar apenas os registros de ponto com situação "Aprovada"
2. WHEN a efetivação for concluída, THE Sistema SHALL registrar data, hora e usuário responsável pelo processamento
3. IF houver registros com inconsistências não resolvidas, THEN THE Sistema SHALL listar os registros pendentes e impedir a efetivação parcial sem confirmação explícita do usuário
4. THE Sistema SHALL permitir a efetivação por competência (mês/ano) e por unidade organizacional

---

### Requisito 7: Monitorar Inconsistências de Frequência

**User Story:** Como usuário de RH, quero monitorar as inconsistências de frequência dos servidores, para que problemas nos registros de ponto sejam identificados e corrigidos antes do fechamento da folha.

#### Critérios de Aceitação

1. THE Sistema SHALL exibir painel com o total de inconsistências agrupadas por tipo e por unidade organizacional
2. WHEN o usuário selecionar uma inconsistência, THE Sistema SHALL exibir os detalhes do registro e as opções de resolução disponíveis
3. THE Sistema SHALL classificar as inconsistências por criticidade: bloqueante e não bloqueante
4. WHEN todas as inconsistências bloqueantes forem resolvidas, THE Sistema SHALL liberar o processo de efetivação na folha
5. THE Sistema SHALL permitir exportar o relatório de inconsistências em formato PDF ou planilha

---

### Requisito 8: Solicitar Frequência

**User Story:** Como servidor, quero solicitar o registro ou ajuste da minha frequência, para que ausências justificadas ou erros de marcação sejam corrigidos de forma formal.

#### Critérios de Aceitação

1. WHEN o servidor acessar a solicitação de frequência, THE Sistema SHALL exibir o calendário do mês corrente com os registros existentes
2. WHEN o servidor selecionar uma data e informar o tipo de ocorrência, THE Sistema SHALL registrar a solicitação com situação "Pendente"
3. IF a data selecionada já possuir registro efetivado na folha, THEN THE Sistema SHALL exibir aviso e exigir justificativa para a solicitação de ajuste
4. THE Sistema SHALL permitir ao servidor anexar documentos comprobatórios à solicitação
5. WHEN a solicitação for enviada, THE Sistema SHALL notificar o responsável de RH para análise

---

### Requisito 9: Ajuste de Ponto

**User Story:** Como servidor, quero solicitar ajuste no meu registro de ponto, para que marcações incorretas ou ausentes sejam corrigidas com a devida justificativa.

#### Critérios de Aceitação

1. WHEN o servidor solicitar ajuste de ponto, THE Sistema SHALL exibir os registros do período selecionado com horários de entrada e saída
2. WHEN o servidor informar o horário correto e a justificativa, THE Sistema SHALL registrar a solicitação de ajuste com situação "Aguardando Aprovação"
3. IF o horário informado for inválido ou fora da jornada cadastrada, THEN THE Sistema SHALL exibir mensagem de validação e impedir o envio
4. THE Sistema SHALL manter histórico de todos os ajustes solicitados e seus resultados


---

### Requisito 10: Manter Agências Bancárias

**User Story:** Como administrador, quero manter o cadastro de agências bancárias, para que os dados bancários dos servidores possam ser validados e vinculados corretamente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar agências com: banco, número da agência, dígito verificador, nome e município
2. WHEN o usuário informar um número de agência já cadastrado para o mesmo banco, THE Sistema SHALL exibir mensagem de duplicidade e impedir o cadastro
3. THE Sistema SHALL permitir pesquisar agências por banco, número e município
4. WHEN uma agência for inativada, THE Sistema SHALL verificar se há servidores com dados bancários vinculados e exibir aviso antes de confirmar

---

### Requisito 11: Manter Bancos

**User Story:** Como administrador, quero manter o cadastro de bancos, para que as agências e dados bancários dos servidores sejam associados a instituições financeiras válidas.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar bancos com: código COMPE, nome e situação (ativo/inativo)
2. WHEN o usuário informar um código COMPE já existente, THE Sistema SHALL exibir mensagem de duplicidade e impedir o cadastro
3. THE Sistema SHALL permitir pesquisar bancos por código e nome
4. WHEN um banco for inativado, THE Sistema SHALL verificar agências ativas vinculadas e exibir aviso antes de confirmar

---

### Requisito 12: Manter Calendário do Agrupamento

**User Story:** Como administrador, quero manter o calendário do agrupamento, para que feriados e dias não úteis sejam considerados nos cálculos de frequência e folha de pagamento.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar datas no calendário com: data, tipo (feriado nacional, estadual, municipal, ponto facultativo) e descrição
2. WHEN uma data já cadastrada for informada novamente para o mesmo agrupamento, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir copiar o calendário de um exercício anterior como base para o novo exercício
4. THE Sistema SHALL exibir o calendário em formato mensal com destaque visual para os dias não úteis

---

### Requisito 13: Manter Calendário do Órgão

**User Story:** Como administrador, quero manter o calendário específico do órgão, para que particularidades locais de dias não úteis sejam consideradas além do calendário do agrupamento.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar datas específicas do órgão com: data, tipo e descrição
2. WHEN o calendário do órgão for consultado, THE Sistema SHALL exibir a composição entre o calendário do agrupamento e as datas específicas do órgão
3. THE Sistema SHALL permitir excluir datas específicas do órgão sem afetar o calendário do agrupamento

---

### Requisito 14: Manter Municípios

**User Story:** Como administrador, quero manter o cadastro de municípios, para que endereços de servidores e unidades organizacionais sejam validados com dados geográficos corretos.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar municípios com: nome, UF, código IBGE e situação
2. WHEN o usuário informar um código IBGE já existente, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir pesquisar municípios por nome e UF
4. THE Sistema SHALL permitir inativar municípios sem excluir registros históricos vinculados

---

### Requisito 15: Manter Bairros

**User Story:** Como administrador, quero manter o cadastro de bairros, para que os endereços dos servidores possam ser preenchidos com dados padronizados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar bairros com: nome e município vinculado
2. WHEN o usuário informar um bairro com o mesmo nome no mesmo município, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir pesquisar bairros por nome e município

---

### Requisito 16: Manter Países

**User Story:** Como administrador, quero manter o cadastro de países, para que a nacionalidade e naturalidade dos servidores sejam registradas com dados padronizados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar países com: nome, sigla e código ISO
2. WHEN o usuário informar um código ISO já existente, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir pesquisar países por nome e sigla

---

### Requisito 17: Manter Tipo de Logradouro

**User Story:** Como administrador, quero manter os tipos de logradouro, para que os endereços dos servidores sejam padronizados conforme nomenclatura oficial.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar tipos de logradouro com: sigla e descrição
2. WHEN o usuário informar uma sigla já existente, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir inativar tipos de logradouro sem excluir registros históricos


---

### Requisito 18: Consultar Organograma

**User Story:** Como usuário, quero consultar o organograma da estrutura organizacional, para que eu possa visualizar a hierarquia dos órgãos e unidades de forma clara.

#### Critérios de Aceitação

1. WHEN o usuário acessar o organograma, THE Sistema SHALL exibir a estrutura hierárquica em formato de árvore com órgãos e unidades organizacionais
2. THE Sistema SHALL permitir expandir e recolher os nós da árvore para facilitar a navegação
3. WHEN o usuário selecionar um nó, THE Sistema SHALL exibir informações resumidas da unidade: nome, sigla, responsável e quantidade de servidores lotados
4. THE Sistema SHALL permitir exportar o organograma em formato de imagem ou PDF

---

### Requisito 19: Manter Órgãos

**User Story:** Como administrador, quero manter o cadastro de órgãos, para que a estrutura organizacional do governo esteja corretamente representada no sistema.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar órgãos com: nome, sigla, CNPJ, tipo e situação
2. WHEN o usuário informar um CNPJ já cadastrado, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir definir o órgão superior na hierarquia organizacional
4. WHEN um órgão for inativado, THE Sistema SHALL verificar servidores ativos lotados e exibir aviso antes de confirmar
5. THE Sistema SHALL manter histórico de alterações no cadastro do órgão

---

### Requisito 20: Manter Unidades Organizacionais

**User Story:** Como administrador, quero manter as unidades organizacionais dos órgãos, para que a estrutura interna de cada órgão esteja representada e os servidores possam ser corretamente lotados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar unidades com: nome, sigla, órgão vinculado, unidade superior e situação
2. WHEN o usuário informar uma sigla já existente no mesmo órgão, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir definir a hierarquia entre unidades do mesmo órgão
4. WHEN uma unidade for inativada, THE Sistema SHALL verificar servidores lotados e exibir aviso antes de confirmar
5. THE Sistema SHALL permitir pesquisar unidades por nome, sigla e órgão

---

### Requisito 21: Manter Ocupação

**User Story:** Como administrador, quero manter o cadastro de ocupações, para que as atividades exercidas pelos servidores sejam classificadas conforme a Classificação Brasileira de Ocupações.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar ocupações com: código CBO, descrição e situação
2. WHEN o usuário informar um código CBO já existente, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir pesquisar ocupações por código e descrição

---

### Requisito 22: Manter Atividades

**User Story:** Como administrador, quero manter o cadastro de atividades funcionais, para que as atribuições dos cargos sejam detalhadas e vinculadas às ocupações correspondentes.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar atividades com: descrição, ocupação vinculada e situação
2. THE Sistema SHALL permitir pesquisar atividades por descrição e ocupação
3. WHEN uma atividade for inativada, THE Sistema SHALL verificar vínculos ativos que a referenciam e exibir aviso

---

### Requisito 23: Manter Quadro Lotacional

**User Story:** Como administrador, quero manter o quadro lotacional das unidades organizacionais, para que o número de vagas autorizadas por cargo e unidade seja controlado.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir definir o quadro lotacional por unidade organizacional, cargo e quantidade de vagas autorizadas
2. WHEN o número de servidores lotados em uma unidade ultrapassar o quadro autorizado, THE Sistema SHALL exibir alerta ao usuário responsável
3. THE Sistema SHALL registrar o histórico de alterações no quadro lotacional com data e responsável
4. THE Sistema SHALL permitir pesquisar o quadro por unidade, cargo e exercício

---

### Requisito 24: Pesquisar Quadro Lotacional

**User Story:** Como usuário de RH, quero pesquisar o quadro lotacional, para que eu possa verificar a distribuição de vagas e servidores por unidade organizacional.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir pesquisar o quadro lotacional por órgão, unidade, cargo e exercício
2. WHEN o resultado da pesquisa for exibido, THE Sistema SHALL mostrar: vagas autorizadas, vagas ocupadas e vagas disponíveis por unidade
3. THE Sistema SHALL permitir exportar o resultado da pesquisa em formato PDF ou planilha


---

### Requisito 25: Elaborar Atos Oficiais

**User Story:** Como usuário de RH, quero elaborar atos oficiais, para que as decisões administrativas sejam formalizadas em documentos com validade jurídica.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir criar atos oficiais com: tipo, número, data, ementa e texto do ato
2. WHEN o usuário selecionar o tipo de ato, THE Sistema SHALL carregar o modelo de texto correspondente para edição
3. THE Sistema SHALL permitir vincular servidores ao ato oficial
4. WHEN o ato for salvo como rascunho, THE Sistema SHALL manter a situação como "Em Elaboração" até a publicação
5. THE Sistema SHALL permitir visualizar prévia do ato antes da publicação

---

### Requisito 26: Manter Tipo de Ato Oficial

**User Story:** Como administrador, quero manter os tipos de ato oficial, para que os modelos de documentos estejam disponíveis para elaboração conforme a natureza de cada decisão administrativa.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar tipos de ato com: nome, modelo de texto e situação
2. THE Sistema SHALL permitir definir campos variáveis no modelo de texto para preenchimento automático
3. WHEN um tipo de ato for inativado, THE Sistema SHALL impedir a criação de novos atos desse tipo sem excluir os existentes

---

### Requisito 27: Publicar Atos Oficiais

**User Story:** Como usuário de RH, quero publicar atos oficiais, para que as decisões administrativas tenham efeito legal a partir da data de publicação.

#### Critérios de Aceitação

1. WHEN o usuário solicitar a publicação de um ato, THE Sistema SHALL verificar se todos os campos obrigatórios estão preenchidos antes de confirmar
2. WHEN um ato for publicado, THE Sistema SHALL registrar a data de publicação e alterar a situação para "Publicado"
3. WHEN um ato for publicado, THE Sistema SHALL disparar os efeitos funcionais vinculados ao tipo de ato
4. THE Sistema SHALL impedir a edição de atos com situação "Publicado"
5. THE Sistema SHALL permitir consultar atos publicados por tipo, período e servidor vinculado

---

### Requisito 28: Consultar Inclusões

**User Story:** Como usuário de RH, quero consultar as inclusões realizadas nos processos do sistema, para que eu possa acompanhar o histórico de registros inseridos por módulo.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir consultar inclusões por módulo, período, usuário responsável e tipo de registro
2. WHEN o resultado for exibido, THE Sistema SHALL mostrar: data/hora, usuário, módulo e descrição do registro incluído
3. THE Sistema SHALL permitir exportar o resultado da consulta em formato PDF ou planilha

---

### Requisito 29: Solicitar Novo Concurso

**User Story:** Como usuário de RH, quero solicitar a abertura de um novo concurso público, para que o processo de seleção de novos servidores seja iniciado formalmente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar a solicitação de concurso com: órgão, cargo, número de vagas, justificativa e documentação de suporte
2. WHEN a solicitação for enviada, THE Sistema SHALL registrar a data e o usuário solicitante e alterar a situação para "Aguardando Aprovação"
3. THE Sistema SHALL permitir acompanhar a situação da solicitação pelo usuário que a criou
4. IF campos obrigatórios não forem preenchidos, THEN THE Sistema SHALL exibir mensagem de validação e impedir o envio

---

### Requisito 30: Manter Edital

**User Story:** Como usuário de RH, quero manter o edital do concurso público, para que as regras, prazos e vagas do processo seletivo sejam formalizados e publicados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar editais com: número, data de publicação, concurso vinculado, cargos, vagas e cronograma
2. WHEN o edital for publicado, THE Sistema SHALL alterar a situação para "Publicado" e registrar data e responsável
3. THE Sistema SHALL permitir adicionar e remover cargos e vagas do edital enquanto a situação for "Em Elaboração"
4. THE Sistema SHALL impedir alterações no edital após a publicação

---

### Requisito 31: Importar Inscritos

**User Story:** Como usuário de RH, quero importar a lista de inscritos no concurso, para que os candidatos aprovados possam ser processados para nomeação.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir importar arquivo com dados dos inscritos no formato definido (CSV ou planilha)
2. WHEN o arquivo for importado, THE Sistema SHALL validar o formato e os campos obrigatórios antes de processar
3. IF o arquivo contiver registros com CPF inválido ou duplicado, THEN THE Sistema SHALL listar os erros encontrados e permitir que o usuário corrija antes de confirmar a importação
4. WHEN a importação for concluída com sucesso, THE Sistema SHALL exibir o total de registros importados


---

### Requisito 32: Nomear Candidatos

**User Story:** Como usuário de RH, quero nomear candidatos aprovados no concurso, para que o ingresso dos novos servidores seja formalizado com o ato de nomeação.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar candidatos da lista de aprovados para nomeação
2. WHEN o usuário confirmar a nomeação, THE Sistema SHALL gerar o ato oficial de nomeação e alterar a situação do candidato para "Nomeado"
3. THE Sistema SHALL registrar a data de nomeação e o cargo para o qual o candidato foi nomeado
4. IF o candidato já possuir vínculo ativo no sistema, THEN THE Sistema SHALL exibir aviso de acumulação antes de confirmar a nomeação

---

### Requisito 33: Registrar Posse

**User Story:** Como usuário de RH, quero registrar a posse do candidato nomeado, para que o vínculo funcional seja efetivado e o servidor passe a exercer o cargo.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar a posse com: candidato nomeado, data da posse, cargo e unidade de lotação
2. WHEN a posse for registrada, THE Sistema SHALL criar o vínculo funcional do servidor e alterar a situação para "Em Exercício"
3. IF a data da posse for posterior ao prazo legal de posse, THEN THE Sistema SHALL exibir aviso de prazo expirado e exigir justificativa
4. WHEN a posse for registrada, THE Sistema SHALL gerar o ato oficial de posse automaticamente

---

### Requisito 34: Manter Estrutura de Carreira

**User Story:** Como administrador, quero manter a estrutura de carreira dos cargos efetivos, para que os níveis, classes e referências salariais estejam corretamente definidos para progressão funcional.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar estruturas de carreira com: nome, órgão, hierarquia (carreira/grupo/cargo) e vigência
2. THE Sistema SHALL permitir definir os níveis e classes da estrutura com os respectivos valores de referência salarial
3. WHEN uma estrutura de carreira for alterada, THE Sistema SHALL registrar o histórico com data e responsável
4. THE Sistema SHALL impedir a exclusão de estruturas de carreira com servidores vinculados

---

### Requisito 35: Manter Itens de Carreira

**User Story:** Como administrador, quero manter os itens de carreira, para que os cargos e grupos ocupacionais estejam disponíveis para vinculação de servidores.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar itens de carreira com: grupo ocupacional, cargo, nível e situação
2. THE Sistema SHALL permitir vincular itens de carreira à estrutura de carreira correspondente
3. WHEN um item de carreira for inativado, THE Sistema SHALL verificar servidores vinculados e exibir aviso antes de confirmar

---

### Requisito 36: Manter Cargo em Comissão

**User Story:** Como administrador, quero manter o cadastro de cargos em comissão, para que as designações e exonerações de servidores comissionados sejam gerenciadas corretamente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar cargos em comissão com: denominação, símbolo, valor, órgão e situação
2. THE Sistema SHALL permitir definir a estrutura hierárquica dos cargos em comissão
3. WHEN um cargo em comissão for inativado, THE Sistema SHALL verificar ocupantes ativos e exibir aviso antes de confirmar
4. THE Sistema SHALL permitir pesquisar cargos em comissão por denominação, símbolo e órgão

---

### Requisito 37: Exonerar Cargo em Comissão

**User Story:** Como usuário de RH, quero registrar a exoneração de servidor de cargo em comissão, para que o desligamento do cargo seja formalizado e os efeitos financeiros sejam processados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar o servidor ocupante do cargo em comissão para exoneração
2. WHEN o usuário confirmar a exoneração, THE Sistema SHALL registrar a data de exoneração e gerar o ato oficial correspondente
3. THE Sistema SHALL alterar a situação do vínculo comissionado para "Exonerado" após a confirmação
4. IF a data de exoneração for retroativa, THEN THE Sistema SHALL calcular os efeitos financeiros do período e exibir resumo antes de confirmar

---

### Requisito 38: Manter Informações do Vínculo

**User Story:** Como usuário de RH, quero manter as informações do vínculo funcional do servidor, para que os dados de lotação, cargo, jornada e situação estejam sempre atualizados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir visualizar e editar as informações do vínculo: cargo, unidade de lotação, jornada, regime de trabalho e situação
2. WHEN uma informação do vínculo for alterada, THE Sistema SHALL registrar o histórico com data, responsável e motivo da alteração
3. THE Sistema SHALL exibir o histórico completo de alterações do vínculo em ordem cronológica
4. IF o cargo informado não pertencer à estrutura de carreira do órgão, THEN THE Sistema SHALL exibir mensagem de validação e impedir o salvamento

---

### Requisito 39: Pesquisar Servidores

**User Story:** Como usuário de RH, quero pesquisar servidores, para que eu possa localizar rapidamente um servidor e acessar suas informações funcionais.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir pesquisar servidores por: nome, CPF, matrícula, cargo, unidade organizacional e situação do vínculo
2. WHEN o resultado da pesquisa for exibido, THE Sistema SHALL mostrar: nome, matrícula, cargo, unidade e situação
3. WHEN o usuário selecionar um servidor no resultado, THE Sistema SHALL navegar para a tela de informações do vínculo
4. THE Sistema SHALL permitir exportar o resultado da pesquisa em formato PDF ou planilha

---

### Requisito 40: Manter Dependentes

**User Story:** Como usuário de RH ou servidor, quero manter os dados dos dependentes do servidor, para que os benefícios e deduções de IRRF sejam calculados corretamente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar dependentes com: nome, CPF, data de nascimento, grau de parentesco e tipo de dependência (IRRF, previdência)
2. WHEN o usuário informar um CPF de dependente já cadastrado para o mesmo servidor, THE Sistema SHALL exibir mensagem de duplicidade
3. THE Sistema SHALL permitir inativar dependentes com registro da data e motivo do encerramento da dependência
4. THE Sistema SHALL calcular automaticamente a dedução de IRRF com base nos dependentes ativos cadastrados


---

### Requisito 41: Manter Progressão Funcional

**User Story:** Como usuário de RH, quero manter as progressões funcionais dos servidores, para que os avanços na carreira sejam registrados e os efeitos financeiros sejam processados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar progressão com: servidor, data de referência, nível/classe de origem, nível/classe de destino e fundamento legal
2. WHEN a progressão for confirmada, THE Sistema SHALL atualizar o nível/classe do servidor e registrar o histórico
3. THE Sistema SHALL calcular a diferença salarial resultante da progressão e exibir antes da confirmação
4. IF o servidor não atender aos requisitos de tempo mínimo para progressão, THEN THE Sistema SHALL exibir aviso e exigir justificativa para prosseguir

---

### Requisito 42: Manter Transformação de Cargo/Enquadramento

**User Story:** Como usuário de RH, quero registrar transformações de cargo e enquadramentos, para que mudanças na estrutura de carreira sejam refletidas nos vínculos dos servidores.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar transformação/enquadramento com: servidor, cargo de origem, cargo de destino, data e fundamento legal
2. WHEN a transformação for confirmada, THE Sistema SHALL atualizar o cargo do servidor e registrar o histórico
3. THE Sistema SHALL permitir processar transformações em lote para múltiplos servidores simultaneamente
4. WHEN o processamento em lote for concluído, THE Sistema SHALL exibir relatório com os servidores processados e eventuais erros

---

### Requisito 43: Retroativo de Progressão

**User Story:** Como usuário de RH, quero processar retroativos de progressão funcional, para que servidores que tiveram progressão com data retroativa recebam as diferenças salariais devidas.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar progressões com data retroativa para cálculo de diferenças
2. WHEN o usuário solicitar o cálculo retroativo, THE Sistema SHALL calcular as diferenças mês a mês entre o valor pago e o valor devido
3. THE Sistema SHALL exibir o resumo do retroativo por servidor antes de confirmar o processamento
4. WHEN o retroativo for confirmado, THE Sistema SHALL incluir os valores na folha de pagamento da competência corrente

---

### Requisito 44: Manter Frequência Funcional

**User Story:** Como usuário de RH, quero manter os registros de frequência dos servidores, para que as ocorrências mensais sejam registradas e validadas antes do fechamento da folha.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar ocorrências de frequência por servidor e competência: faltas, licenças, afastamentos e horas extras
2. WHEN uma ocorrência for registrada, THE Sistema SHALL validar se o tipo de ocorrência é compatível com a situação do vínculo
3. THE Sistema SHALL permitir consultar o espelho de frequência do servidor por competência
4. IF houver ocorrências conflitantes no mesmo período, THEN THE Sistema SHALL exibir alerta e impedir o salvamento sem resolução do conflito

---

### Requisito 45: Emitir Folha de Frequência

**User Story:** Como usuário de RH, quero emitir a folha de frequência dos servidores, para que o documento oficial de controle de presença seja gerado para assinatura e arquivamento.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir emitir a folha de frequência por unidade organizacional e competência
2. WHEN a folha for gerada, THE Sistema SHALL incluir: nome do servidor, matrícula, cargo, ocorrências do mês e total de dias trabalhados
3. THE Sistema SHALL gerar o documento em formato PDF com campo para assinatura
4. THE Sistema SHALL permitir emitir a folha para múltiplos servidores em lote

---

### Requisito 46: Manter Jornada de Trabalho

**User Story:** Como administrador, quero manter as jornadas de trabalho, para que os horários e cargas horárias dos servidores sejam definidos e utilizados no controle de frequência.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar jornadas com: descrição, carga horária semanal, horários de entrada e saída por dia da semana e tolerância de marcação
2. WHEN uma jornada for vinculada a um servidor, THE Sistema SHALL utilizar os horários definidos para validar os registros de ponto
3. THE Sistema SHALL permitir definir jornadas especiais para períodos específicos
4. WHEN uma jornada for alterada, THE Sistema SHALL registrar o histórico com data e responsável


---

### Requisito 47: Manter Eventos de Capacitação

**User Story:** Como usuário de RH, quero manter os eventos de capacitação, para que os treinamentos e cursos oferecidos aos servidores sejam registrados e gerenciados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar eventos com: nome, tipo, modalidade, carga horária, período, local e vagas disponíveis
2. WHEN o número de inscritos atingir o limite de vagas, THE Sistema SHALL impedir novas inscrições e exibir mensagem informativa
3. THE Sistema SHALL permitir pesquisar eventos por nome, tipo, período e situação
4. WHEN um evento for cancelado, THE Sistema SHALL notificar os participantes inscritos

---

### Requisito 48: Manter Participantes de Capacitação

**User Story:** Como usuário de RH, quero manter os participantes dos eventos de capacitação, para que a frequência e o aproveitamento dos servidores nos treinamentos sejam registrados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir inscrever servidores em eventos de capacitação com verificação de disponibilidade de vagas
2. WHEN a participação for confirmada, THE Sistema SHALL registrar a frequência e o resultado (aprovado/reprovado/desistente)
3. THE Sistema SHALL permitir registrar a nota ou conceito obtido pelo servidor no evento
4. IF o servidor não atingir a frequência mínima exigida, THEN THE Sistema SHALL registrar a situação como "Reprovado por Frequência"

---

### Requisito 49: Emitir Certificados de Capacitação

**User Story:** Como usuário de RH, quero emitir certificados de participação em eventos de capacitação, para que os servidores tenham comprovação formal de sua qualificação.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir emitir certificados para servidores com situação "Aprovado" no evento
2. WHEN o certificado for emitido, THE Sistema SHALL incluir: nome do servidor, nome do evento, carga horária, período e assinatura do responsável
3. THE Sistema SHALL gerar o certificado em formato PDF
4. THE Sistema SHALL registrar a data de emissão do certificado e impedir reemissão sem justificativa

---

### Requisito 50: Manter Formação Acadêmica

**User Story:** Como servidor ou usuário de RH, quero manter os dados de formação acadêmica do servidor, para que o currículo funcional esteja atualizado e sirva de base para progressões e benefícios.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar formações com: nível de escolaridade, curso, instituição, data de conclusão e situação (concluído/em andamento)
2. THE Sistema SHALL permitir anexar documentos comprobatórios da formação
3. WHEN uma formação for cadastrada, THE Sistema SHALL verificar se ela gera direito a adicional de qualificação e notificar o usuário de RH
4. THE Sistema SHALL permitir pesquisar servidores por nível de escolaridade e área de formação

---

### Requisito 51: Manter Experiência Profissional

**User Story:** Como servidor ou usuário de RH, quero manter os dados de experiência profissional do servidor, para que o histórico de atuação anterior ao ingresso no serviço público esteja registrado.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar experiências com: empresa/órgão, cargo, período (início e fim) e descrição das atividades
2. THE Sistema SHALL permitir anexar documentos comprobatórios da experiência
3. THE Sistema SHALL permitir pesquisar servidores por área de experiência e período de atuação

---

### Requisito 52: Manter Afastamentos

**User Story:** Como usuário de RH, quero manter os afastamentos dos servidores, para que as ausências temporárias e definitivas sejam registradas com os devidos efeitos funcionais e financeiros.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar afastamentos com: servidor, motivo, data de início, data prevista de retorno e fundamento legal
2. WHEN um afastamento for registrado, THE Sistema SHALL verificar conflito com outros afastamentos no mesmo período e exibir alerta
3. WHEN o servidor retornar do afastamento, THE Sistema SHALL registrar a data efetiva de retorno e encerrar o afastamento
4. THE Sistema SHALL calcular automaticamente os efeitos financeiros do afastamento conforme o motivo cadastrado
5. THE Sistema SHALL permitir pesquisar afastamentos por servidor, motivo, período e situação

---

### Requisito 53: Manter Motivos de Afastamento

**User Story:** Como administrador, quero manter os motivos de afastamento, para que os tipos de ausência sejam padronizados e seus efeitos financeiros e funcionais sejam configurados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar motivos de afastamento com: descrição, tipo (temporário/definitivo), efeitos financeiros e fundamento legal
2. THE Sistema SHALL permitir configurar se o motivo gera desconto em folha, suspensão de benefícios ou outros efeitos
3. WHEN um motivo for inativado, THE Sistema SHALL impedir seu uso em novos afastamentos sem excluir os históricos


---

### Requisito 54: Manter Férias

**User Story:** Como usuário de RH, quero manter as férias dos servidores, para que os períodos aquisitivos e de gozo sejam controlados e os pagamentos sejam processados corretamente.

#### Critérios de Aceitação

1. THE Sistema SHALL calcular automaticamente o período aquisitivo de férias com base na data de ingresso do servidor
2. THE Sistema SHALL permitir programar o período de gozo de férias com: data de início, duração e opção de abono pecuniário
3. WHEN o período de gozo for confirmado, THE Sistema SHALL incluir o pagamento de férias na folha da competência correspondente
4. IF o servidor não usufruir das férias dentro do prazo legal, THEN THE Sistema SHALL sinalizar o período como "Férias Vencidas"
5. THE Sistema SHALL permitir parcelar as férias conforme legislação vigente

---

### Requisito 55: Emitir Aviso de Férias

**User Story:** Como usuário de RH, quero emitir o aviso de férias, para que o servidor seja notificado formalmente sobre o período de gozo programado.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir emitir o aviso de férias com antecedência mínima de 30 dias do início do período de gozo
2. WHEN o aviso for emitido, THE Sistema SHALL incluir: nome do servidor, período de gozo, data de retorno e assinatura do responsável
3. THE Sistema SHALL gerar o aviso em formato PDF
4. IF o aviso for emitido com menos de 30 dias de antecedência, THEN THE Sistema SHALL exibir alerta de prazo legal

---

### Requisito 56: Manter Movimentação Dentro do Agrupamento

**User Story:** Como usuário de RH, quero manter as movimentações de servidores dentro do agrupamento, para que as transferências entre unidades e órgãos sejam formalizadas e registradas.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar movimentações com: servidor, unidade de origem, unidade de destino, data, instituto e motivo
2. WHEN a movimentação for confirmada, THE Sistema SHALL atualizar a lotação do servidor e registrar o histórico
3. THE Sistema SHALL verificar se a unidade de destino possui vaga disponível no quadro lotacional antes de confirmar
4. WHEN a movimentação for registrada, THE Sistema SHALL gerar o ato oficial correspondente automaticamente

---

### Requisito 57: Gerar Movimentações em Lote

**User Story:** Como usuário de RH, quero gerar movimentações em lote, para que múltiplos servidores sejam transferidos simultaneamente de forma eficiente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar múltiplos servidores para movimentação em lote com os mesmos parâmetros de destino
2. WHEN o processamento em lote for iniciado, THE Sistema SHALL validar cada servidor individualmente e listar os que não puderem ser movimentados com o motivo
3. WHEN o lote for processado, THE Sistema SHALL exibir relatório com servidores movimentados com sucesso e os com erro
4. THE Sistema SHALL permitir confirmar o lote parcialmente, excluindo os registros com erro

---

### Requisito 58: Receber Servidor à Disposição

**User Story:** Como usuário de RH, quero registrar o recebimento de servidor à disposição de outro órgão, para que o servidor cedido seja incorporado à estrutura do órgão receptor com os devidos registros.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar o recebimento com: servidor, órgão de origem, unidade de destino, data de início e prazo de disposição
2. WHEN o recebimento for confirmado, THE Sistema SHALL criar o vínculo de disposição e atualizar a lotação do servidor
3. THE Sistema SHALL registrar o ônus financeiro da disposição (cedente ou cessionário)
4. WHEN o prazo de disposição vencer, THE Sistema SHALL notificar o usuário de RH responsável

---

### Requisito 59: Manter Tipos de Gratificação

**User Story:** Como administrador, quero manter os tipos de gratificação, para que as vantagens pecuniárias dos servidores sejam configuradas com suas regras e fundamentos legais.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar tipos de gratificação com: nome, código, base de cálculo, percentual ou valor fixo e fundamento legal
2. THE Sistema SHALL permitir configurar as condições de concessão e cessação de cada tipo de gratificação
3. WHEN um tipo de gratificação for inativado, THE Sistema SHALL verificar servidores com concessão ativa e exibir aviso

---

### Requisito 60: Manter Valores de Gratificação

**User Story:** Como administrador, quero manter os valores das gratificações, para que os percentuais e valores sejam atualizados conforme legislação vigente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar tabelas de valores por tipo de gratificação com vigência definida
2. WHEN uma nova tabela de valores for cadastrada, THE Sistema SHALL encerrar automaticamente a vigência da tabela anterior
3. THE Sistema SHALL manter o histórico de todas as tabelas de valores com suas respectivas vigências


---

### Requisito 61: Recalcular Períodos de ATS

**User Story:** Como usuário de RH, quero recalcular os períodos de Adicional por Tempo de Serviço, para que o tempo de serviço dos servidores seja corretamente apurado e os valores pagos sejam precisos.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar servidores para recálculo de períodos de ATS individualmente ou em lote
2. WHEN o recálculo for processado, THE Sistema SHALL considerar todos os afastamentos e interrupções que afetam a contagem de tempo
3. THE Sistema SHALL exibir o resultado do recálculo com o tempo apurado antes e depois do recálculo
4. WHEN o recálculo resultar em diferença de valor, THE Sistema SHALL calcular o retroativo correspondente e incluir na folha

---

### Requisito 62: Manter Pagamento de Decisão Judicial

**User Story:** Como usuário de RH, quero manter os pagamentos decorrentes de decisões judiciais, para que os valores determinados pela justiça sejam processados corretamente na folha de pagamento.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar pagamentos judiciais com: servidor, número do processo, tipo de verba, valor, competência e fundamento
2. WHEN o pagamento for registrado, THE Sistema SHALL incluir o valor na folha da competência informada
3. THE Sistema SHALL manter o histórico de todos os pagamentos judiciais por servidor e processo
4. THE Sistema SHALL permitir registrar parcelamentos com controle de parcelas pagas e a pagar

---

### Requisito 63: Manter Solicitação de Diárias

**User Story:** Como servidor ou usuário de RH, quero manter as solicitações de diárias, para que os valores de deslocamento a serviço sejam solicitados, aprovados e pagos corretamente.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar solicitações de diárias com: servidor, destino, período, objetivo e valor calculado
2. WHEN a solicitação for enviada, THE Sistema SHALL calcular o valor das diárias com base na tabela vigente e no cargo do servidor
3. WHEN a solicitação for aprovada, THE Sistema SHALL incluir o valor na folha ou gerar ordem de pagamento conforme configuração
4. IF o servidor possuir diárias anteriores não prestadas, THEN THE Sistema SHALL exibir aviso antes de permitir nova solicitação

---

### Requisito 64: Manter Pedido de Vale Transporte

**User Story:** Como servidor ou usuário de RH, quero manter os pedidos de vale transporte, para que o benefício seja concedido conforme as linhas de transporte utilizadas pelo servidor.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar pedidos de vale transporte com: servidor, linhas de transporte, valor unitário e quantidade mensal
2. WHEN o pedido for aprovado, THE Sistema SHALL calcular o desconto de 6% sobre o salário base e incluir o benefício na folha
3. THE Sistema SHALL permitir atualizar as linhas de transporte quando houver mudança de endereço ou trajeto
4. WHEN o servidor entrar em afastamento, THE Sistema SHALL suspender automaticamente o benefício conforme o tipo de afastamento

---

### Requisito 65: Manter Concessão de Auxílio Creche

**User Story:** Como usuário de RH, quero manter as concessões de auxílio creche, para que o benefício seja concedido aos servidores com filhos em idade elegível.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar concessões com: servidor, dependente beneficiário, data de início e documentação comprobatória
2. THE Sistema SHALL verificar automaticamente a elegibilidade do dependente com base na idade e no tipo de dependência cadastrado
3. WHEN o dependente atingir a idade limite, THE Sistema SHALL encerrar automaticamente a concessão e notificar o usuário de RH
4. THE Sistema SHALL incluir o valor do auxílio na folha de pagamento do servidor enquanto a concessão estiver ativa

---

### Requisito 66: Manter Cronograma da Folha de Pagamento

**User Story:** Como administrador, quero manter o cronograma da folha de pagamento, para que as datas de processamento e pagamento sejam planejadas e comunicadas com antecedência.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar o cronograma com: competência, data de fechamento, data de processamento e data de pagamento
2. WHEN a data de fechamento for atingida, THE Sistema SHALL bloquear novos lançamentos na folha da competência correspondente
3. THE Sistema SHALL notificar os usuários de RH sobre as datas do cronograma com antecedência configurável
4. THE Sistema SHALL permitir consultar o cronograma de todas as competências do exercício


---

### Requisito 67: Processar Folha de Pagamento

**User Story:** Como usuário de RH, quero processar a folha de pagamento, para que os vencimentos, descontos e líquidos de todos os servidores sejam calculados para a competência.

#### Critérios de Aceitação

1. WHEN o usuário iniciar o processamento da folha, THE Sistema SHALL calcular vencimentos, descontos e líquido para todos os servidores ativos na competência
2. WHEN o processamento for concluído, THE Sistema SHALL exibir resumo com total de servidores processados, total de vencimentos e total de descontos
3. IF houver erros durante o processamento, THEN THE Sistema SHALL listar os servidores com erro e o motivo para correção
4. THE Sistema SHALL permitir reprocessar a folha após correções sem perder o histórico do processamento anterior
5. WHEN a folha for fechada, THE Sistema SHALL impedir novos processamentos para a competência sem autorização de reabertura

---

### Requisito 68: Emitir Contracheque

**User Story:** Como servidor ou usuário de RH, quero emitir o contracheque, para que o demonstrativo de remuneração mensal esteja disponível para consulta e impressão.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir ao servidor consultar e emitir seu próprio contracheque por competência
2. WHEN o contracheque for emitido, THE Sistema SHALL incluir: vencimentos, descontos, líquido, dados do servidor e competência
3. THE Sistema SHALL gerar o contracheque em formato PDF
4. THE Sistema SHALL manter o histórico de contracheques emitidos por servidor e competência
5. WHERE o servidor possuir múltiplos vínculos, THE Sistema SHALL emitir contracheque separado por vínculo

---

### Requisito 69: Emitir Ficha Financeira

**User Story:** Como usuário de RH, quero emitir a ficha financeira do servidor, para que o histórico completo de pagamentos seja disponibilizado para fins de auditoria e comprovação de renda.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir emitir a ficha financeira por servidor e período (mês/ano inicial e final)
2. WHEN a ficha for emitida, THE Sistema SHALL incluir todos os vencimentos e descontos mês a mês no período selecionado
3. THE Sistema SHALL gerar a ficha em formato PDF com identificação do servidor
4. THE Sistema SHALL permitir emitir fichas financeiras em lote por unidade organizacional

---

### Requisito 70: Manter Laudo de Perícia Médica

**User Story:** Como usuário de saúde do servidor, quero manter os laudos de perícia médica, para que os afastamentos por motivo de saúde sejam fundamentados em avaliação médica oficial.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar laudos com: servidor, médico perito, data da perícia, CID, diagnóstico, prazo de afastamento e resultado
2. WHEN um laudo for registrado com resultado de afastamento, THE Sistema SHALL criar automaticamente o afastamento correspondente
3. THE Sistema SHALL manter o histórico de laudos por servidor em ordem cronológica
4. THE Sistema SHALL permitir pesquisar laudos por servidor, CID, período e resultado

---

### Requisito 71: Registrar Comunicação de Acidente

**User Story:** Como usuário de RH, quero registrar comunicações de acidente de trabalho, para que os acidentes sofridos pelos servidores sejam documentados e os direitos correspondentes sejam assegurados.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar a comunicação com: servidor, data do acidente, local, descrição, testemunhas e consequências
2. WHEN a comunicação for registrada, THE Sistema SHALL gerar o número do CAT automaticamente
3. THE Sistema SHALL permitir anexar documentos e laudos médicos à comunicação
4. THE Sistema SHALL notificar o setor de saúde do servidor sobre o registro do acidente

---

### Requisito 72: Manter Certidão de Averbação

**User Story:** Como usuário de RH, quero manter as certidões de averbação de tempo de serviço, para que o tempo trabalhado em outros órgãos seja reconhecido e contabilizado para fins previdenciários.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir registrar certidões com: servidor, órgão de origem, período averbado, tipo de tempo e documentação
2. WHEN a certidão for registrada, THE Sistema SHALL incluir o período averbado no cálculo do tempo total de serviço do servidor
3. THE Sistema SHALL manter o histórico de todas as certidões por servidor
4. IF o período informado conflitar com outro período já registrado, THEN THE Sistema SHALL exibir alerta e impedir o salvamento sem resolução

---

### Requisito 73: Recadastrar Aposentados

**User Story:** Como usuário de RH, quero recadastrar os aposentados, para que a prova de vida dos servidores inativos seja realizada periodicamente e os pagamentos sejam mantidos apenas para beneficiários ativos.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir abrir período de recadastramento de aposentados com data de início e encerramento
2. WHEN o aposentado realizar o recadastramento, THE Sistema SHALL registrar a data e confirmar a situação como "Recadastrado"
3. IF o aposentado não realizar o recadastramento dentro do prazo, THEN THE Sistema SHALL suspender o pagamento e registrar a situação como "Suspenso por Falta de Recadastramento"
4. THE Sistema SHALL permitir reativar o pagamento após a regularização do recadastramento


---

### Requisito 74: Informações Funcionais — Cubo de Dados

**User Story:** Como gestor ou administrador, quero consultar informações funcionais por meio de cubo de dados, para que análises gerenciais sobre o quadro de pessoal sejam realizadas de forma dinâmica.

#### Critérios de Aceitação

1. THE Sistema SHALL disponibilizar cubo de dados com dimensões: órgão, unidade, cargo, situação do vínculo, faixa etária e escolaridade
2. WHEN o usuário selecionar as dimensões e métricas desejadas, THE Sistema SHALL calcular e exibir os resultados em formato tabular e gráfico
3. THE Sistema SHALL permitir exportar os resultados do cubo em formato de planilha
4. THE Sistema SHALL atualizar os dados do cubo com base na última competência processada

---

### Requisito 75: Emitir Estatísticas Gerenciais

**User Story:** Como gestor, quero emitir relatórios de estatísticas do quadro de pessoal, para que informações consolidadas sobre servidores, cargos e custos estejam disponíveis para tomada de decisão.

#### Critérios de Aceitação

1. THE Sistema SHALL disponibilizar relatórios de estatísticas com: total de servidores por órgão, por cargo, por faixa etária e por escolaridade
2. THE Sistema SHALL permitir filtrar os relatórios por órgão, unidade, período e situação do vínculo
3. WHEN o relatório for gerado, THE Sistema SHALL exibir gráficos e tabelas com os dados consolidados
4. THE Sistema SHALL permitir exportar os relatórios em formato PDF e planilha

---

### Requisito 76: Manter Acesso de Usuários

**User Story:** Como administrador, quero manter os acessos dos usuários ao sistema, para que apenas pessoas autorizadas possam utilizar as funcionalidades conforme seus perfis.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir criar, editar e inativar usuários com: nome, CPF, login, e-mail e perfis de acesso
2. WHEN um usuário for criado, THE Sistema SHALL enviar credenciais de acesso para o e-mail cadastrado
3. WHEN um usuário for inativado, THE Sistema SHALL encerrar imediatamente todas as sessões ativas do usuário
4. THE Sistema SHALL registrar o histórico de alterações de acesso com data e responsável
5. IF um usuário tentar acessar uma funcionalidade sem permissão, THEN THE Sistema SHALL exibir mensagem de acesso negado e registrar a tentativa no log de auditoria

---

### Requisito 77: Manter Perfis de Acesso

**User Story:** Como administrador, quero manter os perfis de acesso, para que as permissões sejam agrupadas por função e atribuídas aos usuários de forma padronizada.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir criar perfis com: nome, descrição e conjunto de permissões por funcionalidade
2. THE Sistema SHALL permitir atribuir múltiplos perfis a um mesmo usuário
3. WHEN um perfil for alterado, THE Sistema SHALL aplicar as novas permissões imediatamente a todos os usuários que possuem o perfil
4. THE Sistema SHALL impedir a exclusão de perfis com usuários ativos vinculados

---

### Requisito 78: Manter Permissões de Funcionalidades

**User Story:** Como administrador, quero manter as permissões das funcionalidades do sistema, para que o controle de acesso seja granular e configurável por perfil.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir configurar permissões por funcionalidade: visualizar, incluir, editar e excluir
2. THE Sistema SHALL exibir a matriz de permissões por perfil e funcionalidade para facilitar a gestão
3. WHEN uma permissão for alterada, THE Sistema SHALL registrar a alteração no log de auditoria com data e responsável
4. THE Sistema SHALL impedir a remoção de permissões essenciais de perfis de administrador do sistema

---

### Requisito 79: Enviar Eventos eSocial

**User Story:** Como usuário de RH, quero enviar eventos ao eSocial, para que as obrigações trabalhistas e previdenciárias sejam cumpridas dentro dos prazos legais.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir selecionar e enviar eventos eSocial por tipo e competência
2. WHEN o envio for realizado, THE Sistema SHALL registrar o protocolo de envio, data/hora e situação do retorno
3. IF o evento for rejeitado pelo eSocial, THEN THE Sistema SHALL exibir o código e a descrição do erro retornado e permitir a correção e reenvio
4. THE Sistema SHALL manter o histórico de todos os envios com seus respectivos retornos

---

### Requisito 80: Evento eSocial S-2200 — Cadastramento Inicial do Vínculo

**User Story:** Como usuário de RH, quero gerar e enviar o evento S-2200 ao eSocial, para que o cadastramento inicial do vínculo empregatício seja comunicado ao governo federal.

#### Critérios de Aceitação

1. WHEN um novo vínculo for criado no sistema, THE Sistema SHALL gerar automaticamente o evento S-2200 com os dados do servidor e do vínculo
2. THE Sistema SHALL validar os dados do evento S-2200 conforme o leiaute definido pelo eSocial antes do envio
3. IF campos obrigatórios do S-2200 estiverem ausentes ou inválidos, THEN THE Sistema SHALL listar as inconsistências e impedir o envio
4. WHEN o S-2200 for aceito pelo eSocial, THE Sistema SHALL registrar o recibo de entrega e atualizar a situação do evento para "Aceito"

---

### Requisito 81: Evento eSocial S-1200 — Remuneração do Trabalhador

**User Story:** Como usuário de RH, quero gerar e enviar o evento S-1200 ao eSocial, para que as remunerações mensais dos servidores sejam comunicadas ao governo federal.

#### Critérios de Aceitação

1. WHEN a folha de pagamento for fechada, THE Sistema SHALL gerar automaticamente os eventos S-1200 para todos os servidores da competência
2. THE Sistema SHALL validar os dados do evento S-1200 conforme o leiaute definido pelo eSocial antes do envio
3. IF o evento S-1200 for gerado para um servidor sem S-2200 aceito, THEN THE Sistema SHALL bloquear o envio e exibir aviso de dependência
4. WHEN o S-1200 for aceito pelo eSocial, THE Sistema SHALL registrar o recibo de entrega e atualizar a situação do evento para "Aceito"

---

### Requisito 82: Agendar Rotinas do Sistema

**User Story:** Como administrador, quero agendar rotinas automáticas do sistema, para que processos periódicos sejam executados sem intervenção manual.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir agendar rotinas com: nome, descrição, frequência (diária, semanal, mensal), horário de execução e parâmetros
2. WHEN uma rotina agendada for executada, THE Sistema SHALL registrar data/hora de início, fim e resultado (sucesso/erro)
3. IF uma rotina agendada falhar, THEN THE Sistema SHALL notificar o administrador com o log de erro
4. THE Sistema SHALL permitir executar uma rotina agendada manualmente fora do horário programado

---

### Requisito 83: Manter Parâmetros do Sistema

**User Story:** Como administrador, quero manter os parâmetros de configuração do sistema, para que o comportamento do SIGRH seja ajustado conforme as necessidades do órgão.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir configurar parâmetros por módulo: valores padrão, limites, prazos e integrações
2. WHEN um parâmetro for alterado, THE Sistema SHALL registrar a alteração no log de auditoria com data, responsável e valor anterior
3. THE Sistema SHALL exibir descrição e impacto de cada parâmetro antes de permitir a edição
4. IF um parâmetro crítico for alterado, THEN THE Sistema SHALL exigir confirmação explícita do administrador antes de salvar

---

### Requisito 84: Manter Procedimentos Administrativos

**User Story:** Como administrador, quero manter os procedimentos administrativos no módulo de ajuda, para que os usuários tenham acesso a orientações sobre os processos de RH diretamente no sistema.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar procedimentos administrativos com: título, categoria, conteúdo em texto rico e situação
2. THE Sistema SHALL permitir organizar os procedimentos em categorias e subcategorias
3. WHEN o usuário pesquisar por palavra-chave, THE Sistema SHALL retornar os procedimentos que contenham o termo no título ou no conteúdo
4. THE Sistema SHALL permitir anexar arquivos e links externos aos procedimentos

---

### Requisito 85: Manter Procedimentos Computacionais

**User Story:** Como administrador, quero manter os procedimentos computacionais no módulo de ajuda, para que os usuários tenham orientações técnicas sobre o uso das funcionalidades do sistema.

#### Critérios de Aceitação

1. THE Sistema SHALL permitir cadastrar procedimentos computacionais com: título, funcionalidade relacionada, passo a passo e capturas de tela
2. THE Sistema SHALL exibir o procedimento computacional relacionado à funcionalidade que o usuário está utilizando quando disponível
3. WHEN o usuário pesquisar por funcionalidade ou palavra-chave, THE Sistema SHALL retornar os procedimentos correspondentes
4. THE Sistema SHALL permitir avaliar a utilidade do procedimento com feedback de "útil" ou "não útil"
