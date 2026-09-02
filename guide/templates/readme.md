# Template: `README.md`

**Quando usar:** no dia zero de qualquer projeto. É um dos cinco arquivos obrigatórios.

**Papel:** apresentar o projeto a pessoas e ferramentas pela primeira vez — propósito, pré-requisitos, caminho inicial de uso, links para a documentação detalhada. Não é inventário arquitetural.

**Convenções:** uma linha que aponta para um documento ainda inexistente sai do arquivo, em vez de virar link quebrado — no dia zero isso costuma valer para a linha do `docs/index.md`, que volta quando o índice nascer. Isso vale só para referências a documentos: campos que outros templates declaram obrigatórios continuam obrigatórios, e ausência deles é lacuna, não simplificação. Comando ainda não verificado entra com a marcação de estado provisório.

````markdown
# <nome do projeto>

<Uma a três linhas: o que é e para quem serve.>

## Pré-requisitos
- <toolchain, versão, sistema operacional, hardware>

## Como começar
```
<comando de configuração>
<comando de build>
<comando de execução ou teste>
```

## Documentação
- Arquitetura: [ARCHITECTURE.md](ARCHITECTURE.md)
- Guia operacional para agentes: [AGENTS.md](AGENTS.md)
- Índice da documentação: [docs/index.md](docs/index.md)

## Licença
<licença ou "uso interno">
````
