# Changelog

Uma linha por versão, do mais recente para o mais antigo. A versão vale para o conjunto inteiro e
está declarada no topo de `PROJECT_GUIDE.md`; cada entrada aqui tem tag correspondente no VCS.

Um projeto que adotou o conjunto por **cópia** usa este arquivo para decidir se atualiza: sem ele,
"o que mudou desde a versão que eu copiei" não tem resposta.

## 4.0 — 2026-09-03

Reorganização, sem mudança de sentido em nenhuma regra técnica. Incremento maior porque a
organização dos arquivos mudou e um arquivo novo entrou no conjunto.

- **Novo `manutencao-do-conjunto.md`**: as regras sobre criar domínio, orçamento de contexto e
  destino de um trecho saíram do índice, que era pago em toda tarefa de todo projeto, para um
  arquivo lido só por quem edita o conjunto. O índice caiu de 15.4k para 12.6k caracteres.
- **Novos `README.md`, `AGENTS.md`, `CHANGELOG.md` e `.gitignore`** no próprio repositório — o
  conjunto passa a cumprir as regras que exige dos projetos.
- **Desduplicação**: o papel de cada documento passa a ter um dono só, o template correspondente;
  a descrição repetida saiu do índice e de `manutencao.md`. O checklist "ao encerrar" do índice
  deixou de repetir os checklists de domínio e passou a apontar para eles.
- **`manutencao.md` ficou só com o que é documental.** Processo de mudança, modificações
  estruturais, submódulos e arquivos gerados foram para `practices/engenharia.md`, onde moram as
  regras de como uma mudança entra no repositório.
- **Rastreabilidade auditável** saiu da meta-seção sobre criar domínios e foi para
  `practices/engenharia.md`, junto da cadeia spec → commit → revisão.
- **`practices/ia.md` cresceu**: arquivos de contexto de outras ferramentas (stub, nunca cópia),
  configuração de ferramenta versionada, qualidade do código gerado e quando não delegar.
- **Regra inviolável 6 qualificada**: "parte do template correspondente, **quando existir um**".
  As quatro áreas sem template (`design-docs/`, `exec-plans/`, `product-specs/`, `generated/`)
  passaram a ter o mínimo exigido declarado em `manutencao.md`.
- **Referências cruzadas por título de seção**, não por número: número quebra em silêncio quando
  uma seção é inserida, e nenhum sensor detecta.
- **Correções pontuais**: `README.md` e `exec-plans/{active,completed}` entraram na árvore do
  índice; "cinco arquivos obrigatórios" virou "quatro documentos mais o conjunto"; o exemplo de
  `templates/agents.md` deixou de fixar uma versão concreta; `templates/skill.md` ganhou a
  alternativa para ambientes onde link simbólico não sobrevive; o sensor de links passou a exigir
  que blocos de código sejam ignorados; a medição de orçamento passou a exigir locale UTF-8, sob
  pena de `wc -m` contar bytes.
- **`practices/c-embarcado.md`** voltou para baixo do gatilho de 15.000 caracteres por
  desduplicação. A divisão da seção de sensores em domínio próprio fica para quando o segundo
  projeto real a exercitar.

## 3.0 — 2026-09-02

Versão anterior a este changelog. O histórico até aqui está nos commits.
