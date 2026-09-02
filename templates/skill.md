# Template: Skill (`.agents/skills/<nome-da-skill>/SKILL.md`)

**Quando criar uma Skill:** somente quando houver um workflow especializado, recorrente, relevante para o projeto, difícil de executar corretamente sem instruções, e estável o bastante para ser reutilizado. Não crie Skills genéricas para linguagem, VCS ou ferramentas comuns sem uma necessidade específica do projeto.

**Caminho canônico:** `.agents/skills/`. Se outra ferramenta de agente exigir caminho diferente (por exemplo `.claude/skills/`), esse caminho deve ser um link simbólico apontando para `.agents/skills/`, nunca uma cópia duplicada. Crie o link apenas quando `.agents/skills/` já tiver conteúdo.

**Estrutura da pasta:**

```text
.agents/skills/<nome-da-skill>/
├── SKILL.md
├── scripts/       # opcional
├── references/    # opcional
└── assets/        # opcional
```

**Regras de `name`:** obrigatório, kebab-case, somente letras minúsculas/números/hífens, máximo 64 caracteres, não começa nem termina com hífen, sem hífens consecutivos, corresponde exatamente ao nome do diretório pai. **`description`:** obrigatória, até 1024 caracteres, explica o que a Skill faz e quando usar. **`compatibility`:** convenção deste conjunto, não campo padronizado — serve para leitura humana e pode ser ignorado pela ferramenta que carrega a Skill; não coloque nele nada de que a execução dependa.

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
