# Sistemas agênticos em escala (do piloto ao “Globo”)

Síntese aplicada a soluções agênticas empresariais: harness, SDD, review com IA e arquitetura modular evolutiva.

## Ideia central

Você não escala “um chat com Cursor”. Você escala uma **fábrica**:

```
Entradas (PM, eng, users, monitoramento)
        │
        ▼
┌─────────────────── Máquina ───────────────────┐
│  Backlog → Spec/Plan → Implement → Gates →    │
│  Verify → Judge → Done                        │
│  (N agentes em paralelo, com harness)         │
└───────────────────┬───────────────────────────┘
                    │
                    ▼
            Humano decide
     (discovery · hotspots · produção)
```

Arquitetura de software **e** harness de IA são o mesmo jogo: **guides + sensores + fronteiras**.

## Matriz: pequeno sistema → empresa grande

| Capacidade | Piloto / time pequeno | Escala enterprise |
|---|---|---|
| Harness | `AGENTS.md` + testes + lint | Catálogo de skills, plugins corporativos, harness-eval |
| Spec | Plan mode / SDD lean | SDD + SoT clara (board **ou** repo, nunca os dois brigando) |
| Review | Verifier + Judge A/B | Judge multi-pass no CI + hotspots humanos |
| Código | Monólito modular simples | Monorepo modular + apps seletivas |
| Contexto IA | Um repo | Service Map MCP + contratos + progressive disclosure |
| Migração | Evitar | Programa com fases, evidência, caracterização |
| Produção de agentes | Logs básicos | Observabilidade / evals (prompt injection ≠ PR review) |

## Playbook em 5 ondas

### Onda 0 — Fundação (1–2 semanas)

- Escolher **um** harness maduro (Cursor / Claude Code…).
- `AGENTS.md` mínimo + CI verde (test/lint/typecheck).
- Fronteiras de pasta por domínio (mesmo que ainda seja “um app”).

### Onda 1 — Qualidade no fluxo

- Spec-driven para features médias/grandes.
- Verifier independente (author ≠ verifier).
- Judge no PR (correctness + security + slop).

### Onda 2 — Fábrica do time

- Skills versionadas no monorepo.
- Templates `.specs/features/...`.
- Rubricas compartilhadas; hotspots documentados.

### Onda 3 — Arquitetura evolutiva

- Ownership de tabelas; ban cross-imports (dependency-cruiser).
- Eventos/outbox entre módulos.
- Extrair app só onde escala/autonomia exigem.

### Onda 4 — Empresa

- Plugins/skills org-wide.
- Service Map (MCP) se multi-repo.
- Governança leve: harness-eval Track A contínuo; B/C com orçamento.
- Observabilidade de agentes em produção.

## Padrões agênticos validados

| Padrão | Papel |
|---|---|
| Orchestrator + workers | Spec/Execute em batches; Judge em passes |
| Verifier separado | Reduz autoengano do implementador |
| Gates determinísticos primeiro | LLM só julga o que script não decide |
| Characterization + bug registry | Migração sem “corrigir o mundo” no meio |
| Progressive disclosure | Contexto sob demanda sem novelão |

## Exemplo de mapeamento “tipo Globo”

Não é blueprint oficial — é aplicação dos conceitos:

| Domínio (módulo) | Por que separar logicamente | Deploy separado? |
|---|---|---|
| Catálogo / CMS | Core editorial | Talvez (picos de publicação) |
| Player / streaming | Escala e runtime distintos | Frequentemente sim |
| Identidade / auth | Segurança transversal | App dedicada comum |
| Direitos / DRM | Compliance + hotspot humano | Cuidado extremo |
| Billing / ads | Dinheiro | Hotspot + possível app |
| Recomendações | Escala ML | Worker/app própria |

Comece com **um monorepo modular** e apps seletivas; não com 40 microserviços no dia 1.

## Checklist anti-ilusão

- [ ] Guides existem e são curtos o bastante para caber no contexto  
- [ ] Sensores falham de forma determinística  
- [ ] SoT única (board XOR repo)  
- [ ] Módulos ≈ domínio, não ≈ entidade  
- [ ] Migração tem evidência, não só “o agente disse que migrou”  
- [ ] Hotspots exigem humano mesmo com Judge APPROVE  
- [ ] Agentes em produção têm observabilidade própria  

## Próximos estudos neste repo

1. Ler anotações em `estudos/anotacoes/`.
2. Instalar skills (`tlc-spec-driven`, `the-judge`, `evolutionary-modular-architecture`) num projeto piloto.
3. Rodar um experimento em `estudos/experimentos/` (ex.: modularizar um CRUD com characterization + dependency-cruiser).
4. Catalogar corner cases reais em `corner-cases/`.
