# Adoção: por onde começar

Cobre como um projeto passa a usar este conjunto: quais documentos existem desde o primeiro dia, em que ordem nascem, como um projeto que já existe adota sem parar o trabalho, e como se declara o que ainda não foi decidido. É lido uma vez por projeto, na adoção — manter a estrutura depois é assunto de [manutencao.md](manutencao.md).

**Leia este arquivo quando:** for criar um projeto do zero, ou adotar o conjunto num projeto que já existe.

**Conjunto mínimo.** Um projeto começa com cinco arquivos, e só eles são obrigatórios desde o primeiro dia: `README.md`, `ARCHITECTURE.md`, `docs/workflow.md`, `AGENTS.md` e este conjunto em `docs/guide/`. Todo o resto nasce quando houver conteúdo real: a primeira spec, com a primeira funcionalidade não trivial; o primeiro ADR, com a primeira decisão que atenda aos critérios de `templates/adr.md`; `docs/index.md`, quando `docs/` passar a ter mais do que o guia e o workflow; um prompt de papel, quando o papel se repetir; uma Skill, quando o workflow justificar. Criar qualquer um antes disso é o antipadrão da árvore preenchida.

**Projeto novo — ordem de criação.** A ordem existe para que cada documento cite os anteriores sem referência quebrada e sem afirmar o que ainda não foi decidido:

1. este conjunto em `docs/guide/`, copiado ou adicionado como submódulo — antes de qualquer documento, porque define o formato dos demais;
2. `ARCHITECTURE.md` — mesmo esquelético; dá vocabulário a tudo que vem depois. O que não estiver decidido vai em "Pontos não determinados", não em suposição. Descrever estrutura que ainda não existe é legítimo desde que rotulada como planejada;
3. `docs/workflow.md` — VCS, remoto, branch principal e branches de integração;
4. `AGENTS.md` — por último entre os obrigatórios: índice só se escreve bem sobre o que já existe. É onde a versão adotada fica registrada;
5. `README.md` — a apresentação para pessoas.

Nessa ordem, toda referência entre documentos aponta para trás. Se um documento novo precisar citar outro que ainda não existe, ou a ordem está errada, ou a citação é desnecessária.

**Projeto existente — adoção incremental.** Não pare o trabalho para documentar tudo: documentação retroativa em massa produz fato não sustentado por evidência. Adote o conjunto; escreva o `AGENTS.md` com o que já é verificável hoje; levante o `ARCHITECTURE.md` a partir das fontes de evidência (`manutencao.md`, Seção 4), marcando o que o código não comprovar; registre como ADR apenas decisões que ainda governam o código e que alguém questionaria; daí em diante, cada tarefa que tocar uma área documenta aquela área. A cobertura cresce pelo uso, não por mutirão.

**Estado provisório.** Nenhum documento fica bloqueado por falta de fato confirmado, e nenhum buraco é preenchido por suposição. Um fato que ainda não existe ou não foi verificado é declarado na própria linha: `<a definir>` quando não há escolha feita, `<a verificar: motivo>` quando há expectativa razoável ainda não confirmada. Comando que ninguém rodou entra como `<a verificar>`, nunca como oficial. Num repositório sem código, é normal que a tabela inteira de comandos esteja marcada — documento obrigatório com campos declaradamente pendentes é o estado correto do dia zero. A marcação é temporária: sai na mesma mudança que confirma o fato.

**Seção sem conteúdo ainda.** Um template lista as seções que o documento pode ter. Seção cujo conteúdo ainda não existe — Skills e prompts de papel no dia zero, por exemplo — mantém o título e recebe uma linha explícita ("Nenhuma até o momento"), para que a ausência seja um fato declarado e o lugar continue visível quando o conteúdo surgir. A exceção é a linha que aponta para um documento inexistente: essa sai do arquivo, porque link quebrado não é informação.
