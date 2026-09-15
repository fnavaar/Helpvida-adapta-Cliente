# AP-2026-09-15-0955 — Escopo de componentes em React

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: F1-T01 / `04-fase-atual/specs/spec-1-001-controles-de-acesso-e-dados.md`
- Sinal: a prévia ficou em branco quando um bloco de UI foi inserido no componente `Login` com referências a estados e funções definidos somente em `Dashboard`.
- Evidência: Debug Summary `06_notas/debug/debug-2026-09-15-f1-t01.md`; prévia v0.0.4 sem conteúdo; prévia v0.0.5 com login renderizado; QA oficial da v0.0.5 passou.
- Regra reutilizável: ao adicionar uma prova ou controle de uma superfície, manter o bloco no componente que possui seus estados e callbacks; executar uma verificação de renderização na prévia além do build.
- Quando aplicar: toda alteração de UI React que move controles entre componentes ou introduz callbacks/estado local.
- Quando não aplicar: componentes que recebem explicitamente todos os estados e callbacks por props e foram validados por teste de renderização equivalente.
- Confiança: alta — causa reproduzida, corrigida e verificada em duas versões observáveis.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.
