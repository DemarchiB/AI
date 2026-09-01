# Spec: <título curto>

## Contexto
<O quê e por quê, 2-3 linhas. Cite a evidência (código, configuração ou decisão) que motiva a spec.>

## Requisitos (EARS)
Use os identificadores `REQ-NNN` e um dos 5 padrões EARS por requisito:

- Ubíquo: "O `<sistema>` deve `<comportamento>`"
- Evento: "Quando `<gatilho>`, o `<sistema>` deve `<resposta>`"
- Estado: "Enquanto `<estado>`, o `<sistema>` deve `<resposta>`"
- Erro/comportamento indesejado: "Se `<condição>`, então o `<sistema>` deve `<resposta>`"
- Recurso opcional: "Onde `<feature presente>`, o `<sistema>` deve `<resposta>`"

REQ-001 (<padrão>): <requisito>
REQ-002 (<padrão>): <requisito>

## Critérios de aceite
- [ ] <critério testável>
- [ ] <critério testável>

## Tasks
Checklist de tarefas sequenciais para implementar os requisitos, cada uma referenciando o(s) requisito(s) que atende. Marque `[x]` conforme cada tarefa for executada, uma a uma.

- [ ] 1. <tarefa> (REQ-NNN)
- [ ] 2. <tarefa> (REQ-NNN)

## Fora de escopo
<O que esta spec explicitamente não cobre.>

## Restrições
<Memória, timing, MISRA, variante de produto afetada, conformidade regulatória se aplicável.>

## Design
<Link para o ADR correspondente em `docs/decisions/`, quando a spec envolver decisão de arquitetura. Não duplique o conteúdo do ADR aqui; omita esta seção se não houver decisão de arquitetura envolvida.>

---

## Exemplo

O exemplo abaixo é ilustrativo, de outro domínio de firmware embarcado, para fixar o formato e o nível de detalhe. Não descreve um requisito real deste repositório.

```markdown
# Spec: Driver UART com detecção de overflow

## Contexto
O driver de UART atual não sinaliza quando o buffer de recepção enche,
causando perda silenciosa de bytes em rajadas de dados.

## Requisitos (EARS)
REQ-001 (Evento): Quando o buffer RX atingir 90% de ocupação, o driver deve sinalizar overflow via flag.
REQ-002 (Estado): Enquanto overflow estiver ativo, o driver deve rejeitar novos bytes recebidos.
REQ-003 (Erro): Se a paridade de um byte for inválida, então o driver deve descartar o byte e incrementar um contador de erro.

## Critérios de aceite
- [ ] Testável via mock de UART (CMock), sem hardware físico
- [ ] Não deve alocar memória dinamicamente
- [ ] Flag de overflow deve ser limpa apenas por chamada explícita de reset

## Tasks
- [ ] 1. Implementar detecção de overflow (REQ-001)
- [ ] 2. Implementar rejeição de bytes durante overflow (REQ-002)
- [ ] 3. Implementar contador de erro de paridade (REQ-003)
- [ ] 4. Escrever testes (Unity/CMock) para cada item acima

## Fora de escopo
Mudança de baud rate não faz parte desta spec.

## Restrições
RAM disponível para o buffer: 256 bytes. Deve compilar sem warning no clang-tidy.

## Design
Ver docs/decisions/ADR-0001-driver-uart-overflow.md
```
