# Spec-Driven Development (SDD)

## O que é (definição operacional)

É spec-driven se:

1. Existe um **artefato** persistido (markdown, ticket, doc) descrevendo problema e comportamento.
2. Alguém **revisa e aprova**.
3. O resultado é **verificado contra** o artefato (checks/provas), não por autoavaliação do modelo.

Não é “usar Spec-kit/Kiro/TLC”. É artefato + validação.

## Modos

| Modo | Uso típico |
|---|---|
| Spec-first | Feature: spec guia a mudança; depois arquiva/descarta |
| Spec-anchored | Spec permanece SoT da feature (cuidado com drift) |
| Spec as source | Só muda código via markdown (alto custo) |

## Pipeline adaptativo (TLC)

```
Specify → (Design) → (Tasks) → Execute
```

- Pequeno/claro → plan mode ou SDD enxuto  
- Grande → fases completas + commits atômicos + Verifier independente  
- Incerto → research/protótipo **antes**

## Fonte da verdade

| Organização | SoT | Commitar `.specs/`? |
|---|---|---|
| Board governa | Ticket | Não |
| Engineering-first | Repo | Sim |
| Mix | — | Evitar (drift) |

## Quando NÃO usar SDD puro

**Migrações de legado:** o código antigo é a SoT. Preferir harness de migração + caracterização; spec no meio pode perder comportamento.

## Ver também

- [Harness](./harness-guides-e-sensores.md)
- Anotações: [`../../estudos/anotacoes/harness-spec-driven-code-review.md`](../../estudos/anotacoes/harness-spec-driven-code-review.md)
