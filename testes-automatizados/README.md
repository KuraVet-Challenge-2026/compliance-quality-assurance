# Parte B — Testes Automatizados (Selenium IDE)

Os testes funcionais do portal web (Thymeleaf) do KuraVet são gravados e executados com o
**Selenium IDE**. Os projetos ficam em [`selenium/`](selenium/) no formato `.side`.

## Fluxos cobertos

| # | Fluxo | O que é verificado |
|---|---|---|
| 1 | Login do veterinário | Login com credenciais válidas; mensagem de erro com credenciais inválidas |
| 2 | Fila de solicitações | A fila de solicitações de teleconsulta da clínica é exibida |
| 3 | Aprovar / recusar | Aprovar uma solicitação; recusar informando o motivo |
| 4 | Registrar diagnóstico | Emitir o diagnóstico ao final da teleconsulta |

## Pré-requisitos

- Google Chrome ou Mozilla Firefox atualizado.
- Extensão **Selenium IDE**:
  [Chrome Web Store](https://chromewebstore.google.com/detail/selenium-ide/mooikfkahbdckldjjndioackbalphokd) ·
  [Firefox Add-ons](https://addons.mozilla.org/firefox/addon/selenium-ide/)
- Portal web do KuraVet rodando (local ou homologação) e um usuário veterinário de teste com massa de dados
  fictícia.

## Como abrir e rodar

1. Abra o Selenium IDE pelo ícone da extensão no navegador.
2. Clique em **Open an existing project** e selecione o arquivo `.side` desta pasta `selenium/`.
3. Ajuste a **Base URL** (canto superior) para o endereço do portal, ex.: `http://localhost:8080`.
4. Para rodar tudo, clique em **Run all tests**; para um fluxo só, selecione o teste e clique em
   **Run current test**.
5. Confira o painel **Log** — cada passo deve ficar verde e o teste terminar com `completed successfully`.

### Execução por linha de comando (opcional)

```bash
npm install -g selenium-side-runner chromedriver
selenium-side-runner --base-url http://localhost:8080 testes-automatizados/selenium/*.side
```

## Boas práticas

- Não salve senhas reais nos arquivos `.side` — use apenas o usuário de teste da massa fictícia.
- Salve prints e o link do vídeo da execução em [`../evidencias/`](../evidencias/).
