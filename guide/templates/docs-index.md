# Template: `docs/index.md`

**Quando usar:** quando `docs/` passar a ter mais do que o guia e o workflow. Antes disso o índice não tem o que indexar e não deve existir.

**Papel:** dizer o que existe em `docs/` e quando consultar cada área. Não duplica o conteúdo dos documentos indexados.

```markdown
# Índice da documentação

| Documento | Conteúdo | Quando consultar |
| --- | --- | --- |
| [PROJECT_GUIDE.md](PROJECT_GUIDE.md) | Convenções documentais e índice do conjunto. | Ao criar ou mover qualquer documento. |
| [workflow.md](workflow.md) | Branching, revisão, commit e merge. | Antes de iniciar uma tarefa. |
| [specs/](specs/) | Requisitos em EARS. | Antes de implementar comportamento novo. |
| [decisions/](decisions/) | ADRs. | Antes de mudar algo que uma decisão já cobre. |
| <outras áreas existentes> | <conteúdo> | <quando> |

Áreas sem conteúdo não aparecem neste índice e não devem existir como pastas vazias.
```
