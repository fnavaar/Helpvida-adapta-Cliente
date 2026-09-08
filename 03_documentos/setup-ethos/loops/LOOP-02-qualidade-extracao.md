# LOOP-02 — Qualidade da extração

**Status:** RASCUNHO — validar na call de setup  
**Fase de origem:** Fase 4  
**Sistemas usados:** F1 extração, conferência e template  
**Agente responsável:** [VALIDAR NA CALL] — analista especializado ou assistente principal, conforme compatibilidade de acesso/contexto

## 1. Meta

- **O que atingir:** reduzir correções sem remover revisão humana.
- **Prazo do ciclo:** [VALIDAR NA CALL DE SETUP].
- **Valor estimado em R$:** não estimado; sem fonte aprovada.

## 2. Validação

- **Como saber que foi atingido:** percentual de campos corrigidos após sugestão, métrica secundária confirmada no escopo definitivo; denominador e alvo [VALIDAR NA CALL].
- **Modo:** o assistente mede; humano valida no fim.
- **Fonte independente:** métricas por campo, versão e coorte — disponibilidade e consulta [VALIDAR]. **[INFERÊNCIA — independência a declarar na call: hoje a fonte é o próprio sistema monitorado; independência real exige log append-only, export auditado ou amostragem humana]**
- **Onde está hoje:** [VALIDAR BASELINE NA CALL DE SETUP].
- **Alvo:** [VALIDAR ALVO NA CALL DE SETUP].
- **Unidade:** %.
- **Cadência de medição:** [VALIDAR NA CALL DE SETUP].
- **Quem valida:** responsável nomeado por Auditoria/Faturamento, no encerramento de cada ciclo [VALIDAR NOME].
- **Veredito:** atingido somente se o valor no prazo cumprir o alvo, sem violar controles de dado, revisão humana ou qualidade.

## 3. Conectores

| Conector | Dado/fonte | Permissão mínima | Uso no loop | Plano B | Status |
|---|---|---|---|---|---|
| Fonte de métricas do sistema | IDs opacos, estados, timestamps e versão | leitura do recorte; sem conteúdo clínico | medir e detectar desvio | relatório manual sanitizado | VALIDAR |
| Canal interno de alerta | ID, categoria, owner e prazo | publicar apenas no canal aprovado | avisar exceção | fila/painel manual | VALIDAR |

## 4. Skills

| Skill | Uso no loop | Entrada | Saída | Obrigatória? |
|---|---|---|---|---|
| medição/revisão operacional [VALIDAR DISPONIBILIDADE] | calcular métrica e comparar ciclo | consulta sanitizada | relatório e veredito proposto | obrigatória; se indisponível, aprovar rotina manual equivalente na call |
| diagnóstico [VALIDAR DISPONIBILIDADE] | investigar desvio sem alterar produção | erro, versão, eventos | causa provável e próxima prova | recomendada |

## 5. Arranque

### Instruções permanentes do loop

Use somente a fonte aprovada e dados mínimos. Meça percentual de campos corrigidos após sugestão. Não altere conta, campo, regra, prioridade, prazo ou acesso. Quando a fonte estiver incompleta ou houver risco de dado sensível, pause e peça validação. Registre evidência sanitizada, comparação com baseline e aprendizado específico; o veredito final é humano.

### Primeiras tarefas

1. Confirmar fonte, acesso, baseline, alvo, prazo, cadência e validador.
2. Executar uma volta assistida sobre massa/coorte autorizada.
3. Comparar, registrar evidência e ajustar somente configuração aprovada.

- **Perseguir a meta sozinho:** não.
- **Limites de autonomia:** sem escrita em produção, mensagem externa, dado clínico em alerta, aprovação operacional ou alteração de regra.
- **Teto de créditos do ciclo:** [VALIDAR NA CALL DE SETUP].
- **Condição de pausa:** o loop pausa quando versão, denominador ou coorte não forem comparáveis; Auditoria/Faturamento decide retomada.

## Dependências e riscos

- **Pré-condições:** sistemas citados aceitos; política e acessos aprovados; baseline e alvo preenchidos; **métricas estáveis (gate da Fase 4 do escopo)**.
- **Riscos:** alerta incorreto, métrica enviesada, exposição de dado, automação antes da maturidade.
- **Rollback/recuperação:** desativar análise automatizada, manter conferência manual e preservar o último relatório sanitizado válido.
- **SPEC da fase 4:** SPEC-4-002 — planejada; ainda não gerada.

## Evidências de origem

- `02-Escopo-Definitivo.md`, Fase 4 — loops candidatos e limites.
- Fases 1–3 do escopo e SPECs aplicáveis.
