# Evolução Arquitetural na Era da IA

## Tese

Microsserviços foram a resposta certa para um momento (empresa grande, necessidade real de escala/organização) — e a resposta **errada** para a maioria dos times que só queria fugir de um monolito difícil. Na era da IA, o sweet spot é **arquitetura modular evolutiva**: fronteiras lógicas fortes desde o dia 1; fronteiras físicas (deploy/DB) sob demanda.

**Frase-chave:** se o time não consegue manter bem o monolito, separar em microsserviços **multiplica a incompetência**.

## 1. Por que microsserviços ficaram “de risco”

Sintomas clássicos (muitos viram monólito distribuído):

- acoplamento distribuído
- bancos/libs compartilhados
- orquestração em cadeia
- contratos opacos
- granularidade errada (entity services, “só roteia”)
- custo operacional multiplicado (CI, login, monitor, libs × N)

### Motivação real das migrações (consultoria pré-IA)

Pergunta-chave: escala? organização? ou “o monolito é instável e o time odeia mexer”?

**Resposta dominante: 3.** Muitos buscavam **greenfield emocional** — prazer de código novo por 1–2 meses — e depois herdavam N serviços.

Sam Newman (contexto ThoughtWorks): livros evoluíram de “como ir para microservices” → “não vá tão rápido” → foco em arquitetura distribuída com mais cautela.

### Custo nativo

Microsserviços nasceram **antes** da automação/cloud madura. Hoje temos containers, monorepo tools, IA para refatorar — o trade-off mudou.

## 2. Quatro caminhos arquiteturais

| Caminho | Ideia | Custo dia-1 | Escala | IA |
|---|---|---|---|---|
| **Monólito modular** | 1 processo, módulos com fronteiras | Baixo | Média (vertical + eventual split) | Boa se módulos ~domínio |
| **Monorepo modular** (recomendado) | Packages + Apps; escolhe o que roda junto/separado | Médio (Nx etc.) | Alta (composição) | Melhor equilíbrio |
| **Microsserviços + monorepo** | Serviços no mesmo repo | Alto | Alta | Melhor que polyrepo; granularidade ainda dói |
| **Polyrepo microsserviços** | 1 serviço = 1 repo | Mais alto | Alta | Pior para contexto/contratos |

**Composição** (princípio que microsserviços perdem): poder rodar módulos juntos quando não precisam escalar sozinhos — não pagar AWS/rede à toa.

### Separação fundamental

> Uma coisa é **como você estrutura o código** (lógico).  
> Outra é **como você roda o código** (físico/deploy).

Misturar as duas decisões no dia 1 é o erro clássico dos microsserviços.

## 3. Domínio primeiro (DDD estratégico)

Na era da IA, **modelar o domínio** é pré-requisito:

- Core / Supporting / Generic
- Módulos alinhados a bounded contexts (não a “UserService”)
- Cada módulo **dono das próprias tabelas** (evolução); 1 DB no início é ok
- Comunicação: sync dentro do agregado; eventos entre módulos

Demo ERP: skill `domain-analysis` → mapa (purchasing, inventory, payables, catalog…) + coesão/acoplamento + plano de melhoria (incluindo bugs de regra de negócio).

## 4. Migração com IA ≠ Spec-Driven puro

### Armadilha

Código legado = fonte da verdade.  
Se você gera spec no meio e o agente implementa **só pela spec**, comportamento fica para trás.

### Abordagem recomendada

1. **Mapear domínio** (validar com humano).
2. **Plano de fases** (cada fase → PR; módulos “donos”).
3. **Harness de migração** (não só SDD):
   - inventário por **script** (IDs), não lista manual
   - baseline + gates determinísticos (test, typecheck, lint, schema, e2e)
   - catraca: progresso não regride
   - evidência: cada arquivo/tabela aparece uma vez; fases antigas zeradas
4. Loop por fase: Inventory → Baseline → Lote → Gates → Bug check → Judgment → Evidence → aprovação humana.

Exemplo de skill: `.claude/skills/modular-migration/` (gates, bug-registry, rubrica).

### Testes de caracterização

Nome para guardar: **characterization tests**.

- Testam o comportamento **atual** (mesmo com bugs).
- Na migração estrutural: preserve comportamento; catalogue bugs em registry; corrija depois.
- Em legado sem testes: caracterização **antes** de refatorar — a única SoT é o código.

### Fases típicas (exemplo ERP)

| Fase | Foco |
|---|---|
| F0 Guardrails | ownership, glossary, dependency-cruiser / lint de imports |
| F1 Characterization | golden master, schema, state machine |
| F2 Move files | mover código sem mudar comportamento |
| F3 Facades | contratos entre módulos |
| F4 Data ownership | dono de tabelas |
| F5+ | eventos, apps dedicadas, etc. |

## 5. Monorepo na prática (Nx)

Conceitos:

- **Packages / libs:** lógica de domínio (`catalog`, `inventory`, `billing`…)
- **Apps:** bootstrap/loader — quase sem negócio (`monolith` importa N módulos; `billing-api` importa só billing)

Evolução:

1. Uma app monolito modular  
2. Fronteiras + ownership  
3. Extrair app só onde métrica justifica (ex.: inventory-api)  
4. Microsserviço “de verdade” só se necessário

Ferramentas: Nx graph, `nx affected`, dependency-cruiser, validate-boundaries.

Whitepaper alinhado: [Arquiteturas Modulares — 10 princípios](https://github.com/tech-leads-club/modular-architectures-principles) · [modular-architectures.com](https://modular-architectures.com/whitepaper-pt.html)

## 6. IA × tipo de arquitetura (contexto)

| Situação | Efeito no agente |
|---|---|
| Monólito gigante sem módulos | Estoura janela de contexto |
| Módulos **muito** finos / microsserviços acoplados | Agente olha um pedaço e “acha que acabou” — deixa pendências |
| Módulos ~ domínio (tamanho de serviço) | Melhor trade-off: contexto local suficiente + menos blast radius |
| Polyrepo sem mapa de contratos | Agente não vê dependências remotas |

**Reflexão:** monólito normal > monólito distribuído para o agente (no normal ele gasta tokens e acha; no distribuído acoplado ele se engana).

### Instruções em camadas

1. **Empresa:** segurança, commits, plugins/skills globais  
2. **Repo:** build, test, convenções locais (`AGENTS.md`)  
3. **Módulo/serviço:** regras de domínio + contrato público  

**Progressive disclosure:** vários `AGENTS.md` aninhados só quando necessário — custo de manutenção. Preferir skills/plugins + docs de módulo.

### Ideia enterprise

MCP **Service Map**: grafo de serviços/dependências da empresa → agente consultando “toquei inventory → olhe também catalog/shared” + fetch via GitHub MCP / checkouts locais.

## 7. Insights para sistemas pequenos

1. Comece monólito modular: pastas por domínio, ownership de tabelas, sem cross-import.
2. Um `dependency-cruiser` / lint de imports > 10 microsserviços.
3. Não migre por moda; melhore observabilidade e deploy do monólito primeiro.
4. Se for migrar: harness + caracterização; não confie só em spec.
5. Granularidade de módulo ≈ bounded context, não ≈ entidade.

## 8. Insights para escala Globo-like

1. Monorepo modular com apps seletivas (conteúdo, auth, billing, publicação…).
2. Core domains com fronteiras rígidas + ACL para vendors (CDM, direitos, ads, players).
3. Migrações legadas como **programa** (fases, PRs, evidência), não como “um chat”.
4. Service Map / contrato publicado para times multi-repo.
5. IA acelera refatoração — mas **gates determinísticos** e hotspots humanos continuam obrigatórios.
6. Avaliar promoção a deploy separado por métrica (CPU, time ownership, frequência de release), não por feeling.

## Referências rápidas

- Whitepaper: [tech-leads-club/modular-architectures-principles](https://github.com/tech-leads-club/modular-architectures-principles)
- Skills: `evolutionary-modular-architecture`, `modular-decomposition`, `nestjs-modular-monolith`, `domain-analysis`
- Livros: Sam Newman; *Software Architecture: The Hard Parts* (fluxo monólito→componentes→domínios→serviços)
