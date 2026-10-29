# Parte B — Testes automatizados (Katalon Recorder)

Os testes funcionais do portal web do KuraVet são executados com o **Katalon Recorder** (Record & Playback).
Os casos ficam em [`katalon/`](katalon/) no formato Selenese HTML, que o Katalon Recorder abre nativamente.
Os mesmos passos estão em [`selenium/kuravet-portal.side`](selenium/kuravet-portal.side) para quem preferir o
Selenium IDE.

🎥 Vídeo da execução: https://youtu.be/8PCD84cKgsU

## Casos de teste

| Caso | Arquivo | Objetivo | PBI |
|---|---|---|---|
| CT-01 | `ct01-login-veterinario.html` | Login válido abre a área da clínica; logout encerra a sessão | 7.1 |
| CT-02 | `ct02-login-invalido.html` | Senha incorreta exibe "Usuário ou senha inválidos." e não autentica | 7.1 |
| CT-03 | `ct03-bloqueio-por-perfil.html` | Tutor em `/portal/solicitacoes` recebe "Acesso não permitido para o seu perfil" | 7.1 |
| CT-04 | `ct04-aprovar-solicitacao.html` | Solicitação aprovada passa para AGENDADA e sai da fila | 7.3 |
| CT-05 | `ct05-recusar-com-validacao.html` | Motivo com menos de 10 caracteres é rejeitado; motivo válido registra a recusa | 7.3 |
| CT-06 | `ct06-emitir-diagnostico.html` | Diagnóstico com menos de 15 caracteres é rejeitado; válido encerra como REALIZADA | 7.4 |
| CT-07 | `ct07-triagem-ia.html` | Relato curto é rejeitado; relato grave gera classificação de urgência e orientação | 6.6 |

## Dados controlados

| Dado | Valor |
|---|---|
| Veterinário | `veterinario` / `vet123` (carga inicial, migration V2) |
| Tutor | `tutor` / `tutor123` (carga inicial, migration V3) |
| Pets | Thor (1), Luna (2), Simba (4) — carga inicial, migration V5 |
| Veterinária das solicitações | Dra. Patricia Gomes (1) |
| Tipo de consulta | `Teleconsulta QA - Aprovar`, `... - Recusar`, `... - Diagnostico` (identifica a solicitação de cada teste) |
| Data da consulta | gerada por `storeEval` a cada execução (o sistema não aceita duas consultas ativas do mesmo pet na mesma data) |

São credenciais de teste da massa fictícia da aplicação, não senhas reais.

## Pré-requisitos

- Microsoft Edge ou Google Chrome com a extensão **Katalon Recorder**.
- Portal do KuraVet (`java-advanced`) rodando em `http://localhost:8080` com o banco migrado pelo Flyway.

## Como executar

1. Abra o Katalon Recorder.
2. Clique em **Suíte de Testes Aberta / Open Test Suite** e selecione os 7 arquivos `ct0*.html` da pasta
   [`katalon/`](katalon/).
3. Selecione a suíte e clique em **Play Test Suite**.
4. Confira o painel **Log**: cada caso termina com "Test case passed / Caso de teste aprovado".

Se o navegador traduzir a página do Katalon automaticamente, desative a tradução (botão direito → Mostrar
original) para que os comandos apareçam em inglês.
