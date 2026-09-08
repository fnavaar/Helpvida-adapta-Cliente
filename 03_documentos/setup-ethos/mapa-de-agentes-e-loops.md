# Mapa de agentes e loops — Help Vida

**Estado:** RASCUNHO — nenhum agente, conector ou loop ativado.

## Assistente principal

**Missão:** coordenar a execução segura do plano, orientar uma task por vez, conferir TDD/evidência e preparar os loops operacionais sem assumir gates humanos.

| Entrada | Saída | Skills necessárias | Conectores | Limites |
|---|---|---|---|---|
| STATUS, fase.md, SPECs, evidências autorizadas | próximo passo, bloqueio, checklist, relatório sanitizado | leitura de projeto, revisão, debug e medição — disponibilidade [VALIDAR] | GitHub/ambiente apenas após autorização | não executa próxima task sem teste; não usa dado real ou ação externa por inferência |

## Agentes especializados candidatos

| Agente | Missão | Entradas | Saídas | Skills | Conectores | Sistemas/fases usados | Limites | Estado |
|---|---|---|---|---|---|---|---|---|
| Monitor de confiabilidade da origem | detectar quebra, lote parcial, duplicidade e acionamento do fallback | lotes, diagnósticos, eventos técnicos | alerta, quarentena e recomendação de fallback | diagnóstico/monitoramento [VALIDAR] | fonte de eventos + canal interno, após prova | F1 origem/fallback; F2 central | sem contornar acesso ou expor conteúdo; não aprova, envia ou paga | VALIDAR NA CALL |
| Analista de qualidade operacional | medir correção, retrabalho, tempo, custo e regressão | métricas sanitizadas e versões | comparação, anomalias e recomendação | medição/revisão [VALIDAR] | fonte de métricas + canal interno, após prova | F1 extração/template; F2/F3 métricas | não aprova campo, conta, pagamento ou expansão | VALIDAR NA CALL |

**Decisão de desenho:** não criar agente separado para cada loop. O principal só pode operar mais de um loop se missão, permissões, fonte e isolamento forem compatíveis, verificação feita por loop na call. Agentes candidatos só se justificam se volume, acessos ou isolamento exigirem.

## Loops candidatos da Fase 4

| Loop | Meta única | Sistemas F1–F3 | Executor candidato | SPEC F4 planejada | Estado |
|---|---|---|---|---|---|
| LOOP-01 Confiabilidade da origem | eliminar quebra silenciosa | origem/fallback F1; central F2 | monitor de origem ou principal | SPEC-4-001 [INFERÊNCIA — numeração não definida no escopo] | RASCUNHO |
| LOOP-02 Qualidade da extração | reduzir correção mantendo revisão humana | extração/conferência F1 | analista ou principal | SPEC-4-002 [INFERÊNCIA] | RASCUNHO |
| LOOP-03 Pendências e SLA | reduzir tempo parado em pendências | central/fila F2 | principal | SPEC-4-003 [INFERÊNCIA] | RASCUNHO |
| LOOP-04 Saúde operacional | manter ganho de tempo/custo sem degradar qualidade | métricas F1–F3 | analista ou principal | SPEC-4-004 [INFERÊNCIA] | RASCUNHO |

## Relação com a Fase 5

A Fase 5 valida os sistemas das Fases 1–3, os conectores autorizados, os agentes efetivamente configurados e os quatro loops. Loop sem baseline, alvo, fonte independente e responsável pelo veredito permanece não ativado.
