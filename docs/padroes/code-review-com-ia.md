# Code Review com IA

Padrão de review com evidência, alinhado à skill `the-judge`.

## Problema

Volume de código gerado por IA >> capacidade humana de revisar linha a linha. Humanos devem focar **hotspots**; o restante precisa de confiança sistêmica (gates + juiz com evidência).

## Padrão: Judge com sub-passes

Um agente único dilui atenção. Separe passes:

| Pass | Foco |
|---|---|
| A | Correctness / lógica |
| B | Security (só alta confiança) |
| C | Estrutura / fronteiras / file size |
| D | AI slop (código/comentários sem relação com a mudança) |
| E | Claims do PR vs evidência |
| F | Bypasses, gambiarras, testes pulados |

## Regras não negociáveis

1. **Evidence or silence** — `file:line` ou URL oficial; senão não posta.
2. Rodar **lint/typecheck/test do repo primeiro**; não repetir o que já é vermelho.
3. Noise budget (poucos nits).
4. Gate determinístico nos comentários antes de publicar.
5. Round 1 = review completo (sem drip de findings).

## Integração sugerida

```
PR aberto → CI (sensores) → /the-judge ou Action →
  sub-agents por pass → review consolidado →
  humano só em 🔴 / hotspots de domínio
```

## Hotspots humanos (enterprise)

Pagamentos, auth, PII, rights/DRM, publicação em produção, migrations irreversíveis: mesmo com Judge verde, **review humano obrigatório**.

## Custo

Comece com A+B; adicione D (slop) cedo — alto ROI. Performance e nits depois. Orçamento de referência: ~US$ 2/review com modelo bom de julgamento (não precisa ser o top absoluto).

## Ver também

- Skill: [the-judge](https://github.com/tech-leads-club/agent-skills)
- [Harness](../conceitos/harness-guides-e-sensores.md)
