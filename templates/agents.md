# Template: `AGENTS.md`

**Quando usar:** no dia zero, por último entre os quatro documentos obrigatórios — índice só se escreve bem sobre o que já existe.

**Papel:** índice operacional curto, carregado em toda sessão de agente. Alvo de ~100 linhas. Detalhe extenso vive em `ARCHITECTURE.md`, em `docs/`, nas Skills, no código ou nas configurações — aqui fica o resumo e o ponteiro.

**Aninhamento.** O `AGENTS.md` da raiz é carregado em toda sessão e deve continuar curto. Uma subárvore com convenções próprias — uma pasta de documentação, um componente, uma área com ferramental diferente — pode ter o seu próprio `AGENTS.md`, com o mesmo formato deste template reduzido ao que vale ali. A divisão de trabalho é: o raiz descreve o repositório inteiro em uma tabela e **aponta** para os aninhados; o aninhado descreve só a sua pasta e nunca repete regra do raiz. Assim o detalhe caro só é carregado quando a tarefa toca aquela pasta. Duas ressalvas: confirme que o seu harness carrega arquivos aninhados sob demanda — se não carregar, o raiz precisa dizer explicitamente qual arquivo ler para cada área; e o aninhamento vale para o `AGENTS.md`, nunca para o `ARCHITECTURE.md`, que é único no projeto por descrever o que existe entre as partes.

**Pastas que o agente não deve abrir** — saídas de build, artefatos gerados, binários arquivados — se resolvem primeiro no `.gitignore` e no arquivo de exclusão do harness, quando houver. Listá-las aqui é o último recurso, não o primeiro: documentar "não leia isto" gasta contexto para evitar contexto.

**Convenções:** somente comandos verificados; comando inferido entra com marcação de estado provisório, nunca como oficial. É neste arquivo que fica registrado qual estado do conjunto de convenções o projeto adotou — e é dele que os arquivos de contexto de outras ferramentas de IA (`CLAUDE.md`, `.cursorrules`, `copilot-instructions.md`) devem ser apenas um ponteiro de três linhas, nunca uma cópia ([practices/ia.md](../practices/ia.md), Seção *Arquivos de contexto de cada ferramenta*).

```markdown
# AGENTS.md

## Objetivo do projeto
<2-3 linhas: o que o projeto faz e qual o resultado esperado de uma contribuição.>

## Convenções adotadas
Este projeto segue [docs/guide/PROJECT_GUIDE.md](docs/guide/PROJECT_GUIDE.md).
Adotado como <cópia | submódulo>; estado adotado: `<commit curto do conjunto>`.
<Se houve reavaliação sem atualizar: por que se manteve nesse estado.>

## Mapa do repositório
| Caminho | Conteúdo |
| --- | --- |
| `<pasta>` | <o que vive aqui> |

Arquitetura detalhada: [ARCHITECTURE.md](ARCHITECTURE.md).

## Comandos oficiais
| Ação | Comando | Diretório |
| --- | --- | --- |
| Configurar | `<comando>` | `<dir>` |
| Build | `<comando>` | `<dir>` |
| Testes | `<comando>` | `<dir>` |
| Lint | `<comando>` | `<dir>` |
| Varredura de segredos | `<comando>` | `<dir>` |

Somente comandos verificados. Comando inferido não entra aqui.

## Antes de alterar
1. Ler esta página e `ARCHITECTURE.md`.
2. Verificar `docs/specs/` e `docs/decisions/` sobre a área afetada.
3. Criar branch de trabalho conforme [docs/workflow.md](docs/workflow.md).

## Depois de alterar
1. Rodar os sensores existentes (build, teste direcionado, lint, segredos).
2. Atualizar a documentação afetada na mesma mudança.
3. Listar arquivos alterados, validações executadas e verificações pendentes.

## Restrições críticas
- <limite que nunca deve ser violado: compatibilidade, interface pública, submódulo, memória, norma>
- O agente não executa merge, push para branch principal nem reescrita de histórico.

## Skills disponíveis
- `.agents/skills/<nome>/` — <quando usar>

## Prompts de papel disponíveis
- `.agents/prompts/<nome>.md` — <para que serve>
```

## Exemplo preenchido (ilustrativo)

Projeto fictício de firmware embarcado, o mesmo dos demais exemplos deste conjunto. Repare na marcação de estado provisório numa linha da tabela de comandos.

```markdown
# AGENTS.md

## Objetivo do projeto
Firmware do módulo de comunicação: expõe UART e Ethernet ao controlador
principal. Uma contribuição está completa quando o comportamento novo tem
spec, teste passando e nenhum aumento de uso de RAM no módulo.

## Convenções adotadas
Este projeto segue [docs/guide/PROJECT_GUIDE.md](docs/guide/PROJECT_GUIDE.md).
Adotado como submódulo; estado adotado: `a1b2c3d` (o commit fixado em `.gitmodules`).

## Mapa do repositório
| Caminho | Conteúdo |
| --- | --- |
| `src/drivers/` | Drivers de periférico (UART, Ethernet, timers) |
| `src/net/` | Pilha de rede e adaptação de protocolo |
| `src/app/` | Máquina de estados da aplicação |
| `test/` | Testes unitários com mock de periférico |
| `tools/` | Scripts de configuração, build e gravação |

Arquitetura detalhada: [ARCHITECTURE.md](ARCHITECTURE.md).

## Comandos oficiais
| Ação | Comando | Diretório |
| --- | --- | --- |
| Configurar | `./tools/setup.sh` | raiz |
| Build | `make firmware` | raiz |
| Testes | `make test` | raiz |
| Lint | `make lint` | raiz |
| Uso de memória | `make size` | raiz |
| Varredura de segredos | `<a definir: nenhuma ferramenta adotada ainda>` | — |

## Antes de alterar
1. Ler esta página e `ARCHITECTURE.md`.
2. Verificar `docs/specs/` e `docs/decisions/` sobre a área afetada.
3. Criar branch de trabalho conforme [docs/workflow.md](docs/workflow.md).

## Depois de alterar
1. Rodar `make test`, `make lint` e `make size`.
2. Atualizar a documentação afetada na mesma mudança.
3. Listar arquivos alterados, validações executadas e verificações pendentes.

## Restrições críticas
- Sem alocação dinâmica depois da inicialização, em nenhum ponto de `src/`.
- RAM livre do módulo não pode cair abaixo de 4 KB (medida por `make size`).
- Protocolo serial deve permanecer compatível com a versão 1.4 do controlador.
- O agente não executa merge, push para `develop` nem reescrita de histórico.

## Skills disponíveis
- `.agents/skills/analisar-mapa-de-memoria/` — quando `make size` acusar
  estouro e for preciso localizar o crescimento.

## Prompts de papel disponíveis
- `.agents/prompts/bugfix.md` — correção de defeito com escopo restrito.
- `.agents/prompts/review.md` — revisão sem escrita.
```
