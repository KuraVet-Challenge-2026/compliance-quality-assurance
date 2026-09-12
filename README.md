# KuraVet 

**Sistema de Cuidado Contínuo Pet | Estratégia de Qualidade e Testes**

Este documento define como a qualidade é garantida no KuraVet ao longo das sprints: o que é testado, por
quem, com qual ferramenta, e o que precisa estar verdadeiro para um item do backlog ser considerado "Done".
Serve de referência para quem for testar, revisar Pull Requests ou auditar a Sprint 3 (em curso).

## Integrantes

- Pedro Henrique Luiz Alves Duarte – RM563405
- Henrique Martins Oliveira – RM563620
- Guilherme Macedo Martins – RM562396

---

## 1. Filosofia de QA do projeto

QA no KuraVet não é uma etapa final — é parte de cada PBI, desde a definição dos Critérios de Aceite até o
merge do código. Três princípios guiam isso:

1. **Testável por definição:** todo PBI só entra na sprint com Critérios de Aceite verificáveis (ver o
   Backlog da Sprint 3). Se um critério não dá pra confirmar como verdadeiro/falso, ele é reescrito antes
   do PBI ser aceito.
2. **Rastreabilidade:** por lidar com dados de saúde de pets e dados pessoais de tutores, o sistema trata
   auditoria e LGPD como requisito de qualidade, não só de segurança — testes de auditoria (`:OLD`/`:NEW`)
   fazem parte do escopo de QA, não são só responsabilidade da squad de Dados.
3. **Teste no nível certo:** lógica de negócio pesada (regras de status de cuidado, cálculo de subtotais)
   é testada o mais perto possível da fonte (PL/SQL, unidade), não só via clique manual na ponta.

---

## 2. Estratégia de teste por camada

| Camada | O que é testado | Ferramenta / técnica | Squad responsável |
|---|---|---|---|
| Dados (Oracle) | Procedures, functions, trigger de auditoria, tratamento de exceção | Execução manual de scripts de teste (`script_bd.sql` + massa de 5 registros/tabela), inspeção de `tb_auditoria_log` | Dados |
| Infraestrutura (Azure) | Provisionamento reprodutível, CRUD funcional em nuvem, non-root | Reexecução dos scripts `az CLI`, chamadas HTTP reais (Postman/curl) contra o recurso provisionado | Cloud |
| Microsserviço .NET | Health checks, logs estruturados, métricas, regras internas | xUnit + Moq, padrão AAA (Arrange-Act-Assert), `WebApplicationFactory` para testes de integração | .NET |
| Monólito Java | Views Thymeleaf, migrations Flyway, perfis de acesso, API JSON | Teste manual guiado de telas, apply/rollback de migration em homologação, testes de autorização por perfil, Postman/Insomnia para a API | Java |
| App Mobile (React Native) | Navegação entre as 6 telas, guard de rotas, loading/timeout, login Firebase | Roteiro de QA manual (checklist abaixo), inspeção de estados de loading/erro em conexão simulada lenta | Mobile |

Testes end-to-end cruzando todas as camadas (mobile → API Java → Oracle) ficam para a **Sprint 4** (PBI
6.2 — Testes E2E Integrados), depois que o pipeline de CI/CD (PBI 6.1) estiver de pé — não faz sentido
automatizar E2E antes de ter onde rodar isso de forma repetível.

---

## 3. Pirâmide de testes da Sprint 3

<a href="https://ibb.co/NgqTgN2R"><img src="https://i.ibb.co/8nwYnxgk/piramide-testes-qa.jpg" alt="piramide-testes-qa" border="0"></a>

Na Sprint 3, a base da pirâmide (unidade) é a que tem mais cobertura automatizada real (PBI 3.2, no
microsserviço .NET). Nas demais camadas, a validação ainda é majoritariamente manual — isso é uma dívida
técnica conhecida e reconhecida, não um esquecimento; ela é endereçada explicitamente no roadmap (Sprint 4).

---

## 4. Checklist de QA por PBI (Sprint 3)

### Camada de Dados (Oracle)
- [ ] `script_bd.sql` roda do zero em um schema limpo sem erro.
- [ ] Reexecutar o script não quebra nada (idempotência).
- [ ] Cada tabela tem exatamente 5 registros, com FKs válidas.
- [ ] `PRC_PET_PRONTUARIO_JSON` retorna JSON válido e coerente com os dados de origem.
- [ ] `PRC_SUBTOTAL_CONSULTAS_POR_PET` bate com a soma manual (conferir contra `SUM()` simples).
- [ ] `FN_TO_JSON_MANUAL` não usa `JSON_OBJECT`/`JSON_ARRAY`/`TO_JSON` nativo (revisão de código).
- [ ] `FN_CALC_STATUS_CUIDADO` retorna o status esperado para os 3 cenários (em dia / atrasado / crítico).
- [ ] Cada procedure/function tem pelo menos 3 `EXCEPTION WHEN` distintos (revisão de código).
- [ ] Trigger de auditoria grava `:OLD`/`:NEW`, usuário e timestamp em INSERT, UPDATE e DELETE.

### Infraestrutura (Azure)
- [ ] Todo recurso existe só por causa de um comando `az` documentado (nada criado manualmente no portal).
- [ ] Se conteinerizado: contêiner roda non-root (`whoami` dentro do contêiner ≠ root).
- [ ] CRUD das 2 tabelas responde certo via chamada HTTP real (não só localhost).
- [ ] Dado sobrevive a um restart do recurso.
- [ ] Nenhuma credencial em texto plano no repositório ou nos scripts.

### Microsserviço .NET
- [ ] `/health` e `/health/ready` respondem 200 em cenário saudável.
- [ ] Logs saem estruturados (JSON) e incluem um id de correlação por requisição.
- [ ] Métricas aparecem no exportador configurado (OpenTelemetry).
- [ ] Testes de unidade e integração passam localmente e (quando existir) no pipeline.
- [ ] Cobertura mínima combinada (ex.: 70%) documentada no relatório da PBI 3.2.

### Monólito Java
- [ ] Migrations Flyway aplicam do zero sem erro; rollback testado em homologação.
- [ ] Usuário `ADMIN` acessa rotas administrativas; usuário `VETERINARIO` não acessa o que não deveria (e vice-versa).
- [ ] Senhas armazenadas com hashing (nunca texto plano no banco).
- [ ] Endpoints da API JSON testados com Postman/Insomnia simulando o app mobile.
- [ ] Contrato da API documentado (Swagger/OpenAPI) bate com o comportamento real.

### App Mobile (React Native)
- [ ] As 6 telas existem e são navegáveis via rotas reais.
- [ ] Usuário não autenticado é redirecionado ao tentar acessar tela protegida.
- [ ] Estado de loading aparece em toda tela que busca dado da API.
- [ ] Timeout de 60s confirmado (simular API lenta/instável e checar o comportamento de erro).
- [ ] Login/cadastro via Firebase funciona; logout limpa a sessão de fato (não só a tela).

---

## 5. Classificação de severidade de bugs

| Severidade | Critério | Exemplo | SLA sugerido |
|---|---|---|---|
| **Crítica** | Sistema indisponível, perda ou vazamento de dado, falha de auditoria/LGPD | Trigger de auditoria não grava alteração em prontuário | Corrigir antes de qualquer novo merge |
| **Alta** | Funcionalidade principal do PBI não funciona | CRUD na nuvem não persiste dado | Corrigir na sprint atual |
| **Média** | Funcionalidade funciona com comportamento incorreto em caso de borda | Timeout do mobile não dispara em exatamente 60s | Corrigir na sprint atual, se der; senão próxima |
| **Baixa** | Cosmético, não afeta função nem dado | Texto de log poderia ser mais claro | Backlog, sem urgência |

Um PBI com bug **Crítico ou Alto** em aberto não pode ser marcado como "Done" (ver Definition of Done no
Backlog da Sprint 3, seção 4).

---

## 6. Rastreabilidade: Critério de Aceite → Teste

Todo Critério de Aceite de PBI (documentado em `KuraVet_Sprint3_Backlog.md`) deve ter pelo menos uma
evidência de verificação associada antes do PBI fechar. Formato recomendado, por PBI:

| Critério de Aceite | Tipo de evidência | Onde fica a evidência |
|---|---|---|
| Ex.: "Script cria todas as tabelas sem erro" | Log de execução | Anexo no work item da Task no Azure Boards |
| Ex.: "Health checks implementados" | Print/log da chamada HTTP | Anexo no work item ou link do pipeline |
| Ex.: "Dois perfis com permissões testadas" | Print do teste manual de autorização | Anexo no work item do PBI 4.2 |

Isso evita "Done de palavra" — todo PBI fechado tem algo concreto anexado no Azure Boards que comprova que
o critério foi mesmo verificado, não só implementado.

---

## 7. Como rodar os testes automatizados (.NET)

```bash
# Dentro da pasta do microsserviço .NET
dotnet test                              # roda unidade + integração
dotnet test --collect:"XPlat Code Coverage"   # roda com coleta de cobertura
```

Para os demais componentes (Oracle, Java, Mobile), a Sprint 3 ainda não tem suíte automatizada — a
verificação segue os checklists manuais da seção 4 até que a automação correspondente entre no backlog
(parte do trabalho de hardening da Sprint 4).

---

## 8. Compliance e LGPD (visão de QA)

QA também cobre, não só bug: aqui garantimos que dado sensível de tutor e pet é tratado corretamente.

- Toda alteração em `tb_prontuario` deve aparecer em `tb_auditoria_log` — isso é testado junto com o PBI 1.3,
  não é opcional nem "nice to have".
- Nenhum dado sensível deve aparecer em log de aplicação em texto plano (checar logs do microsserviço .NET
  e do monólito Java).
- Antes de qualquer demo ou ambiente compartilhado, confirmar que a massa de dados usada é fictícia (os "5
  registros por tabela" do `script_bd.sql`), nunca dado real de tutor/pet.

---

## 9. Quando um item pode virar "Done"

Um PBI só fecha quando:
1. Todos os Critérios de Aceite têm evidência anexada (seção 6).
2. Não há bug Crítico ou Alto em aberto (seção 5).
3. Os itens do checklist de QA correspondente (seção 4) foram verificados.
4. Code review (Pull Request) aprovado por outra pessoa da squad.
5. Nenhum segredo/credencial em texto plano foi introduzido no código.

Este README deve ser revisado a cada sprint nova — conforme a automação avança (Sprint 4 em diante), os
checklists manuais devem ser progressivamente substituídos por testes automatizados no pipeline de CI/CD.
