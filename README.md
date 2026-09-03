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
# como cópia (a versão adotada é declarada no AGENTS.md do projeto)
# como submódulo (a versão adotada é o commit fixado)
git submodule add https://github.com/DemarchiB/AI.git docs/guide
```

Depois siga [`adocao.md`](adocao.md): quatro documentos obrigatórios, nesta ordem —
`ARCHITECTURE.md`, `docs/workflow.md`, `AGENTS.md`, `README.md`.

## Estrutura

```text
PROJECT_GUIDE.md            índice: onde cada informação mora, e o que ler para cada tarefa
adocao.md                   como um projeto adota o conjunto
manutencao.md               como o projeto mantém sua documentação
manutencao-do-conjunto.md   como este conjunto evolui (domínios, orçamento, versão)
practices/                  um arquivo por domínio: engenharia, ia, c-embarcado, firmware
templates/                  um arquivo por documento: o formato de cada um
CHANGELOG.md                o que mudou em cada versão
```

## Versão

A versão é única para o conjunto inteiro, declarada no topo de `PROJECT_GUIDE.md` e marcada com
tag no VCS. Um projeto que adotou por cópia registra em seu `AGENTS.md` a versão que copiou e
consulta o [`CHANGELOG.md`](CHANGELOG.md) para decidir se atualiza.

O conteúdo é escrito em português. Uso interno.
