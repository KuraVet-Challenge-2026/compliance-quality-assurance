# KuraVet — Backlog Sprint 3
**Sistema de Cuidado Contínuo Pet | Estrutura Ágil de Compliance e Qualidade**

Preparado por: Scrum Master Sênior
Iteration Path: `KuraVet\Release 1\Sprint 3`
Modelo organizacional: 1 squad técnicas trabalhando em paralelo na mesma iteração (Dados, Cloud, .NET, Java, Mobile)

---

## 1. Visão Geral da Sprint 3

A Sprint 3 consolida a **fundação técnica multi-camada** do KuraVet: modelo de dados core em Oracle,
infraestrutura Azure provisionada via CLI, microsserviço .NET de observabilidade, monólito Java com
segurança e API para mobile, e o app React Native com navegação, integração de API e autenticação
desacoplada via Firebase.

**Total de esforço planejado:** 91 pontos (Fibonacci), distribuídos em 5 squads paralelas.
**Sprints anteriores (assumidas concluídas):** Sprint 1 (setup de ambientes, repositórios, pipelines base) e
Sprint 2 (modelagem ER, wireframes de telas, PoCs de conectividade Azure/Firebase).

---

## 2. Hierarquia do Backlog

### ÉPICO — KuraVet: Plataforma de Cuidado Contínuo Pet
**Descrição:** Plataforma multi-camada para gestão do cuidado contínuo de pets (prontuário, vacinação,
consultas e planos de acompanhamento), com backend de dados robusto em Oracle, infraestrutura em nuvem,
serviços de backend distribuídos (observabilidade em .NET e regras de negócio administrativas em Java) e
aplicativo mobile para tutores.

---

### FEATURE 1 — Camada de Dados (Oracle)
Núcleo relacional do domínio KuraVet, incluindo lógica de negócio em PL/SQL e auditoria.

#### PBI 1.1 — Modelagem e Script Core do Banco de Dados
- **Descrição:** Criar `script_bd.sql` contendo o modelo relacional core do KuraVet (tutores, pets,
  espécies/raças, prontuários, consultas, vacinas e planos de cuidado contínuo), com constraints
  (PK/FK/CHECK), sequences e massa de dados de exemplo.
- **Critérios de Aceite:**
  - Script cria todas as tabelas core sem erros em um schema Oracle limpo.
  - Cada tabela contém exatamente **5 registros** de massa de teste, com integridade referencial válida.
  - Nomenclatura de objetos segue padrão definido (`tb_`, `pk_`, `fk_`, `ck_`).
  - Script é idempotente (trata reexecução sem falhar, via `DROP IF EXISTS`/checagem de existência).
- **Prioridade:** 1 (Mais alta)
- **Esforço:** 8 pontos
- **Dependências técnicas:** Modelo ER aprovado (Sprint 2); ambiente Oracle disponível (Squad Cloud).

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T1.1.1 | Modelagem lógica e DDL das tabelas core | 3 | — |
| T1.1.2 | Constraints (PK/FK/CHECK) e sequences | 2 | T1.1.1 |
| T1.1.3 | INSERTs com 5 registros por tabela (massa consistente) | 2 | T1.1.2 |
| T1.1.4 | Validação de execução idempotente do `script_bd.sql` | 1 | T1.1.3 |

#### PBI 1.2 — Procedures e Functions de Negócio (PL/SQL)
- **Descrição:** Implementar lógica server-side manual: 2 procedures (uma com JOIN retornando JSON; outra
  calculando subtotais sem `ROLLUP`/`CUBE`) e 2 functions (uma com montagem manual de JSON — proibido usar
  conversão JSON nativa do Oracle; outra implementando uma regra de negócio de status de cuidado contínuo).
- **Critérios de Aceite:**
  - `PRC_PET_PRONTUARIO_JSON`: JOIN entre pet, tutor, consultas e vacinas, retornando JSON válido.
  - `PRC_SUBTOTAL_CONSULTAS_POR_PET`: subtotais por pet e total geral **sem** `ROLLUP`/`CUBE`/`GROUPING SETS`
    (agregação manual via `GROUP BY` + `UNION ALL`).
  - `FN_TO_JSON_MANUAL`: monta string JSON via concatenação, **sem** usar `JSON_OBJECT`/`JSON_ARRAY`/`TO_JSON`
    ou qualquer função nativa de conversão JSON do Oracle.
  - `FN_CALC_STATUS_CUIDADO`: aplica regra de negócio (janelas de vencimento de vacina/consulta) e retorna
    status categorizado (`EM_DIA` / `ATRASADO` / `CRITICO`).
  - Cada procedure/function trata **no mínimo 3 cláusulas `EXCEPTION WHEN`** distintas (ex.: `NO_DATA_FOUND`,
    `TOO_MANY_ROWS`, `VALUE_ERROR`, `OTHERS`).
- **Prioridade:** 1 (Mais alta)
- **Esforço:** 13 pontos
- **Dependências técnicas:** PBI 1.1 concluído (schema + massa de dados).

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T1.2.1 | Procedure `PRC_PET_PRONTUARIO_JSON` (JOIN + JSON) | 3 | PBI 1.1 |
| T1.2.2 | Procedure `PRC_SUBTOTAL_CONSULTAS_POR_PET` (subtotal manual, sem ROLLUP/CUBE) | 3 | PBI 1.1 |
| T1.2.3 | Function `FN_TO_JSON_MANUAL` (concatenação, sem função nativa) | 3 | T1.2.1 |
| T1.2.4 | Function `FN_CALC_STATUS_CUIDADO` (regra de negócio) | 3 | PBI 1.1 |
| T1.2.5 | Tratamento de exceções (3 `EXCEPTION WHEN`/bloco) e testes manuais | 1 | T1.2.1–T1.2.4 |

#### PBI 1.3 — Trigger de Auditoria
- **Descrição:** Implementar trigger de auditoria em tabela crítica (ex.: prontuário), capturando alterações
  via `:OLD` e `:NEW` e persistindo em tabela de log de auditoria — suporte a compliance/LGPD.
- **Critérios de Aceite:**
  - Trigger dispara em `INSERT`, `UPDATE` e `DELETE`.
  - Registro de auditoria contém valores antigos (`:OLD`), novos (`:NEW`), usuário, timestamp e operação.
  - Tabela de auditoria própria criada.
  - Trigger trata **no mínimo 3 `EXCEPTION WHEN`**.
  - Testes comprovam rastreabilidade completa das alterações.
- **Prioridade:** 2
- **Esforço:** 5 pontos
- **Dependências técnicas:** PBI 1.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T1.3.1 | Criar tabela `tb_auditoria_log` | 1 | PBI 1.1 |
| T1.3.2 | Implementar `TRG_AUD_PRONTUARIO` (INSERT/UPDATE/DELETE, `:OLD`/`:NEW`) | 2 | T1.3.1 |
| T1.3.3 | Tratamento de exceções (3 `EXCEPTION WHEN`) no trigger | 1 | T1.3.2 |
| T1.3.4 | Testes de rastreabilidade e evidência de compliance | 1 | T1.3.3 |

---

### FEATURE 2 — Infraestrutura Azure (DevOps/Cloud)
Provisionamento via Azure CLI e primeira carga funcional em nuvem.

#### PBI 2.1 — Provisionamento via Azure CLI
- **Descrição:** Provisionar toda a infraestrutura necessária (Resource Group, ACR, ACI ou serviço PaaS,
  Key Vault, Log Analytics) **exclusivamente via Azure CLI**, sem uso do portal, com arquitetura 100%
  conteinerizada (ACR + ACI sem privilégios root) ou 100% PaaS.
- **Critérios de Aceite:**
  - Todos os recursos criados via comandos `az` versionados em script.
  - Decisão arquitetural (conteinerizado vs. PaaS) documentada e aplicada de forma consistente (sem mix).
  - Se conteinerizado: imagem valida execução em contexto **non-root**.
  - Scripts idempotentes e documentados (pré-requisitos, ordem de execução).
  - Segredos não expostos em texto plano (Key Vault ou equivalente).
- **Prioridade:** 1 (Mais alta)
- **Esforço:** 8 pontos
- **Dependências técnicas:** Subscription Azure ativa; decisão arquitetural aprovada.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T2.1.1 | Script AZ CLI — Resource Group e ACR | 2 | — |
| T2.1.2 | Build & push de imagem non-root para o ACR | 2 | T2.1.1 |
| T2.1.3 | Provisionamento de ACI (ou serviço PaaS) via AZ CLI | 2 | T2.1.2 |
| T2.1.4 | Key Vault/segredos e Log Analytics | 1 | T2.1.1 |
| T2.1.5 | Validação non-root e documentação do provisionamento | 1 | T2.1.3, T2.1.4 |

#### PBI 2.2 — CRUD Funcional na Nuvem
- **Descrição:** Implementar CRUD funcional em nuvem para duas tabelas do domínio (ex.: Pet e Consulta),
  expondo endpoints operacionais no recurso provisionado.
- **Critérios de Aceite:**
  - CRUD completo (Create/Read/Update/Delete) para as 2 tabelas, testável via HTTP real no ambiente Azure.
  - Persistência sobrevive a reinícios (storage/DB gerenciado).
  - Sem privilégios administrativos/root expostos.
- **Prioridade:** 2
- **Esforço:** 8 pontos
- **Dependências técnicas:** PBI 2.1; PBI 1.1 (referência de modelo de dados).

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T2.2.1 | Definição dos endpoints CRUD (Pet, Consulta) | 1 | PBI 2.1 |
| T2.2.2 | Implementação do CRUD e conexão com banco cloud | 3 | T2.2.1 |
| T2.2.3 | Deploy do CRUD no recurso provisionado | 2 | T2.2.2 |
| T2.2.4 | Testes end-to-end no ambiente Azure | 2 | T2.2.3 |

---

### FEATURE 3 — Microsserviço .NET (Observabilidade e Qualidade)

#### PBI 3.1 — Health Check, Logs Estruturados e Métricas
- **Descrição:** Criar microsserviço .NET isolado, focado em observabilidade, com endpoints de health check,
  logging estruturado via Serilog e métricas via OpenTelemetry.
- **Critérios de Aceite:**
  - Endpoints `/health` (liveness) e `/health/ready` (readiness) implementados.
  - Logs estruturados em JSON via Serilog, com correlação de request id.
  - Métricas expostas via OpenTelemetry (exportador configurado).
  - Serviço isolado, sem acoplamento a outros serviços do KuraVet.
- **Prioridade:** 2
- **Esforço:** 8 pontos
- **Dependências técnicas:** Nenhuma (serviço isolado); Docker disponível.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T3.1.1 | Criar projeto .NET isolado e estrutura base | 1 | — |
| T3.1.2 | Health Checks (liveness/readiness) | 2 | T3.1.1 |
| T3.1.3 | Configurar Serilog (logs estruturados) | 2 | T3.1.1 |
| T3.1.4 | Configurar OpenTelemetry (métricas) | 2 | T3.1.1 |
| T3.1.5 | Containerização do microsserviço | 1 | T3.1.2–T3.1.4 |

#### PBI 3.2 — Testes Automatizados (xUnit, Moq, AAA)
- **Descrição:** Escrever testes de unidade e integração do microsserviço, usando xUnit, Moq e o padrão AAA.
- **Critérios de Aceite:**
  - Testes de unidade cobrindo lógica isolada, com Moq para dependências.
  - Testes de integração validando os endpoints de health check (`WebApplicationFactory`).
  - Todos os testes seguem estrutura AAA explícita.
  - Cobertura mínima (ex.: 70%) documentada.
- **Prioridade:** 3
- **Esforço:** 5 pontos
- **Dependências técnicas:** PBI 3.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T3.2.1 | Testes de unidade (xUnit + Moq, AAA) | 2 | PBI 3.1 |
| T3.2.2 | Testes de integração de endpoints (WebApplicationFactory) | 2 | PBI 3.1 |
| T3.2.3 | Relatório de cobertura e evidências | 1 | T3.2.1, T3.2.2 |

---

### FEATURE 4 — Monólito Java (Spring Boot)

#### PBI 4.1 — Views Thymeleaf e Banco Versionado (Flyway)
- **Descrição:** Desenvolver módulo administrativo monolítico com views server-side em Thymeleaf e banco
  versionado via Flyway.
- **Critérios de Aceite:**
  - Telas administrativas (listagem/cadastro) funcionais via Thymeleaf.
  - Migrations Flyway versionadas (`V1__...`, `V2__...`) aplicadas automaticamente no startup.
  - Rollback de migration testado em ambiente de homologação.
- **Prioridade:** 2
- **Esforço:** 8 pontos
- **Dependências técnicas:** PBI 1.1 (referência de modelo, se schema compartilhado).

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T4.1.1 | Setup Spring Boot + Flyway (migrations iniciais) | 2 | — |
| T4.1.2 | Views Thymeleaf (listagem/cadastro) | 3 | T4.1.1 |
| T4.1.3 | Testes de migration (apply/rollback) | 2 | T4.1.1 |
| T4.1.4 | Ajustes de UX e validação server-side de formulário | 1 | T4.1.2 |

#### PBI 4.2 — Segurança por Perfis (Spring Security)
- **Descrição:** Proteger rotas do monólito com Spring Security, definindo dois perfis de acesso
  (ex.: `ADMIN` e `VETERINARIO`) com autorizações distintas.
- **Critérios de Aceite:**
  - Login funcional protegendo rotas administrativas.
  - Dois perfis configurados com permissões distintas e testadas (acesso negado/permitido conforme perfil).
  - Senhas armazenadas com hashing seguro (BCrypt).
  - Testes de autorização por perfil documentados.
- **Prioridade:** 2
- **Esforço:** 5 pontos
- **Dependências técnicas:** PBI 4.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T4.2.1 | Configuração Spring Security + autenticação | 2 | PBI 4.1 |
| T4.2.2 | Perfis (`ADMIN`/`VETERINARIO`) e regras de rota | 2 | T4.2.1 |
| T4.2.3 | Testes de autorização por perfil | 1 | T4.2.2 |

#### PBI 4.3 — API JSON Paralela para o App Mobile
- **Descrição:** Expor API REST paralela (JSON), a partir do mesmo monólito, para consumo pelo app mobile,
  reaproveitando as regras de negócio internas.
- **Critérios de Aceite:**
  - Endpoints REST em JSON para Pet, Consulta e Plano de Cuidado.
  - API protegida (token/sessão) compatível com o app mobile.
  - Contrato documentado (OpenAPI/Swagger).
  - Testado com client externo (Postman/Insomnia) simulando o app mobile.
- **Prioridade:** 3
- **Esforço:** 5 pontos
- **Dependências técnicas:** PBI 4.2; PBI 1.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T4.3.1 | Endpoints REST JSON (Pet, Consulta, Plano) | 2 | PBI 4.2 |
| T4.3.2 | Proteção da API (token/sessão compatível com mobile) | 2 | T4.3.1 |
| T4.3.3 | Documentação OpenAPI/Swagger e testes com client externo | 1 | T4.3.1 |

---

### FEATURE 5 — App Mobile (React Native)

#### PBI 5.1 — Navegação Real e Telas Protegidas
- **Descrição:** Implementar navegação real (React Navigation) com no mínimo 6 telas protegidas por
  autenticação (ex.: Login, Home, Pets, Detalhe do Pet, Agenda, Perfil).
- **Critérios de Aceite:**
  - Mínimo de **6 telas** navegáveis via rotas reais (Stack/Tab Navigator).
  - Telas protegidas redirecionam para login quando o usuário não está autenticado.
  - Fluxo de navegação validado por roteiro de QA.
- **Prioridade:** 2
- **Esforço:** 8 pontos
- **Dependências técnicas:** PBI 5.3 (autenticação), para proteção efetiva das rotas.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T5.1.1 | Setup React Navigation (Stack/Tab) | 1 | — |
| T5.1.2 | Implementação das 6 telas (estrutura e layout) | 4 | T5.1.1 |
| T5.1.3 | Guard de rotas protegidas (redirecionamento para login) | 2 | PBI 5.3 |
| T5.1.4 | Testes manuais de navegação (roteiro de QA) | 1 | T5.1.2, T5.1.3 |

#### PBI 5.2 — Integração de API (TanStack Query)
- **Descrição:** Consumir a API do backend Java via TanStack Query, com estados de loading e timeout
  estendido de 60 segundos.
- **Critérios de Aceite:**
  - Requisições usam TanStack Query (`useQuery`/`useMutation`) com cache configurado.
  - Estados de loading visíveis em todas as telas que consomem API.
  - Timeout de requisição configurado para **60 segundos**.
  - Tratamento de erro de timeout/falha de rede com feedback ao usuário.
- **Prioridade:** 3
- **Esforço:** 5 pontos
- **Dependências técnicas:** PBI 4.3 (API JSON disponível); PBI 5.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T5.2.1 | Configuração do client HTTP com timeout de 60s | 1 | PBI 4.3 |
| T5.2.2 | Implementação de queries/mutations (TanStack Query) | 2 | T5.2.1 |
| T5.2.3 | Estados de loading e tratamento de erro nas telas | 2 | T5.2.2 |

#### PBI 5.3 — Autenticação Externa e Desacoplada (Firebase)
- **Descrição:** Configurar autenticação via Firebase Authentication, integrada ao fluxo de login do app,
  mantendo a autenticação desacoplada da lógica de negócio.
- **Critérios de Aceite:**
  - Login/cadastro via Firebase Authentication funcional (e-mail/senha, no mínimo).
  - Token do Firebase validado/utilizado nas chamadas subsequentes à API.
  - Autenticação desacoplada da lógica de negócio (camada de serviço isolada).
  - Logout e persistência de sessão funcionando corretamente.
- **Prioridade:** 2
- **Esforço:** 5 pontos
- **Dependências técnicas:** Projeto Firebase configurado (Squad Cloud); PBI 5.1.

**Tasks (Sprint 3):**
| Task | Descrição | Esforço | Dependências |
|---|---|---|---|
| T5.3.1 | Configuração do projeto Firebase e SDK no app | 1 | — |
| T5.3.2 | Login/cadastro (Firebase Auth) | 2 | T5.3.1 |
| T5.3.3 | Persistência de sessão e logout | 1 | T5.3.2 |
| T5.3.4 | Testes de fluxo de autenticação | 1 | T5.3.2, T5.3.3 |

---

## 3. Release Plan (Roadmap de Entregas)

| Sprint | Foco | Squads envolvidas | Esforço (pts) | Status |
|---|---|---|---|---|
| Sprint 1 | Setup de ambientes, repositórios e pipelines base | Todas | — | Concluída |
| Sprint 2 | Modelagem ER, wireframes de telas, PoCs de conectividade Azure/Firebase | Todas | — | Concluída |
| **Sprint 3** | **Fundação técnica multi-camada (este backlog)** | Dados, Cloud, .NET, Java, Mobile | **91** | Em planejamento |
| Sprint 4 | Integração cross-squad, pipelines CI/CD, testes E2E integrados, hardening LGPD | Todas | ~55 (estimado) | Futura |
| Sprint 5 | Homologação, performance testing, release candidate | Todas | ~40 (estimado) | Futura |

### Balanceamento de esforço por squad na Sprint 3

| Squad | PBIs | Esforço (pts) | % da Sprint |
|---|---|---|---|
| Dados (Oracle) | 1.1, 1.2, 1.3 | 26 | 28,6% |
| Cloud (Azure) | 2.1, 2.2 | 16 | 17,6% |
| Backend .NET | 3.1, 3.2 | 13 | 14,3% |
| Backend Java | 4.1, 4.2, 4.3 | 18 | 19,8% |
| Mobile (React Native) | 5.1, 5.2, 5.3 | 18 | 19,8% |
| **Total** | 13 PBIs / 51 Tasks | **91** | 100% |

**Observação de capacidade:** 91 pontos distribuídos entre 5 squads paralelas resulta em 13–26 pontos por
squad — carga saudável para uma sprint de 2 semanas por squad especializada. Caso a equipe seja única
(sem paralelismo real entre squads), recomenda-se dividir esta Sprint 3 em duas iterações (3a e 3b),
priorizando PBIs de Prioridade 1 (1.1, 1.2, 2.1) na primeira metade.

### Ordem de dependências críticas (caminho crítico)
`PBI 1.1 → PBI 1.2 / PBI 1.3` (Dados) · `PBI 2.1 → PBI 2.2` (Cloud) ·
`PBI 4.1 → PBI 4.2 → PBI 4.3` (Java) · `PBI 5.3 → PBI 5.1 → PBI 5.2` (Mobile, autenticação antes de navegação
protegida e antes do consumo de API)

---

## 4. Definition of Done (Compliance e Qualidade)

Um item só é considerado "Done" quando:
- [ ] Critérios de aceite validados e evidenciados (print/log/execução).
- [ ] Código revisado por par (Pull Request aprovado).
- [ ] Testes automatizados (quando aplicável) executando com sucesso no pipeline.
- [ ] Sem segredos/credenciais em texto plano no código ou nos scripts.
- [ ] Alterações em dados sensíveis rastreáveis via auditoria (LGPD).
- [ ] Documentação técnica mínima atualizada (README/Swagger/comentários de script).
- [ ] Item validado pelo Product Owner ou responsável técnico da squad.

## 5. Notas para importação no Azure Boards

O arquivo `KuraVet_Sprint3_AzureBoards_Import.csv` traz as 70 linhas (1 Épico, 5 Features, 13 PBIs, 51 Tasks)
já formatadas para importação via **Boards → Work Items → Import Work Items**. Como o CSV nativo do Azure
Boards não cria relações Pai/Filho automaticamente, siga:

1. Importe o CSV normalmente (cria os work items sem hierarquia).
2. Abra a view de **Backlog** ou **Queries → New Query**, agrupe por `Parent Title (referência)` (coluna
   auxiliar do CSV, não é um campo nativo).
3. Use **Boards → Backlogs** (arrastar item para dentro do Épico/Feature/PBI) ou a função **Bulk Edit** para
   setar o campo `Parent` de cada item, usando a coluna auxiliar como guia.
4. Alternativa mais rápida para times com CLI: usar `az boards work-item create --parent` via um script que
   itere o CSV — posso gerar esse script se for útil.
