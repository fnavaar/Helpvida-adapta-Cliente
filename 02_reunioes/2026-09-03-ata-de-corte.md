# Ata de corte para handoff — Help Vida

**Reunião:** Alinhamento Helpvida <> ADAPTA  
**Data:** 03/09/2026  
**Fonte:** tl;dv `6a996f5d2a24e10013a44347`  
**Participantes identificados:** Navaar Figueiredo e Fernando (gestor de TI da Help Vida)  
**Uso:** corte operacional revalidado para a pasta externa do cliente; não contém transcrição bruta.

## Decisões incorporadas

1. A primeira entrega deve atacar coleta e redigitação: captura de imagens na origem, extração assistida, conferência humana e pré-preenchimento do template.
2. O piloto começa com cinco casas e execução paralela, sem expansão automática.
3. A captura será feita por profissional de operação/enfermeiro visitador, não deixada como responsabilidade da casa.
4. A TI da Help Vida, liderada por Fernando, participará da construção e executará as tasks a partir de SPECs e TDD.
5. A inexistência de API confirmada de Unimed/Solus impede tratá-la como premissa do MVP; o scraping existente é condicionado a permissão, contrato técnico, monitoramento e fallback.
6. O MVP termina em template pré-preenchido e aprovado internamente. Envio à operadora, aceite externo, custos, pagamentos e integração Future ficam fora da Fase 1.
7. A hipótese de reduzir o esforço da equipe deve ser medida por tempo/custo comparável; “14 para 10 pessoas” não é meta automática do sistema.

## Ajustes incorporados após a reunião

- O escopo definitivo foi consolidado em cinco fases com a direção captura/pré-preenchimento primeiro e central determinística depois.
- Checks de escopo e cliente foram aprovados em 08/09/2026.
- Cinco SPECs da Fase 1 foram aprovadas e decompostas em F1-T01..F1-T10.
- A proteção de dados, o ambiente/repositório, o contrato de arquivos, o template, a métrica, o provedor de IA e o scraping permanecem gates explícitos.

## Corte externo autorizado

A pasta do cliente recebe somente a fase atual: tasks F1, SPECs F1, esta ata, orientações operacionais e estado. Não recebe escopo base/definitivo, análise crítica, requisitos, transcrição bruta, fases futuras, `.adapta` ou materiais internos.

## Gate de execução

Nenhuma task está iniciada. F1-T01 é a primeira candidata, bloqueada até `B-ENV-01` e autorização explícita do ambiente/repositório. Após cada task, executar a prova da SPEC e aguardar teste humano antes da próxima.
