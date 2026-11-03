# 🧪 Collections de Testes – Oficina Mecânica

Este repositório contém collections de testes Postman desenvolvidas para validar a API do sistema de **Oficina Mecânica**.

As collections contemplam os principais fluxos da aplicação, incluindo autenticação, gerenciamento de usuários, clientes, veículos, serviços, peças, estoque e ordens de serviço.

Os testes abrangem cenários de sucesso, erros de validação, restrições de acesso e regras de negócio, além de fluxos completos de utilização do sistema.

---

## 📁 Estrutura das Collections

O repositório possui duas collections, cada uma com uma finalidade específica:

### 1. **oficina-mecanica.postman_collection.json**

Collection principal, destinada aos testes funcionais dos recursos da API.

Contempla os seguintes módulos:

- **Autenticação:** login, geração de tokens JWT e validação de permissões por perfil.
- **Usuários:** cadastro, consulta, atualização e remoção de usuários.
- **Clientes:** cadastro, consulta, atualização e remoção de clientes.
- **Veículos:** cadastro, consulta, atualização e gerenciamento de veículos vinculados aos clientes.
- **Serviços:** gerenciamento do catálogo de serviços da oficina.
- **Peças e Estoque:** cadastro de peças, consulta e movimentações de estoque.
- **Ordens de Serviço:** abertura, consulta, atualização e gerenciamento das ordens de serviço.

Os cenários são organizados para validar operações de sucesso e situações de erro, incluindo dados inválidos, registros duplicados, ausência de autenticação e restrições de acesso.

**Collection:** [oficina-mecanica.postman_collection.json](https://github.com/TC-FIAP-SOAT-MECANICA/collections/blob/main/oficina-mecanica.postman_collection.json)

### 2. **oficina-mecanica-fluxo-os.postman_collection.json**

Collection destinada à execução de um fluxo completo de Ordem de Serviço, simulando a utilização real do sistema desde o cadastro inicial até a entrega do veículo ao cliente.

Os testes são executados sequencialmente, pois cada etapa depende dos dados gerados nas etapas anteriores.

A collection está organizada nas seguintes etapas:

#### 1. Preparação
- Verificação de disponibilidade da API (Health Check).
- Geração automática de dados únicos, como CPF e placa.
- Autenticação do usuário ADMIN.

#### 2. Cadastros
- Cadastro de um mecânico pelo ADMIN.
- Autenticação do mecânico.
- Autocadastro de um cliente.
- Autenticação do cliente.
- Cadastro de veículo vinculado ao cliente.
- Cadastro de serviço no catálogo.
- Cadastro de peça e movimentação de entrada no estoque.

#### 3. Abertura da OS
- Abertura de uma nova Ordem de Serviço pelo mecânico.
- Vinculação do cliente, veículo, serviço e peças.
- Definição da descrição do problema e agendamento do diagnóstico.
- Consulta da OS pelo cliente.

**Status esperado:** `RECEBIDA`

#### 4. Diagnóstico e orçamento
- Início do diagnóstico pelo mecânico.
- Inclusão de novas peças no orçamento.
- Registro do diagnóstico técnico.
- Envio do orçamento para aprovação do cliente.
- Validação de que o estoque ainda não sofreu baixa.

**Transição de status:** `RECEBIDA → EM_DIAGNOSTICO → AGUARDANDO_APROVACAO`

#### 5. Aprovação do cliente
- Consulta do orçamento pelo cliente.
- Aprovação do orçamento.
- Agendamento da execução do serviço.
- Validação da movimentação de saída das peças do estoque.

**Transição de status:** `AGUARDANDO_APROVACAO → EM_EXECUCAO`

#### 6. Execução e conclusão
- Finalização da Ordem de Serviço pelo mecânico.
- Validação da data de conclusão e do status da OS.

**Transição de status:** `EM_EXECUCAO → FINALIZADA`

#### 7. Entrega
- Registro da entrega do veículo pelo mecânico.
- Consulta do histórico de ordens de serviço pelo cliente.
- Validação de que a OS foi entregue e está presente no histórico do cliente.

**Transição de status:** `FINALIZADA → ENTREGUE`

**Collection:** [oficina-mecanica-fluxo-os.postman_collection.json](https://github.com/TC-FIAP-SOAT-MECANICA/collections/blob/main/oficina-mecanica-fluxo-os.postman_collection.json)

---

## 🌐 Configuração das Collections

As collections utilizam variáveis para facilitar a configuração dos endpoints e das credenciais de acesso.

Principais variáveis utilizadas:

| Variável | Finalidade |
|---|---|
| `baseUrl` | URL base da API |
| `adminEmail` | E-mail do usuário administrador |
| `adminSenha` | Senha do administrador |
| `tokenAdmin` | Token JWT do ADMIN |
| `tokenMecanico` | Token JWT do MECANICO |
| `tokenCliente` | Token JWT do CLIENTE |

Na collection de fluxo completo, o endereço padrão da API é:

`http://localhost:8080/api/v1`

As credenciais do administrador também podem ser ajustadas conforme a configuração do ambiente.

Os tokens são obtidos automaticamente durante a execução dos respectivos fluxos de autenticação.

---

## ▶️ Como Executar os Testes no Postman

1. Importe os arquivos `oficina-mecanica.postman_collection.json` e `oficina-mecanica-fluxo-os.postman_collection.json` no Postman.
2. Configure a variável `baseUrl` conforme o endereço em que a API está sendo executada.
3. Configure as credenciais do administrador, caso sejam diferentes das configurações padrão.
4. Execute a collection desejada utilizando a opção **Run collection**.
5. Consulte os resultados na aba de execução do Postman para verificar os cenários aprovados e as possíveis falhas.

### Recomendações de execução

**Collection principal:**
- Pode ser utilizada para validar individualmente os recursos e funcionalidades da API.
- Permite verificar cenários de sucesso e erro de forma organizada por módulo.

**Collection de fluxo completo:**
- Deve ser executada integralmente, respeitando a ordem das requisições.
- Não é recomendado executar requisições isoladas, pois existem dependências entre os cadastros, autenticações e operações da Ordem de Serviço.
- Cada execução gera novos dados, permitindo repetir o fluxo sem depender dos registros criados anteriormente.

---

## ⚙️ Recursos de Automação dos Testes

As collections possuem scripts automatizados para facilitar a execução e validação dos cenários:

- **Autenticação JWT:** armazenamento automático dos tokens dos perfis ADMIN, MECANICO e CLIENTE.
- **Dados dinâmicos:** geração automática de CPFs, placas e identificadores únicos.
- **Idempotência:** inclusão automática do header `Idempotency-Key` nas requisições POST.
- **Rastreamento:** validação da presença do header `X-Request-Id`.
- **Tempo de resposta:** validação de respostas em menos de 3 segundos.
- **Padronização de erros:** validação do formato RFC 9457 para respostas de erro.
- **Validação de regras de negócio:** verificação automática dos códigos HTTP, mensagens, resultados esperados e transições de status.
- **Validação de estoque:** conferência das movimentações de entrada e saída de peças durante o ciclo da OS.

---

## ✅ Requisitos para Execução

Para que os testes sejam executados corretamente, é necessário:

- API da Oficina Mecânica em execução e acessível pelo `baseUrl` configurado.
- Banco de dados disponível e configurado corretamente.
- Estrutura do banco de dados atualizada conforme as migrations da aplicação.
- Usuário ADMIN inicial criado com as credenciais configuradas.
- Postman instalado para importação e execução das collections.

**Observação:** As collections criam registros e realizam movimentações no banco de dados. Recomenda-se utilizar um ambiente local ou de testes, evitando a execução em ambientes de produção.

---

## 📌 Considerações

As duas collections se complementam na validação da API da Oficina Mecânica.

A collection principal permite testar os recursos individualmente, verificando suas operações, validações e regras de acesso.

Já a collection de fluxo completo permite validar a integração entre os diferentes recursos, simulando a jornada de uma Ordem de Serviço desde a chegada do veículo à oficina até sua entrega ao cliente.

Dessa forma, os testes verificam tanto o funcionamento individual dos módulos quanto a integração e consistência das regras de negócio ao longo do processo.