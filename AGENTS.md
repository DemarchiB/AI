# AGENTS.md

## Objetivo do projeto
Este repositório é o conjunto de convenções que outros projetos adotam em `docs/guide/`. Uma
contribuição está completa quando a regra alterada tem um dono só, nenhum arquivo estourou seu
orçamento de contexto, e o corpo do commit declara o impacto para os projetos que já adotaram.

> Num projeto que adotou o conjunto, este arquivo vira `docs/guide/AGENTS.md` — um `AGENTS.md`
> aninhado, que vale só para esta pasta. O `AGENTS.md` da raiz do projeto continua sendo o
> operacional; este só é carregado quando a tarefa toca o conjunto.

## Convenções adotadas
Este repositório segue a si mesmo: [PROJECT_GUIDE.md](PROJECT_GUIDE.md).

## Mapa do repositório
| Caminho | Conteúdo |
| --- | --- |
| `PROJECT_GUIDE.md` | Índice: onde cada informação mora; lido em toda tarefa de todo projeto. |
| `adocao.md` | Como um projeto passa a usar o conjunto. |
| `manutencao.md` | Como o projeto mantém sua documentação. |
| `manutencao-do-conjunto.md` | Como este conjunto evolui: propagação, domínios, orçamento. |
| `practices/` | Um arquivo por domínio. |
| `templates/` | Um arquivo por documento gerado. |

Este repositório não tem `ARCHITECTURE.md` — não há código nem componentes, e a estrutura
inteira está na Seção *Estrutura de arquivos do projeto* do `PROJECT_GUIDE.md`.

## Comandos oficiais
| Ação | Comando | Diretório |
| --- | --- | --- |
| Orçamento de contexto | `LC_ALL=C.UTF-8 wc -m PROJECT_GUIDE.md practices/*.md` | raiz |
| Verificação de links | `<a definir: nenhuma ferramenta adotada ainda>` | — |
| Varredura de segredos | `<a definir: nenhuma ferramenta adotada ainda>` | — |

Somente comandos verificados. Comando inferido não entra aqui.

## Antes de alterar
1. Ler o `PROJECT_GUIDE.md` e o `manutencao-do-conjunto.md`.
2. Procurar a regra no conjunto inteiro antes de escrevê-la: ela pode já ter um dono.
3. Criar branch de trabalho `<tipo>/<assunto-curto>` a partir de `main`.

## Depois de alterar
1. Medir o orçamento dos arquivos tocados (`LC_ALL=C.UTF-8 wc -m`).
2. Conferir o checklist de `manutencao-do-conjunto.md`.
3. Se alguma regra mudou de sentido, declarar no corpo do commit o que os projetos que já
   adotaram precisam fazer.

## Restrições críticas
- Nenhum fato específico de um projeto entra em arquivo deste conjunto.
- Uma regra tem um dono só; o segundo lugar recebe ponteiro, nunca uma segunda redação.
- Referência cruzada cita arquivo e **título** da seção, nunca só o número.
- `PROJECT_GUIDE.md` tem teto firme de 18.000 caracteres; cada domínio, gatilho de revisão em 15.000.
- O agente não executa merge, push para `main` nem reescrita de histórico.

## Skills disponíveis
Nenhuma até o momento.

## Prompts de papel disponíveis
Nenhum até o momento.
