# Manual de trabalho com agentes de IA e organização documental

## Finalidade

Este manual é um guia autossuficiente sobre como organizar a documentação de um projeto de software e trabalhar com agentes de IA. Ele não descreve nenhum projeto específico — é escrito para ser copiado, sem alteração, para qualquer repositório que adote esta mesma abordagem.

Toda informação específica de um projeto (domínio, stack, comandos de build reais, convenções de nomenclatura de código, ferramenta de VCS e branch principal, etc.) pertence aos documentos do próprio projeto — `AGENTS.md`, `ARCHITECTURE.md`, `docs/workflow.md` e demais áreas descritas na Seção 2 — nunca a este arquivo. Se, ao ler este manual, um exemplo específico parecer necessário, ele deve ir para os documentos do projeto, não para aqui.

Diferente de `AGENTS.md`, que é carregado em toda sessão e por isso precisa ser curto, este documento é consultado sob demanda: por uma pessoa entendendo o processo, ou ao iniciar um novo projeto. Por isso pode ser mais completo, desde que continue organizado por seções curtas e objetivas, com links relativos apenas para os templates da própria convenção (nunca para conteúdo específico de um projeto).

## 1. Conceitos fundamentais

- **Agente**: um modelo de linguagem com acesso a ferramentas (leitura/escrita de arquivos, execução de comandos, busca, sub-agentes). O modelo decide o que fazer; as ferramentas definem o que ele é capaz de fazer.
- **Harness**: o conjunto de ferramentas mais a orientação que molda como o agente as usa — prompts de sistema, `AGENTS.md`, Skills, specs, ADRs, hooks. Um agente sem harness tem acesso, mas não contexto nem restrições; harness é o que torna esse acesso confiável e repetível.
- **Regra vs. sensor**: uma **regra** é orientação prévia, consultada antes de agir (`AGENTS.md`, `ARCHITECTURE.md`, specs, prompts de papel). Um **sensor** é verificação automática posterior, que roda depois da ação e detecta desvio (build, lint, testes, hooks `PostToolUse`/`PostFileSave`). Regras reduzem a chance de erro; sensores detectam o erro que passou pela regra. Um harness maduro usa os dois — não apenas um.

## 2. Estrutura de arquivos do projeto

### 2.1 Hierarquia de navegação

```text
AGENTS.md
    ↓
ARCHITECTURE.md
    ↓
docs/
    ├── index.md
    ├── ai_project_documentation_instructions.md  (este guia, copiado sem alteração)
    ├── specs/          (requisitos em EARS)
    ├── decisions/       (ADRs)
    ├── workflow.md      (revisão e branching)
    ├── design-docs/
    ├── exec-plans/
    ├── product-specs/
    ├── generated/
    └── references/
    ↓
.agents/
    ├── skills/          (workflows especializados)
    └── prompts/         (prompts complementares por papel)
    ↓
código, testes e configurações
```

A hierarquia representa navegação e nível de detalhe, não precedência sobre o código executável. Cada nível aponta para o de baixo; nenhum deve duplicar o conteúdo do outro.

### 2.2 Catálogo de arquivos e pastas

| Local | Papel | Detalhe |
| --- | --- | --- |
| `AGENTS.md` | Índice operacional curto (~100 linhas), carregado em toda sessão. | Seção A.0.2 do Anexo. |
| `ARCHITECTURE.md` | Mapa arquitetural de alto nível: domínios, limites, fluxos. | Seção A.0.3 do Anexo. |
| `docs/ai_project_documentation_instructions.md` | Este guia; referência canônica sob demanda, idêntica entre projetos. | — |
| `.agents/skills/` | Workflows especializados, recorrentes e reutilizáveis. Caminho canônico de Skills. | Seção A.4 do Anexo. |
| `.agents/prompts/` | Prompts por papel (ex.: bugfix, revisão) que complementam o harness global. | Seção 5 deste documento. |
| `docs/specs/` | Requisitos em notação EARS, antes de codar uma funcionalidade. | Seção 3 deste documento. |
| `docs/decisions/` | Registros de decisão de arquitetura (ADR). | Seção 4 deste documento. |
| `docs/workflow.md` | Workflow de revisão: branch separada, `git worktree`, Pull/Merge Request. | Seção 6 deste documento. |
| `docs/design-docs/`, `docs/exec-plans/`, `docs/product-specs/`, `docs/generated/`, `docs/references/` | Áreas documentais: designs/decisões informais, planos de execução, especificações de produto, artefatos gerados, referências de dependências. | Seção A.0.4 a A.0.8 do Anexo. |

Se outra ferramenta de agente exigir um caminho diferente de Skills (por exemplo `.claude/skills/`), esse caminho deve ser um link simbólico apontando para `.agents/skills/`, nunca uma cópia duplicada — a pasta canônica é sempre `.agents/skills/`.

### 2.3 Como decidir onde registrar uma informação

1. É necessário para qualquer primeira contribuição? Resumo ou link em `AGENTS.md`.
2. Explica limites e componentes de alto nível? `ARCHITECTURE.md`.
3. É um requisito ou comportamento esperado ainda não implementado? `docs/specs/`, usando EARS.
4. É uma decisão de arquitetura que outros vão precisar consultar antes de mudar algo relacionado? `docs/decisions/`, como ADR.
5. É um workflow especializado e reutilizável? Considere uma Skill em `.agents/skills/`.
6. É uma orientação de papel para um sub-agente (bugfix, revisão)? `.agents/prompts/`.
7. Registra uma decisão ou design mais informal, sem a rigidez de um ADR? `docs/design-docs/`.
8. Planeja trabalho relevante ainda não concluído? `docs/exec-plans/active/`.
9. É produzido por automação? `docs/generated/`, se precisar ser versionado.
10. Explica uma dependência externa no contexto do projeto? `docs/references/`.
11. É detalhe de implementação local e permanece claro no código? Mantenha no código, sem criar documento separado.
12. É uma convenção específica deste projeto (domínio, stack, comandos de build reais, VCS, branch principal)? Vai em `AGENTS.md`, `ARCHITECTURE.md` ou `docs/workflow.md` do próprio projeto — nunca neste guia.

Se nenhuma opção tiver conteúdo suficiente, não crie um novo arquivo.

## 3. Especificação antes de codar (EARS)

Antes de implementar uma funcionalidade nova ou um comportamento não trivial, registre requisitos (padrão EARS), critérios de aceite e a checklist de Tasks que implementa esses requisitos em `docs/specs/`:

- Ubíquo: "O `<sistema>` deve `<comportamento>`"
- Evento: "Quando `<gatilho>`, o `<sistema>` deve `<resposta>`"
- Estado: "Enquanto `<estado>`, o `<sistema>` deve `<resposta>`"
- Erro/comportamento indesejado: "Se `<condição>`, então o `<sistema>` deve `<resposta>`"
- Recurso opcional: "Onde `<feature presente>`, o `<sistema>` deve `<resposta>`"

Preceder a implementação por uma spec reduz ambiguidade antes de escrever código, torna o comportamento esperado testável e dá ao revisor um contrato claro para comparar com o resultado. Use o template e o exemplo preenchido em [`docs/specs/TEMPLATE.md`](specs/TEMPLATE.md); não duplique esse conteúdo aqui. Ferramentas com assistência nativa de planejamento (ex: modo Spec do Kiro) devem escrever o resultado nesse formato e local genéricos do projeto, não em seu formato ou pasta proprietários.

## 4. Decisões de arquitetura (ADR)

Registre um ADR quando a decisão:

- afeta mais de um módulo ou componente;
- é difícil ou cara de reverter depois;
- envolve alternativas descartadas que alguém vai perguntar "por que não X" no futuro;
- muda um limite, dependência ou responsabilidade descrito em `ARCHITECTURE.md`.

Não registre ADR para escolhas locais, reversíveis ou já cobertas pelas convenções de código do próprio projeto. Consulte `docs/decisions/` antes de uma mudança que toque uma decisão já registrada. Use o template e o exemplo preenchido em [`docs/decisions/TEMPLATE.md`](decisions/TEMPLATE.md); não duplique esse conteúdo aqui.

## 5. Sub-agentes por papel

Um prompt em `.agents/prompts/` complementa o harness global — ele nunca o substitui. Todo prompt de papel deve instruir o agente a ler `AGENTS.md` e `ARCHITECTURE.md` primeiro, e então restringir o escopo de atuação a um papel específico (por exemplo, corrigir apenas o bug descrito, ou revisar sem escrever).

Prompts recomendados como ponto de partida: `bugfix.md` e `review.md`. Um novo prompt de papel deve seguir o mesmo formato: harness global primeiro, escopo restrito, critério de conclusão explícito.

## 6. Workflow de revisão

Dois cenários, resumidos aqui e detalhados em [`docs/workflow.md`](workflow.md):

- **Local, sem plataforma de Pull/Merge Request**: toda tarefa roda em uma branch separada; a revisão é feita pelo diff (`git diff <base>...<branch>`); o merge é sempre manual. Para paralelismo local, use `git worktree` — cada worktree é uma pasta com sua própria branch ativa.
- **Plataforma com Pull/Merge Request**: cada tarefa declara explicitamente um branch base; a branch da tarefa nasce dele e o PR/MR é aberto de volta para ele; revisão humana e merge continuam manuais. Esse cenário permite múltiplos agentes em paralelo, cada um com sua branch/PR independente.

## 7. Modelo de execução do agente

O agente sempre trabalha em uma branch separada, nunca direto na branch principal ou nas branches de integração. A revisão acontece depois, via diff da branch — a aprovação prévia por plano não precisa ser obrigatória por padrão, desde que a branch separada esteja em uso. O merge continua sendo sempre uma ação manual e humana; o agente nunca executa `merge`, `push` para a branch principal, ou qualquer outra operação de escrita Git além da criação da própria branch de trabalho, exceto quando isso for explicitamente solicitado numa tarefa.

A branch separada é o mecanismo de segurança que substitui o gate de aprovação prévia: como nada chega à branch principal sem uma ação manual de merge, o risco de uma ação não revisada afetar o projeto fica contido à própria branch de trabalho.

## 8. Quando revisitar este guia

Este guia não é lido só uma vez. Ele deve ser reconsultado sempre que uma tarefa alterar a **estrutura documental ou organizacional** do projeto — não apenas o código. Isso inclui, entre outros:

- criar, mover, renomear ou remover uma Skill, um prompt de papel, uma spec, um ADR ou qualquer documento em `docs/`;
- reorganizar `AGENTS.md`, `ARCHITECTURE.md` ou `docs/index.md`;
- introduzir uma nova área documental não prevista no catálogo da Seção 2.2;
- migrar um caminho convencional (por exemplo, o local onde Skills ou prompts residem).

Ao revisitar este guia numa dessas situações, confirme:

1. a alteração segue as convenções descritas aqui (nomenclatura, formato, local correto conforme a Seção 2.3);
2. os documentos do próprio projeto — `AGENTS.md`, `ARCHITECTURE.md`, `docs/index.md` e qualquer outro índice afetado — descrevem a nova estrutura com clareza, sem duplicar o conteúdo detalhado que já vive no documento de destino;
3. nenhuma referência cruzada do projeto ainda aponta para um caminho antigo, caso algo tenha sido movido ou renomeado;
4. o checklist do Anexo (seção A.12) foi verificado antes de considerar a mudança concluída.

Este guia em si nunca deve ser editado para incorporar um fato específico de um projeto; ele só muda quando a convenção geral evolui.

---

## Anexo — Regras operacionais de manutenção documental

O conteúdo abaixo também é geral e reutilizável (complementa as seções 1 a 8); trata do processo de manter a documentação e a estrutura de um repositório coerentes ao longo do tempo, com nível de detalhe maior do que cabe nas seções acima.

### A.0 Responsabilidade de cada documento

- **A.0.1 README.md**: apresenta o projeto a pessoas e ferramentas pela primeira vez — propósito, pré-requisitos, caminho inicial de uso, links para documentação detalhada. Não é inventário arquitetural completo.
- **A.0.2 AGENTS.md**: índice operacional curto (~100 linhas). Inclui objetivo resumido, mapa do repositório, referência a `ARCHITECTURE.md`, comandos oficiais, workflow antes/depois de alterações, restrições críticas e Skills relevantes. Detalhes extensos ficam em `ARCHITECTURE.md`, `docs/`, `.agents/skills/`, código ou configurações.
- **A.0.3 ARCHITECTURE.md**: mapa arquitetural de alto nível — domínios, camadas, componentes, dependências, fluxos, interfaces e limites externos, restrições a preservar, pontos não determinados. Não descreve exaustivamente funções ou arquivos.
- **A.0.4 docs/design-docs/**: designs, princípios e decisões de funcionamento que exigem mais explicação do que o mapa arquitetural, mas sem a rigidez formal de um ADR.
- **A.0.5 docs/exec-plans/**: trabalhos relevantes que exigem etapas, riscos, migração ou coordenação (`active/` em execução, `completed/` concluídos com valor histórico).
- **A.0.6 docs/product-specs/**: comportamento esperado, requisitos e critérios de aceitação já consolidados como especificação de produto (distinto de `docs/specs/`, que é o ponto de entrada em EARS antes da implementação).
- **A.0.7 docs/generated/**: exclusivamente artefatos produzidos automaticamente, com fonte, gerador e condições de regeneração identificados.
- **A.0.8 docs/references/**: conhecimento de apoio sobre ferramentas, bibliotecas, protocolos e dependências usados pelo projeto, sem copiar documentação externa integralmente.

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

Crie uma Skill em `.agents/skills/` somente quando houver um workflow especializado, recorrente, relevante para o projeto, difícil de executar corretamente sem instruções, e estável o bastante para ser reutilizado. Não crie Skills genéricas para linguagem, Git ou ferramentas comuns sem uma necessidade específica do projeto.

Estrutura:

```text
.agents/skills/<skill-name>/
├── SKILL.md
├── scripts/       # opcional
├── references/    # opcional
└── assets/        # opcional
```

Toda `SKILL.md` deve começar com frontmatter YAML válido:

```yaml
---
name: nome-da-skill
description: "Executa um workflow específico e explica quando deve ser usado; palavras-chave relevantes."
compatibility: "Requer a ferramenta X na versão Y ou superior"
---
```

Regras de `name`: obrigatório, kebab-case, somente letras minúsculas/números/hífens, máximo 64 caracteres, não começa nem termina com hífen, sem hífens consecutivos, corresponde exatamente ao nome do diretório pai. `description` é obrigatória, até 1024 caracteres, explica o que a Skill faz e quando usar.

### A.5 Processo para qualquer modificação futura

1. **Classificar**: objetivo e critérios de sucesso, arquivos/módulos/produtos afetados, risco, se há código gerado/submódulo/dependência externa, se muda comportamento/arquitetura/build ou só documentação, validações disponíveis.
2. **Obter contexto proporcional**: inspecione a estrutura relevante, leia documentação e configurações aplicáveis, identifique linguagem/plataforma/toolchain, rastreie interfaces e dependências afetadas, verifique o estado Git inicial. Pare de investigar quando houver evidência suficiente.
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
- [ ] Skills em `.agents/skills/` possuem nome, diretório e frontmatter válidos.
- [ ] Specs em `docs/specs/` seguem a notação EARS e têm critérios de aceite verificáveis.
- [ ] ADRs em `docs/decisions/` registram alternativas descartadas e consequências.
- [ ] Prompts em `.agents/prompts/` referenciam o harness global e não o substituem.
- [ ] Links relativos resolvem corretamente, incluindo após qualquer migração de caminho.
- [ ] Arquivos gerados e suas fontes estão identificados.
- [ ] Nenhum submódulo foi alterado sem autorização.
- [ ] O diff contém somente mudanças explicáveis pela tarefa.
- [ ] Este guia (`docs/ai_project_documentation_instructions.md`) permanece livre de conteúdo específico do projeto.

### A.13 Antipadrões

Evite: documentação criada apenas para preencher uma árvore; `AGENTS.md` longo duplicando todo o repositório; arquitetura baseada em suposição não registrada; comandos hipotéticos apresentados como oficiais; specs sem critério de aceite verificável; ADR para decisões triviais ou reversíveis; Skills genéricas ou redundantes; edição direta de arquivos gerados; alterações silenciosas em submódulos; prompts de papel que substituem (em vez de complementar) o harness global; merge executado pelo próprio agente; fatos específicos de um projeto incorporados a este guia.

### A.14 Critérios de sucesso

A organização documental está adequada quando uma pessoa ou agente sem histórico prévio consegue: entender o propósito do projeto; encontrar o mapa arquitetural; localizar comandos e pré-requisitos reais; reconhecer limites, riscos e dependências externas; encontrar specs, ADRs e planos quando existirem; localizar Skills e prompts de papel; distinguir fonte manual de artefato gerado; modificar o projeto sem depender de conhecimento privado; validar e comunicar a mudança de forma reproduzível.
