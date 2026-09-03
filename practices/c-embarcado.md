# Domínio: C em alvo embarcado

Cobre como se produz o código-fonte em C para microcontrolador: toolchain e build, tipos e conversões, uso de memória, estrutura dos arquivos, defensividade, adoção de um subconjunto normativo (MISRA) e análise estática. Não cobre a arquitetura do firmware — camadas, máquinas de estado, interrupções, RTOS e tempo estão em [firmware.md](firmware.md). As regras de [engenharia.md](engenharia.md) continuam valendo integralmente; este domínio acrescenta os sensores próprios da linguagem.

**Leia este arquivo quando:** for escrever ou revisar código C de firmware, configurar o build de um projeto novo, decidir se um desvio de regra é aceitável, ou introduzir análise estática num projeto que ainda não tem.

Comandos e flags aparecem na notação do GCC e do CMake como ilustração. O conjunto real de um projeto — compilador, padrão, flags, ferramenta de análise — é fato daquele projeto e mora no seu `docs/workflow.md`.

## 1. Toolchain, build e identificação

1. **O padrão da linguagem é declarado explicitamente** no build (`-std=c99`, `-std=c11`), nunca deixado no default do compilador. Padrão implícito muda quando a toolchain é atualizada.
2. **Warning é erro.** O build roda no mínimo com `-Wall -Wextra`, e `-Werror` é o alvo. Projeto legado que ainda não passa nessa configuração adota o ratchet da Seção *Sensores da linguagem* — não desliga o aviso.
3. **A toolchain é fixada e arquivada.** O projeto declara a versão exata do compilador e guarda o meio de reproduzi-la (instalador, contêiner, gerenciador de toolchain). Produto de automação vive muitos anos: recompilar depois com outra versão produz um binário diferente do que foi validado, e não há como demonstrar equivalência.
4. **O build é reprodutível por linha de comando**, independente de IDE. Build que só existe dentro de uma IDE não pode ser verificado por sensor nem por agente.
5. **O firmware carrega sua própria identificação**: versão, identificador do commit e data de build, **gerados pelo build** e legíveis em execução — nunca digitados à mão numa constante. O binário liberado é arquivado junto com a tag correspondente no VCS.
6. **Consumo de flash e RAM é orçado**, extraído do mapa de memória a cada build e comparado com um limite declarado. Descobrir que a flash acabou na última funcionalidade é o defeito clássico de firmware, e ele só aparece cedo se for medido desde cedo.
7. **Extensão de compilador é decisão consciente**, em ADR quando afeta portabilidade, e o código que depende dela fica isolado na camada de hardware.
8. **Otimização faz parte da configuração declarada.** Código que só funciona em um nível de otimização tem defeito — tipicamente `volatile` ausente ou comportamento indefinido — e o defeito se corrige, não se contorna baixando a otimização.

## 2. Tipos, expressões e conversões

1. **Tipos de largura explícita** (`uint8_t`, `int32_t`, de `<stdint.h>`) em toda variável cuja largura importe: registradores, protocolos, buffers, campos persistidos. `int` e `char` só onde a largura é irrelevante.
2. **`bool` para condição lógica** (`<stdbool.h>`), não `int` nem `uint8_t` com 0/1.
3. **Não misture sinalizado e não sinalizado na mesma expressão.** A conversão implícita é silenciosa e é uma das fontes mais comuns de defeito em C embarcado. Onde a conversão for necessária, ela é explícita e comentada com o motivo.
4. **Toda conversão que perde faixa ou precisão é explícita** e acompanhada da verificação que garante que o valor cabe.
5. **Comparação de ponto flutuante nunca por igualdade**, e ponto flutuante não entra em caminho crítico de tempo sem justificativa — em muitos alvos não há FPU.
6. **`const` é o padrão**, não a exceção: parâmetro de ponteiro que não modifica é `const`, tabela imutável é `const` (fica em flash, não em RAM).
7. **Enum para conjunto fechado de valores**, com todos os casos tratados no `switch` — sem `default` que mascare um caso novo esquecido. Onde houver `default`, ele leva ao estado de erro, não ao silêncio.
8. **Precedência explícita por parênteses** em expressão composta, mesmo quando a linguagem já garantiria o resultado.

## 3. Memória e recursos

1. **Sem alocação dinâmica.** Nada de `malloc`/`free` em tempo de execução. Onde a alocação for inevitável, ela acontece uma única vez na inicialização, nunca é liberada, e é registrada em ADR.
2. **Sem recursão** e sem arranjo de tamanho variável (VLA): ambos tornam o consumo de pilha indeterminável.
3. **Todo buffer tem tamanho conhecido em tempo de compilação** e todo acesso indexado tem limite verificado quando o índice vem de fora do módulo.
4. **Consumo de pilha é orçado**, não descoberto: pilha de cada tarefa ou contexto é dimensionada, e o projeto documenta como isso foi verificado (marca d'água, análise da toolchain) ou registra a verificação como pendente.
5. **Escopo mínimo.** Variável usada por um único arquivo é `static`; variável global compartilhada tem dono declarado — um módulo que a escreve — e os demais leem por função de acesso.
6. **Estruturas usadas em protocolo ou persistência não dependem de layout implícito**: alinhamento, preenchimento e ordem de bytes são tratados na serialização, nunca assumidos do compilador.

## 4. Estrutura, nomes e interface

1. **Um módulo = um `.c` + um `.h`**, com uma responsabilidade declarada no topo do header. Módulo com estado — que tem ou pode vir a ter mais de uma instância — parte de `../templates/modulo-c.md`.
2. **Todo símbolo público leva o prefixo do módulo** (`motor_iniciar`, `motor_parar`, `MOTOR_ESTADO_PARADO`). C não tem espaço de nomes: o prefixo é o que evita colisão no link, torna o módulo de origem visível na revisão e permite localizar todos os usos por busca textual.
3. **A unidade faz parte do nome** sempre que a grandeza tiver uma: `timeout_ms`, `corrente_ma`, `tensao_mv`, `angulo_decigrau`. Elimina uma classe inteira de defeito que nenhum analisador estático detecta, e custa nada.
4. **Identificador reservado não se usa.** Nome começando com sublinhado duplo, ou com sublinhado seguido de maiúscula, é reservado para a implementação em qualquer escopo — inclusive guarda de inclusão, onde o erro é mais comum. `__MODULO__` é comportamento indefinido; a forma correta é `MODULO_H`.
5. **O header expõe a interface, nunca a implementação**: sem definição de variável, sem corpo de função não `inline`, sem `#include` que só a implementação precisa. Guarda de inclusão em todo header.
6. **Cada função pública tem contrato declarado no header**: o que ela faz, pré-condições, faixa válida de cada parâmetro com a unidade, e o significado de cada valor de retorno de erro. É o contrato que torna verificável, na revisão, a regra de validação da Seção *Defensividade* — e é o material que uma norma de safety vai exigir depois, escrito no momento em que custa pouco.
7. **Sem número mágico.** Valor com significado é `const`, `enum` ou máscara nomeada; registrador é acessado por campos e máscaras com nome, não por literal hexadecimal no meio da expressão.
8. **Função tem uma responsabilidade e cabe na tela.** Função que precisa de comentário de seção interna normalmente são duas funções.
9. **Toda função tem protótipo** e o parâmetro vazio se escreve `(void)`.
10. **Macro só onde função não serve.** Constante é `const` ou `enum`; cálculo é função (`static inline` quando o custo importar). Macro que sobrar é parametrizada com parênteses em cada uso do argumento e sem efeito colateral no argumento.
11. **Sem `goto`**, exceto o salto único para um bloco de limpeza ao fim da própria função.
12. **Código morto não fica no repositório.** Alternativa antiga vive no histórico do VCS, não comentada nem sob `#if 0`.

## 5. Defensividade e comportamento indefinido

1. **Toda função pública valida o que vem de fora do módulo**: ponteiro nulo, índice fora de faixa, valor de enum inválido, tamanho incoerente.
2. **Retorno de erro é verificado ou explicitamente descartado** com comentário do motivo. Função que pode falhar retorna estado de erro; não sinaliza falha por valor mágico dentro da faixa útil.
3. **Erro tem um tipo próprio** (enum de resultado) usado por todo o projeto, em vez de cada módulo inventar sua convenção.
4. **Suposição sobre o alvo vira verificação de compilação.** Tamanho de estrutura persistida ou trafegada, largura de tipo, potência de dois de um buffer circular, coerência entre um enum e o tamanho de uma tabela: tudo isso se afirma com asserção estática (`_Static_assert`, ou macro equivalente em C99). Falha em tempo de compilação custa segundos; a mesma suposição quebrada em campo custa uma visita.
5. **`assert` de execução é para invariante de programação**, verificada em desenvolvimento; nunca para validar entrada externa, que é sempre tratada em produção.
6. **Nada de comportamento indefinido como recurso**: deslocamento maior que a largura do tipo, estouro de sinalizado, leitura de variável não inicializada, ponteiro para objeto fora de escopo, violação de aliasing. O compilador é livre para otimizar em cima disso, e o sintoma aparece longe da causa.
7. **`volatile` marca o que muda fora do fluxo do programa** — registrador de periférico, variável escrita por interrupção. `volatile` **não** é mecanismo de sincronização: acesso compartilhado precisa de seção crítica ou primitiva do RTOS ([firmware.md](firmware.md), Seção *Interrupções*).

## 6. Adoção do MISRA

MISRA C é um subconjunto seguro da linguagem, publicado como documento normativo. O texto das regras **não é reproduzido aqui**: é protegido por direito autoral e só citável a partir do documento oficial. O que este guia define é o processo de adoção.

1. **Adoção declarada, não presumida.** O projeto registra em ADR: a edição adotada, o conjunto de regras adotado e a ferramenta que verifica. Projeto que "segue MISRA" sem esses três fatos não segue.
2. **Adesão parcial é legítima quando é explícita.** O padrão deste guia é o subconjunto pragmático: as regras de categoria *Mandatory* na íntegra, as *Required* de maior retorno, as *Advisory* como recomendação. O que ficou de fora é listado, com o motivo.
3. **Desvio é registrado, não silenciado.** Todo desvio de regra adotada tem registro com: identificador da regra, local, motivo técnico, análise do risco e quem aprovou. Supressão inline sem esse registro é violação do processo, mesmo que a ferramenta fique verde.
4. **Aplica-se a código novo primeiro.** Código legado entra pelo ratchet da Seção *Sensores da linguagem* e é migrado quando a área for tocada por outra tarefa — nunca por mutirão, que gera diff irrevisável ([engenharia.md](engenharia.md), Seção *Convenções de branch e commit*).
5. **Código de terceiro é isolado e declarado fora do escopo**: HAL do fabricante, RTOS, biblioteca de comunicação. Fronteira explícita e validação dos dados que atravessam.
6. **As regras deste arquivo não substituem o documento.** Boa parte das seções de tipos, memória, interface e defensividade coincide com o espírito do MISRA e é adotável hoje, sem o documento e sem ferramenta — mas conformidade declarada exige o documento e a ferramenta.

## 7. Sensores da linguagem e adoção incremental

Ao conjunto mínimo de sensores de [engenharia.md](engenharia.md), este domínio acrescenta:

1. **Compilador em modo estrito** — o analisador estático mais barato que existe, e o primeiro a adotar.
2. **Analisador estático dedicado** rodando no mesmo comando de build ou num alvo próprio do build (`make analyze`, alvo do CMake), não como passo manual.
3. **Orçamento de memória** verificado a cada build (Seção *Toolchain, build e identificação*, regra 6).
4. **Complexidade limitada por sensor**, onde houver ferramenta: função acima do limite declarado falha o build ou entra na lista de dívida. Complexidade alta não é defeito, mas é o melhor indicador barato de onde os defeitos vão aparecer — tipicamente uma máquina de estado implícita pedindo para virar explícita ([firmware.md](firmware.md), Seção *Máquinas de estado*).
5. **Teste em host é sensor, não luxo.** A lógica independente de hardware compila e roda no PC; onde ainda não houver suíte, isso é registrado como verificação pendente — nunca declarado como coberto por teste manual em bancada. O que o torna possível é a separação de camadas ([firmware.md](firmware.md), Seção *Camadas e portabilidade*).

**Ordem de adoção num projeto sem nada:** warnings do compilador → analisador aberto na configuração padrão → conjunto de regras normativo → conformidade completa. Pular etapa produz milhares de achados e abandono da ferramenta.

**Como impedir achado novo sem parar para limpar o legado.** O mecanismo depende de quem emite o achado, e confundir os dois é o erro comum:

- **Compilador — ratchet, não linha de base.** `-Werror` é tudo-ou-nada: não existe baseline nativo. Duas formas de avançar, combináveis: *por escopo*, aplicando `-Werror` apenas aos alvos já limpos e deixando o legado em `-Wall -Wextra` sem promoção (em CMake isso é por alvo, com `target_compile_options`, nunca uma flag global); e *por regra*, promovendo uma categoria de cada vez (`-Werror=implicit-function-declaration`, `-Werror=return-type`, `-Werror=conversion`) à medida que ela zera no projeto inteiro. Escopo ou regra promovida nunca regride: essa é a catraca.
- **Analisador estático — linha de base de supressões.** Os achados existentes são congelados num arquivo versionado, e o sensor falha apenas em achado fora dele. A alternativa equivalente é analisar somente as linhas alteradas pelo diff da tarefa.
- **A linha de base é indexada por arquivo e regra, nunca por número de linha.** Congelar `driver.c:412` faz com que qualquer inserção acima reabra o achado antigo e mascare o novo; em uma semana o sensor vira ruído e é desligado.
- **A dívida é medida e encolhe.** O tamanho atual da linha de base — e a lista de alvos ainda sem `-Werror` — é registrado no projeto e revisitado; catraca sem número não é catraca, é adiamento.
- **Achado suprimido tem justificativa no próprio local** e, quando for desvio de regra adotada, o registro da Seção *Adoção do MISRA*.

## Checklist deste domínio

- [ ] O build declara padrão e toolchain fixada, e não introduziu aviso novo no escopo já promovido.
- [ ] O firmware identifica sua própria versão e commit; o orçamento de flash e RAM foi verificado.
- [ ] Tipos de largura explícita onde a largura importa; nenhuma conversão implícita entre sinalizado e não sinalizado.
- [ ] Nenhuma alocação dinâmica, recursão ou arranjo de tamanho variável foi introduzida.
- [ ] Símbolos públicos levam prefixo do módulo, grandezas levam unidade no nome, e nenhum número mágico entrou no diff.
- [ ] Toda função pública tem contrato no header, valida entrada externa e tem retorno de erro verificado ou descartado com motivo.
- [ ] Nenhum código morto, comentado ou sob `#if 0` entrou no diff.
- [ ] Desvio de regra adotada tem registro com motivo, risco e aprovação.
- [ ] Os sensores da linguagem rodaram; o analisador não acusa achado fora da linha de base.
