# Template: ADR (`docs/decisions/ADR-NNNN-<slug>.md`)

**Quando registrar um ADR** — a decisão precisa atender a pelo menos um destes:

- afeta mais de um módulo ou componente;
- é difícil ou cara de reverter depois;
- envolve alternativas descartadas que alguém vai perguntar "por que não X" no futuro;
- muda um limite, dependência ou responsabilidade descrito no `ARCHITECTURE.md`.

**Quando não registrar:** escolhas locais, reversíveis ou já cobertas pelas convenções de código do projeto. Consulte `docs/decisions/` antes de uma mudança que toque uma decisão já registrada.

**Convenções:** um arquivo por decisão, numeração sequencial nunca reaproveitada, `Status` e `Data` obrigatórios. **Um ADR nunca é apagado nem reescrito quando a decisão muda**: o antigo passa a `substituído por ADR-NNNN` e o novo registra `substitui ADR-NNNN`. É esse par de campos que distingue decisão vigente de decisão histórica — sem ele, a pasta vira arquivo morto em que decisões velhas e válidas têm a mesma aparência.

```markdown
# ADR-NNNN: <título curto>

- **Status:** proposto | aceito | substituído por ADR-NNNN
- **Data:** <AAAA-MM-DD>
- **Substitui:** <ADR-NNNN, ou "—">

## Contexto
<O problema que motivou a decisão, 2-4 linhas.>

## Decisão
<A decisão tomada, de forma direta.>

## Alternativas consideradas
- <alternativa 1>: descartada por <motivo em 1 linha>.
- <alternativa 2>: descartada por <motivo em 1 linha>.

## Consequências
<Trade-offs aceitos: custo, risco, ganho.>
```

## Exemplo preenchido (ilustrativo)

```markdown
# ADR-0001: Uso de FreeRTOS em vez de bare-metal no módulo de comunicação

- **Status:** aceito
- **Data:** 2026-02-02
- **Substitui:** —

## Contexto
O módulo precisa gerenciar UART, Ethernet e um timer de watchdog
concorrentemente, e o código bare-metal atual já está com lógica de
polling difícil de manter.

## Decisão
Adotar FreeRTOS para esse módulo, com uma task por periférico.

## Alternativas consideradas
- Continuar bare-metal com máquina de estados única: descartado por
  complexidade crescente de manutenção.
- Usar Zephyr: descartado por custo de migração maior que o benefício
  neste módulo específico.

## Consequências
Aumento de ~8KB de flash pelo kernel do RTOS. Ganho de isolamento entre
periféricos e testabilidade de cada task separadamente.
```
