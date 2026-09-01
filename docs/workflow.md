# Workflow de revisão

Este projeto usa Git com remoto no GitLab (`gitlab.weg.net/dds/plcs/plc200.git`), mas não há hoje um workflow de Merge Request documentado nem pipeline de CI configurado (ver [AGENTS.md](../AGENTS.md), seção "Build e validação"). Os dois cenários abaixo cobrem o fluxo local, sempre disponível, e o fluxo com plataforma de MR, para quando ele for adotado formalmente.

## Cenário 1 — Local, sem plataforma de MR

Este é o fluxo padrão hoje neste repositório:

1. Toda tarefa de agente roda em uma branch separada, nunca direto na
   branch principal ou nas branches de integração (ex: `develop`,
   `master`, `stable/*`): `git checkout -b fix-uart-driver`.
2. Ao terminar, revise o diff completo antes de decidir o próximo
   passo: `git diff develop...fix-uart-driver`.
3. O merge para a branch de destino é sempre um comando executado
   manualmente por um humano. O agente nunca executa o merge, mesmo
   que sugira que a mudança está pronta.

Um único diretório de trabalho local só tem uma branch ativa por vez.
Para rodar mais de um agente em paralelo localmente, use
`git worktree` — cada worktree é uma pasta separada com sua própria
branch ativa, a partir do mesmo repositório:
`git worktree add ../plc200-bugfix fix-uart-driver`

## Cenário 2 — Plataforma com Merge Request (permite paralelismo real)

Quando este fluxo for adotado no GitLab do projeto, este é o preferido
para rodar múltiplos agentes em paralelo, cada um em sua própria
branch/ambiente:

1. Para cada tarefa, especifique explicitamente o **branch base** —
   o branch a partir do qual a nova branch deve ser criada, que é
   também o branch de destino do Merge Request (ex: base = `develop`).
2. O agente cria sua branch a partir do branch base indicado (ex:
   `fix-uart-driver` a partir de `develop`), nunca a partir de outro
   branch não especificado.
3. Ao concluir, o agente abre um Merge Request da sua branch de volta
   para o mesmo branch base indicado no passo 1 — nunca direto para a
   branch principal, a menos que seja explicitamente o branch base da
   tarefa.
4. Revisão humana do MR é obrigatória antes do merge, e o merge em si
   continua sendo uma ação humana, não do agente.
5. Múltiplos agentes podem rodar assim ao mesmo tempo, cada um com sua
   branch e seu MR independentes, desde que cada tarefa informe
   claramente qual é o branch base a ser usado.

## Regra operacional deste repositório

Independentemente do cenário, o agente nunca executa `commit`,
`push`, `merge`, `rebase` ou qualquer outra operação de escrita Git
por conta própria, exceto quando explicitamente solicitado numa
tarefa (ver [AGENTS.md](../AGENTS.md), seção "Regras operacionais").
A branch separada é o mecanismo de segurança que substitui a
aprovação prévia por plano.
