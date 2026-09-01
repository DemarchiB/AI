# ADR-NNNN: <título curto>

## Contexto
<O problema que motivou a decisão, 2-4 linhas.>

## Decisão
<A decisão tomada, de forma direta.>

## Alternativas consideradas
- <alternativa 1>: descartada por <motivo em 1 linha>.
- <alternativa 2>: descartada por <motivo em 1 linha>.

## Consequências
<Trade-offs aceitos: custo, risco, ganho.>

---

## Exemplo

O exemplo abaixo é ilustrativo, de outro projeto de firmware embarcado, para fixar o formato e o nível de detalhe. Não descreve uma decisão real deste repositório.

```markdown
# ADR-0001: Uso de FreeRTOS em vez de bare-metal no módulo de comunicação

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
