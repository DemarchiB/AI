# Template: máquina de estado (`docs/design-docs/fsm-<nome>.md`)

**Quando documentar uma máquina de estado** — a máquina precisa atender a pelo menos um destes:

- tem mais de três estados, ou mais de um evento que dispara a mesma transição;
- controla saída física, comunicação ou qualquer comportamento observável do produto;
- é consultada ou alterada por mais de um módulo (mesmo que o dono seja um só);
- implementa comportamento descrito por um requisito em `docs/specs/`.

**Quando não documentar:** máquina local e trivial, com dois estados e uma transição, cujo enum já é autoexplicativo no código.

**Convenções:** um arquivo por máquina; o nome do arquivo, os nomes dos estados e os nomes dos eventos são **os mesmos identificadores usados no código**, sem tradução nem sinônimo — é isso que torna a documentação verificável contra a implementação. A tabela de transição é a fonte da verdade do documento: se o código e a tabela divergirem, um dos dois é defeito, e a divergência não se resolve apagando a linha da tabela. Documento e código mudam na mesma alteração ([firmware.md](../practices/firmware.md), Seção 2).

```markdown
# FSM: <nome>

- **Módulo dono:** <arquivo .c que altera o estado>
- **Requisitos atendidos:** <REQ-NNN, ...  ou "—">
- **Estado inicial:** <ESTADO>
- **Estado seguro / de falha:** <ESTADO, e como se sai dele>

## Propósito
<O que esta máquina controla, em 2-3 linhas. Por que é uma máquina de
estado e não uma sequência direta.>

## Estados
| Estado | Significado | Saídas / efeito enquanto ativo | Tempo limite |
| --- | --- | --- | --- |
| `<ESTADO>` | <o que significa estar aqui> | <o que o produto faz> | <prazo e destino, ou "—"> |

## Eventos
| Evento | Origem | Dado associado |
| --- | --- | --- |
| `<EVENTO>` | <interrupção, tarefa, temporizador, comando externo> | <payload, ou "—"> |

## Tabela de transição
Todo par estado × evento tem destino. Onde nada acontece, escreva
`— (ignorado)` e o motivo; lacuna não é o mesmo que decisão.

| Estado atual | Evento | Condição | Próximo estado | Ação na transição |
| --- | --- | --- | --- | --- |
| `<ESTADO>` | `<EVENTO>` | <guarda, ou "—"> | `<ESTADO>` | <ação, ou "—"> |

## Invariantes
- <o que é sempre verdade, em qualquer estado — tipicamente sobre saídas
  físicas e recursos.>

## Comportamento em falha
<O que acontece diante de evento inesperado, tempo esgotado, dado
inválido ou reset. Qual estado é alcançado e como o evento é registrado.>

## Verificação
<Como se comprova que a implementação corresponde a esta tabela: teste em
host por par estado × evento, teste em bancada, inspeção. O que ainda não
existe entra como pendência, não como feito.>
```

## Exemplo preenchido (ilustrativo)

```markdown
# FSM: acionamento_saida

- **Módulo dono:** `app/acionamento.c`
- **Requisitos atendidos:** REQ-014, REQ-015
- **Estado inicial:** `ACION_DESLIGADO`
- **Estado seguro / de falha:** `ACION_FALHA` — saída desligada; sai apenas por reset ou por comando explícito de rearme.

## Propósito
Controla a saída de potência do produto respeitando o intervalo mínimo
entre partidas. É máquina de estado porque a permissão de ligar depende
do que aconteceu antes, não só do comando atual.

## Estados
| Estado | Significado | Saídas / efeito enquanto ativo | Tempo limite |
| --- | --- | --- | --- |
| `ACION_DESLIGADO` | Repouso, apto a ligar | Saída desligada | — |
| `ACION_LIGADO` | Saída acionada | Saída ligada | — |
| `ACION_BLOQUEIO` | Intervalo mínimo entre partidas | Saída desligada | 30 s → `ACION_DESLIGADO` |
| `ACION_FALHA` | Falha detectada | Saída desligada | — |

## Eventos
| Evento | Origem | Dado associado |
| --- | --- | --- |
| `EV_COMANDO_LIGAR` | Tarefa de comunicação | — |
| `EV_COMANDO_DESLIGAR` | Tarefa de comunicação | — |
| `EV_SOBRECORRENTE` | Interrupção do comparador | corrente medida |
| `EV_TICK` | Base de tempo, 10 ms | — |

## Tabela de transição
| Estado atual | Evento | Condição | Próximo estado | Ação na transição |
| --- | --- | --- | --- | --- |
| `ACION_DESLIGADO` | `EV_COMANDO_LIGAR` | — | `ACION_LIGADO` | liga saída, marca instante |
| `ACION_DESLIGADO` | `EV_COMANDO_DESLIGAR` | — | — (ignorado) | já está desligado |
| `ACION_LIGADO` | `EV_COMANDO_DESLIGAR` | — | `ACION_BLOQUEIO` | desliga saída, arma temporizador |
| `ACION_LIGADO` | `EV_SOBRECORRENTE` | — | `ACION_FALHA` | desliga saída, registra código |
| `ACION_BLOQUEIO` | `EV_TICK` | decorrido ≥ 30 s | `ACION_DESLIGADO` | — |
| `ACION_BLOQUEIO` | `EV_COMANDO_LIGAR` | — | — (ignorado) | intervalo mínimo não cumprido |
| `ACION_FALHA` | qualquer | — | — (ignorado) | sai apenas por rearme explícito |

## Invariantes
- A saída só está energizada em `ACION_LIGADO`.
- `EV_SOBRECORRENTE` leva a `ACION_FALHA` a partir de qualquer estado.

## Comportamento em falha
Evento não previsto é contado e descartado. `ACION_FALHA` persiste o
código de falha e mantém a saída desligada; a causa do último reset é
lida na inicialização e registrada.

## Verificação
Teste em host cobrindo cada linha da tabela: pendente — a suíte de host
ainda não existe neste projeto. Hoje a verificação é manual em bancada,
registrada no relatório de validação.
```
