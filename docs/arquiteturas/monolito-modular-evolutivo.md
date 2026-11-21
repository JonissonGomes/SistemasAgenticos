# Monólito Modular Evolutivo

Arquitetura-alvo recomendada para a maioria dos sistemas — inclusive antes de “parecer Globo”.

## Regra de ouro

**Fronteira lógica forte desde o dia 1. Fronteira física (deploy/DB) só quando métrica justificar.**

```
Monólito modular → Apps seletivas no monorepo → Microsserviço (só se preciso)
```

## Estrutura mental

```
apps/           # bootstrap (quase sem negócio)
  monolith/     # carrega N módulos
  billing-api/  # carrega só billing

packages/       # domínio
  billing/
  content/
  identity/
  shared/       # infra sem regra de negócio
```

## Princípios (resumo dos 10)

1. Limites bem definidos (bounded context)
2. Componibilidade (rodar junto ou separado)
3. Independência (evoluir sem cascata)
4. Isolamento de estado (módulo dono das tabelas)
5. Comunicação explícita (facade/eventos; sem import profundo)
6. Substituibilidade
7. Independência de deploy (opcional)
8. Escala individual (opcional)
9. Observabilidade
10. Falhas isoladas

## Decisões práticas

| Tema | Default |
|---|---|
| Banco | 1 DB; ownership por módulo (sem FK cross-module) |
| Comunicação | Sync no agregado; outbox/eventos entre módulos |
| Externos | Sempre atrás de Port + ACL |
| Granularidade | Módulo ≈ domínio, não ≈ entidade |
| Ferramentas | Nx/Turborepo + dependency-cruiser + testes |

## Migração de legado

1. Domain analysis  
2. Fases + PRs  
3. Harness: inventário scriptado, baseline, gates, evidência  
4. Characterization tests + bug registry  
5. **Não** substituir o código legado por spec intermediária como SoT  

Detalhes: [`../../estudos/anotacoes/evolucao-arquitetural-ia.md`](../../estudos/anotacoes/evolucao-arquitetural-ia.md)

## Anti-padrões

- Microsserviços no dia 1 “porque vai crescer”
- Entity services / user-service
- AGENTS.md novelão sem progressive disclosure
- Módulos tão finos que o agente “acha que acabou” sem olhar o vizinho

## Referências

- [Whitepaper Arquiteturas Modulares](https://github.com/tech-leads-club/modular-architectures-principles)
- Skill `evolutionary-modular-architecture`
