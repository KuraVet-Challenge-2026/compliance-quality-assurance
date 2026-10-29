# Parte A — Testes manuais

O plano de testes manuais está no **Azure Boards** como work items do tipo **Test Case**, na Sprint 4.
Cada caso tem **objetivo, dados de entrada, dados de saída esperados e passos**, e está vinculado
(*Tested By*) ao Product Backlog Item que valida.

🔗 **Azure Boards:** https://dev.azure.com/KuraVet/KuraVet

Esta página é um índice para consulta rápida. Em caso de divergência, **o Azure Boards é a fonte oficial**.

## Casos de teste

| Caso | Work item | Título |
|---|---|---|
| TC01 | [#237](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/237) | Login do tutor com credenciais válidas |
| TC02 | [#238](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/238) | Login do tutor com senha incorreta |
| TC03 | [#239](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/239) | Cadastrar pet com código válido |
| TC04 | [#240](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/240) | Bloquear pet com código duplicado |
| TC05 | [#241](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/241) | Buscar veterinários disponíveis |
| TC06 | [#242](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/242) | Solicitar teleconsulta com pet cadastrado |
| TC07 | [#243](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/243) | Impedir solicitação sem pet cadastrado |
| TC08 | [#244](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/244) | Cancelar consulta em status SOLICITADA |
| TC09 | [#245](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/245) | Bloquear cancelamento de consulta REALIZADA |
| TC10 | [#246](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/246) | Triagem por IA — relato de emergência |
| TC11 | [#247](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/247) | Triagem por IA — relato de rotina |
| TC12 | [#248](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/248) | Registrar check-in com valores válidos |
| TC13 | [#249](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/249) | Recusar check-in com valor fora da faixa |
| TC14 | [#250](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/250) | Acessar o portal com perfil veterinário |
| TC15 | [#251](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/251) | Aprovar solicitação SOLICITADA para AGENDADA |
| TC16 | [#252](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/252) | Recusar solicitação com motivo |
| TC17 | [#253](https://dev.azure.com/KuraVet/KuraVet/_workitems/edit/253) | Registrar diagnóstico e concluir consulta |

Os casos TC14 a TC17 e TC10 também estão automatizados (Parte B) — ver
[`../testes-automatizados/README.md`](../testes-automatizados/README.md).
