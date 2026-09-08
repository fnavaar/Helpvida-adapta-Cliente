# Sugestões de conectores e automações — Help Vida

> RASCUNHO. Sugestão não confirma disponibilidade nem autoriza instalação. Conectar somente na call ou após gate humano específico.

| Sugestão | Tipo | Prioridade | Necessidade/evidência | Dado acessado | Permissão mínima | Consumidor | Responsável | Momento | Risco | Alternativa sem conector | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|
| GitHub | conector | necessário | versionar construção/evidências; reunião 03/09 | código, commits e checks; sem dado clínico | leitura/escrita apenas no repo autorizado | assistente principal | Fernando/TI | antes de F1-T01, após repo criado e autorizado | push/alteração indevida | upload manual de patch e evidência | VALIDAR NA CALL |
| Plataforma de construção (Skip) | conector/ferramenta | necessário | TI executa sistema; reunião 03/09 | projeto, preview e logs técnicos | projeto específico, sem produção | assistente principal | Fernando/TI | após `B-ENV-01` | ação em projeto errado/publicação | execução manual supervisionada | VALIDAR NA CALL |
| Persistência/autenticação | conector técnico | necessário | RBAC, ocorrências e auditoria; SPEC-1-001 | dados sintéticos primeiro; reais só após política | ambiente de teste e menor privilégio | sistema F1–F3 | Fernando/TI | após `B-ENV-01`; real após `B-DADOS-01` | dado sensível/acesso excessivo | fixture local temporária e removível | PLATAFORMA A VALIDAR |
| Provedor de extração visual | conector/API | futuro condicionado | Estágio B da SPEC-1-003 | imagem/documento autorizado e campos extraídos | chamada limitada, sem treino/retenção não aprovada | sistema F1 + loop qualidade | TI + jurídico/DPO | somente após `B-IA-01` | retenção, erro, custo, transferência | extrator simulado + conferência manual | BLOQUEADO |
| Fonte Solus/Unimed | integração read-only | futuro condicionado | origem candidata; SPEC-1-005 | relatório permitido | leitura mínima, conta de teste, segredo em cofre | sistema F1 + loop origem | TI + jurídico/DPO | após `B-SCRAPE-01/02` e `B-SECRET-01` | quebra, bloqueio, termos, segredo | captura/upload assistido | BLOQUEADO |
| Alertas internos | automação | recomendado | falha de origem, qualidade, pendência ou SLA na Fase 4 | IDs opacos, estado, prazo; sem conteúdo clínico | canal/grupo específico | loops 01/02/03/04 | [VALIDAR RESPONSÁVEL] | [VALIDAR NA CALL] | exposição e fadiga | fila/painel revisado manualmente | VALIDAR NA CALL |

## Prova mínima antes de conectar

Cada item em `VALIDAR` só muda para `PROVADO` após teste read-only no recurso correto, com conta e escopo autorizados, evidência sanitizada, timebox e resultado `PROVADO` ou `REJEITADO`. Falha, recurso errado ou permissão excessiva mantém o item desconectado.

## Automações candidatas

| Automação | Gatilho | Frequência | Parada/erro | Ação permitida | Estado |
|---|---|---|---|---|---|
| Canário da origem | janela aprovada | [VALIDAR] | 401/403/429/CAPTCHA/MFA/schema drift → parar, quarentenar e alertar | leitura mínima; nunca contornar ou enviar | futuro |
| Revisão de pendências | itens abertos | [VALIDAR] | dado/prazo/owner ausente → listar para humano | lembrar e escalonar conforme alçada aprovada | futuro |
| Relatório de qualidade | lote/coorte encerrada | [VALIDAR] | coortes incomparáveis → não emitir ganho | calcular métricas sanitizadas | futuro |
