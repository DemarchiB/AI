# Manutenção do próprio conjunto

Cobre o que só quem **edita este conjunto** precisa saber: como uma alteração chega aos projetos que já adotaram, quando um domínio novo se justifica, para onde vai um trecho que não cabe mais onde está, e quanto cada arquivo pode pesar. Nada aqui é lido para trabalhar num projeto — é por isso que este conteúdo saiu do índice, que é pago em toda tarefa de todo projeto.

**Leia este arquivo quando:** for criar, dividir ou remover um domínio; mover um trecho de um arquivo do conjunto para outro; ou propagar uma mudança aos projetos que já adotaram.

## 1. Identificação e propagação

Não há número de versão nem changelog neste conjunto. O VCS responde melhor, e de graça, as duas perguntas que eles responderiam:

- **Que estado do conjunto este projeto adotou?** No submódulo, o commit fixado. Na cópia, o commit de origem, registrado numa linha do `AGENTS.md` do projeto — `git rev-parse --short HEAD` no clone do conjunto, no momento em que a cópia foi feita.
- **O que mudou desde então?** `git diff <estado adotado>..main` no repositório do conjunto. Na cópia, esse mesmo diff; e comparar a pasta `docs/guide/` com o conjunto no commit adotado mostra de quebra se alguém editou a cópia, o que a regra proíbe.

Um número de versão só carregaria informação se fosse decidido com cuidado a cada alteração e nunca esquecido. Na prática ele vira decoração que discorda do repositório — e um rótulo em que ninguém confia é pior do que rótulo nenhum, porque ainda custa a manutenção. Duas obrigações o substituem, e as duas são baratas porque acontecem no momento em que a informação existe:

1. **O corpo do commit declara o impacto para quem já adotou** sempre que uma regra mudar de sentido, um caminho canônico mudar ou a organização dos arquivos mudar. Uma linha — "quem já adotou precisa: …" — escrita onde não tem como divergir do diff que a acompanha. Alteração que não obriga ninguém a nada não gera linha nenhuma.
2. **O conjunto é propagado inteiro**, nunca arquivo a arquivo: atualizar metade produz combinação que ninguém validou.

**Quando um arquivo do conjunto muda, os projetos que o adotaram reavaliam — não necessariamente atualizam.** Reavaliar é ler o diff e decidir. Ficar no estado atual é decisão legítima, e ela fica registrada no `AGENTS.md` do projeto ao lado do commit adotado; o que não é legítimo é o projeto não saber em que estado está.

## 2. Antes de criar um domínio, descarte três alternativas

Domínio nasce de repetição observada, não de lacuna percebida — lista de tópicos que faltam é o antipadrão da árvore preenchida aplicado a este conjunto. Verifique nesta ordem:

- **É reformulação de regras que já existem em outro domínio, na voz de quem executa uma tarefa específica?** Então é prompt de papel em `.agents/prompts/`, ou seção do `docs/workflow.md` do projeto — e ele **aponta** para os domínios em vez de repetir suas regras. Duas cópias da mesma regra divergem, e a partir daí ninguém sabe qual vale.
- **Depende do produto, da linguagem, da ferramenta ou da norma adotada por um projeto?** Então é documento daquele projeto, não deste conjunto.
- **Ainda não foi praticado em nenhum projeto real?** Então espere. Convenção escrita antes do primeiro uso é palpite com aparência de norma, e vira o arquivo que todos contornam. Um projeto só mostra uma solução; o que é geral só fica visível no segundo.

Só o que sobrevive aos três é domínio. O mesmo teste vale para um template novo: ele nasce quando o segundo documento real daquele tipo mostra qual é a forma repetida, não quando alguém percebe que a área existe sem template.

## 3. Para adicionar um domínio novo

1. **Só entra o que é geral.** Regra que depende da linguagem, do produto ou da norma adotada por um projeto pertence aos documentos daquele projeto.
2. **Um arquivo por domínio** em `practices/`, no formato declarado na Seção *Domínios de prática* do índice, e listado na tabela de lá.
3. **Templates novos** vão para `templates/`, um por documento, e entram no catálogo da Seção *Estrutura de arquivos do projeto* e na lista da Seção *Onde registrar uma informação* do índice.
4. **Regra pertence a um domínio só.** Se parecer caso particular de regra existente em outro domínio, refine a existente em vez de duplicar.
5. **Não crie o arquivo vazio.** Domínio só nasce quando há regras reais a escrever.
6. **Declare o impacto no corpo do commit** se alguma regra mudou de sentido (Seção *Identificação e propagação*).

## 4. Orçamento de contexto

Meça com `wc -m`, não em linhas: linha longa e linha curta pesam diferente, e um arquivo pode estar folgado no limite de linhas pesando o dobro de outro que parece maior. Rode a medição sob locale UTF-8 (`LC_ALL=C.UTF-8 wc -m arquivo.md`): fora dele, `wc -m` conta **bytes**, e em texto em português cada acento vira um caractere a mais — a diferença chega a 4% e já foi suficiente para dar um arquivo como estourado sem estar.

- **O índice: teto firme de ~18.000 caracteres.** Ele é lido em toda tarefa, então cada caractere ali é pago em todas elas. Estourou, alguma coisa sai — e para onde, decide a regra de destino da Seção *Para onde vai um trecho que precisa sair*.
- **Cada domínio: ~15.000 caracteres como gatilho de revisão**, não como tesoura. Domínios são carregados sob demanda, um ou dois por tarefa; o número não manda cortar texto, manda parar e decidir. As respostas legítimas são três: o domínio virou dois; parte dele é fato específico de projeto e vai para os documentos do projeto; ou há regra duplicada de outro domínio para eliminar. Encolher a prosa até a regra parar de se explicar não é uma delas.
- **Documentos procedurais e templates não têm teto** — são lidos um por vez, e só por quem vai fazer aquela tarefa ou criar aquele documento.

## 5. Para onde vai um trecho que precisa sair

**O destino se escolhe pelo assunto, nunca pelo espaço livre.** Empurrar um trecho para o arquivo que tem folga é o jeito mais fácil de caber no orçamento e o jeito mais rápido de tornar o conjunto ilegível: quem procura o assunto não vai olhar ali. Se o trecho não pertence a nenhum arquivo existente, ele **vira arquivo próprio** — o conjunto admite documentos procedurais novos ao lado de `adocao.md` e `manutencao.md`, e um arquivo a mais lido sob demanda custa menos que um parágrafo no lugar errado. Arquivo novo assim entra na árvore da Seção *Estrutura de arquivos do projeto* e na tabela *Como usar* do índice.

**O que nunca é cortado para caber:** o motivo de uma regra. Regra sem o porquê é contornada na primeira vez que incomoda, e isso custa mais do que os caracteres economizados. Corte duplicação, exemplo redundante e adjetivo — nunca a justificativa.

**Uma regra tem um dono só.** Antes de escrever uma regra num arquivo, procure se ela já existe em outro: o conjunto inteiro cabe numa busca textual. Quando a mesma regra parecer necessária em dois lugares, o segundo lugar recebe um **ponteiro**, não uma segunda redação — duas redações da mesma regra divergem na primeira alteração, e a partir daí ninguém sabe qual vale. A exceção é o texto que os templates **contêm** para o projeto copiar: ali a repetição é conteúdo gerado, não regra duplicada.

## 6. Checklist de alteração do conjunto

- [ ] A regra alterada tem um dono só, e os outros arquivos apontam para ele em vez de repeti-la.
- [ ] Nenhum fato específico de um projeto entrou em arquivo do conjunto.
- [ ] O índice continua abaixo do teto e cada domínio tocado continua abaixo do gatilho (`wc -m`).
- [ ] Referências cruzadas citam o arquivo e o **título** da seção, nunca só o número.
- [ ] Documento ou domínio novo entrou na árvore, no catálogo e na tabela "Como usar" do índice.
- [ ] Mudança de sentido de regra, de caminho canônico ou de organização tem o impacto declarado no corpo do commit.
