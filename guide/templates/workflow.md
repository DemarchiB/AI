# Template: `docs/workflow.md`

**Quando usar:** no dia zero, antes do `AGENTS.md`, que vai apontar para ele.

**Papel:** instanciar as regras de revisão do domínio de engenharia com o que é específico deste projeto — VCS em uso, remoto, nomes reais da branch principal e das branches de integração, e se a plataforma de PR/MR já é usada hoje. As regras em si estão em `guide/practices/engenharia.md` e não mudam; este arquivo diz como elas se realizam aqui.

**Convenções:** os comandos abaixo estão na notação do Git; projeto em outro VCS os substitui pelos equivalentes. VCS ou remoto ainda não escolhidos entram como `<a definir>`, e o documento descreve o Cenário 1 sem citar comando concreto até a escolha existir.

```markdown
# Workflow de revisão

Este projeto usa <VCS> com remoto em <plataforma/URL>. Hoje <há / não há>
workflow de Pull/Merge Request formalizado e <há / não há> pipeline de CI.
Enquanto não houver, vale o Cenário 1.

## Cenário 1 — Local, sem plataforma de PR/MR

1. Toda tarefa roda em uma branch separada, criada a partir de um base
   declarado, nunca direto na branch principal ou nas de integração
   (ex.: `<develop>`, `<main>`, `<stable/*>`).
2. Revisão pelo diff completo: `git diff <base>...<branch>`.
3. O merge é sempre executado manualmente por um humano.
4. Para paralelismo local: `git worktree add -b <branch> ../<pasta> <base>`.
   Remova depois com `git worktree remove ../<pasta>`.

## Cenário 2 — Plataforma com PR/MR

1. Cada tarefa declara o **branch base** — origem da branch de trabalho e
   destino do PR/MR.
2. A branch nasce desse base, nunca de outro não especificado.
3. O PR/MR é aberto de volta para o mesmo base.
4. Revisão humana obrigatória; merge é ação humana.
5. Vários trabalhos podem correr em paralelo, cada um com sua branch/PR.

## Convenções deste repositório

- Branch: `<tipo>/<assunto-curto>`, tipos aceitos: `feat`, `fix`,
  `refactor`, `docs`, `test`, `chore`.
- Commit: assunto no imperativo, corpo explicando o porquê, citação do
  `REQ-NNN` ou do ADR quando houver.
- O agente nunca executa `merge`, `push` para branch principal, `rebase`
  de histórico compartilhado ou `push --force` por conta própria.
```
