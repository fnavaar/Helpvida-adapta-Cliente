# SPEC-1-003 — Extração assistida e conferência humana

**Fase:** 1  
**Status:** planejada; provedor e dado real bloqueados  
**Dono:** Fernando/TI; homologação por Auditoria/Faturamento  
**Origem no escopo:** AC-014 (v2), DH-02, DH-04, DH-09; Fase 1 — IA e conferência  
**Degrau da solução:** construção mínima sobre capacidade de extração formalmente aprovada — a SPEC define o contrato e a PoC, mas não escolhe modelo/provedor nem envia conteúdo a terceiro sem avaliação e autorização.

## Contexto e decisões fechadas

- **Estado atual:** transcrição/redigitação manual; capacidade em manuscrito/foto não foi provada nas fontes.
- **Estado desejado:** uma PoC extrai somente os campos homologados, mostra origem e confiança por campo e exige confirmação/correção humana antes de avançar.
- **Decisões fechadas:** IA sugere; humano decide; piloto valida completude/formato, não cobrança; baixa qualidade não segue silenciosamente.
- **Bloqueios:** `B-IA-01` provedor/modelo, contrato, região, retenção, unidade de custo, orçamento/quota máxima da PoC e autorização; `B-TEMPLATE-01` campos do documento/template; `B-DADOS-01` para amostra real. **Estágio A** usa FIX-06 e extrator simulado determinístico sem provedor; **Estágio B** executa o motor real somente após decisão humana que resolva `B-IA-01`.

## Resultado observável

Conferente abre ocorrência sintética, vê imagem e campos sugeridos lado a lado, identifica confiança/origem, corrige valores, informa motivo quando exigido e aprova internamente apenas quando todos os obrigatórios foram conferidos. O sistema mede acerto bruto e correções.

## Limites e dependências

- **Inclui:** fila; execução assíncrona idempotente; estados; mapeamento campo→região/origem; confiança; campo não encontrado; edição; motivo; versão da extração; métricas.
- **Fora:** diagnóstico, interpretação clínica, regra de cobrança, aprovação automática, treinamento com dado do cliente, envio externo.
- **Entradas:** occurrence_id e artefato da SPEC-1-002; dicionário homologado; fixture.
- **Saídas:** campos sugeridos/conferidos, status, correções e métricas.
- **Superfícies/arquivos/configurações afetadas:** worker/adaptador de extração, tela de conferência, persistência de sugestões/revisões e testes; caminhos físicos ficam bloqueados até `B-ENV-01`.
- **Risco e plano B:** indisponibilidade/baixa precisão → conferência e preenchimento manual; nunca bloquear acesso à origem.
- **Rollback:** desligar extração assistida e manter a mesma fila em modo manual.

## Contrato lógico da extração

| Campo | Tipo | Origem | Estado permitido | Observação |
|---|---|---|---|---|
| field_key | enum homologado | dicionário | sugerido/conferido/corrigido/não_encontrado | sem chaves inventadas |
| value | texto estruturado | extração/humano | — | preservar valor original separado |
| source_ref | página/região | artefato | — | abrir prova visual |
| confidence | 0–1 ou classe documentada | motor | — | não equivale a aprovação |
| extractor_version | string | motor/config | — | obrigatório para reprodução |
| execution_key | occurrence_id + artefato_hash + extractor_version | sistema | — | idempotência; timeout/retry limitados são definidos e homologados em `B-IA-01` antes do Estágio B |
| reviewer/reviewed_at | id/timestamp | conferência | — | obrigatório ao aprovar |

| Regra | Condição | Ação |
|---|---|---|
| RN-301 | campo obrigatório não encontrado/não conferido | impedir aprovação |
| RN-302 | valor alterado pelo humano | guardar sugerido, final, ator, instante e motivo conforme matriz |
| RN-303 | confiança abaixo do limiar homologado | destacar e exigir inspeção; nunca preencher como confirmado |
| RN-304 | timeout/erro do motor | marcar falha recuperável e habilitar modo manual |
| RN-305 | mesma ocorrência/versão já processada | retornar resultado existente; não cobrar/processar em duplicidade |

## Fluxo e cenários

1. Ocorrência `recebido` entra na fila.
2. Serviço autorizado processa uma vez e registra versão/duração/custo técnico.
3. Sistema muda para `extraído` ou `falha de extração`, sem aprovação.
4. Conferente vê origem e sugestão, confirma/corrige cada obrigatório.
5. Sistema muda para `em conferência`; pendência leva a `corrigir`; conjunto completo pode ir a `aprovado internamente`.
6. Métricas comparam sugestão e valor final sem expor conteúdo no relatório.

## Cenários obrigatórios

| Cenário | Condição | Resultado esperado | Recuperação |
|---|---|---|---|
| Principal | FIX-01/FIX-06 | sugestões com origem/confiança; revisão humana completa | — |
| Limite | FIX-02 confiança baixa | destaque e bloqueio de confirmação implícita | revisão manual |
| Falha | FIX-03 ou timeout simulado | obrigatório ausente/erro explícito; ocorrência preservada | modo manual e retry limitado quando homologado |
| Duplicidade | mesma execution_key | resultado existente, sem novo processamento/custo | consultar execução anterior |

## Checklist de execução

- [ ] `B-IA-01` e dicionário resolvidos antes de provedor externo.
- [ ] Fixtures nominal, manuscrita difícil, incompleta e inválida preparadas.
- [ ] Origem e confiança exibidas por campo.
- [ ] Modo manual funciona com motor indisponível.
- [ ] Correções e versões auditadas.
- [ ] Métricas calculadas sem conteúdo sensível.

## Critérios de aceite

- [ ] **CA-1-013:** nenhum campo é considerado conferido sem ação humana identificada.
- [ ] **CA-1-014:** cada sugestão mostra referência à origem e versão do extrator.
- [ ] **CA-1-015:** obrigatório ausente impede aprovação interna.
- [ ] **CA-1-016:** correção preserva valor sugerido, final, ator e instante.
- [ ] **CA-1-017:** erro/timeout permite concluir manualmente sem perder a ocorrência.
- [ ] **CA-1-018:** relatório da PoC apresenta tamanho da amostra, acerto/correção por campo, falhas, duração e custo, sem prometer precisão antes da medição.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | contrato de sugestão ausente | no Estágio A, submeter FIX-06 antes do adaptador simulado | campos/origem/versão ausentes ou inconsistentes | baseline RED |
| GREEN | sugestão supervisionada | no Estágio A, processar FIX-01/FIX-06, revisar e aprovar; repetir no Estágio B só após `B-IA-01` | todos os obrigatórios conferidos; trilha completa | teste do simulador; relatório PoC quando Estágio B |
| REFACTOR/REGRESSÃO | erro e confiança | processar difícil/incompleta; simular timeout/retry | destaque, bloqueio e fallback manual; sem duplicação | relatório de borda |

**Dados/fixtures:** FIX-01, FIX-02, FIX-03 e FIX-06 do índice. Amostra real futura deve ser autorizada, minimizada e registrada.  
**Evidência exigida:** relatório PoC reproduzível, demonstração e aceite de Auditoria/Faturamento.

## Handoff e operação

- **Como demonstrar:** sugestão nominal, correção, obrigatório ausente e indisponibilidade.
- **Como operar:** conferente revisa tudo; TI monitora falha/custo/versão.
- **Como monitorar:** correção por campo, falha, tempo, custo e drift por versão.
- **Pendência:** limiar de qualidade/aceite será definido a partir da PoC, aprovado por Auditoria/Faturamento; não é inventado agora.

## Instruções de execução para o Ethos

1. **Ler antes:** SPEC-1-001/002, dicionário homologado, FIX-01/02/03/06 e bloqueios de IA.
2. **Alterar somente:** adaptador simulado/real autorizado, sugestões, tela de conferência, métricas e testes.
3. **Não alterar:** valores conferidos sem humano, cobrança, envio, treinamento ou provedor não aprovado.
4. **Ordem:** Estágio A RED→GREEN→bordas; parar; Estágio B somente após decisão `B-IA-01`, repetindo a suíte.
5. **Parar:** dicionário ausente, orçamento/contrato indefinido, conteúdo externo não autorizado ou obrigatório sem origem.
6. **Estado válido ao parar:** ocorrência acessível e concluível manualmente; nenhuma sugestão marcada como aprovada.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T05 | Implementar Estágio A simulado de extração e conferência | Fernando/TI | SPEC-1-003 | CA-1-013..017 | Processar FIX-01/02/03/06 com adaptador determinístico; verificar source_ref e extractor_version por campo; revisar, corrigir, bloquear obrigatório ausente e simular timeout | suíte do simulador + trilha sugerido→final + demonstração de modo manual | F1-T01 e F1-T03 aceitas; dicionário B-TEMPLATE-01 homologado para campos sintéticos | Bloqueada — depende F1-T01/F1-T03 e dicionário |
| F1-T06 | Executar PoC autorizada do motor de extração | Fernando/TI | SPEC-1-003 | CA-1-018 | Repetir suíte do Estágio A no motor aprovado e medir amostra, acerto/correção por campo, falhas, duração e custo | relatório PoC reproduzível + versão/config + aceite Auditoria/Faturamento | F1-T05 aceita; B-IA-01 resolvido; B-DADOS-01 se amostra real; orçamento/quota definidos | Bloqueada — depende F1-T05 e B-IA-01 |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
