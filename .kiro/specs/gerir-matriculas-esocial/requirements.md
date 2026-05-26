# Requirements Document

## Introduction

A funcionalidade **Gerir Matrículas eSocial** permite que o Núcleo eSocial (perfil NUGESP) cadastre, altere e finalize regras de matrícula eSocial por período de vigência. O objetivo é substituir regras hardcoded em SQL por uma camada de parametrização que define, para um determinado vínculo de servidor, qual matrícula (fictícia ou original), qual categoria eSocial e quais datas devem ser utilizadas no envio de eventos ao eSocial. A funcionalidade não altera cadastros reais de pessoa ou vínculo funcional — atua exclusivamente como configuração de envio ao eSocial.

O sistema está implementado como protótipo single-file HTML (index.html) com vanilla JS/CSS, seguindo os padrões visuais e de interação já estabelecidos no SIGrh.

---

## Glossary

- **Matrícula_eSocial**: identificador fictício ou ajustado utilizado no envio de eventos ao eSocial, podendo diferir da matrícula original do servidor.
- **Matrícula_Original**: matrícula funcional real do servidor no SIGrh (formato `XXXXXXX-X-XX`).
- **Vínculo_Original**: código numérico que identifica o vínculo funcional do servidor no SIGrh.
- **Regra_de_Matrícula**: registro que associa um vínculo original a um tipo de tratamento eSocial, com período de vigência definido.
- **Vigência**: intervalo de datas (início obrigatório, fim opcional) durante o qual a Regra_de_Matrícula está ativa.
- **Tipo_de_Tratamento**: define como o vínculo será enviado ao eSocial — `Considerar matrícula fictícia` ou `Considerar matrícula original com datas ajustadas`.
- **Categoria_eSocial**: código numérico da categoria do trabalhador conforme tabela do eSocial (ex.: 410).
- **NUGESP**: Núcleo eSocial — perfil de usuário com acesso exclusivo a esta funcionalidade.
- **Forçar_Consideração**: flag que, quando ativada, inclui o vínculo no envio ao eSocial mesmo que ele não atenda às regras padrão de elegibilidade.
- **Situação**: estado atual da Regra_de_Matrícula — `Ativa`, `Vigência finalizada` ou `Inativa`.
- **Sistema**: o módulo Gerir Matrículas eSocial do SIGrh.
- **Gestor_eSocial**: usuário autenticado com perfil NUGESP que opera o Sistema.

---

## Requirements

### Requirement 1: Controle de Acesso

**User Story:** Como administrador do sistema, quero que apenas usuários com perfil NUGESP acessem a funcionalidade, para que dados sensíveis de parametrização eSocial sejam protegidos.

#### Acceptance Criteria

1. WHILE o usuário autenticado não possui o perfil NUGESP, THE Sistema SHALL ocultar o item de menu "Gerir Matrículas eSocial" do caminho eSocial > Geração/Transmissão.
2. IF um usuário sem perfil NUGESP tentar acessar diretamente a funcionalidade, THEN THE Sistema SHALL exibir mensagem de acesso negado e redirecionar para a página inicial.

---

### Requirement 2: Listagem de Regras de Matrícula

**User Story:** Como Gestor_eSocial, quero visualizar todas as Regras_de_Matrícula cadastradas em formato de grade, para que eu possa consultar e gerenciar os registros existentes.

#### Acceptance Criteria

1. THE Sistema SHALL exibir uma grade com as colunas: Nome do servidor, CPF, Vínculo original, Matrícula original, Matrícula eSocial/fictícia, Tipo de tratamento, Categoria eSocial, Data início da vigência, Data fim da vigência, Situação, Usuário de cadastro, Data de cadastro, Usuário de alteração, Data de alteração.
2. THE Sistema SHALL exibir os registros paginados, com controles de navegação entre páginas.
3. WHEN o Gestor_eSocial clica em uma linha da grade, THE Sistema SHALL selecionar o registro e habilitar as ações disponíveis para ele.

---

### Requirement 3: Filtros da Listagem

**User Story:** Como Gestor_eSocial, quero filtrar a listagem por múltiplos critérios, para que eu localize rapidamente as Regras_de_Matrícula relevantes.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar os seguintes campos de filtro: Nome do servidor, CPF, Vínculo original, Matrícula original, Matrícula eSocial/fictícia, Categoria eSocial, Tipo de tratamento, Situação, Data início da vigência, Data fim da vigência.
2. WHEN o Gestor_eSocial aciona o filtro, THE Sistema SHALL retornar apenas os registros cuja Data_fim_da_vigência seja maior ou igual à data inicial do filtro de período E cuja Data_início_da_vigência seja menor ou igual à data final do filtro de período.
3. WHEN o Gestor_eSocial aciona o filtro sem preencher nenhum campo, THE Sistema SHALL retornar todos os registros cadastrados.
4. WHEN o Gestor_eSocial aciona o botão de limpar filtros, THE Sistema SHALL restaurar todos os campos de filtro para o estado vazio e reexibir todos os registros.

---

### Requirement 4: Cadastro de Nova Regra de Matrícula

**User Story:** Como Gestor_eSocial, quero cadastrar uma nova Regra_de_Matrícula para um vínculo de servidor, para que o envio ao eSocial utilize os dados parametrizados no lugar dos dados originais.

#### Acceptance Criteria

1. THE Sistema SHALL disponibilizar um formulário de cadastro com os campos: CPF, Servidor (nome, preenchido automaticamente após CPF), Vínculo original, Matrícula original (preenchida automaticamente após seleção do vínculo), Tipo de tratamento, Matrícula eSocial/fictícia, Categoria eSocial, Data início da vigência, Data fim da vigência, Data início eSocial ajustada, Data fim eSocial ajustada, Forçar consideração no eSocial, Justificativa da criação, Situação.
2. WHEN o Gestor_eSocial seleciona o Tipo_de_Tratamento "Considerar matrícula fictícia", THE Sistema SHALL tornar o campo Matrícula eSocial/fictícia obrigatório.
3. WHEN o Gestor_eSocial seleciona o Tipo_de_Tratamento "Considerar matrícula original com datas ajustadas", THE Sistema SHALL tornar o campo Matrícula eSocial/fictícia não obrigatório e desabilitá-lo.
4. IF o Gestor_eSocial tentar salvar sem preencher os campos obrigatórios (CPF, Vínculo original, Matrícula original, Tipo de tratamento, Data início da vigência, Categoria eSocial, Justificativa da criação), THEN THE Sistema SHALL exibir mensagem de validação indicando os campos pendentes e não salvar o registro.
5. IF o Gestor_eSocial tentar salvar uma Regra_de_Matrícula cujo CPF + Vínculo_Original já possui outra Regra_de_Matrícula ativa com período sobreposto, THEN THE Sistema SHALL exibir mensagem de conflito de vigência e não salvar o registro.
6. WHEN o Gestor_eSocial salva um cadastro válido, THE Sistema SHALL registrar automaticamente o usuário autenticado, a data e hora do cadastro, e definir a Situação como "Ativa".
7. THE Sistema SHALL permitir que o campo Data fim da vigência seja deixado em aberto no cadastro inicial.

---

### Requirement 5: Alteração de Regra de Matrícula

**User Story:** Como Gestor_eSocial, quero alterar os dados principais de uma Regra_de_Matrícula existente, para que eu corrija informações cadastradas incorretamente.

#### Acceptance Criteria

1. WHEN o Gestor_eSocial seleciona um registro e aciona "Alterar", THE Sistema SHALL exibir o formulário de edição com os dados atuais do registro.
2. THE Sistema SHALL permitir a edição dos campos: Tipo de tratamento, Matrícula eSocial/fictícia, Categoria eSocial, Data início da vigência, Data fim da vigência, Data início eSocial ajustada, Data fim eSocial ajustada, Forçar consideração no eSocial, Situação.
3. IF o Gestor_eSocial tentar salvar a alteração sem preencher os campos obrigatórios, THEN THE Sistema SHALL exibir mensagem de validação e não salvar.
4. WHEN o Gestor_eSocial salva uma alteração válida, THE Sistema SHALL registrar automaticamente o usuário autenticado e a data e hora da alteração.

---

### Requirement 6: Alterar Matrícula eSocial (menu + Opções)

**User Story:** Como Gestor_eSocial, quero alterar especificamente os dados de matrícula e datas eSocial de um registro, para que eu ajuste parametrizações sem precisar recriar a regra.

#### Acceptance Criteria

1. WHEN o Gestor_eSocial aciona "Alterar Matrícula eSocial" no menu "+ Opções" com um registro selecionado, THE Sistema SHALL exibir um formulário modal ou seção com os campos: Matrícula eSocial/fictícia, Categoria eSocial, Data início da vigência, Data fim da vigência, Data início eSocial ajustada, Data fim eSocial ajustada, Forçar consideração no eSocial, Justificativa da alteração.
2. THE Sistema SHALL exigir o preenchimento do campo Justificativa da alteração para salvar.
3. WHEN o Gestor_eSocial salva a alteração válida, THE Sistema SHALL registrar automaticamente o usuário autenticado e a data e hora da operação.

---

### Requirement 7: Finalizar Vigência (menu + Opções)

**User Story:** Como Gestor_eSocial, quero finalizar a vigência de uma Regra_de_Matrícula ativa, para que o vínculo deixe de utilizar a matrícula fictícia a partir da data informada.

#### Acceptance Criteria

1. WHEN o Gestor_eSocial aciona "Finalizar Vigência" no menu "+ Opções" com um registro selecionado, THE Sistema SHALL exibir um formulário com os campos: Data fim da vigência e Justificativa da finalização.
2. THE Sistema SHALL exigir o preenchimento de ambos os campos (Data fim da vigência e Justificativa da finalização) para confirmar a operação.
3. WHEN o Gestor_eSocial confirma a finalização com dados válidos, THE Sistema SHALL gravar a Data fim da vigência, a Justificativa da finalização, o usuário autenticado, a data e hora da operação, e alterar a Situação do registro para "Vigência finalizada".
4. WHEN a data atual ultrapassa a Data_fim_da_vigência de uma Regra_de_Matrícula com Situação "Vigência finalizada", THE Sistema SHALL não aplicar a matrícula fictícia para novos eventos eSocial desse vínculo.

---

### Requirement 8: Impressão

**User Story:** Como Gestor_eSocial, quero imprimir a listagem de Regras_de_Matrícula, para que eu tenha um relatório físico ou em PDF dos registros.

#### Acceptance Criteria

1. WHEN o Gestor_eSocial aciona o botão "Imprimir", THE Sistema SHALL exibir um modal de configuração de impressão com opções de título do relatório, disposição (retrato/paisagem) e formato (PDF/Excel/HTML).
2. WHEN o Gestor_eSocial confirma a impressão, THE Sistema SHALL gerar o relatório com os registros atualmente exibidos na listagem (respeitando os filtros aplicados).

---

### Requirement 9: Aplicação das Regras no Envio ao eSocial

**User Story:** Como Gestor_eSocial, quero que as Regras_de_Matrícula ativas sejam aplicadas automaticamente na geração de eventos eSocial, para que os dados enviados ao governo reflitam as parametrizações cadastradas.

#### Acceptance Criteria

1. WHEN o Sistema processa um vínculo para envio ao eSocial e existe uma Regra_de_Matrícula ativa com Tipo_de_Tratamento "Considerar matrícula fictícia" para o período em questão, THE Sistema SHALL utilizar a Matrícula_eSocial no lugar da Matrícula_Original no evento gerado.
2. WHEN o Sistema processa um vínculo para envio ao eSocial e existe uma Regra_de_Matrícula ativa com Tipo_de_Tratamento "Considerar matrícula original com datas ajustadas" para o período em questão, THE Sistema SHALL manter a Matrícula_Original e aplicar as datas ajustadas (Data início eSocial ajustada e Data fim eSocial ajustada) no evento gerado.
3. WHEN o Sistema processa um vínculo para envio ao eSocial e não existe Regra_de_Matrícula ativa para o período em questão, THE Sistema SHALL aplicar o comportamento padrão do Vínculo_Original.
4. WHEN o campo Forçar_Consideração está marcado em uma Regra_de_Matrícula ativa, THE Sistema SHALL incluir o vínculo no envio ao eSocial mesmo que ele não atenda às regras padrão de elegibilidade.
5. THE Sistema SHALL determinar a interseção de vigência pela regra: Data_fim_da_vigência >= data_inicial_do_período_consultado E Data_início_da_vigência <= data_final_do_período_consultado.

---

### Requirement 10: Rastreabilidade de Operações

**User Story:** Como Gestor_eSocial, quero que toda criação, alteração e finalização de Regras_de_Matrícula seja registrada com usuário, data/hora e justificativa, para que haja rastreabilidade completa das parametrizações.

#### Acceptance Criteria

1. WHEN qualquer operação de criação é realizada, THE Sistema SHALL persistir: usuário de cadastro, data e hora de cadastro, e justificativa da criação.
2. WHEN qualquer operação de alteração é realizada, THE Sistema SHALL persistir: usuário de alteração, data e hora de alteração, e justificativa da alteração.
3. WHEN qualquer operação de finalização de vigência é realizada, THE Sistema SHALL persistir: usuário da finalização, data e hora da finalização, e justificativa da finalização.
4. THE Sistema SHALL exibir na grade de listagem os campos: Usuário de cadastro, Data de cadastro, Usuário de alteração e Data de alteração.

---

### Requirement 11: Integridade dos Dados de Origem

**User Story:** Como Gestor_eSocial, quero que a funcionalidade utilize dados reais de pessoa e vínculo sem alterá-los, para que a parametrização eSocial não impacte o cadastro funcional do servidor.

#### Acceptance Criteria

1. THE Sistema SHALL buscar os dados pessoais do servidor (nome, CPF) exclusivamente do módulo Manter Pessoas, sem permitir edição direta nesta tela.
2. THE Sistema SHALL buscar os dados trabalhistas (vínculo, matrícula original) exclusivamente do módulo Vínculo do Servidor, sem permitir edição direta nesta tela.
3. THE Sistema SHALL garantir que nenhuma operação realizada nesta funcionalidade altere registros nas tabelas de Pessoa ou Vínculo funcional do SIGrh.
