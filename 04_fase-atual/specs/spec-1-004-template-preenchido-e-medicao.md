# SPEC-1-004 — Template pré-preenchido, aprovação interna e medição

**Fase:** 1  
**Status:** planejada; template nominal bloqueado  
**Dono:** Fernando/TI; homologação por Milene + Auditoria/Faturamento + Gestão  
**Origem no escopo:** DH-02, DH-06, DH-10; Fase 1 — template e baseline  
**Degrau da solução:** reuso — preencher o template operacional homologado pela Help Vida sem redesenhá-lo nem criar regra de cobrança.

## Contexto e decisões fechadas

- **Estado atual:** preenchimento manual e custo/tempo sem série comparável consolidada.
- **Estado desejado:** dados conferidos geram versão pré-preenchida do template com prova de origem; execução paralela mede manual versus sistema na mesma coorte.
- **Decisões fechadas:** somente completude/formato; fim do MVP em aprovação interna; métrica norte é tempo mediano origem→aprovação interna; hipótese 14→10 não é meta.
- **Bloqueios:** `B-TEMPLATE-01` arquivo/versão/campos e regras de formato homologados; `B-PILOTO-01`; `B-METRICA-01` responsável, relógios, fonte do tempo/custo manual e janela de comparação.

## Resultado observável

Uma ocorrência integralmente conferida gera um template marcado como pré-preenchido, com versão e rastreabilidade de cada campo; nenhum documento incompleto é liberado. Um painel/relatório compara a mesma coorte manual e sistema sem misturar volume ou período.

## Limites e dependências

- **Inclui:** mapeamento campo→template; versão; validação de presença/formato; prévia; regeneração versionada; aprovação interna; exportação apenas de fixture sintética para destino autorizado da sessão de teste, com ator/instante registrados e marca `NÃO ENVIADO`; eventos e métricas. Dado real não pode ser exportado na Fase 1 sem política e autorização específica do canal/destino.
- **Fora:** envio/e-mail/portal/XML/TISS, aceite da operadora, cálculo de cobrança, glosa, regra clínica, impressão automática.
- **Entradas:** campos conferidos da SPEC-1-003; template homologado; relógios da coorte.
- **Saídas:** template pré-preenchido; manifesto de campos/origens; relatório de baseline e resultado.
- **Superfícies/arquivos/configurações afetadas:** gerador de template, manifesto, tela de aprovação interna, instrumentação/relatório e testes; caminhos físicos ficam bloqueados até `B-ENV-01`.
- **Risco e plano B:** template muda → bloquear nova geração, manter versão anterior identificada e voltar ao preenchimento manual.
- **Rollback:** invalidar versão gerada sem apagar trilha; regenerar somente após nova homologação.

## Dados e métricas

| Métrica | Início | Fim | Agregação | Regra |
|---|---|---|---|---|
| tempo mediano | disponibilidade registrada da origem | aprovação interna | mediana por coorte | manual e sistema na mesma modalidade/documento |
| correção de campos | sugestão inicial | valor final | % por campo | excluir campos sem sugestão do denominador e reportá-los à parte |
| retrabalho | retorno a `corrigir` | nova conferência | taxa por ocorrência | motivo obrigatório |
| primeira conferência | abertura da conferência | aprovado sem retorno | proporção | mesma coorte |
| custo de coleta | ocorrência | entrega da origem | unidade monetária e composição definidas em `B-METRICA-01` | fonte e responsável registrados; não misturar unidades |

| Regra | Condição | Resultado |
|---|---|---|
| RN-401 | obrigatório não conferido | não gerar versão liberável |
| RN-402 | template/versão divergente | bloquear e solicitar homologação |
| RN-403 | regeneração após correção | criar nova versão; preservar anterior como substituída |
| RN-404 | coortes/períodos diferentes | relatório sinaliza incomparável; não calcula ganho conclusivo |
| RN-405 | documento aprovado internamente | marcar explicitamente `NÃO ENVIADO À OPERADORA` |

## Fluxo

1. Conferente conclui a revisão da SPEC-1-003.
2. Sistema valida completude/formato contra versão homologada.
3. Gera prévia + manifesto de origem.
4. Conferente compara e aprova internamente ou devolve a `corrigir`.
5. Sistema fecha relógios e atualiza relatório comparável.
6. Demonstração deixa claro que não houve envio/aceite externo.

## Checklist de execução

- [ ] Template e mapeamento assinados por Auditoria/Faturamento.
- [ ] Versão exibida no documento e manifesto.
- [ ] Incompleto, formato inválido e mudança de versão testados.
- [ ] Relógios manual/sistema definidos e reconciliados.
- [ ] Aviso `NÃO ENVIADO` visível.
- [ ] Relatório não contém hipótese 14→10 como resultado.

## Critérios de aceite

- [ ] **CA-1-019:** somente campos conferidos alimentam o template.
- [ ] **CA-1-020:** cada campo do template rastreia valor final e origem.
- [ ] **CA-1-021:** incompletude/formato inválido bloqueiam aprovação interna.
- [ ] **CA-1-022:** versão/regeneração são rastreáveis e reversíveis.
- [ ] **CA-1-023:** saída distingue pré-preenchido/aprovado internamente de enviado/aceito externamente.
- [ ] **CA-1-024:** relatório compara coorte e janela equivalentes e apresenta baseline, resultado, volume e limitações.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | template não mapeado | gerar com fixture conferida | ausência/mapeamento errado é detectado | diff RED |
| GREEN | geração homologada | gerar nominal e comparar campo a campo | template/manifesto corretos; aprovação interna possível | diff + versão + aceite |
| REFACTOR/REGRESSÃO | incompleto/versão/coorte | remover obrigatório, trocar versão e misturar períodos | bloqueios explícitos; ganho não calculado indevidamente | suíte/roteiro + relatório |

**Dados/fixtures:** FIX-01 e FIX-03 do índice, enriquecidas apenas pelo mapeamento do template homologado; preservar os IDs ponta a ponta.  
**Evidência exigida:** diff esperado×gerado, manifesto, relatório comparativo e aceite humano.

## Handoff e operação

- **Como demonstrar:** gerar, devolver para correção, regenerar e mostrar relatório.
- **Como operar:** Auditoria/Faturamento homologa template e aprova; Gestão valida comparação.
- **Como monitorar:** tempo mediano, correção, retrabalho, primeira conferência e custo de coleta.
- **Pendência:** valores nominais dependem de `B-PILOTO-01`, `B-TEMPLATE-01` e `B-METRICA-01`.

## Instruções de execução para o Ethos

1. **Ler antes:** SPEC-1-003 aceita, template/versionamento e contrato `B-METRICA-01`.
2. **Alterar somente:** mapeamento, gerador, manifesto, aprovação interna, relógios e relatório.
3. **Não alterar:** conteúdo do template homologado, regras de cobrança, canais externos ou status de aceite da operadora.
4. **Ordem:** RED de mapeamento → geração nominal → bloqueios → versionamento → comparação de coorte.
5. **Parar:** template sem versão, campo sem origem, coortes incomparáveis ou destino de exportação não autorizado.
6. **Estado válido ao parar:** versão anterior preservada; preenchimento manual disponível; artefato marcado `NÃO ENVIADO`.

## Tasks vinculadas

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| F1-T07 | Gerar template versionado com manifesto e aprovação interna | Fernando/TI | SPEC-1-004 | CA-1-019..023 | Gerar FIX-01, comparar campo a campo, remover obrigatório em FIX-03, testar formato inválido e trocar versão; devolver, regenerar e provar marca NÃO ENVIADO | diff esperado×gerado + manifesto + versões + aceite Auditoria/Faturamento | F1-T05 aceita; B-TEMPLATE-01 e B-PILOTO-01 resolvidos | Bloqueada — depende F1-T05 e template |
| F1-T08 | Instrumentar e provar comparação manual versus sistema | Fernando/TI | SPEC-1-004 | CA-1-024 | Registrar relógios equivalentes, gerar relatório e testar mistura de coortes/períodos | relatório com baseline, resultado, volume, limitações + teste de incomparabilidade | F1-T07 aceita; B-METRICA-01 resolvido; coorte/janela/fontes/owner homologados | Bloqueada — depende F1-T07 e B-METRICA-01 |

## Emendas

| Data | Origem do sinal | Micro-spec/task | Motivo |
|---|---|---|---|
