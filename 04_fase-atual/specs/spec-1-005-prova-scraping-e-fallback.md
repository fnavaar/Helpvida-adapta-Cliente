# SPEC-1-005 — Prova controlada de scraping e fallback reconciliável

**Fase:** 1  
**Status:** bloqueada até permissão e contrato técnico  
**Dono:** Fernando/TI; autorização por patrocinador + TI + jurídico/DPO; homologação por Auditoria/Faturamento  
**Origem no escopo:** DC-02, AC-011/AC-012 (v2); Fase 1 — conector de scraping e gate  
**Degrau da solução:** reuso controlado — avaliar a rota de scraping já usada pela operação, sem assumir que é API, permissão ou integração estável.

## Contexto e decisões fechadas

- **Estado atual:** Fernando relatou extração por scraping; não há API confirmada nem contrato técnico anexado.
- **Estado desejado:** somente após autorização, uma PoC limitada lê o relatório permitido, registra origem/versão, detecta quebra e reconcilia com entrada assistida; falha nunca é silenciosa.
- **Decisões fechadas:** scraping não decide cobrança nem envia; fallback obrigatório; sem prova/permissão a task não executa.
- **Bloqueios:** `B-SCRAPE-01` autorização/termos/conta de teste/escopo permitido; `B-SCRAPE-02` URL/relatório/campos/frequência/limites; `B-SECRET-01` cofre e rotação; `B-DADOS-01` para dado real.

## Resultado observável

Com fonte de teste autorizada, uma execução cria lote identificado e reconciliável; alteração simulada de estrutura ou indisponibilidade gera alerta e nenhuma ocorrência é marcada como completa; o operador consegue concluir pelo fallback da SPEC-1-002 sem duplicar.

## Limites e dependências

- **Inclui:** leitura limitada; autenticação autorizada; segredo fora do código/log; timeout/retry com backoff; idempotência; fingerprint de estrutura; lote; quarentena; reconciliação; kill switch.
- **Fora:** contornar CAPTCHA/MFA/controle, coleta além da permissão, escrita/envio no portal, alta frequência, API futura, decisão financeira.
- **Entradas:** autorização escrita e contrato de teste; conta não produtiva quando disponível; mapeamento homologado.
- **Saídas:** lote, registros de origem, diagnóstico, alerta e relatório de reconciliação.
- **Superfícies/arquivos/configurações afetadas:** adaptador isolado, cofre/referência de segredo, scheduler limitado, lote/quarentena, monitoramento e testes; caminhos físicos dependem de `B-ENV-01`.
- **Risco e plano B:** bloqueio/quebra/mudança → desligar conector e usar entrada assistida; nenhuma tentativa agressiva.
- **Rollback:** kill switch; revogar/rotacionar segredo; invalidar lote não reconciliado.

## Contrato de integração

| Item | Exigência antes da execução |
|---|---|
| origem | URL/relatório e responsável nomeados |
| permissão | autorização e termos avaliados |
| autenticação | método permitido, conta de teste e segredo em cofre |
| campos | contrato origem→ocorrência homologado |
| frequência | limite explícito; sem varredura livre |
| timeout/retry | valores definidos na task conforme restrição da fonte; sem retry infinito |
| idempotência | chave do lote + origem + competência + hash |
| mudança | fingerprint/schema e teste de canário |
| erro | quarentena + alerta + fallback |

| Regra | Condição | Resultado |
|---|---|---|
| RN-501 | qualquer bloqueio B-SCRAPE/B-SECRET | não executar conector |
| RN-502 | estrutura/campo obrigatório mudou | parar lote, alertar, quarentenar |
| RN-503 | 401/403/429/CAPTCHA/MFA | parar imediatamente, quarentenar o lote e alertar; não contornar nem repetir automaticamente |
| RN-504 | item já entrou por fallback | reconciliar por chave/hash; não duplicar |
| RN-505 | lote incompleto | não marcar ocorrências como completas/aprovadas |
| RN-506 | timeout transitório | aplicar somente o retry limitado homologado no contrato técnico; na ausência dele, parar, quarentenar e alertar |

## Fluxo

1. Validar autorização, conta, campos, frequência e cofre.
2. Rodar canário sem escrita operacional.
3. Se compatível, criar lote e ler recorte de teste.
4. Validar contrato; itens inválidos ficam em quarentena.
5. Reconciliar com ocorrências/fallback.
6. Simular quebra e provar alerta/kill switch.
7. Auditoria/Faturamento valida somente completude/formato do recorte.

## Checklist de execução

- [ ] Todos os bloqueios resolvidos e anexados.
- [ ] Segredo fora de código/log/evidência.
- [ ] Canário, 401/403/429, timeout e mudança estrutural testados.
- [ ] Fallback e reconciliação demonstrados.
- [ ] Kill switch e rotação exercitados.
- [ ] Nenhum envio/escrita externa realizado.

## Critérios de aceite

- [ ] **CA-1-025:** nenhuma execução ocorre sem autorização e contrato técnico anexados.
- [ ] **CA-1-026:** segredo não aparece em código, configuração versionada, log ou evidência.
- [ ] **CA-1-027:** mudança/indisponibilidade interrompe o lote e alerta; não existe quebra silenciosa.
- [ ] **CA-1-028:** retry é limitado e idempotente.
- [ ] **CA-1-029:** fallback conclui a entrada e a reconciliação impede duplicidade.
- [ ] **CA-1-030:** conector não escreve, envia, aprova nem decide cobrança.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | contrato ausente | tentar iniciar sem autorização/campos/segredo seguro | execução recusada | log sanitizado do gate |
| GREEN | canário autorizado | executar fixture/fonte de teste e reconciliar | lote único, campos válidos, origem completa | relatório do lote |
| REFACTOR/REGRESSÃO | quebra e limite | simular mudança, timeout, 401/403/429 e duplicidade | parada/alerta/fallback; sem contorno/duplicação | relatório de resiliência |

**Dados/fixtures:** fonte simulada até autorização; dado real mínimo somente após política.  
**Evidência exigida:** autorizações referenciadas, testes de resiliência, reconciliação e scan de segredo.

## Handoff e operação

- **Como demonstrar:** canário, quebra simulada, kill switch e fallback.
- **Como operar:** TI monitora; Auditoria/Faturamento reconcilia exceções.
- **Como monitorar:** sucesso por lote, schema drift, latência, quarentena, duplicidade e uso do fallback.
- **Pendência:** SPEC permanece bloqueada até `B-SCRAPE-01/02`, `B-SECRET-01` e, para real, `B-DADOS-01`.

## Instruções de execução para o Ethos

1. **Ler antes:** autorizações `B-SCRAPE-01/02`, cofre `B-SECRET-01`, contrato técnico e SPEC-1-002.
2. **Alterar somente:** adaptador isolado, canário, lote/quarentena, alerta, reconciliação e testes autorizados.
3. **Não alterar:** portal externo, CAPTCHA/MFA, frequência, credencial, campos ou permissões por inferência; nunca escrever/enviar.
4. **Ordem:** provar gate RED → canário → lote mínimo → reconciliação → falhas → kill switch/rotação.
5. **Parar:** 401/403/429/CAPTCHA/MFA, mudança estrutural, segredo exposto, autorização ausente ou limite alcançado.
6. **Estado válido ao parar:** conector desligado, lote em quarentena, segredo revogável e fallback operacional.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T09 | Validar gate e executar canário autorizado do scraping | Fernando/TI | SPEC-1-005 | CA-1-025..026, CA-1-030 | Provar recusa sem contrato; depois executar canário read-only autorizado e scan de segredo | recibo dos gates + relatório canário + scan limpo + prova de ausência de escrita/envio | Independente de F1-T01 por usar canário isolado; B-SCRAPE-01/02, B-SECRET-01 e B-ENV-01 resolvidos; B-DADOS-01 se dado real | Bloqueada — gates de scraping/segredo |
| F1-T10 | Provar quebra detectável, retry limitado e reconciliação com fallback | Fernando/TI | SPEC-1-005 | CA-1-027..029 | Em fixtures, simular mudança estrutural, timeout e 401/403/429/CAPTCHA/MFA; reconciliar item já criado pelo fallback FIX-05. Ocorrência real desses códigos pertence ao ponto de parada de F1-T09 | relatório de resiliência + alerta/quarentena + reconciliação + kill switch | F1-T04 e F1-T09 aceitas; contrato de retry/frequência homologado | Bloqueada — depende F1-T04/F1-T09 |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
