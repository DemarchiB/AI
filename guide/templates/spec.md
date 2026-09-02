# Template: spec em EARS (`docs/specs/<nome-da-spec>.md`)

**Quando usar:** antes de implementar uma funcionalidade nova ou um comportamento não trivial. Preceder a implementação por uma spec reduz ambiguidade antes de escrever código, torna o comportamento esperado testável e dá ao revisor um contrato claro para comparar com o resultado.

**Padrões EARS** — todo requisito usa um dos cinco:

- Ubíquo: "O `<sistema>` deve `<comportamento>`"
- Evento: "Quando `<gatilho>`, o `<sistema>` deve `<resposta>`"
- Estado: "Enquanto `<estado>`, o `<sistema>` deve `<resposta>`"
- Erro/comportamento indesejado: "Se `<condição>`, então o `<sistema>` deve `<resposta>`"
- Recurso opcional: "Onde `<feature presente>`, o `<sistema>` deve `<resposta>`"

**Convenções:** um arquivo por spec, nome em kebab-case sem acento (`deteccao-overflow-uart.md`); identificadores `REQ-NNN` únicos dentro da spec; campo `Status` no cabeçalho (`rascunho` → `aprovada` → `implementada` → `substituída por <spec>`). Uma spec implementada não é apagada: vira o registro do que foi acordado. O commit que implementa um requisito cita o `REQ-NNN` correspondente. Ferramentas com modo de planejamento nativo devem escrever o resultado neste formato e local, não em formato ou pasta proprietários.

````markdown
# Spec: <título curto>

- **Status:** rascunho | aprovada | implementada | substituída por <spec>
- **Data:** <AAAA-MM-DD da última atualização>

## Contexto
<O quê e por quê, 2-3 linhas. Cite a evidência (código, configuração ou decisão) que motiva a spec.>

## Requisitos (EARS)
REQ-001 (<padrão>): <requisito>
REQ-002 (<padrão>): <requisito>

## Critérios de aceite
- [ ] <critério testável>
- [ ] <critério testável>

## Tasks
Checklist sequencial, cada tarefa referenciando o(s) requisito(s) que atende.
Marque `[x]` conforme cada uma for executada, uma a uma.

- [ ] 1. <tarefa> (REQ-NNN)
- [ ] 2. <tarefa> (REQ-NNN)

## Rastreabilidade
| Requisito | Implementação | Teste | Commit / PR |
| --- | --- | --- | --- |
| REQ-001 | `<arquivo ou módulo>` | `<teste>` | `<hash ou PR>` |

## Fora de escopo
<O que esta spec explicitamente não cobre.>

## Restrições
<Memória, timing, norma de codificação, variante de produto, conformidade regulatória se aplicável.>

## Design
<Link para o ADR correspondente, quando a spec envolver decisão de arquitetura.
Não duplique o conteúdo do ADR aqui; omita esta seção se não houver decisão envolvida.>
````

## Exemplo preenchido (ilustrativo)

```markdown
# Spec: Driver UART com detecção de overflow

- **Status:** implementada
- **Data:** 2026-03-14

## Contexto
O driver de UART atual não sinaliza quando o buffer de recepção enche,
causando perda silenciosa de bytes em rajadas de dados.

## Requisitos (EARS)
REQ-001 (Evento): Quando o buffer RX atingir 90% de ocupação, o driver deve sinalizar overflow via flag.
REQ-002 (Estado): Enquanto overflow estiver ativo, o driver deve rejeitar novos bytes recebidos.
REQ-003 (Erro): Se a paridade de um byte for inválida, então o driver deve descartar o byte e incrementar um contador de erro.

## Critérios de aceite
- [x] Testável via mock de UART, sem hardware físico
- [x] Não deve alocar memória dinamicamente
- [x] Flag de overflow deve ser limpa apenas por chamada explícita de reset

## Tasks
- [x] 1. Implementar detecção de overflow (REQ-001)
- [x] 2. Implementar rejeição de bytes durante overflow (REQ-002)
- [x] 3. Implementar contador de erro de paridade (REQ-003)
- [x] 4. Escrever testes para cada item acima

## Rastreabilidade
| Requisito | Implementação | Teste | Commit / PR |
| --- | --- | --- | --- |
| REQ-001 | `src/drivers/uart.c` | `test/test_uart_overflow.c` | `a1b2c3d` |
| REQ-002 | `src/drivers/uart.c` | `test/test_uart_overflow.c` | `a1b2c3d` |
| REQ-003 | `src/drivers/uart.c` | `test/test_uart_parity.c` | `d4e5f6a` |

## Fora de escopo
Mudança de baud rate não faz parte desta spec.

## Restrições
RAM disponível para o buffer: 256 bytes. Deve compilar sem warning na análise estática do projeto.

## Design
Ver docs/decisions/ADR-0001-driver-uart-overflow.md
```
