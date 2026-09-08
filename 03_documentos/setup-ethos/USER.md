# USER — contexto operacional

## Empresa e champion

- **Empresa:** Help Vida Home Care e Remoções Ltda — CONFIRMADO.
- **Champion:** Fernando, gestor de TI — CONFIRMADO na reunião de 03/09 e no escopo definitivo.
- **Aprovadores:** Milene, Auditoria e Faturamento para operação/regras; Gestão para baseline/expansão; TI + jurídico/DPO para dados — CONFIRMADO no escopo definitivo.

## Objetivos e processo crítico

- **Objetivo:** reduzir coleta física, redigitação, retrabalho e tempo de preparação de contas sem remover conferência humana.
- **Métrica norte:** tempo mediano entre disponibilidade da origem e aprovação interna do documento pré-preenchido.
- **Métricas secundárias:** custo de coleta por ocorrência, correção de campos, retrabalho, aprovação na primeira conferência e disponibilidade da origem.
- **Processo:** origem/captura → extração assistida → conferência/correção → pré-preenchimento → aprovação interna → fases posteriores de central e custos.

## Ferramentas e fontes de verdade

| Ferramenta/fonte | Uso | Responsável | Status de acesso |
|---|---|---|---|
| Pasta `adapta-cliente` | fase atual, SPECs e tasks | Fernando/TI | criada; forma de acesso no Ethos [VALIDAR] |
| GitHub | versionamento e evidências de construção | Fernando/TI | citado na reunião; repo da solução não criado/autorizado |
| Skip | construção visual | Fernando/TI | Fernando declarou ter acesso; projeto/ambiente [VALIDAR] |
| Banco/persistência | ocorrências, eventos e artefatos | TI | plataforma [VALIDAR]; nenhum acesso presumido |
| Provedor de extração/IA | Estágio B da PoC | TI + aprovadores | `B-IA-01` não resolvido |
| Solus/Unimed por scraping | fonte candidata | TI + jurídico/DPO | bloqueado por `B-SCRAPE-01/02` e `B-SECRET-01` |

## Preferências de trabalho

- **Comunicação:** direta, técnica e por evidência — [VALIDAR NA CALL DE SETUP].
- **Cadência:** uma task por vez, teste humano antes da próxima — CONFIRMADO.
- **Formato de entrega:** task + SPEC + prova + evidência + estado final.

## Restrições e decisões que exigem validação

- Política de dados reais, retenção, exclusão, processamento externo e incidente.
- Ambiente/repositório, matriz de acessos e gestão de segredos.
- Cinco casas, operadora, modalidade, documento e operadores do piloto.
- Contrato de arquivos, template/dicionário e baseline.
- Provedor, custo, quota e autorização da IA.
- Permissão, contrato técnico, frequência e retry do scraping.
- Nome do assistente, canais de comunicação e autonomia dos loops.
