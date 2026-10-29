# KuraVet — Compliance, Quality Assurance & Tests

Repositório de QA do **KuraVet**, plataforma de teleconsulta veterinária contínua (Challenge FIAP 2026 × Clyvo Vet),
para a disciplina **Compliance, Quality Assurance & Tests** — Sprint 4.

## Entrega

| Item | Link |
|---|---|
| **Azure Boards** — plano de testes manuais (Parte A) | https://dev.azure.com/KuraVet/KuraVet |
| **Vídeo** — configuração, execução e resultados da automação (Parte B) | https://youtu.be/8PCD84cKgsU |
| Repositório (branch `develop`) | https://github.com/KuraVet-Challenge-2026/compliance-quality-assurance/tree/develop |

## Equipe — Turma 2TDSPO

| Nome | RM |
|---|---|
| Guilherme Macedo Martins | RM562396 |
| Pedro Henrique Luiz Alves Duarte | RM563405 |
| Henrique Martins Oliveira | RM563620 |

---

## Sistema testado

Backend **Java / Spring Boot** com portal web **Thymeleaf** e banco **Oracle** (repositório
[`java-advanced`](https://github.com/KuraVet-Challenge-2026/java-advanced)). O portal atende dois perfis:

- **Veterinário** (`/portal/**`) — fila de solicitações, aprovação, recusa com motivo, diagnóstico, triagens.
- **Tutor** (`/tutor/**`) — solicita teleconsultas, acompanha o status e faz a triagem por IA do relato do pet.

---

## Parte A — Testes manuais (Azure Boards)

O plano de testes de validação em nível de sistema está no **Azure Boards** como work items do tipo
**Test Case**. Cada caso tem objetivo, dados de entrada, dados de saída esperados e passos, e está vinculado
(*Tested By*) ao Product Backlog Item que valida. As iterações das Sprints 1 a 4 estão datadas e formam o
plano de release.

Resumo dos casos em [`testes-manuais/README.md`](testes-manuais/README.md). O Azure Boards é a fonte oficial.

---

## Parte B — Automação de testes (Katalon Recorder)

O sistema tem interface web, então a automação usa **Record & Playback** com o **Katalon Recorder**.
São **7 casos automatizados** sobre o portal, com asserts de texto e de elemento:

| Caso | O que valida |
|---|---|
| CT-01 | Login do veterinário com credenciais válidas e logout |
| CT-02 | Login com senha incorreta é rejeitado |
| CT-03 | Tutor é bloqueado na área da clínica (controle de acesso por perfil) |
| CT-04 | Aprovar solicitação de teleconsulta (SOLICITADA → AGENDADA) |
| CT-05 | Recusar solicitação com validação do motivo (mín. 10 caracteres) |
| CT-06 | Emitir diagnóstico com validação e encerrar a consulta (AGENDADA → REALIZADA) |
| CT-07 | Triagem por IA: relato curto rejeitado e relato grave classificado como urgente |

Os casos CT-04 a CT-06 **criam a própria solicitação** como tutor antes de agir como veterinário, com uma
data gerada a cada execução. Assim a suíte roda repetidas vezes sem precisar restaurar dados no banco.

Como executar: [`testes-automatizados/README.md`](testes-automatizados/README.md).
Evidências de execução: [`evidencias/`](evidencias/).

---

## Estrutura do repositório

```
.
├── README.md
├── Integrantes.txt
├── docs/
│   ├── estrategia-qa.md            # estratégia de QA por camada
│   └── backlog/epicos-e-pbis.md    # backlog por jornada de usuário
├── testes-manuais/
│   └── README.md                   # resumo do plano de testes manuais (espelho do Azure Boards)
├── testes-automatizados/
│   ├── README.md                   # como abrir e rodar os testes
│   ├── katalon/                    # 7 casos em Selenese HTML para o Katalon Recorder
│   └── selenium/                   # os mesmos casos em .side (Selenium IDE)
└── evidencias/                     # prints da execução, extraídos do vídeo
```
