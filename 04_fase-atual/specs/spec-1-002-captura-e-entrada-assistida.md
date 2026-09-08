# SPEC-1-002 — Captura e entrada assistida rastreável

**Fase:** 1  
**Status:** planejada  
**Dono:** Fernando/TI; homologação por operador de campo + Auditoria/Faturamento  
**Origem no escopo:** DH-01, DH-04, DH-07, DH-08, DH-10; Fase 1 — ocorrência e fallback  
**Degrau da solução:** construção mínima — fluxo móvel/web responsivo de captura e fila, sem presumir PWA, framework ou fornecedor antes de `B-ENV-01`.

## Contexto e decisões fechadas

- **Estado atual:** documentos são coletados/transportados e redigitados; a reunião de 03/09 propôs captura no ponto de origem.
- **Estado desejado:** operador registra uma ocorrência, anexa material sintético/autorizado, recebe validação de qualidade e envia para conferência com origem rastreável.
- **Decisões fechadas:** piloto paralelo; cinco casas; uma operadora, modalidade e tipo documental; fallback por upload/entrada assistida; sem envio à operadora.
- **Bloqueios:** `B-ENV-01`; `B-PILOTO-01` nomes/códigos das cinco casas, operadora, modalidade, documento, operador e template homologado. Sem `B-DADOS-01`, usar apenas fixture sintética.

## Resultado observável

Em dispositivo de teste, um operador cria ocorrência em até um fluxo contínuo, captura ou envia documento sintético, recebe orientação quando a imagem é insuficiente e entrega a ocorrência em `recebido`, visível na fila do conferente com origem/timestamp.

## Limites e dependências

- **Inclui:** criação; chave opaca; competência; casa/operadora/modalidade/tipo por listas aprovadas; captura/upload; pré-visualização; confirmação; qualidade básica; duplicidade; retry; modo de entrada assistida.
- **Fora:** OCR/IA, regra de cobrança, assinatura, envio externo, portal, pagamentos, expansão além do recorte.
- **Entradas:** cadastros homologados ou fictícios; imagem sintética JPG/PNG/PDF; conta Operador. Tipos, tamanho máximo, resolução mínima e quantidade por ocorrência formam o bloqueio `B-ARQ-01` e devem ser homologados pelo dono antes de gerar a task — o executor não os escolhe.
- **Saídas:** ocorrência em `recebido`; artefato com hash, tipo, tamanho, timestamp e canal de origem; evento de auditoria.
- **Dependências:** SPEC-1-001 para papéis/auditoria; política para dado real.
- **Superfícies/arquivos/configurações afetadas:** tela móvel de entrada, fila do conferente, persistência de ocorrência/artefato e testes; caminhos físicos ficam bloqueados até `B-ENV-01` e devem ser nomeados na task.
- **Risco e plano B:** rede ou câmera ruim → salvar rascunho local/temporário somente se política e plataforma autorizarem criptografia no dispositivo, expiração automática e remoção verificável após sincronização/cancelamento; caso contrário, não persistir localmente e preservar entrada assistida posterior sem cópia oculta.
- **Rollback:** desabilitar captura do piloto e manter entrada assistida; remover fixtures.

## Dados e regras

| Campo | Obrigatório | Validação | Fonte |
|---|---:|---|---|
| occurrence_id | sim | opaco, único | sistema |
| casa | sim | lista do piloto | champion/operação |
| competência | sim | formato homologado | operador |
| operadora/modalidade/documento | sim | valor do recorte | `B-PILOTO-01` |
| artefato | sim | tipo/tamanho/legibilidade mínima | câmera/upload |
| origem | sim | captura ou upload assistido | sistema |
| origem_disponivel_em | sim | instante informado/capturado quando a origem ficou disponível; editável só com motivo auditado | operador/sistema |
| timestamp/hash | sim | gerado, imutável | sistema |

| Regra | Condição | Resultado |
|---|---|---|
| RN-201 | obrigatório ausente | não enviar; destacar campo |
| RN-202 | hash já vinculado à mesma chave/competência | alertar duplicidade e impedir segundo envio sem decisão do conferente |
| RN-203 | qualidade abaixo do limiar técnico | pedir nova captura; permitir encaminhar somente marcado `baixa qualidade` para conferência |
| RN-204 | falha de rede | não confirmar envio; retry idempotente pela chave `casa + competência + tipo_documental + hash_artefato`, sem duplicar ocorrência |

## Fluxo e cenários

1. Operador autentica e escolhe o recorte permitido.
2. Sistema cria rascunho opaco.
3. Operador captura/envia e revisa a prévia.
4. Sistema valida presença, tipo, qualidade e duplicidade.
5. Operador confirma; sistema grava uma vez e mostra protocolo.
6. Conferente encontra a ocorrência em `recebido`.

| Cenário | Condição | Resultado esperado | Recuperação |
|---|---|---|---|
| Principal | fixture legível | protocolo único e fila atualizada | — |
| Baixa qualidade | corte/desfoque fixture | orientação e marcação explícita | recapturar ou encaminhar marcado |
| Duplicidade | mesmo hash/chave | sem segunda ocorrência silenciosa | conferente decide vínculo |
| Offline/timeout | confirmação interrompida | retry não duplica | consultar protocolo/estado |

## Checklist de execução

- [ ] Listas do piloto aprovadas ou fixture declarada.
- [ ] Captura e upload exercitados no dispositivo-alvo de teste.
- [ ] Qualidade baixa, duplicidade e timeout exercitados.
- [ ] Protocolo e fila reconciliados.
- [ ] Nenhum dado real usado sem gate.
- [ ] Se houver rascunho local autorizado, criptografia, expiração e remoção foram provadas; caso contrário, o teste confirma ausência de persistência oculta.

## Critérios de aceite

- [ ] **CA-1-007:** ocorrência válida gera um único protocolo e aparece em `recebido`.
- [ ] **CA-1-008:** origem, hash, timestamp e recorte ficam rastreáveis.
- [ ] **CA-1-009:** obrigatório ausente e arquivo inválido impedem confirmação.
- [ ] **CA-1-010:** baixa qualidade é detectada ou marcada e nunca apresentada como extração confiável.
- [ ] **CA-1-011:** retry e duplicidade não criam duas ocorrências silenciosas.
- [ ] **CA-1-012:** entrada assistida funciona quando captura direta não estiver disponível.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | fluxo ausente | tentar registrar fixtures nominal, ruim e duplicada | não há protocolo/fila confiáveis | relatório RED |
| GREEN | fluxo mínimo | executar nominal e consultar fila/evento | protocolo único e rastreabilidade completa | vídeo/capturas sanitizadas + teste |
| REFACTOR/REGRESSÃO | bordas | repetir com arquivo inválido, baixa qualidade, timeout e retry | erros explícitos; sem perda/duplicação | relatório de regressão |

**Dados/fixtures:** FIX-01, FIX-02, FIX-04 e FIX-05 do índice; os mesmos IDs seguem para as SPECs posteriores.  
**Caminhos de erro obrigatórios:** sessão expirada, papel ausente, obrigatório vazio, arquivo acima/fora do contrato, baixa qualidade, duplicidade, timeout e retry.  
**Evidência exigida:** protocolo, evento auditado e demonstração em dispositivo de teste.

## Handoff e operação

- **Como demonstrar:** nominal, baixa qualidade e retry.
- **Como operar:** operador captura; conferente decide exceções.
- **Como monitorar:** taxa de falha, baixa qualidade, duplicidade e tempo captura→recebido.
- **Pendência:** recorte nominal depende de `B-PILOTO-01`.

## Instruções de execução para o Ethos

1. **Ler antes:** SPEC-1-001 aceita, contrato `B-ARQ-01`, recorte do piloto e fixture indexada.
2. **Alterar somente:** tela de entrada, protocolo, fila, validações e testes sintéticos.
3. **Não alterar:** extração IA, template, scraping, regra de cobrança ou envio externo.
4. **Ordem:** RED → rascunho/protocolo → arquivo/qualidade/idempotência → fila → bordas/regressão.
5. **Parar:** contrato de arquivo ausente, persistência local insegura, dado real ou superfície não nomeada na task.
6. **Estado válido ao parar:** entrada assistida continua disponível; nenhum envio duplicado; rascunhos removidos.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T03 | Implementar entrada assistida com protocolo e fila | Fernando/TI | SPEC-1-002 | CA-1-007..009 | Executar FIX-01 e FIX-04 mais cenário com obrigatório vazio: criar uma ocorrência, rejeitar obrigatório/arquivo inválido e localizar um único protocolo na fila | capturas sanitizadas + protocolo + evento de auditoria + teste | F1-T01 aceita; B-ARQ-01 homologado; usar exclusivamente recorte sintético até B-PILOTO-01 e B-DADOS-01 | Bloqueada — depende F1-T01 e B-ARQ-01; recorte sintético |
| F1-T04 | Provar baixa qualidade, retry, duplicidade e fallback de captura | Fernando/TI | SPEC-1-002 | CA-1-010..012 | Executar FIX-02/FIX-05, timeout e captura indisponível; repetir confirmação com mesma chave | relatório de regressão + protocolos reconciliados + prova de ausência/remoção de rascunho | F1-T03 aceita; dispositivo-alvo de teste disponível | Bloqueada — depende F1-T03 |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
