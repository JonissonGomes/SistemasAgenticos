# Harness: Guides e Sensores

Conceito central para sistemas agênticos.

## Definição

**Harness** (sentido amplo) é tudo que você coloca **em volta** do modelo/agente para:

1. **Guiar** o comportamento (guides)
2. **Validar** o resultado com sinais determinísticos (sensors)

O harness de ferramenta (Cursor, Claude Code…) é só o runtime. O harness do **time** é o diferencial competitivo.

## Diagrama mental

```
┌─────────────────────────────────────────┐
│                 GUIDES                  │
│  AGENTS.md · rules · skills · ADRs ·    │
│  arquitetura do código · constraints    │
└──────────────────┬──────────────────────┘
                   │ direciona
                   ▼
            ┌─────────────┐
            │ Agent loop  │
            └──────┬──────┘
                   │ produz código/ação
                   ▼
┌─────────────────────────────────────────┐
│                SENSORS                  │
│  testes · lint · typecheck · CI gates · │
│  evals · hooks · screenshots · obs.     │
└─────────────────────────────────────────┘
```

## Por que importa em escala

- Mais código gerado → review humano não escala linearmente.
- Guides sem sensores = “torcida” (o modelo se autoavalia).
- Sensores sem guides = rejeição sem direção (só vermelho, sem norte).

## Mínimo viável (sistema pequeno)

1. `AGENTS.md` com 1 página: como rodar, o que não fazer, onde está o domínio.
2. Testes + lint no CI.
3. Uma skill de verificação (“não fui eu quem implementou”).

## Escala (empresa tipo Globo)

1. Catálogo de skills versionado + instalação padronizada.
2. Gates por domínio (publicação, direitos, pagamento…).
3. Harness-eval periódico (correctness sempre; redundancy/usefulness com orçamento).
4. Separar: review de PR ≠ observabilidade de agentes em produção.

## Ver também

- [Spec-Driven Development](./spec-driven-development.md)
- Anotações: [`../../estudos/anotacoes/harness-spec-driven-code-review.md`](../../estudos/anotacoes/harness-spec-driven-code-review.md)
