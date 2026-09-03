# Conjunto de convenções de projeto

Este repositório **não é um projeto** — é o conjunto de convenções que outros projetos adotam
inteiro, sem alteração. Ele diz onde cada informação mora, como se trabalha em cada domínio e qual
o formato de cada documento, de modo que uma pessoa ou um agente de IA consiga entrar num
repositório qualquer e saber onde procurar e onde escrever.

> Se você chegou aqui por `docs/guide/README.md` dentro de um projeto: este é o README **do
> conjunto de convenções**, não o do projeto. O README do projeto está na raiz dele.

## Por onde começar

| Você é... | Leia |
| --- | --- |
| alguém adotando o conjunto num projeto | [`adocao.md`](adocao.md) |
| alguém trabalhando num projeto que já adotou | [`PROJECT_GUIDE.md`](PROJECT_GUIDE.md) — é o índice, e ele diz o que ler para cada tarefa |
| alguém alterando **este** conjunto | [`manutencao-do-conjunto.md`](manutencao-do-conjunto.md) e [`AGENTS.md`](AGENTS.md) |

## Como um projeto adota

Monte o conjunto em `docs/guide/` de uma das duas formas — os caminhos internos são relativos, e
as duas funcionam sem editar nada:

```
# como submódulo: o commit fixado é o estado adotado
git submodule add https://github.com/DemarchiB/AI.git docs/guide

# como cópia: registre no AGENTS.md do projeto o commit de origem
git -C <clone-do-conjunto> rev-parse --short HEAD
```

Depois siga [`adocao.md`](adocao.md): quatro documentos obrigatórios, nesta ordem —
`ARCHITECTURE.md`, `docs/workflow.md`, `AGENTS.md`, `README.md`.

## Estrutura

```text
PROJECT_GUIDE.md            índice: onde cada informação mora, e o que ler para cada tarefa
adocao.md                   como um projeto adota o conjunto
manutencao.md               como o projeto mantém sua documentação
manutencao-do-conjunto.md   como este conjunto evolui (propagação, domínios, orçamento)
practices/                  um arquivo por domínio: engenharia, ia, c-embarcado, firmware
templates/                  um arquivo por documento: o formato de cada um
```

## Estado adotado

Não há número de versão nem changelog: o conjunto é identificado pelo commit. Um projeto que adota
por **submódulo** tem o estado fixado no `.gitmodules`; um projeto que adota por **cópia** registra
o commit de origem numa linha do seu `AGENTS.md`. Para saber o que mudou desde então,
`git diff <estado adotado>..main` aqui neste repositório.

Quando um arquivo daqui muda, os projetos que adotaram **reavaliam** — leem o diff e decidem se
atualizam. Commits que mudam o sentido de uma regra declaram, no corpo, o que quem já adotou
precisa fazer.

O conteúdo é escrito em português. Uso interno.
