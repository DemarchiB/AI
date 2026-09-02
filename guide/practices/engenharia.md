# Domínio: engenharia e qualidade

Cobre como uma mudança entra no repositório e como se verifica que ela está correta: branching e revisão, mensagens de commit, verificações automáticas e tratamento de segredos. Vale para qualquer contribuição, feita por pessoa ou por agente. O que é específico do trabalho com agentes está em [ia.md](ia.md).

**Leia este arquivo quando:** for iniciar, revisar ou integrar uma mudança; for preencher o `docs/workflow.md` do projeto; ou for decidir quais verificações rodar antes de concluir uma tarefa.

## 1. Workflow de revisão

Dois cenários cobrem qualquer projeto: o fluxo local, sempre disponível, e o fluxo com plataforma de Pull/Merge Request. As regras abaixo são completas e valem como estão. O que vale é o conceito — branch de trabalho separada, branch base declarado, diff contra o ponto comum, cópias de trabalho independentes para paralelismo; os comandos aparecem na notação do Git por ser a mais difundida, como ilustração. Projeto em outro VCS registra os comandos equivalentes no seu `docs/workflow.md`, sem que a regra mude. Onde o VCS não tiver branch local barata, o equivalente pode não ser um comando e sim outro mecanismo de isolamento — cópia de trabalho separada, changelist, shelve; o que permanece obrigatório é o princípio: trabalho isolado do tronco, revisão por diff antes de integrar, e integração feita manualmente por uma pessoa.

### Cenário 1 — Local, sem plataforma de PR/MR

1. Toda tarefa roda em uma branch separada, criada a partir de um branch base declarado, nunca direto na branch principal ou nas branches de integração.
2. Ao terminar, a revisão é feita pelo diff completo da branch contra esse base: `git diff <base>...<branch>`. A forma com três pontos compara contra o merge-base, então mudanças que entraram no base depois não poluem o diff.
3. O merge é sempre um comando executado manualmente por um humano. Um agente nunca executa o merge, mesmo que sugira que a mudança está pronta.
4. Um diretório de trabalho local só tem uma branch ativa por vez. Para trabalhar em mais de uma branch em paralelo localmente, use `git worktree` — cada worktree é uma pasta separada com sua própria branch ativa, a partir do mesmo repositório: `git worktree add -b <branch> ../<pasta> <base>`.

Como as worktrees compartilham o mesmo repositório, os commits feitos nelas já estão visíveis no diretório principal no instante em que são criados: revisão (`git log --oneline <base>..<branch>`, `git diff <base>...<branch>`) e merge acontecem normalmente a partir dele, sem remoto no meio. Quatro ressalvas práticas:

- a mesma branch não pode estar ativa em duas worktrees — o VCS recusa, e isso é proteção, não limitação;
- só o histórico é compartilhado: cada worktree começa sem artefatos de build e sem arquivos ignorados ou não versionados (configuração local, ambiente virtual, dependências baixadas), e projetos de IDE com caminho absoluto podem precisar de reimportação;
- submódulos não vêm populados — inicialize-os dentro da worktree nova;
- remova a worktree pelo comando próprio (`git worktree remove`), nunca apagando a pasta na mão, para não deixar metadado órfão.

Worktree isola o repositório, não o ambiente: trabalhos em paralelo continuam disputando toolchain, portas, dispositivos e hardware de gravação/depuração.

Enquanto o projeto não tiver uma plataforma de PR/MR efetivamente adotada, o Cenário 1 é o vigente, e o `docs/workflow.md` registra o Cenário 2 como o fluxo previsto para quando ela existir — nunca como se já valesse.

### Cenário 2 — Plataforma com PR/MR

1. Cada tarefa declara explicitamente o **branch base**: o branch de onde a branch de trabalho nasce e para onde o PR/MR é aberto de volta.
2. A branch da tarefa é criada a partir desse base, nunca de outro branch não especificado.
3. Ao concluir, o PR/MR é aberto de volta para o mesmo base — nunca direto para a branch principal, a menos que ela seja explicitamente o base da tarefa.
4. Revisão humana é obrigatória antes do merge, e o merge continua sendo uma ação humana.
5. Vários trabalhos podem correr em paralelo, cada um com sua branch e seu PR/MR independentes.

## 2. Convenções de branch e commit

Estas convenções existem para que o diff seja revisável e para fechar a rastreabilidade **spec → commit → revisão**, que sem elas termina dentro da spec.

1. **Uma tarefa, uma branch, um assunto.** Nome no formato `<tipo>/<assunto-curto>` em kebab-case, com os tipos: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`.
2. **Assunto do commit no imperativo**, curto (alvo de 72 caracteres) e sem ponto final.
3. **O corpo explica o porquê e o impacto**, não o que o diff já mostra. Commit sem corpo é aceitável apenas quando o assunto esgota a explicação.
4. **Todo commit que implementa requisito cita o identificador** (`REQ-NNN`) e, quando houver, o ADR relacionado. O mesmo vale para a descrição do PR/MR.
5. **Não misture assuntos no mesmo commit.** Reformatação em massa, renomeação de arquivos e mudança de comportamento vão em commits separados — misturá-los torna o diff irrevisável.
6. **Histórico já compartilhado não é reescrito** sem combinação explícita entre quem trabalha nele.

## 3. Sensores e validação automática

Uma **regra** é orientação prévia, consultada antes de agir; um **sensor** é verificação automática posterior, que roda depois da ação e detecta desvio. Regras reduzem a chance de erro; sensores detectam o erro que passou pela regra. Um projeto que só tem regras depende de todo mundo lembrar de tudo.

Conjunto mínimo, em ordem crescente de custo:

1. **Build** do módulo afetado.
2. **Teste direcionado** à mudança, ampliando para a suíte conforme o risco.
3. **Lint / análise estática** na configuração do próprio projeto.
4. **Verificação de links relativos** da documentação — o que impede que uma migração de caminho quebre referências em silêncio.
5. **Varredura de segredos** antes do commit.

Regras de uso:

- os sensores existentes são executados **antes** de declarar a tarefa concluída, e quem executou informa quais rodou;
- sensor que não existe no projeto é registrado como verificação pendente, com o motivo — nunca simulado, nunca presumido como aprovado;
- sensor que falha bloqueia a conclusão: o resultado é corrigir ou relatar, não seguir adiante;
- onde o ambiente permitir, os sensores rodam automaticamente (hook local, verificação de pré-commit, pipeline). Sensor que depende de alguém lembrar não é sensor, é regra.

## 4. Segredos e dados sensíveis

1. Credencial, chave, token, certificado privado ou dado pessoal nunca vão para o repositório — nem em código, nem em configuração versionada, nem em spec, ADR, exemplo de documentação ou log de execução colado num documento.
2. O `.gitignore` (ou equivalente do VCS) faz parte do harness: mantê-lo correto é parte da tarefa que introduz um arquivo local sensível. No mínimo ele cobre artefatos de build, dependências instaladas, configuração local de máquina ou IDE, arquivos de ambiente e credenciais (`.env` e equivalentes) e saídas de ferramenta — e nunca serve de justificativa para manter um segredo real dentro da pasta do projeto.
3. Documentação cita o **nome** da variável ou do parâmetro de configuração, nunca o valor.
4. Se um segredo chegou ao histórico, **rotacione o segredo**. Remover o arquivo num commit seguinte não desfaz a exposição: o valor continua no histórico e em toda cópia já clonada.
5. Dado de produção (log real, dump, base de clientes) não entra no repositório nem em exemplo de documentação; use dado sintético.

## 5. Testes e qualidade

Use testes existentes como mecanismo de validação, não como especificação absoluta. Execute primeiro os testes mais direcionados, amplie a validação conforme o risco, não declare cobertura que não foi medida, registre validações manuais quando forem o mecanismo real do projeto.

## 6. Scripts, comandos e dependências

Documente somente comandos existentes e verificáveis (configuração, build, testes, lint, geração, empacotamento, execução, implantação), registrando diretório de execução, pré-requisitos, parâmetros, arquivos produzidos, efeitos colaterais e modo de validação quando relevante. Não transforme comandos inferidos em instruções oficiais. Dependências novas devem ser necessárias, confiáveis e versionadas de forma compatível; nomes incomuns devem ser verificados para evitar pacotes maliciosos ou typosquatting. Nenhum comando documentado deve conter credencial embutida.

## Checklist deste domínio

- [ ] A mudança rodou em branch separada, a partir de um base declarado.
- [ ] Commits seguem a Seção 2 e citam requisito ou ADR quando aplicável.
- [ ] Os sensores existentes foram executados; os ausentes, registrados como pendência.
- [ ] Nenhum segredo, credencial ou dado de produção entrou no diff ou na documentação.
- [ ] Nenhum comando inferido foi documentado como oficial.
