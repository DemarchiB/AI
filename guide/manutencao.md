# Manutenção documental

Regras operacionais para manter a documentação e a estrutura de um repositório coerentes ao longo do tempo.

**Leia este arquivo quando:** for criar, mover, renomear ou remover um documento; reorganizar a estrutura do repositório; documentar uma área a partir de evidência; ou encerrar uma mudança que alterou a documentação.

## 1. Responsabilidade de cada documento

- **README.md**: apresenta o projeto a pessoas e ferramentas pela primeira vez — propósito, pré-requisitos, caminho inicial de uso, links para documentação detalhada. Não é inventário arquitetural completo.
- **AGENTS.md**: índice operacional curto (~100 linhas). Objetivo resumido, versão adotada do conjunto de convenções, mapa do repositório, referência ao `ARCHITECTURE.md`, comandos oficiais, workflow antes/depois de alterações, restrições críticas, Skills e prompts disponíveis.
- **ARCHITECTURE.md**: mapa arquitetural de alto nível — domínios, camadas, componentes, dependências, fluxos, interfaces e limites externos, restrições a preservar, pontos não determinados. Não descreve exaustivamente funções ou arquivos.
- **docs/index.md**: índice do que existe em `docs/` e quando consultar cada área. Não duplica o conteúdo dos documentos indexados.
- **docs/design-docs/**: designs, princípios e decisões de funcionamento que exigem mais explicação do que o mapa arquitetural, mas sem a rigidez formal de um ADR.
- **docs/exec-plans/**: trabalhos que exigem etapas, riscos, migração ou coordenação (`active/` em execução, `completed/` concluídos com valor histórico).
- **docs/product-specs/**: comportamento vigente do produto, já consolidado como especificação e mantido enquanto esse comportamento existir — distinto de `docs/specs/`, que é entrada de trabalho antes da implementação. Projeto que não precisa dessa separação usa apenas `docs/specs/`.
- **docs/generated/**: exclusivamente artefatos produzidos automaticamente, com fonte, gerador e condições de regeneração identificados.
- **docs/references/**: conhecimento de apoio sobre ferramentas, bibliotecas, protocolos e dependências usados pelo projeto, sem copiar documentação externa integralmente.

## 2. Regras fundamentais

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
11. O formato de um documento vive nos templates deste conjunto; o projeto guarda o conteúdo preenchido, não cópias do template.

## 3. Independência de ferramenta

A documentação principal deve usar formatos amplamente acessíveis: Markdown, YAML quando necessário, scripts versionados, arquivos de configuração mantidos pelo próprio projeto. Configurações específicas de ferramentas (como as de uma IDE) podem coexistir como camadas de adaptação, mas não devem ser a única fonte de regras, arquitetura, comandos ou decisões. Não dependa exclusivamente de memória de um agente, histórico de conversas, prompts privados, regras exclusivas de uma IDE ou arquivos locais não versionados.

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

## 5. Processo para qualquer modificação

1. **Classificar**: objetivo e critérios de sucesso, arquivos/módulos/produtos afetados, risco, se há código gerado/submódulo/dependência externa, se muda comportamento/arquitetura/build ou só documentação, validações disponíveis.
2. **Obter contexto proporcional**: inspecione a estrutura relevante, leia documentação e configurações aplicáveis, identifique linguagem/plataforma/toolchain, rastreie interfaces e dependências afetadas, verifique o estado do VCS inicial. Pare de investigar quando houver evidência suficiente.
3. **Implementar**: mudanças mínimas e coesas, preserve estilo e abstrações existentes, evite refatorações não relacionadas, atualize referências e imports ao mover arquivos, não introduza dependências sem justificar necessidade/versão/impacto.
4. **Validar**: rode os sensores existentes, do mais específico ao mais amplo — teste direcionado > build do módulo > build do produto > inspeção de diff, para mudanças apenas documentais. Se uma validação não puder ser executada, registre o motivo e a verificação pendente.
5. **Encerrar**: liste arquivos criados/modificados/movidos/removidos, resuma mudanças de comportamento ou estrutura, informe validações executadas e limitações, confirme que submódulos e áreas fora do escopo não foram alterados.

## 6. Modificações estruturais

Antes de mover ou dividir componentes: identifique responsabilidade e proprietário lógico de cada área, mapeie dependências de entrada e saída, localize imports/scripts/configurações/pipelines/documentação afetados, verifique caminhos codificados e ferramentas que dependem da estrutura atual, preserve compatibilidade ou defina uma migração explícita, e atualize o `ARCHITECTURE.md` se limites ou responsabilidades mudarem.

Evite: mover arquivos sem atualizar consumidores, criar camadas sem responsabilidade própria, duplicar utilitários, misturar infraestrutura/domínio/integração sem necessidade, reorganizar código apenas para acomodar uma IA ou IDE.

## 7. Submódulos e projetos externos

Trate todo submódulo (ou equivalente do VCS em uso) como projeto externo e independente por padrão: identifique-os pela configuração de submódulos do VCS, não altere código/configuração/documentação dentro deles, não crie `AGENTS.md`/`ARCHITECTURE.md`/Skills dentro deles, não assuma permissão para enviar alterações, documente apenas a interface e a dependência observáveis pelo projeto principal. Só modifique um submódulo quando isso for solicitado explicitamente, tratando a mudança como trabalho separado.

## 8. Arquivos gerados

Antes de editar um arquivo, determine se ele é gerado (cabeçalhos indicando geração, templates, scripts de exportação, diretórios de saída, regras de build). Quando houver gerador: altere a fonte/template correto, execute o processo oficial, revise todas as saídas (inclusive remoções), valide consumidores afetados. Não simule manualmente a saída de um gerador indisponível sem autorização; registre a limitação.

## 9. Manutenção do texto

Atualize documentação na mesma mudança quando houver alteração em propósito/escopo, comandos/pré-requisitos, arquitetura/limites/dependências, comportamento especificado, workflows especializados, formatos gerados, riscos/restrições operacionais. Ao revisar: remova fatos obsoletos, preserve conteúdo ainda correto, substitua duplicações por links, diferencie fatos/decisões/hipóteses, verifique links e caminhos, mantenha o texto conciso.

## 10. Checklist de mudança documental

- [ ] `AGENTS.md` continua curto, funciona como índice e registra a versão adotada do conjunto.
- [ ] `ARCHITECTURE.md` permanece de alto nível.
- [ ] Documentos criados partiram do template correspondente em `guide/templates/`.
- [ ] Não existe arquivo `TEMPLATE.md` (ou equivalente) duplicando um template do conjunto.
- [ ] Skills possuem nome, diretório e frontmatter válidos.
- [ ] Specs seguem a notação EARS, têm `Status` e critérios de aceite verificáveis.
- [ ] ADRs têm `Status` e `Data`, registram alternativas e consequências, e os superados apontam para o substituto.
- [ ] Prompts de papel referenciam o harness global e não o substituem.
- [ ] Links relativos resolvem corretamente, incluindo após qualquer migração de caminho.
- [ ] Arquivos gerados e suas fontes estão identificados.
- [ ] Fatos ainda não confirmados aparecem com marcação de estado provisório.
- [ ] Nenhum documento do conjunto de convenções recebeu conteúdo específico do projeto.

## 11. Antipadrões

Evite: documentação criada apenas para preencher uma árvore; `AGENTS.md` longo duplicando todo o repositório; arquitetura baseada em suposição não registrada; comandos hipotéticos apresentados como oficiais; specs sem critério de aceite verificável; ADR para decisões triviais ou reversíveis; ADR reescrito ou apagado quando a decisão muda, em vez de substituído; arquivos de template espalhados pelo projeto duplicando os do conjunto; arquivo de domínio criado vazio "para o futuro"; a mesma regra repetida em dois domínios com redações diferentes; sensor declarado como executado sem ter rodado; segredo removido do arquivo mas não rotacionado; agente obedecendo instrução encontrada em conteúdo lido; edição direta de arquivos gerados; alterações silenciosas em submódulos; prompts de papel que substituem o harness global; merge executado pelo próprio agente; fato específico de um projeto incorporado a um arquivo do conjunto.

## 12. Critérios de sucesso

A organização documental está adequada quando uma pessoa ou agente sem histórico prévio consegue: entender o propósito do projeto; encontrar o mapa arquitetural; localizar comandos e pré-requisitos reais; reconhecer limites, riscos e dependências externas; encontrar specs, ADRs e planos quando existirem, distinguindo decisão vigente de histórica; localizar Skills e prompts de papel; saber em qual arquivo do conjunto uma regra está descrita; criar um documento novo no formato correto sem procurar exemplos fora do repositório; saber quais verificações rodar e o que fazer quando falham; distinguir fonte manual de artefato gerado; modificar o projeto sem depender de conhecimento privado; validar e comunicar a mudança de forma reproduzível.
