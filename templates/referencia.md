# Template: referência de dependência (`docs/references/<dependência>.md`)

**Quando usar:** quando alguém precisar consultar, mais de uma vez, o que *este projeto* sabe sobre uma dependência externa. Um arquivo por dependência relevante.

**Papel:** registrar o conhecimento de apoio sobre ferramentas, bibliotecas, protocolos e dependências no contexto deste projeto — nunca copiar a documentação oficial integralmente.

```markdown
# Referência: <dependência>

## Papel neste projeto
<2-3 linhas: para que ela é usada aqui e o que deixaria de funcionar sem ela.>

## Versão em uso
<versão, e o arquivo onde essa versão está declarada>

## Como este projeto a usa
<Recursos e APIs efetivamente usados, e por quais módulos. O que este projeto
deliberadamente NÃO usa, com o motivo.>

## Limites e armadilhas conhecidas
- <limite de uso, comportamento surpreendente, incompatibilidade observada,
  com a evidência que sustenta cada item>

## Documentação oficial
<link ou referência de onde encontrar; sem cópia integral do conteúdo.>
```
