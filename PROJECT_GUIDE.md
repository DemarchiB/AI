# Guia de projeto — organização, boas práticas e templates

## Finalidade

Este guia é um documento autossuficiente sobre como **criar, organizar e manter** um projeto de software com boas práticas. Ele reúne três coisas em um único arquivo:

1. **Estrutura e organização** — quais documentos existem, o papel de cada um e onde cada informação deve ser registrada (Parte I);
2. **Boas práticas por domínio** — as regras específicas de cada assunto que o projeto adote; hoje o guia cobre o trabalho com agentes de IA, e novos domínios (programação, safety, segurança, etc.) entram como subseções da Parte II;
3. **Templates canônicos** — o conteúdo inicial de todo documento citado aqui, pronto para copiar (Parte III).

Os templates vivem **apenas neste arquivo**. O projeto não deve conter arquivos `TEMPLATE.md` separados: ao criar uma spec, um ADR, uma Skill ou um prompt de papel, copie o bloco correspondente da Parte III e preencha. Isso mantém uma única fonte de verdade para o formato e evita que cópias divirjam entre projetos.

Este guia é escrito para ser copiado, **sem alteração**, para qualquer repositório que adote esta abordagem. Toda informação específica de um projeto (domínio, stack, comandos de build reais, convenções de nomenclatura de código, ferramenta de VCS e branch principal, etc.) pertence aos documentos do próprio projeto — `AGENTS.md`, `ARCHITECTURE.md`, `docs/workflow.md` e demais áreas descritas na Seção 2 — nunca a este arquivo. Se, ao ler este guia, um exemplo específico parecer necessário, ele deve ir para os documentos do projeto, não para aqui.

Diferente de `AGENTS.md`, que é carregado em toda sessão e por isso precisa ser curto, este documento é consultado sob demanda: por uma pessoa entendendo o processo, ao iniciar um novo projeto, ou ao criar um documento a partir de um template. Por isso pode ser mais completo, desde que continue organizado por seções curtas e objetivas, sem links para conteúdo específico de um projeto.

## Mapa deste guia

| Parte | Conteúdo | Quando ler |
| --- | --- | --- |
| **Parte I** (Seções 1–8) | Conceitos, estrutura de arquivos, EARS, ADR, sub-agentes, workflow e execução. | Ao iniciar um projeto ou ao decidir onde registrar algo. |
| **Parte II** (Seção 9) | Boas práticas por domínio e como adicionar um domínio novo. | Ao adotar um novo conjunto de práticas no projeto. |
| **Parte III** (Seções T.1–T.10) | Templates canônicos de todos os documentos. | Sempre que for criar um documento. |
| **Anexo** (A.0–A.14) | Regras operacionais de manutenção documental e checklists. | Antes de encerrar qualquer mudança estrutural. |

---

# Parte I — Estrutura e organização do projeto

## 1. Conceitos fundamentais

- **Agente**: um modelo de linguagem com acesso a ferramentas (leitura/escrita de arquivos, execução de comandos, busca, sub-agentes). O modelo decide o que fazer; as ferramentas definem o que ele é capaz de fazer.
- **Harness**: o conjunto de ferramentas mais a orientação que molda como o agente as usa — prompts de sistema, `AGENTS.md`, Skills, specs, ADRs, hooks. Um agente sem harness tem acesso, mas não contexto nem restrições; harness é o que torna esse acesso confiável e repetível.
- **Regra vs. sensor**: uma **regra** é orientação prévia, consultada antes de agir (`AGENTS.md`, `ARCHITECTURE.md`, specs, prompts de papel). Um **sensor** é verificação automática posterior, que roda depois da ação e detecta desvio (build, lint, testes, hooks `PostToolUse`/`PostFileSave`). Regras reduzem a chance de erro; sensores detectam o erro que passou pela regra. Um harness maduro usa os dois — não apenas um.
- **Template**: o formato canônico de um documento, definido na Parte III deste guia. Um template é copiado e preenchido; nunca é referenciado como arquivo separado dentro do projeto.

## 2. Estrutura de arquivos do projeto

### 2.1 Hierarquia de navegação

```text
AGENTS.md
    ↓
ARCHITECTURE.md
    ↓
docs/
    ├── index.md
    ├── PROJECT_GUIDE.md   (este guia, copiado sem alteração)
    ├── specs/             (requisitos em EARS)
    ├── decisions/         (ADRs)
    ├── workflow.md        (revisão e branching)
    ├── design-docs/
    ├── exec-plans/
    ├── product-specs/
    ├── generated/
    └── references/
    ↓
.agents/
    ├── skills/            (workflows especializados)
    └── prompts/           (prompts complementares por papel)
    ↓
código, testes e configurações
```

A hierarquia representa navegação e nível de detalhe, não precedência sobre o código executável. Cada nível aponta para o de baixo; nenhum deve duplicar o conteúdo do outro. As pastas só existem quando têm conteúdo real — nenhuma delas deve ser criada apenas para completar a árvore.

### 2.2 Catálogo de arquivos e pastas

| Local | Papel | Detalhe | Template |
| --- | --- | --- | --- |
| `README.md` | Apresentação do projeto para pessoas e ferramentas. | Seção A.0.1 | T.1 |
| `AGENTS.md` | Índice operacional curto (~100 linhas), carregado em toda sessão. | Seção A.0.2 | T.2 |
| `ARCHITECTURE.md` | Mapa arquitetural de alto nível: domínios, limites, fluxos. | Seção A.0.3 | T.3 |
| `docs/index.md` | Índice da documentação: o que existe em `docs/` e para que serve. | Seção A.0.9 | T.4 |
| `docs/PROJECT_GUIDE.md` | Este guia; referência canônica sob demanda, idêntica entre projetos. | — | — |
| `docs/specs/` | Requisitos em notação EARS, antes de codar uma funcionalidade. | Seção 3 | T.5 |
| `docs/decisions/` | Registros de decisão de arquitetura (ADR). | Seção 4 | T.6 |
| `docs/workflow.md` | Workflow de revisão: branch separada, `git worktree`, Pull/Merge Request. | Seção 6 | T.7 |
| `.agents/skills/` | Workflows especializados, recorrentes e reutilizáveis. Caminho canônico de Skills. | Seção A.4 | T.8 |
| `.agents/prompts/` | Prompts por papel (ex.: bugfix, revisão) que complementam o harness global. | Seção 5 | T.9, T.10 |
| `docs/design-docs/`, `docs/exec-plans/`, `docs/product-specs/`, `docs/generated/`, `docs/references/` | Áreas documentais: designs/decisões informais, planos de execução, especificações de produto, artefatos gerados, referências de dependências. | Seções A.0.4 a A.0.8 | — |

Se outra ferramenta de agente exigir um caminho diferente de Skills (por exemplo `.claude/skills/`), esse caminho deve ser um link simbólico apontando para `.agents/skills/`, nunca uma cópia duplicada — a pasta canônica é sempre `.agents/skills/`.

### 2.3 Como decidir onde registrar uma informação

1. É necessário para qualquer primeira contribuição? Resumo ou link em `AGENTS.md`.
2. Explica limites e componentes de alto nível? `ARCHITECTURE.md`.
3. É um requisito ou comportamento esperado ainda não implementado? `docs/specs/`, usando EARS (template T.5).
4. É uma decisão de arquitetura que outros vão precisar consultar antes de mudar algo relacionado? `docs/decisions/`, como ADR (template T.6).
5. É um workflow especializado e reutilizável? Considere uma Skill em `.agents/skills/` (template T.8).
6. É uma orientação de papel para um sub-agente (bugfix, revisão)? `.agents/prompts/` (templates T.9 e T.10).
7. Registra uma decisão ou design mais informal, sem a rigidez de um ADR? `docs/design-docs/`.
8. Planeja trabalho relevante ainda não concluído? `docs/exec-plans/active/`.
9. É produzido por automação? `docs/generated/`, se precisar ser versionado.
10. Explica uma dependência externa no contexto do projeto? `docs/references/`.
11. É detalhe de implementação local e permanece claro no código? Mantenha no código, sem criar documento separado.
12. É o **formato** de um documento (não o conteúdo)? Vai na Parte III deste guia — nunca como um arquivo `TEMPLATE.md` no projeto.
13. É uma **prática geral** de um domínio (IA, programação, safety), válida para qualquer projeto? Vai na Parte II deste guia.
14. É uma convenção **específica deste projeto** (domínio, stack, comandos de build reais, VCS, branch principal)? Vai em `AGENTS.md`, `ARCHITECTURE.md` ou `docs/workflow.md` do próprio projeto — nunca neste guia.

Se nenhuma opção tiver conteúdo suficiente, não crie um novo arquivo.

## 3. Especificação antes de codar (EARS)

Antes de implementar uma funcionalidade nova ou um comportamento não trivial, registre requisitos (padrão EARS), critérios de aceite e a checklist de Tasks que implementa esses requisitos em `docs/specs/`:

- Ubíquo: "O `<sistema>` deve `<comportamento>`"
- Evento: "Quando `<gatilho>`, o `<sistema>` deve `<resposta>`"
- Estado: "Enquanto `<estado>`, o `<sistema>` deve `<resposta>`"
- Erro/comportamento indesejado: "Se `<condição>`, então o `<sistema>` deve `<resposta>`"
- Recurso opcional: "Onde `<feature presente>`, o `<sistema>` deve `<resposta>`"

Preceder a implementação por uma spec reduz ambiguidade antes de escrever código, torna o comportamento esperado testável e dá ao revisor um contrato claro para comparar com o resultado. Use o template T.5. Ferramentas com assistência nativa de planejamento (ex.: modo Spec do Kiro) devem escrever o resultado nesse formato e local genéricos do projeto, não em seu formato ou pasta proprietários.

## 4. Decisões de arquitetura (ADR)

Registre um ADR quando a decisão:

- afeta mais de um módulo ou componente;
- é difícil ou cara de reverter depois;
- envolve alternativas descartadas que alguém vai perguntar "por que não X" no futuro;
- muda um limite, dependência ou responsabilidade descrito em `ARCHITECTURE.md`.

Não registre ADR para escolhas locais, reversíveis ou já cobertas pelas convenções de código do próprio projeto. Consulte `docs/decisions/` antes de uma mudança que toque uma decisão já registrada. Use o template T.6.

## 5. Sub-agentes por papel

Um prompt em `.agents/prompts/` complementa o harness global — ele nunca o substitui. Todo prompt de papel deve instruir o agente a ler `AGENTS.md` e `ARCHITECTURE.md` primeiro, e então restringir o escopo de atuação a um papel específico (por exemplo, corrigir apenas o bug descrito, ou revisar sem escrever).

Prompts recomendados como ponto de partida: `bugfix.md` (template T.9) e `review.md` (template T.10). Um novo prompt de papel deve seguir o mesmo formato: harness global primeiro, escopo restrito, critério de conclusão explícito.

## 6. Workflow de revisão

Dois cenários cobrem qualquer projeto: o fluxo local, sempre disponível, e o fluxo com plataforma de Pull/Merge Request. As regras abaixo são completas e valem como estão. O `docs/workflow.md` do projeto (template T.7) apenas as instancia com o que é específico dele — VCS em uso, remoto, nomes reais da branch principal e das branches de integração, e se a plataforma de PR/MR já é usada hoje.

### 6.1 Cenário 1 — Local, sem plataforma de PR/MR

1. Toda tarefa roda em uma branch separada, criada a partir de um branch base declarado, nunca direto na branch principal ou nas branches de integração.
2. Ao terminar, a revisão é feita pelo diff completo da branch contra esse base: `git diff <base>...<branch>`.
3. O merge é sempre um comando executado manualmente por um humano. O agente nunca executa o merge, mesmo que sugira que a mudança está pronta.
4. Um diretório de trabalho local só tem uma branch ativa por vez. Para rodar mais de um agente em paralelo localmente, use `git worktree` — cada worktree é uma pasta separada com sua própria branch ativa, a partir do mesmo repositório.

### 6.2 Cenário 2 — Plataforma com PR/MR

1. Cada tarefa declara explicitamente o **branch base**: o branch de onde a branch de trabalho nasce e para onde o PR/MR é aberto de volta.
2. A branch da tarefa é criada a partir desse base, nunca de outro branch não especificado.
3. Ao concluir, o PR/MR é aberto de volta para o mesmo base — nunca direto para a branch principal, a menos que ela seja explicitamente o base da tarefa.
4. Revisão humana é obrigatória antes do merge, e o merge continua sendo uma ação humana.
5. Múltiplos agentes podem trabalhar em paralelo, cada um com sua branch e seu PR/MR independentes.

A regra operacional que vale para os dois cenários — o que o agente pode e não pode executar no VCS — está na Seção 7.

## 7. Modelo de execução do agente

O agente sempre trabalha em uma branch separada, nunca direto na branch principal ou nas branches de integração. A revisão acontece depois, via diff da branch — a aprovação prévia por plano não precisa ser obrigatória por padrão, desde que a branch separada esteja em uso. O merge continua sendo sempre uma ação manual e humana; o agente nunca executa `merge`, `push` para a branch principal, ou qualquer outra operação de escrita Git além da criação da própria branch de trabalho, exceto quando isso for explicitamente solicitado numa tarefa.

A branch separada é o mecanismo de segurança que substitui o gate de aprovação prévia: como nada chega à branch principal sem uma ação manual de merge, o risco de uma ação não revisada afetar o projeto fica contido à própria branch de trabalho.

## 8. Quando revisitar este guia

Este guia não é lido só uma vez. Ele deve ser reconsultado sempre que uma tarefa alterar a **estrutura documental ou organizacional** do projeto — não apenas o código. Isso inclui, entre outros:

- criar, mover, renomear ou remover uma Skill, um prompt de papel, uma spec, um ADR ou qualquer documento em `docs/`;
- reorganizar `AGENTS.md`, `ARCHITECTURE.md` ou `docs/index.md`;
- introduzir uma nova área documental não prevista no catálogo da Seção 2.2;
- migrar um caminho convencional (por exemplo, o local onde Skills ou prompts residem);
- adotar um novo domínio de boas práticas (Seção 9).

Ao revisitar este guia numa dessas situações, confirme:

1. a alteração segue as convenções descritas aqui (nomenclatura, formato, local correto conforme a Seção 2.3);
2. o documento criado partiu do template correspondente na Parte III, sem inventar formato paralelo;
3. os documentos do próprio projeto — `AGENTS.md`, `ARCHITECTURE.md`, `docs/index.md` e qualquer outro índice afetado — descrevem a nova estrutura com clareza, sem duplicar o conteúdo detalhado que já vive no documento de destino;
4. nenhuma referência cruzada do projeto ainda aponta para um caminho antigo, caso algo tenha sido movido ou renomeado;
5. o checklist do Anexo (Seção A.12) foi verificado antes de considerar a mudança concluída.

Este guia em si nunca deve ser editado para incorporar um fato específico de um projeto; ele só muda quando a convenção geral evolui — e, quando muda, muda para todos os projetos que o adotam.

---

# Parte II — Boas práticas por domínio

## 9. Domínios cobertos por este guia

A Parte I descreve a organização documental, que é comum a qualquer projeto. Esta parte concentra as práticas que dependem de **assunto**: como trabalhar com agentes de IA, como escrever código, como tratar requisitos de segurança funcional, e assim por diante. Cada domínio é uma subseção própria, geral e reutilizável — nunca específica de um projeto.

### 9.1 Domínio: trabalho com agentes de IA e documentação

Domínio coberto hoje. Suas regras estão distribuídas assim:

- conceitos (agente, harness, regra vs. sensor): Seção 1;
- estrutura documental que serve de contexto ao agente: Seção 2;
- spec antes de codar e ADR: Seções 3 e 4;
- sub-agentes por papel: Seção 5;
- workflow de revisão e modelo de execução: Seções 6 e 7;
- regras operacionais detalhadas de manutenção documental: Anexo A.

### 9.2 Como adicionar um novo domínio

Um novo domínio (por exemplo: boas práticas de programação, safety, segurança, testes, performance) entra como uma nova subseção `9.x`, seguindo estas regras:

1. **Só entra o que é geral.** Se a regra depende da linguagem, do produto ou da norma específica adotada por um projeto, ela pertence ao `AGENTS.md`/`ARCHITECTURE.md` daquele projeto, não a este guia.
2. **Formato uniforme.** Cada subseção `9.x` contém: propósito do domínio em 2–3 linhas; as regras em lista numerada, cada uma acionável e verificável; e, quando aplicável, os sensores automáticos que detectam desvio (lint, análise estática, testes, hooks).
3. **Templates novos vão para a Parte III**, numerados na sequência (`T.11`, `T.12`, …) e referenciados no catálogo da Seção 2.2.
4. **Documentos novos entram no catálogo** da Seção 2.2 e na lista de decisão da Seção 2.3 — caso contrário ninguém saberá onde registrá-los.
5. **O checklist A.12 é estendido** com os itens verificáveis do novo domínio.
6. **Não crie a subseção vazia.** Um domínio só é adicionado quando há regras reais a escrever; um cabeçalho reservado "para o futuro" é o antipadrão descrito em A.13.

---

# Parte III — Templates canônicos

Todos os templates dos documentos citados neste guia. Copie o bloco, salve no caminho indicado e preencha, substituindo o que está entre `<>`. Os exemplos marcados como ilustrativos servem para fixar formato e nível de detalhe — não devem ser copiados como conteúdo.

## T.1 `README.md`

````markdown
# <nome do projeto>

<Uma a três linhas: o que é e para quem serve.>

## Pré-requisitos
- <toolchain, versão, sistema operacional, hardware>

## Como começar
```
<comando de configuração>
<comando de build>
<comando de execução ou teste>
```

## Documentação
- Arquitetura: [ARCHITECTURE.md](ARCHITECTURE.md)
- Guia operacional para agentes: [AGENTS.md](AGENTS.md)
- Índice da documentação: [docs/index.md](docs/index.md)

## Licença
<licença ou "uso interno">
````

## T.2 `AGENTS.md`

Alvo: ~100 linhas. É índice, não manual — detalhe vai para o documento de destino.

```markdown
# AGENTS.md

## Objetivo do projeto
<2-3 linhas: o que o projeto faz e qual o resultado esperado de uma contribuição.>

## Mapa do repositório
| Caminho | Conteúdo |
| --- | --- |
| `<pasta>` | <o que vive aqui> |

Arquitetura detalhada: [ARCHITECTURE.md](ARCHITECTURE.md).
Convenções documentais e templates: [docs/PROJECT_GUIDE.md](docs/PROJECT_GUIDE.md).

## Comandos oficiais
| Ação | Comando | Diretório |
| --- | --- | --- |
| Configurar | `<comando>` | `<dir>` |
| Build | `<comando>` | `<dir>` |
| Testes | `<comando>` | `<dir>` |
| Lint | `<comando>` | `<dir>` |

Somente comandos verificados. Comando inferido não entra aqui.

## Antes de alterar
1. Ler esta página e `ARCHITECTURE.md`.
2. Verificar `docs/specs/` e `docs/decisions/` sobre a área afetada.
3. Criar branch de trabalho conforme [docs/workflow.md](docs/workflow.md).

## Depois de alterar
1. Rodar a validação mais específica disponível.
2. Atualizar a documentação afetada na mesma mudança.
3. Listar arquivos alterados e validações executadas.

## Restrições críticas
- <limite que nunca deve ser violado: compatibilidade, interface pública, submódulo, memória, norma>

## Skills relevantes
- `.agents/skills/<nome>/` — <quando usar>
```

## T.3 `ARCHITECTURE.md`

```markdown
# Arquitetura

## Visão geral
<O sistema em 3-5 linhas: o que faz, onde roda, quais são suas fronteiras.>

## Domínios e componentes
| Componente | Responsabilidade | Caminho |
| --- | --- | --- |
| <nome> | <responsabilidade única> | `<caminho>` |

## Fluxos principais
<1-3 fluxos ponta a ponta, em texto ou diagrama simples.>

## Dependências e interfaces externas
| Dependência | Uso | Limite |
| --- | --- | --- |
| <nome> | <para que serve> | <o que não deve vazar para o resto do sistema> |

## Restrições a preservar
- <compatibilidade, protocolo, limite de memória/timing, norma aplicável>

## Pontos não determinados
- <incerteza registrada como incerteza, nunca preenchida por suposição>
```

## T.4 `docs/index.md`

```markdown
# Índice da documentação

| Documento | Conteúdo | Quando consultar |
| --- | --- | --- |
| [PROJECT_GUIDE.md](PROJECT_GUIDE.md) | Convenções documentais e templates. | Ao criar ou mover qualquer documento. |
| [workflow.md](workflow.md) | Branching, revisão e merge. | Antes de iniciar uma tarefa. |
| [specs/](specs/) | Requisitos em EARS. | Antes de implementar comportamento novo. |
| [decisions/](decisions/) | ADRs. | Antes de mudar algo que uma decisão já cobre. |
| <outras áreas existentes> | <conteúdo> | <quando> |

Áreas sem conteúdo não aparecem neste índice e não devem existir como pastas vazias.
```

## T.5 `docs/specs/<nome-da-spec>.md` (EARS)

````markdown
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
````

### Exemplo preenchido (ilustrativo)

Exemplo de outro domínio de firmware embarcado, apenas para fixar formato e nível de detalhe.

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

## T.6 `docs/decisions/ADR-NNNN-<slug>.md`

```markdown
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
```

### Exemplo preenchido (ilustrativo)

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

## T.7 `docs/workflow.md`

Preencha com a realidade do projeto: VCS em uso, remoto, branch principal e branches de integração, e se há ou não plataforma de Pull/Merge Request hoje.

```markdown
# Workflow de revisão

Este projeto usa <VCS> com remoto em <plataforma/URL>. Hoje <há / não há>
workflow de Pull/Merge Request formalizado e <há / não há> pipeline de CI
(ver [AGENTS.md](../AGENTS.md)). Os dois cenários abaixo cobrem o fluxo
local, sempre disponível, e o fluxo com plataforma de PR/MR.

## Cenário 1 — Local, sem plataforma de PR/MR

1. Toda tarefa de agente roda em uma branch separada, nunca direto na
   branch principal ou nas branches de integração (ex.: `<develop>`,
   `<main>`, `<stable/*>`): `git checkout -b <tipo>-<assunto>`.
2. Ao terminar, revise o diff completo antes de decidir o próximo passo:
   `git diff <base>...<branch>`.
3. O merge para a branch de destino é sempre um comando executado
   manualmente por um humano. O agente nunca executa o merge, mesmo que
   sugira que a mudança está pronta.

Um único diretório de trabalho local só tem uma branch ativa por vez.
Para rodar mais de um agente em paralelo localmente, use `git worktree` —
cada worktree é uma pasta separada com sua própria branch ativa, a partir
do mesmo repositório: `git worktree add ../<projeto>-<tarefa> <branch>`.

## Cenário 2 — Plataforma com PR/MR (permite paralelismo real)

1. Para cada tarefa, especifique explicitamente o **branch base** — o
   branch a partir do qual a nova branch é criada, que é também o destino
   do PR/MR.
2. O agente cria sua branch a partir do branch base indicado, nunca de
   outro branch não especificado.
3. Ao concluir, o agente abre o PR/MR de volta para o mesmo branch base —
   nunca direto para a branch principal, a menos que ela seja
   explicitamente o branch base da tarefa.
4. Revisão humana é obrigatória antes do merge, e o merge continua sendo
   ação humana.
5. Múltiplos agentes podem rodar em paralelo, cada um com sua branch e
   seu PR/MR independentes.

## Regra operacional deste repositório

Independentemente do cenário, o agente nunca executa `commit`, `push`,
`merge`, `rebase` ou qualquer outra operação de escrita no VCS por conta
própria, exceto quando explicitamente solicitado numa tarefa. A branch
separada é o mecanismo de segurança que substitui a aprovação prévia por
plano.
```

## T.8 `.agents/skills/<nome-da-skill>/SKILL.md`

Estrutura da pasta:

```text
.agents/skills/<nome-da-skill>/
├── SKILL.md
├── scripts/       # opcional
├── references/    # opcional
└── assets/        # opcional
```

Conteúdo do `SKILL.md` (o frontmatter YAML é obrigatório e deve ser válido):

```markdown
---
name: nome-da-skill
description: "Executa <workflow específico> e explica quando deve ser usado; palavras-chave relevantes."
compatibility: "Requer a ferramenta <X> na versão <Y> ou superior"
---

# <Nome da Skill>

## Quando usar
<Gatilhos concretos. Se não houver gatilho claro e recorrente, a Skill não deveria existir.>

## Quando não usar
<Casos em que o fluxo padrão do projeto já resolve.>

## Passos
1. <passo verificável>
2. <passo verificável>

## Validação
<Como confirmar que o resultado está correto.>
```

Regras de `name`: obrigatório, kebab-case, somente letras minúsculas/números/hífens, máximo 64 caracteres, não começa nem termina com hífen, sem hífens consecutivos, corresponde exatamente ao nome do diretório pai. `description` é obrigatória, até 1024 caracteres, explica o que a Skill faz e quando usar.

## T.9 `.agents/prompts/bugfix.md`

```markdown
# Papel: correção de bug

## Contexto obrigatório
Leia, nesta ordem, antes de qualquer ação: `AGENTS.md`, `ARCHITECTURE.md`
e qualquer spec ou ADR que cubra a área afetada. Este prompt complementa
o harness global; não o substitui.

## Escopo
Corrija apenas o bug descrito na tarefa. Não refatore código não
relacionado, não altere interfaces públicas e não modifique submódulos.

## Procedimento
1. Reproduza ou localize a evidência do defeito (teste, log, trecho de código).
2. Identifique a causa raiz antes de propor a correção.
3. Aplique a menor mudança coesa que corrige a causa raiz.
4. Adicione ou ajuste o teste que falharia sem a correção.
5. Rode a validação mais específica disponível.

## Critério de conclusão
A correção está pronta quando: a causa raiz está identificada por escrito,
o teste correspondente passa, nenhuma área fora do escopo foi alterada, e
o diff contém apenas mudanças explicáveis pela tarefa.
```

## T.10 `.agents/prompts/review.md`

```markdown
# Papel: revisão

## Contexto obrigatório
Leia, nesta ordem, antes de qualquer ação: `AGENTS.md`, `ARCHITECTURE.md`
e a spec ou ADR relacionados à mudança. Este prompt complementa o harness
global; não o substitui.

## Escopo
Revise sem escrever. Não altere arquivos, não corrija o que encontrar e
não execute operações de escrita no VCS. O resultado é um relatório.

## O que verificar
1. A mudança atende aos requisitos e critérios de aceite declarados.
2. Limites arquiteturais, interfaces e compatibilidade foram preservados.
3. Não há fatos, comandos ou decisões inventados na documentação alterada.
4. A documentação afetada foi atualizada na mesma mudança.
5. O diff contém somente mudanças explicáveis pela tarefa.
6. O checklist documental (Seção A.12 do guia) se aplica e foi cumprido.

## Formato do relatório
- **Bloqueadores**: <problemas que impedem o merge>
- **Sugestões**: <melhorias não bloqueantes>
- **Verificado**: <o que foi conferido e está correto>
```

---

# Anexo — Regras operacionais de manutenção documental

O conteúdo abaixo também é geral e reutilizável (complementa as Partes I a III); trata do processo de manter a documentação e a estrutura de um repositório coerentes ao longo do tempo, com nível de detalhe maior do que cabe nas seções acima.

### A.0 Responsabilidade de cada documento

- **A.0.1 README.md**: apresenta o projeto a pessoas e ferramentas pela primeira vez — propósito, pré-requisitos, caminho inicial de uso, links para documentação detalhada. Não é inventário arquitetural completo.
- **A.0.2 AGENTS.md**: índice operacional curto (~100 linhas). Inclui objetivo resumido, mapa do repositório, referência a `ARCHITECTURE.md`, comandos oficiais, workflow antes/depois de alterações, restrições críticas e Skills relevantes. Detalhes extensos ficam em `ARCHITECTURE.md`, `docs/`, `.agents/skills/`, código ou configurações.
- **A.0.3 ARCHITECTURE.md**: mapa arquitetural de alto nível — domínios, camadas, componentes, dependências, fluxos, interfaces e limites externos, restrições a preservar, pontos não determinados. Não descreve exaustivamente funções ou arquivos.
- **A.0.4 docs/design-docs/**: designs, princípios e decisões de funcionamento que exigem mais explicação do que o mapa arquitetural, mas sem a rigidez formal de um ADR.
- **A.0.5 docs/exec-plans/**: trabalhos relevantes que exigem etapas, riscos, migração ou coordenação (`active/` em execução, `completed/` concluídos com valor histórico).
- **A.0.6 docs/product-specs/**: comportamento esperado, requisitos e critérios de aceitação já consolidados como especificação de produto (distinto de `docs/specs/`, que é o ponto de entrada em EARS antes da implementação).
- **A.0.7 docs/generated/**: exclusivamente artefatos produzidos automaticamente, com fonte, gerador e condições de regeneração identificados.
- **A.0.8 docs/references/**: conhecimento de apoio sobre ferramentas, bibliotecas, protocolos e dependências usados pelo projeto, sem copiar documentação externa integralmente.
- **A.0.9 docs/index.md**: índice do que existe em `docs/` e quando consultar cada área. Não duplica o conteúdo dos documentos indexados.

### A.1 Regras fundamentais

1. O repositório é a fonte de verdade.
2. Conhecimento permanente deve ser versionado em formatos simples e acessíveis.
3. A documentação deve refletir o projeto; o projeto não deve ser reorganizado apenas para atender a uma ferramenta ou agente.
4. Fatos devem ser sustentados por código, configuração, documentação vigente ou decisão explícita.
5. Incertezas devem ser registradas como incertezas, nunca preenchidas por suposição.
6. Documentos, diretórios, planos e Skills não devem ser criados apenas para completar uma estrutura idealizada.
7. Alterações devem preservar interfaces, dependências, compatibilidade e limites arquiteturais conhecidos.
8. Submódulos e outros projetos externos não devem ser alterados sem solicitação explícita.
9. Arquivos gerados devem ser modificados por sua fonte ou gerador sempre que esse processo existir.
10. Toda alteração deve terminar com validação proporcional ao risco e ao escopo.
11. O formato de um documento vive nos templates deste guia; o projeto guarda o conteúdo preenchido, não cópias do template.

### A.2 Independência de ferramenta

A documentação principal deve usar formatos amplamente acessíveis: Markdown, YAML quando necessário, scripts versionados, arquivos de configuração mantidos pelo próprio projeto. Configurações específicas de ferramentas (como as de uma IDE específica) podem coexistir como camadas de adaptação, mas não devem ser a única fonte de regras, arquitetura, comandos ou decisões. Não dependa exclusivamente de memória de um agente, histórico de conversas, prompts privados, regras exclusivas de uma IDE ou arquivos locais não versionados.

### A.3 Fontes de evidência

Antes de documentar ou alterar uma área, procure evidências nesta ordem, adaptando ao projeto:

1. código-fonte e interfaces públicas;
2. arquivos de build, dependências e configuração;
3. testes e validações executáveis;
4. README e documentação versionada;
5. scripts operacionais e automações;
6. histórico de decisões explicitamente registrado (ADRs em `docs/decisions/`);
7. comportamento observado e reproduzível.

Quando fontes divergirem: registre a divergência, determine qual fonte governa o comportamento atual, evite atualizar documentação com uma conclusão não comprovada, e solicite decisão quando a correção exigir conhecimento externo ao repositório.

### A.4 Skills

Crie uma Skill em `.agents/skills/` somente quando houver um workflow especializado, recorrente, relevante para o projeto, difícil de executar corretamente sem instruções, e estável o bastante para ser reutilizado. Não crie Skills genéricas para linguagem, Git ou ferramentas comuns sem uma necessidade específica do projeto. Estrutura de pastas, frontmatter e regras de nomenclatura: template T.8.

### A.5 Processo para qualquer modificação futura

1. **Classificar**: objetivo e critérios de sucesso, arquivos/módulos/produtos afetados, risco, se há código gerado/submódulo/dependência externa, se muda comportamento/arquitetura/build ou só documentação, validações disponíveis.
2. **Obter contexto proporcional**: inspecione a estrutura relevante, leia documentação e configurações aplicáveis, identifique linguagem/plataforma/toolchain, rastreie interfaces e dependências afetadas, verifique o estado do VCS inicial. Pare de investigar quando houver evidência suficiente.
3. **Implementar**: mudanças mínimas e coesas, preserve estilo e abstrações existentes, evite refatorações não relacionadas, atualize referências e imports ao mover arquivos, não introduza dependências sem justificar necessidade/versão/impacto.
4. **Validar**: use a validação mais específica disponível (teste direcionado > build do módulo > build do produto > inspeção de diff, para mudanças apenas documentais). Se uma validação não puder ser executada, registre o motivo e a verificação pendente.
5. **Encerrar**: liste arquivos criados/modificados/movidos/removidos, resuma mudanças de comportamento ou estrutura, informe validações executadas e limitações, confirme que submódulos e áreas fora do escopo não foram alterados. Se a mudança alterou a estrutura documental, revisite este guia conforme a Seção 8.

### A.6 Modificações estruturais

Antes de mover ou dividir componentes: identifique responsabilidade e proprietário lógico de cada área, mapeie dependências de entrada e saída, localize imports/scripts/configurações/pipelines/documentação afetados, verifique caminhos codificados e ferramentas que dependem da estrutura atual, preserve compatibilidade ou defina uma migração explícita, e atualize `ARCHITECTURE.md` se limites ou responsabilidades mudarem.

Evite: mover arquivos sem atualizar consumidores, criar camadas sem responsabilidade própria, duplicar utilitários, misturar infraestrutura/domínio/integração sem necessidade, reorganizar código apenas para acomodar uma IA ou IDE.

### A.7 Submódulos e projetos externos

Trate todo submódulo Git (ou equivalente de outro VCS) como projeto externo e independente por padrão: identifique-os pela configuração de submódulos do VCS em uso, não altere código/configuração/documentação dentro deles, não crie `AGENTS.md`/`ARCHITECTURE.md`/Skills dentro deles, não assuma permissão para enviar alterações, documente apenas a interface e a dependência observáveis pelo projeto principal. Só modifique um submódulo quando isso for solicitado explicitamente, tratando a mudança como trabalho separado.

### A.8 Arquivos gerados

Antes de editar um arquivo, determine se ele é gerado (cabeçalhos indicando geração, templates, scripts de exportação, diretórios de saída, regras de build). Quando houver gerador: altere a fonte/template correto, execute o processo oficial, revise todas as saídas (inclusive remoções), valide consumidores afetados. Não simule manualmente a saída de um gerador indisponível sem autorização; registre a limitação.

### A.9 Scripts, comandos e dependências

Documente somente comandos existentes e verificáveis (configuração, build, testes, lint, geração, empacotamento, execução, implantação), registrando diretório de execução, pré-requisitos, parâmetros, arquivos produzidos, efeitos colaterais e modo de validação quando relevante. Não transforme comandos inferidos em instruções oficiais. Dependências novas devem ser necessárias, confiáveis e versionadas de forma compatível; nomes incomuns devem ser verificados para evitar pacotes maliciosos ou typosquatting.

### A.10 Testes e qualidade

Use testes existentes como mecanismo de validação, não como especificação absoluta. Execute primeiro os testes mais direcionados, amplie a validação conforme o risco, não declare cobertura que não foi medida, registre validações manuais quando forem o mecanismo real do projeto.

### A.11 Manutenção da documentação

Atualize documentação na mesma mudança quando houver alteração em propósito/escopo, comandos/pré-requisitos, arquitetura/limites/dependências, comportamento especificado, workflows especializados, formatos gerados, riscos/restrições operacionais. Ao revisar: remova fatos obsoletos, preserve conteúdo ainda correto, substitua duplicações por links, diferencie fatos/decisões/hipóteses, verifique links e caminhos, mantenha o texto conciso.

### A.12 Checklist de validação documental

- [ ] O conteúdo corresponde ao código e às configurações atuais.
- [ ] Comandos, caminhos e nomes citados existem ou estão marcados como exemplo.
- [ ] Não há requisitos, decisões ou arquitetura inventados.
- [ ] Incertezas e divergências estão explícitas.
- [ ] `AGENTS.md` continua curto e funciona como índice.
- [ ] `ARCHITECTURE.md` permanece de alto nível.
- [ ] Documentos criados partiram do template correspondente na Parte III.
- [ ] Não existe arquivo `TEMPLATE.md` (ou equivalente) duplicando um template deste guia.
- [ ] Skills em `.agents/skills/` possuem nome, diretório e frontmatter válidos.
- [ ] Specs em `docs/specs/` seguem a notação EARS e têm critérios de aceite verificáveis.
- [ ] ADRs em `docs/decisions/` registram alternativas descartadas e consequências.
- [ ] Prompts em `.agents/prompts/` referenciam o harness global e não o substituem.
- [ ] Links relativos resolvem corretamente, incluindo após qualquer migração de caminho.
- [ ] Arquivos gerados e suas fontes estão identificados.
- [ ] Nenhum submódulo foi alterado sem autorização.
- [ ] O diff contém somente mudanças explicáveis pela tarefa.
- [ ] Este guia (`docs/PROJECT_GUIDE.md`) permanece livre de conteúdo específico do projeto.

### A.13 Antipadrões

Evite: documentação criada apenas para preencher uma árvore; `AGENTS.md` longo duplicando todo o repositório; arquitetura baseada em suposição não registrada; comandos hipotéticos apresentados como oficiais; specs sem critério de aceite verificável; ADR para decisões triviais ou reversíveis; Skills genéricas ou redundantes; arquivos `TEMPLATE.md` espalhados pelo projeto duplicando a Parte III; seções de domínio criadas vazias "para o futuro"; edição direta de arquivos gerados; alterações silenciosas em submódulos; prompts de papel que substituem (em vez de complementar) o harness global; merge executado pelo próprio agente; fatos específicos de um projeto incorporados a este guia.

### A.14 Critérios de sucesso

A organização documental está adequada quando uma pessoa ou agente sem histórico prévio consegue: entender o propósito do projeto; encontrar o mapa arquitetural; localizar comandos e pré-requisitos reais; reconhecer limites, riscos e dependências externas; encontrar specs, ADRs e planos quando existirem; localizar Skills e prompts de papel; criar um documento novo no formato correto sem procurar exemplos fora do repositório; distinguir fonte manual de artefato gerado; modificar o projeto sem depender de conhecimento privado; validar e comunicar a mudança de forma reproduzível.
