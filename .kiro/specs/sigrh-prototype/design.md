uia visual clara entre módulos (azul escuro/amarelo), submódulos (azul médio) e funcionalidades (azul claro)

## Módulos e Responsabilidades

| Módulo | Responsabilidade Principal |
|---|---|
| Pessoa | Cadastro e manutenção de dados pessoais |
| Recadastramentos | Processo periódico de confirmação cadastral |
| Frequência | Controle de ponto e jornada de trabalho |
| Organização | Estrutura organizacional, endereçamento e calendários |
| Processos | Atos oficiais e inclusões |
| Processo Seletivo | Concursos, editais e ingresso |
| Vínculos | Gestão de vínculos funcionais e dependentes |
| Funcional | Progressão, frequência funcional e jornada |
| Desenvolvimento | Capacitação e currículo |
| Afastamentos | Afastamentos e férias |
| Movimentação | Transferências e disposições |
| Vantagem Pecuniária | Gratificações e ATS |
| Direitos | Decisões judiciais e diárias |
| Benefícios | Vale transporte e auxílio creche |
| Pagamentos | Folha, contracheque e relatórios financeiros |
| Saúde do Servidor | Perícias médicas e acidentes |
| Previdência | Averbação e recadastramento de aposentados |
| Dados Gerenciais | Cubo de dados e estatísticas |
| Segurança | Usuários, perfis e permissões |
| eSocial | Geração e envio de eventos |
| Serviços | Agendador e parâmetros |
| Ajuda | Procedimentos administrativos e computacionais |

## Correctness Properties

*Uma propriedade é uma característica ou comportamento que deve ser verdadeiro em todas as execuções válidas do sistema — essencialmente, uma declaração formal sobre o que o sistema deve fazer. As propriedades servem como ponte entre especificações legíveis por humanos e garantias de corretude verificáveis por máquina.*

---

### Propriedade 1: Unicidade de identificadores de pessoas

*Para qualquer* conjunto de pessoas cadastradas no sistema, todos os identificadores únicos atribuídos devem ser distintos entre si.

**Validates: Requirements 1.3**

---

### Propriedade 2: Rejeição de CPF duplicado

*Para qualquer* CPF já presente no cadastro de pessoas, uma tentativa de cadastrar uma nova pessoa com o mesmo CPF deve ser rejeitada pelo sistema.

**Validates: Requirements 1.2**

---

### Propriedade 3: Validação de campos obrigatórios

*Para qualquer* formulário de cadastro com ao menos um campo obrigatório ausente, o sistema deve rejeitar o salvamento e identificar os campos faltantes.

**Validates: Requirements 1.6**

---

### Propriedade 4: Auditoria de alterações

*Para qualquer* registro editado no sistema, o histórico de alterações deve conter ao menos uma entrada a mais do que antes da edição, com data, hora e usuário responsável.

**Validates: Requirements 1.5**

---

### Propriedade 5: Transição de estado de recadastramento

*Para qualquer* recadastramento com situação "Pendente", após a aprovação pelo usuário de RH, a situação deve ser "Validado" e nunca permanecer "Pendente".

**Validates: Requirements 3.3**

---

### Propriedade 6: Rejeição exige motivo

*Para qualquer* rejeição de recadastramento ou solicitação de frequência, o sistema deve bloquear a confirmação enquanto o campo de motivo estiver vazio.

**Validates: Requirements 3.4, 5.4**

---

### Propriedade 7: Efetivação filtra apenas registros aprovados

*Para qualquer* conjunto de registros de ponto submetidos à efetivação, somente os registros com situação "Aprovada" devem ser incluídos no processamento.

**Validates: Requirements 6.1**

---

### Propriedade 8: Consistência do total de inconsistências

*Para qualquer* conjunto de inconsistências de frequência registradas, o total exibido no painel deve ser igual à soma dos registros individuais agrupados por tipo e unidade.

**Validates: Requirements 7.1**

---

### Propriedade 9: Alerta de capacidade do quadro lotacional

*Para qualquer* unidade organizacional onde o número de servidores lotados supere o número de vagas autorizadas no quadro lotacional, o sistema deve exibir alerta ao usuário responsável.

**Validates: Requirements 23.2**

---

### Propriedade 10: Imutabilidade de atos publicados

*Para qualquer* ato oficial com situação "Publicado", qualquer tentativa de edição deve ser rejeitada pelo sistema.

**Validates: Requirements 27.4**

---

### Propriedade 11: Integridade referencial de estruturas de carreira

*Para qualquer* estrutura de carreira que possua servidores vinculados ativos, a tentativa de exclusão deve ser bloqueada pelo sistema.

**Validates: Requirements 34.4**

---

### Propriedade 12: Não-sobreposição de afastamentos

*Para qualquer* servidor, não deve existir no sistema dois afastamentos cujos períodos se sobreponham; qualquer tentativa de registrar afastamento com período conflitante deve ser sinalizada.

**Validates: Requirements 52.2**

---

### Propriedade 13: Completude do processamento da folha

*Para qualquer* competência processada, todos os servidores com vínculo ativo na data de fechamento devem possuir registro de cálculo na folha de pagamento.

**Validates: Requirements 67.1**

---

### Propriedade 14: Controle de acesso por permissão

*Para qualquer* usuário sem permissão atribuída a uma funcionalidade, o acesso a essa funcionalidade deve ser negado e a tentativa deve ser registrada no log de auditoria.

**Validates: Requirements 76.5**

---

### Propriedade 15: Propagação imediata de alterações de perfil

*Para qualquer* perfil de acesso alterado, todos os usuários que possuem esse perfil devem refletir as novas permissões imediatamente após a alteração.

**Validates: Requirements 77.3**

---

### Propriedade 16: Geração automática de S-2200 para novos vínculos

*Para qualquer* vínculo funcional criado no sistema, deve existir um evento S-2200 correspondente gerado automaticamente antes do envio ao eSocial.

**Validates: Requirements 80.1**

---

### Propriedade 17: Dependência S-2200 antes de S-1200

*Para qualquer* evento S-1200 gerado para um servidor, deve existir um evento S-2200 aceito pelo eSocial para o mesmo servidor; caso contrário, o envio do S-1200 deve ser bloqueado.

**Validates: Requirements 81.3**

---

### Propriedade 18: Cálculo de dedução de IRRF por dependentes

*Para qualquer* servidor com dependentes ativos cadastrados, o valor total de dedução de IRRF calculado pelo sistema deve ser igual à soma das deduções individuais de cada dependente ativo.

**Validates: Requirements 40.4**

---

### Propriedade 19: Validação de importação de inscritos

*Para qualquer* arquivo de importação de inscritos que contenha registros com CPF inválido ou duplicado, o sistema deve listar todos os erros encontrados antes de permitir a confirmação da importação.

**Validates: Requirements 31.3**

