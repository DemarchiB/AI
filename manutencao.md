# Manutenção documental

Cobre como a documentação de um projeto se mantém coerente ao longo do tempo: de onde vem o fato que ela registra, o que se exige das áreas que não têm template, como o texto é revisado e o que se confere ao terminar. O processo de conduzir uma mudança — classificar, validar, encerrar — é de [practices/engenharia.md](practices/engenharia.md); aqui ficam só as regras documentais.

**Leia este arquivo quando:** for criar, mover, renomear ou remover um documento; reorganizar a estrutura documental; documentar uma área a partir de evidência; ou encerrar uma mudança que alterou a documentação.

## 1. Princípios

1. O repositório é a fonte de verdade.
2. Conhecimento permanente é versionado em formatos simples e acessíveis.
3. A documentação reflete o projeto; o projeto não é reorganizado apenas para atender a uma ferramenta ou agente.
4. Documentos, diretórios, planos e Skills não são criados apenas para completar uma estrutura idealizada.

O papel de cada documento — o que ele é e o que ele não é — está no template correspondente, listado no catálogo da Seção *Estrutura de arquivos do projeto* do índice. Não há uma segunda descrição aqui: template é quem define forma e responsabilidade do documento que gera.

## 2. Áreas sem template

Quatro áreas não têm template e são de formato livre. O que se exige delas é o mínimo abaixo — e um template só nasce quando o segundo documento real da área mostrar qual é a forma repetida.

- **`docs/design-docs/`**: designs, princípios e decisões de funcionamento que exigem mais explicação do que o mapa arquitetural, mas sem a rigidez formal de um ADR. Mínimo: propósito, a alternativa que foi descartada e por quê, e a data. Máquina de estado é a exceção: tem template (`templates/fsm.md`).
- **`docs/exec-plans/`**: trabalhos que exigem etapas, riscos, migração ou coordenação. Mínimo: objetivo, etapas em ordem, risco de cada uma e critério de conclusão. `active/` para o que está em execução, `completed/` para o concluído com valor histórico.
- **`docs/product-specs/`**: comportamento vigente do produto, já consolidado e mantido enquanto esse comportamento existir. Mínimo: o formato de `templates/spec.md` sem as seções de trabalho (Tasks, Rastreabilidade), já que não há implementação pendente.
- **`docs/generated/`**: exclusivamente artefatos produzidos automaticamente. Mínimo: fonte, gerador e condições de regeneração identificados no próprio arquivo ou num README da pasta.

## 3. Independência de ferramenta

A documentação principal usa formatos amplamente acessíveis: Markdown, YAML quando necessário, scripts versionados, arquivos de configuração mantidos pelo próprio projeto. Configurações específicas de ferramentas (as de uma IDE, por exemplo) podem coexistir como camadas de adaptação, mas não são a única fonte de regras, arquitetura, comandos ou decisões. Não dependa exclusivamente de memória de um agente, histórico de conversas, prompts privados, regras exclusivas de uma IDE ou arquivos locais não versionados.

O caso mais frequente disso hoje são os arquivos de contexto que cada ferramenta de IA espera com nome próprio: a regra de como conviver com eles sem duplicar o `AGENTS.md` está em [practices/ia.md](practices/ia.md).

## 4. Fontes de evidência

Antes de documentar ou alterar uma área, procure evidências nesta ordem, adaptando ao projeto:

1. código-fonte e interfaces públicas;
2. arquivos de build, dependências e configuração;
3. testes e validações executáveis;
4. README e documentação versionada;
5. scripts operacionais e automações;
6. histórico de decisões explicitamente registrado (ADRs, considerando o `Status` de cada um);
7. comportamento observado e reproduzível.

Quando fontes divergirem: registre a divergência, determine qual fonte governa o comportamento atual, evite atualizar documentação com uma conclusão não comprovada, e solicite decisão quando a correção exigir conhecimento externo ao repositório.

## 5. Manutenção do texto

Atualize a documentação na mesma mudança quando houver alteração em propósito ou escopo, comandos e pré-requisitos, arquitetura, limites ou dependências, comportamento especificado, workflows especializados, formatos gerados, riscos e restrições operacionais.

Ao revisar: remova fatos obsoletos, preserve conteúdo ainda correto, substitua duplicações por links, diferencie fatos de decisões e de hipóteses, verifique links e caminhos, mantenha o texto conciso — cortando duplicação e adjetivo, nunca a justificativa de uma regra.

## 6. Checklist de mudança documental

- [ ] `AGENTS.md` continua curto, funciona como índice e registra qual estado do conjunto o projeto adotou.
- [ ] `ARCHITECTURE.md` permanece de alto nível e continua único no projeto.
- [ ] Documentos criados partiram do template correspondente, ou pertencem a uma área sem template e cumprem o mínimo da Seção *Áreas sem template*.
- [ ] Não existe arquivo `TEMPLATE.md` (ou equivalente) duplicando um template do conjunto.
- [ ] Skills possuem nome, diretório e frontmatter válidos.
- [ ] Specs seguem a notação EARS, têm `Status` e critérios de aceite verificáveis.
- [ ] ADRs têm `Status` e `Data`, registram alternativas e consequências, e os superados apontam para o substituto.
- [ ] Prompts de papel referenciam o harness global e não o substituem.
- [ ] Links relativos resolvem corretamente, incluindo após qualquer migração de caminho.
- [ ] Arquivos gerados e suas fontes estão identificados.
- [ ] Fatos ainda não confirmados aparecem com marcação de estado provisório.
- [ ] Nenhum documento do conjunto de convenções recebeu conteúdo específico do projeto.

## 7. Antipadrões

Evite: documentação criada apenas para preencher uma árvore; `AGENTS.md` longo duplicando todo o repositório; arquitetura baseada em suposição não registrada; comandos hipotéticos apresentados como oficiais; specs sem critério de aceite verificável; ADR para decisões triviais ou reversíveis; ADR reescrito ou apagado quando a decisão muda, em vez de substituído; arquivos de template espalhados pelo projeto duplicando os do conjunto; arquivo de domínio criado vazio "para o futuro"; a mesma regra repetida em dois lugares com redações diferentes; arquivo de contexto de ferramenta com uma cópia do `AGENTS.md` em vez de um ponteiro; fato específico de um projeto incorporado a um arquivo do conjunto.

## 8. Critérios de sucesso

A organização documental está adequada quando uma pessoa ou agente sem histórico prévio consegue: entender o propósito do projeto; encontrar o mapa arquitetural; localizar comandos e pré-requisitos reais; reconhecer limites, riscos e dependências externas; encontrar specs, ADRs e planos quando existirem, distinguindo decisão vigente de histórica; localizar Skills e prompts de papel; saber em qual arquivo do conjunto uma regra está descrita; criar um documento novo no formato correto sem procurar exemplos fora do repositório; saber quais verificações rodar e o que fazer quando falham; distinguir fonte manual de artefato gerado; modificar o projeto sem depender de conhecimento privado; validar e comunicar a mudança de forma reproduzível.
