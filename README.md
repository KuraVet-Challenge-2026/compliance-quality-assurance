# KuraVet — Compliance, Quality Assurance & Tests

Repositório de QA do **KuraVet** (Sistema de Cuidado Contínuo Pet) para a disciplina
**Compliance, Quality Assurance & Tests**. Ele reúne o plano de testes manuais, a automação de testes
funcionais do portal web (Thymeleaf) com Selenium IDE e as evidências de execução.

> A estratégia geral de QA do projeto (testes por camada, checklists por PBI, severidade de bugs, LGPD)
> está em [`docs/estrategia-qa.md`](docs/estrategia-qa.md). O backlog por jornada de usuário está em
> [`docs/backlog/epicos-e-pbis.md`](docs/backlog/epicos-e-pbis.md).

## Equipe — Turma 2TDSPO

| Nome | RM |
|---|---|
| Guilherme Macedo Martins | RM562396 |
| Pedro Henrique Luiz Alves Duarte | RM563405 |
| Henrique Martins Oliveira | RM563620 |

## Estrutura do repositório

```
.
├── README.md
├── docs/
│   ├── estrategia-qa.md          # estratégia de QA por camada
│   └── backlog/epicos-e-pbis.md  # épicos e PBIs
├── testes-manuais/
│   └── README.md                 # plano de testes manuais (espelho do Azure Boards)
├── testes-automatizados/
│   ├── README.md                 # como abrir e rodar o .side no Selenium IDE
│   └── selenium/                 # projetos .side do Selenium IDE
└── evidencias/                   # prints e links das execuções/vídeo
```

---

## Parte A — Testes Manuais

**Objetivo:** validar manualmente os fluxos principais do KuraVet a partir dos Critérios de Aceite dos PBIs,
registrando passos, resultado esperado e resultado obtido de cada caso de teste.

Os casos de teste vivem no **Azure Boards** como work items do tipo *Test Case* (Test Cases 6.x / 7.x),
vinculados aos PBIs correspondentes. Este repositório documenta e aponta para lá — veja
[`testes-manuais/README.md`](testes-manuais/README.md).

🔗 **Azure Boards:** https://dev.azure.com/KuraVet/KuraVet

---

## Parte B — Automação

**Ferramenta:** [Selenium IDE](https://www.selenium.dev/selenium-ide/) (extensão de navegador para
Chrome/Firefox), gravando e reproduzindo testes sobre o portal web (Thymeleaf) do KuraVet.

**Fluxos cobertos:**

1. **Login do veterinário** — acesso ao portal com credenciais válidas e rejeição de credenciais inválidas.
2. **Fila de solicitações** — visualização da fila de solicitações de teleconsulta da clínica.
3. **Aprovar / recusar solicitação** — aprovação de uma solicitação e recusa informando o motivo.
4. **Registrar diagnóstico** — emissão do diagnóstico ao final da teleconsulta.

**Como executar:**

1. Instale a extensão Selenium IDE no Chrome ou Firefox.
2. Suba o portal web do KuraVet localmente (ou use a URL do ambiente de homologação).
3. Abra o Selenium IDE → **Open an existing project** → selecione o arquivo `.side` em
   [`testes-automatizados/selenium/`](testes-automatizados/selenium/).
4. Confira a *Base URL* do projeto e clique em **Run all tests**.

Detalhes em [`testes-automatizados/README.md`](testes-automatizados/README.md).

---

## Entrega

| Item | Link |
|---|---|
| Azure Boards (Test Cases — Parte A) | https://dev.azure.com/KuraVet/KuraVet |
| Repositório (branch `develop`) | https://github.com/KuraVet-Challenge-2026/compliance-quality-assurance/tree/develop |
| Vídeo da execução (YouTube) | _A definir — link será adicionado após a gravação_ |
