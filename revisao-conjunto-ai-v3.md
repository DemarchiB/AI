# Revisão do conjunto de convenções (repo `AI`, versão 3.0)

Revisão de todos os 20 arquivos do conjunto (114.801 caracteres), na data de 2026-09-03.
Ordem: o que está certo, o que quebra, o que duplicar custa caro, e o que falta.

---

## 1. O conjunto faz sentido para o objetivo?

Faz — mas o objetivo declarado e o conjunto real já não são o mesmo.

A descrição do projeto ainda diz *"gerar arquivos de boas práticas de configuração de ambientes
para AI"*. O que existe hoje é maior e melhor que isso: um conjunto reutilizável de convenções de
**documentação, engenharia e firmware**, do qual "ambiente para IA" é uma consequência (o
`AGENTS.md` e a estrutura documental são exatamente o que configura o ambiente do agente).

O problema é a proporção: `practices/ia.md` é o **menor** arquivo do conjunto — 4.941 caracteres
de 114.801, ou 4,3% do total, contra 15.643 de C embarcado. O domínio que dá nome ao objetivo é o
menos desenvolvido. Duas saídas legítimas, e a escolha é sua:

- **atualizar a descrição** para o que o conjunto virou (recomendado — o escopo maior é o que tem
  valor), e tratar IA como um domínio entre outros; ou
- **engrossar `ia.md`** com o que falta (Seção 6 abaixo), se o foco em IA for para valer.

O que **não** funciona é manter a descrição atual: ela leva quem chega (pessoa ou agente) a
esperar um kit de configuração de IA e encontrar um manual de documentação e firmware.

### O que está bem resolvido (não mexa)

- A separação **índice → domínios → templates**, com carregamento sob demanda e um orçamento de
  contexto declarado. É a decisão de arquitetura mais acertada do conjunto.
- **"Descarte três alternativas antes de criar um domínio"** e o antipadrão da árvore preenchida.
  É o tipo de regra que impede o conjunto de inchar sozinho.
- **"O que nunca é cortado para caber: o motivo de uma regra."** Raro e correto.
- O tratamento do **MISRA**: define o processo de adoção sem reproduzir texto normativo. Resolve o
  problema jurídico e o prático de uma vez.
- `templates/modulo-c.md` e `templates/fsm.md` com exemplo preenchido — o exemplo carrega o que o
  esqueleto não consegue mostrar (unidade no nome, faixa, pré-condição).
- A distinção **regra × sensor**, e "sensor que depende de alguém lembrar não é sensor, é regra".

---

## 2. Duplicação — o problema número um

O conjunto proíbe explicitamente ("a mesma regra repetida em dois domínios com redações
diferentes" está na lista de antipadrões) e é, hoje, o seu defeito mais caro. Cinco casos:

**2.1 O papel de cada documento está escrito em três lugares.**
`PROJECT_GUIDE.md` §1 (tabela Local/Papel), `manutencao.md` §1 ("Responsabilidade de cada
documento") e o campo **Papel:** no topo de cada template dizem a mesma coisa sobre README,
AGENTS, ARCHITECTURE, docs/index, design-docs, exec-plans, product-specs, generated e references.
Três redações diferentes que vão divergir na primeira alteração.
→ **Dono canônico: o template** (é quem cria o documento que lê). O índice fica só com
`Local | Template`; `manutencao.md` §1 vira um ponteiro de uma linha. Economia estimada: ~2.000
caracteres, sendo ~700 no arquivo mais caro do conjunto.

**2.2 As "Regras invioláveis" e o checklist da §4 são a mesma lista, duas vezes, no mesmo arquivo.**
Evidência → "não há requisitos inventados"; segredos → "nenhum segredo entrou no diff"; sensores →
"sensores executados, ausentes registrados"; diff explicável → idem. E os mesmos itens aparecem
ainda nos checklists de `engenharia.md` e `ia.md` — quatro cópias de "sensores/segredos/diff".
→ Mantenha **as invioláveis com o porquê** (é o que justifica ler o índice em toda tarefa) e
reduza a §4 a: *rode o checklist dos domínios que a tarefa tocou; se a estrutura documental mudou,
o de `manutencao.md` §10*. Os itens específicos já vivem nos checklists de domínio.

**2.3 `manutencao.md` duplica a si mesmo.** A §2 ("Regras fundamentais", 11 itens) antecipa seções
inteiras do próprio arquivo: item 8 = §7 (submódulos), item 9 = §8 (arquivos gerados), itens 4-5 =
§4 (evidência), item 11 = inviolável 6.
→ A §2 pode virar 4-5 princípios que **não** têm seção própria, ou sumir.

**2.4 `docs/specs/` × `docs/product-specs/`** é explicado por extenso em `PROJECT_GUIDE.md` §2.4 e
de novo em `manutencao.md` §1. Uma das duas basta.

**2.5 "O agente nunca faz merge"** aparece em cinco lugares: inviolável 4, `engenharia.md` §1.3,
`ia.md` §3, `templates/workflow.md` e `templates/agents.md`. Aqui a repetição nos dois templates é
defensável (é texto que o projeto vai *conter*, não regra do conjunto) — mas três enunciados
normativos para a mesma regra continuam sendo dois a mais.

---

## 3. Fronteiras ambíguas

**3.1 `manutencao.md` tem duas identidades.** O título diz "Manutenção **documental**", mas §5
(processo para qualquer modificação), §6 (modificações estruturais de código), §7 (submódulos) e
§8 (arquivos gerados) são **processo de engenharia**, não documentação. Consequência prática: quem
pergunta "como conduzo uma mudança?" tem dois arquivos candidatos, com listas de validação
diferentes — `manutencao.md` §5.4 e `engenharia.md` §3 descrevem a mesma escada de sensores com
palavras distintas.
→ Mova §5-§8 para `practices/engenharia.md` (ou um `practices/mudanca.md`), que é onde o próprio
índice diz que moram "as regras por assunto". `manutencao.md` fica com o que é de fato documental:
independência de ferramenta, manutenção do texto, checklist documental, antipadrões.

**3.2 Por que `adocao.md` e `manutencao.md` não estão em `practices/`?** A resposta implícita é
"porque são procedimento, não domínio" — mas isso não está escrito em lugar nenhum, e a seção
*Finalidade* enumera só três categorias (índice, practices, templates) quando existem quatro.
→ Uma frase na *Finalidade* reconhecendo a quarta categoria (documentos procedurais na raiz)
elimina a impressão de arbitrariedade.

**3.3 Uma regra de engenharia está enterrada numa meta-seção.** A rastreabilidade auditável
(identificador na spec, no commit, no teste e no registro de validação) está no `PROJECT_GUIDE.md`
§3 como **item 5 de "Para adicionar um domínio novo"** — e `firmware.md` §8.1 aponta para lá. É
uma regra de produto num lugar que fala sobre como editar o conjunto.
→ Vai para `engenharia.md` §2, que já é dona da cadeia spec → commit → revisão.

---

## 4. Contradições e inconsistências pontuais

| # | Onde | O quê |
| --- | --- | --- |
| 1 | Inviolável 6 × `templates/` | "Documento novo parte do template correspondente" — mas `design-docs/`, `exec-plans/`, `product-specs/` e `generated/` **não têm template**. Ou a regra ganha a ressalva ("quando existir template"), ou faltam arquivos. O de `exec-plans` é o que mais se paga. |
| 2 | `PROJECT_GUIDE.md` §1 | O `README.md` é obrigatório desde o dia zero (`adocao.md`) e está na tabela, mas **não aparece na árvore de arquivos** logo acima dela. |
| 3 | `adocao.md` + tabela "Como usar" | "Cinco arquivos obrigatórios" — um deles é um **diretório** (`docs/guide/`). E "leia os templates dos cinco obrigatórios" pede cinco templates que não existem: são quatro (o conjunto não tem template de si mesmo). |
| 4 | `PROJECT_GUIDE.md` §1 × §2.9 | A árvore mostra `docs/exec-plans/`; a §2.9 e `manutencao.md` §1 falam de `active/` e `completed/`. A árvore não mostra a subdivisão. |
| 5 | `templates/agents.md` | O exemplo preenchido fixa **"versão 3.0"**. Todo incremento de versão obriga a lembrar de editar o exemplo; esquecer uma vez e o exemplo passa a mentir. Use `<X.Y>`. |
| 6 | `PROJECT_GUIDE.md` (numeração) | *Finalidade*, *Como usar* e *Regras invioláveis* não são numeradas; depois vêm §1 a §4. `firmware.md` §8.1 cita "a Seção 3 do índice" — quem conta a partir do topo chega em "Regras invioláveis", não em "Domínios de prática". |
| 7 | Todo o conjunto | **17 referências cruzadas por número de seção** ("`engenharia.md`, Seção 3"). Inserir uma seção quebra todas em silêncio, e nenhum sensor detecta — o verificador de links só vê o arquivo. Referencie por título ou âncora. |
| 8 | `engenharia.md` §3.4 × `templates/` | O sensor de links relativos que o conjunto exige acusa **12 falsos positivos** nos próprios templates (links dentro de blocos de código, como `[ARCHITECTURE.md](ARCHITECTURE.md)` em `templates/readme.md`). Ao adotar o sensor, ele precisa ignorar blocos cercados — vale uma linha dizendo isso. |
| 9 | `templates/skill.md` | O link simbólico `.claude/skills/ → .agents/skills/` **não funciona no Windows por padrão** (exige modo desenvolvedor ou privilégio, e `core.symlinks=false` está no `.git/config` deste próprio repositório). A regra precisa de uma alternativa declarada para Windows, senão vira letra morta na sua máquina. |

---

## 5. Orçamento de contexto — dois limites atingidos

Medido com `wc -m`, como o próprio guia manda:

- **`PROJECT_GUIDE.md`: 15.976 de um teto firme de 18.000 (89%).** Resta pouco, e é o arquivo lido
  em toda tarefa.
- **`practices/c-embarcado.md`: 15.643, acima do gatilho de revisão de 15.000.** Pela regra do
  próprio guia, é hora de parar e decidir entre as três respostas legítimas.

Duas movimentações resolvem os dois de uma vez:

1. **Tire do índice a meta-seção sobre editar o conjunto.** "Antes de criar um domínio, descarte
   três alternativas", "Para adicionar um domínio novo", "Orçamento de contexto" e "O destino se
   escolhe pelo assunto" somam ~4.500 caracteres que só interessam a **quem edita o conjunto** — e
   hoje são pagos em toda tarefa de todo projeto. Vão para um `manutencao-do-conjunto.md` na raiz
   (a §3 do índice fica com a tabela de domínios e o formato). Isso derruba o índice para ~11.000 e
   abre espaço para o conjunto crescer.
2. **`c-embarcado.md` §7 (sensores, ratchet, linha de base) é assunto próprio.** O conceito de
   sensor pertence a `engenharia.md` §3; o ratchet e a linha de base são a versão C dele. Ou viram
   arquivo, ou a parte conceitual sobe para engenharia e fica aqui só o específico da linguagem.

Um efeito colateral bom: a movimentação 1 é a mesma que resolve o item 3.3.

---

## 6. O que falta, dado que o objetivo é ambiente para IA

`ia.md` cobre bem **limites de execução** e **conteúdo não confiável**. Não cobre o que você
encontra na prática:

- **Arquivos de contexto proprietários.** `CLAUDE.md`, `.cursorrules`,
  `.github/copilot-instructions.md` — `manutencao.md` §3 diz "não dependa exclusivamente deles",
  mas não diz o que fazer. A regra útil é curta: *arquivo proprietário é um stub de três linhas
  apontando para o `AGENTS.md`, nunca uma cópia* — mesma lógica que você já aplicou aos templates.
- **Configuração de ferramentas do agente versionada** (servidores MCP, permissões, o que o agente
  pode executar sem perguntar). É literalmente "configuração de ambiente para IA" e não está em
  lugar nenhum do conjunto.
- **Qualidade do que o agente produz.** Hoje o domínio trata do que o agente *pode fazer*, não do
  que entra no repositório: código gerado precisa de teste que falharia sem ele, e o revisor humano
  responde pelo diff — inclusive o que não leu.
- **Quando *não* delegar.** O contraponto que todo domínio maduro tem.

E, fora do domínio: **o próprio repositório não tem `AGENTS.md` nem `README.md`** — justamente o
repositório que um agente vai ler para aplicar o guia.

---

## 7. O conjunto não segue as próprias regras

Não é pedantismo: é o teste mais barato de que as regras são praticáveis.

- **Sem `README.md`.** Quem abre `github.com/DemarchiB/AI` não descobre o que o repositório é nem
  por onde entrar. (Atenção ao efeito colateral: como o conjunto é montado em `docs/guide/`, esse
  arquivo vira `docs/guide/README.md` no projeto que adota — o que é aceitável, mas deve ser
  deliberado, e a primeira linha dele precisa dizer "isto é o conjunto de convenções, não o README
  do projeto".)
- **Sem `.gitignore`**, que `engenharia.md` §4.2 declara parte do harness.
- **Commits fora da convenção do §2 de `engenharia.md`**, e direto na `main`: "mais um incremento",
  "Alterada org", "Adicionadas boas práticas de programação". Sem tipo, sem imperativo, sem corpo
  explicando o porquê. Um conjunto que exige `feat/`, `fix/` e assunto no imperativo dos outros
  precisa passar no próprio sensor.
- **Versão 3.0 sem tag e sem changelog.** Um projeto que declara "versão 2.0" no `AGENTS.md` não
  tem como descobrir o que mudou para a 3.0 — e é exatamente a pergunta que o modelo de adoção por
  **cópia** obriga a responder. Com submódulo o commit resolve; com cópia, não.
  → Mínimo viável: `git tag v3.0` a cada incremento + um `CHANGELOG.md` com uma linha por versão.
  Sem isso, a regra de versionamento do topo do índice não é acionável.

---

## 8. Ordem sugerida

Da maior relação valor/esforço para a menor:

1. **Tirar a meta-seção do índice** para `manutencao-do-conjunto.md` (resolve §5 e §3.3 juntos).
2. **`CHANGELOG.md` + tag `v3.0`** — sem isso o esquema de versão não funciona para adoção por cópia.
3. **Desduplicar o papel dos documentos** (§2.1): dono canônico é o template.
4. **Mover §5-§8 de `manutencao.md`** para `engenharia.md` (§3.1).
5. **`README.md`, `.gitignore` e `AGENTS.md` do próprio repositório** — dogfooding.
6. **Corrigir a tabela de inconsistências** da §4 (é uma passada de meia hora).
7. **Colapsar invioláveis × checklist da §4** (§2.2).
8. **Engrossar `ia.md`** (§6) — ou reescrever a descrição do projeto, se o escopo real for outro.
9. **Dividir `c-embarcado.md`** quando o próximo acréscimo chegar.
