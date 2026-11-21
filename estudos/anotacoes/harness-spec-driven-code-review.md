# Harness, Spec-Driven Development e Code Review com IA

## Tese

Quanto mais o volume de código gerado por IA sobe, menos o “prompt solto” escala. O que escala é um **sistema em volta do modelo**: guias + sensores + revisão com evidência. O diferencial não é um framework pronto — é a capacidade de **construir e evoluir skills/harness** para o seu contexto.

## 1. Harness (dois sentidos)

### 1.1 Harness de ferramenta (street meaning)

O runtime que orquestra o agente: Cursor, Claude Code, Copilot, OpenCode etc.

Dentro dele (você **não** controla de verdade):

- chamada do modelo
- tool calling / MCP
- gestão de contexto e memória
- permissões / sandbox

**Insight prático:**

- Prefira harness de **labs grandes** ou projetos muito maduros.
- Evite abstrações em cascata (“harness em cima de harness”).
- Um modelo “piorou” às vezes é harness desatualizado (ex.: update do Opus em que o harness limitava o modelo).

### 1.2 Harness no sentido amplo

Tudo que você instrumenta **em volta** do agente para direcionar e validar:

| Camada | Exemplos | Papel |
|---|---|---|
| **Guides (guias)** | `AGENTS.md`, rules, skills, prompts, arquitetura do repo, ADRs | Direcionam o agente |
| **Sensors (sensores)** | testes, lint, typecheck, gates de CI, evals, hooks, observabilidade | Validam sem depender do modelo se autoavaliar |

Mantra: **guides e sensores, guides e sensores.**

Arquitetura do código também é harness: fronteiras e pastas bem definidas **guiam** o agente tanto quanto um markdown.

### 1.3 Agent loop (contexto)

Harness típico: modelo → tool → permissão → executa → atualiza contexto → modelo de novo. Skills/specs entram como **instruções + conhecimento + constraints** no topo do loop; sensores fecham o ciclo com 0/1 determinístico.

## 2. Spec-Driven Development

### 2.1 Por que surgiu

1. Chat solto → difícil revisar.
2. Agent mode → diffs enormes, correção cara.
3. Plan mode → melhor, mas resume/trunca em mudanças grandes.
4. Spec-driven → artefato persistido, revisável, quebrado em tarefas verificáveis.

Hype inicial (“código não importa, só markdown”) foi exagero. O que importa é o **ciclo com validação**.

### 2.2 Taxonomia (Martin Fowler / Birgitta Böckeler)

| Modo | Ideia | Manutenção da spec |
|---|---|---|
| **Spec-first** | Spec guia **esta** mudança; depois pode ser descartada/arquivada | Ciclo da feature |
| **Spec-anchored** | Spec permanece fonte da verdade da feature; mudanças atualizam a spec | Contínua (risco de drift) |
| **Spec as source** | Toda mudança começa no markdown; código só via spec | Máxima (alto custo operacional) |

**Definição operacional:** algo é spec-driven se:

1. Existe **artefato** (markdown, ticket, doc) descrevendo o problema/comportamento.
2. Alguém **revisa e aprova** (não é decoração).
3. O resultado é **conferido contra** esse artefato (checks/provas).

Não é “usar um framework”. É ter artefato + validação.

### 2.3 Auto-sizing (quando usar o quê)

| Situação | Abordagem |
|---|---|
| Pequeno e claro | Plan mode da ferramenta / SDD enxuto |
| Médio e claro | Spec + plano (design/tasks implícitos se óbvios) |
| Grande | SDD completo: Specify → Design → Tasks → Execute |
| Pequeno/incerto | Exploração/protótipo **antes** de SDD |
| Grande e incerto | Research + design doc compartilhado → depois SDD |

Frameworks citados: Kiro, Spec-kit, playbooks Anthropic, Spec-Driven (skills de planejamento), Tessl (spec-anchored / agent enablement).

### 2.4 Onde mora a fonte da verdade (decisão crítica)

| Contexto organizacional | Fonte da verdade | Specs no git? |
|---|---|---|
| PM/PO cria ticket; você só executa | Board (Jira/Linear…) | **Não** (drift board↔repo) |
| Você cria o card, mas board governa o time | Board + MCP; SDD local descartável | Geralmente não |
| Empresa **engineering-first** (tudo no repo) | Specs/ADRs versionados | **Sim** |
| Mix board + specs commitadas | Duas fontes | Evitar — drift garantido |

**Drift** = duas fontes da verdade divergindo (board vs `.specs/` vs Notion vs código).

### 2.5 Spec lean (ideia prática)

Em vez de recriar vários markdowns depois do ticket:

1. Spec + design **antes** / alinhados ao card.
2. Card representa a mudança.
3. Implementação puxa o card (MCP) sem duplicar narrativa.

Útil quando você tem autonomia para criar tasks e quer menos cerimônia.

### 2.6 Pipeline Spec-Driven

```
SPECIFY → (DESIGN) → (TASKS) → EXECUTE
```

Regras práticas:

- Aprovar spec e tasks são **stops humanos**.
- Testes derivam dos critérios (EARS), **não** espelham a implementação.
- Gate determinístico decide “done” (não autoavaliação do modelo).
- Um commit atômico por task.
- Após a última task: provas (`check_proofs`) + **Verifier independente** (author ≠ verifier).
- UI: provar com **screenshot** (custo/benefício melhor que navegação completa do agente).
- Sub-agentes para batches grandes; Verifier/screens sempre-on.

Estrutura típica no repo:

```
.specs/
  STATE.md
  LESSONS.md
  features/<feature>/
    spec.md | design.md | tasks.md | proofs.md | verification.md | captures/
AGENTS.md
CONTEXT.md / DESIGN.md / ROADMAP.md   # contexto de projeto
```

### 2.7 Modelos: planejar vs implementar

Histórico: modelo forte planeja, modelo fraco implementa.  
**Com modelos atuais:** preferir modelos bons também na implementação — retrabalho cai. Specs muito longas + implementador fraco = decisões ruins no meio.

## 3. Code review com IA (`the-judge`)

### 3.1 Problema

Volume de código ↑ → fadiga humana. Humanos devem focar em **partes críticas**; o restante precisa de confiança sistêmica.

### 3.2 Por que um agente único falha

Atenção diluída entre segurança, arquitetura, slop, etc. Solução: **sub-agentes por pass** (correctness, security, structure, AI slop…).

Ferramentas sem sub-agents / sem ler skills do projeto ficam limitadas (ex.: Vercel Review, CodeRabbit vs skill própria no repo).

### 3.3 Categorias de review

- Segurança (customizar ao domínio; ex.: dinheiro, auth, PII)
- Requirements / plano cumprido
- Testes
- Arquitetura / padrões do projeto
- **Slop / alucinação** (código sem relação com a mudança — “um dos mais valiosos”)
- Performance (opcional; custo)

Custo de referência: ~US$ 2/review com modelo forte de julgamento (ex.: Grok 4.7), não necessariamente o top absoluto.

### 3.4 Contratos do Judge (skill)

1. **Evidence or silence** — finding sem `file:line` ou URL oficial não posta.
2. Rodar **checks determinísticos do repo primeiro**; não repetir o que lint/test já pegam.
3. Noise budget (máx. nits).
4. Gate determinístico nos comentários antes de postar.
5. Round 1 = review completo (sem ping-pong infinito).
6. Severidade → verdict: blocker → REQUEST_CHANGES; só nits → APPROVE.

Integração: `/the-judge`, comentário `slash review` no PR, workflow GitHub Actions.

### 3.5 Segurança além do diff

| Ameaça | Onde tratar |
|---|---|
| Código malicioso no PR | Pass de security + SAST + “na dúvida, chama humano” |
| Prompt injection em agente em produção | Separação clara system vs user prompt + observabilidade (ex.: LangWatch) — **não** aparece só no review de PR |
| Domínios críticos (pagamentos, ledger) | Hotspots: tocar arquivo X → exigir review humano |

## 4. Harness-eval (saudade do harness)

Avaliar se `AGENTS.md`/skills/rules estão quebrados, redundantes ou inúteis:

| Track | Pergunta | Custo |
|---|---|---|
| A Correctness | Path/comando citado existe? | ~0 tokens (script) |
| B Redundancy | Agente redescobriria barato sem esse texto? | Alto (dual judge) |
| C Usefulness | Muda comportamento ou é teoria/demo? | Mais alto |

Princípio: **Ship ≠ Slim**. Rediscoverable ≠ useless.

## 5. Insights para sistemas pequenos

1. Comece com `AGENTS.md` curto + testes + lint — já é harness.
2. Use plan mode para mudanças pequenas; SDD só quando a incerteza/tamanho justifica.
3. Um Verifier independente (mesmo “nova sessão, não foi você quem implementou”) já muda a qualidade.
4. Não comite specs se o board é a SoT do time.
5. Code review IA: comece com 2 passes (correctness + security); adicione slop depois.

## 6. Insights para escala enterprise (ex.: Globo)

1. **Padronizar harness do time** (guides + sensores) > liberar N ferramentas sem contrato.
2. Skills versionadas no monorepo; instalação via catálogo (`@tech-leads-club/agent-skills`).
3. SDD + Judge no CI/PR como **fábrica**: Implementar → Gates → Verify → Review.
4. Hotspots de domínio (audiência, direitos, pagamento, publicação) com review humano obrigatório.
5. Observabilidade de agentes em produção separada do review de código.
6. Avaliar harness periodicamente (Track A sempre; B/C com orçamento) para evitar “AGENTS.md novelão”.

## 7. Prática recomendada

Construir a **própria skill de fluxo de desenvolvimento** e avaliá-la no seu contexto — o objetivo é aprender a **construir** ferramentas, não só consumir frameworks.

## Referências rápidas

- Skill: [tlc-spec-driven](https://github.com/tech-leads-club/agent-skills)
- Skill: [the-judge](https://github.com/tech-leads-club/agent-skills)
- Skill: [harness-eval](https://github.com/tech-leads-club/agent-skills)
- Artigo-base: Spec-Driven Development (Martin Fowler / Birgitta)
