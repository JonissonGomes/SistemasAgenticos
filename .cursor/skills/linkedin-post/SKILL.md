---
name: linkedin-post
description: >-
  Creates LinkedIn technical posts from this repo's docs/notes, with a bilingual
  caption (PT-BR + English) and a non-standardized infographic whose on-image
  text is English only. Post copy must teach the idea (not vague hype or pure
  CTA); images must be meaningful and visually intuitive. Use when the user
  asks for a LinkedIn post, post do LinkedIn, carrossel, infográfico para
  LinkedIn, conteúdo LinkedIn, or wants to publish ideias from estudos/docs as
  social posts.
---

# LinkedIn Post (Sistemas Agênticos)

Transforma conceitos do repo em posts LinkedIn com texto bilingue e imagem **clara, intuitiva e única**.

## Non-negotiables

1. **Texto do post em PT-BR e EN** — sempre as duas versões no mesmo `post.md`.
2. **Texto comunica a ideia (não vago, não puro CTA)** — o caption deve ensinar o conceito.
   - Quem lê só o texto (sem a imagem) sai entendendo a tese + o “como”.
   - Obrigatório: definição ou mecanismo concreto (o que é / como funciona / o que muda).
   - Bullets com conteúdo acionável ou critérios — não slogans.
   - CTA no máximo na **última** linha e só como pergunta/reflexão leve; nunca o corpo do post.
   - Proibido: motivacional vazio, “comenta se concorda”, “link na bio”, teaser sem payload, lista de hashtags no lugar de explicação.
3. **Texto na imagem só em inglês** — titles, labels, footers, pills: English only.
4. **Imagem faz sentido e é visualmente intuitiva** — prioridade sobre “ficar bonito” ou “variar por variar”.
   - Em ~3 segundos, alguém entende a tese **só olhando a arte** (sem ler o caption).
   - O layout espelha a lógica do conceito (comparação → colunas; fluxo → setas; stack → camadas; conflito → split).
   - Poucos elementos, hierarquia óbvia (1 título > grupos > labels curtos), leitura mobile.
   - Se o estilo escolhido obscurecer a ideia, **troque o estilo** — clareza vence variedade.
5. **Imagens não padronizadas** — variar layout/paleta vs o post anterior **depois** de garantir clareza (regra 4).
6. **Uma ideia só** por post. Sem citar cursos/marcas de formação.
7. **Fontes do repo** — puxar de `docs/` ou `estudos/anotacoes/` (ou tema pedido pelo usuário).

## Quando disparar

- Pedidos: “post LinkedIn”, “faz um post”, “conteúdo pro LinkedIn”, “gera a arte”, “próximo post”.
- Se o usuário não especificar o tema, sugerir a partir de `estudos/linkedin/posts/*/notas.md` ou docs ainda não postados; se houver ambiguidade, perguntar em 1 linha.

## Workflow

### 1. Escolher tema e slug

- Próximo número: `estudos/linkedin/posts/` → `NNN-slug-em-kebab`.
- Ler fonte interna e extrair **1 tese + 3–5 bullets**.

### 2. Desenhar a metáfora visual (antes do estilo)

Escrever em 1–2 linhas (vai para `post.md` / `notas.md`):

- **Idea at a glance:** o que a pessoa deve “ver” em 3 segundos.
- **Spatial metaphor:** ex. left=guides / right=sensors; bottom=models → top=user; stages 1→N.

Só então escolher o estilo em [references/image-styles.md](references/image-styles.md) que **melhor expressa** essa metáfora. Evitar o estilo do post anterior se outro estilo servir igualmente bem; se só um estilo deixa a ideia clara, use-o mesmo assim e varie detalhes (cor, densidade, metáfora).

### 3. Escrever `post.md`

Usar [references/post-template.md](references/post-template.md). Estrutura do texto (PT e EN):

```
[HOOK — 1 strong line that names the idea]

[CONTEXT — define the concept / mechanism in 2–4 lines]

[BULLETS — 3 to 5 concrete points: criteria, steps, tradeoffs]

[CLOSE — concrete consequence / insight; avoid binary engagement questions]

—
#hashtags
```

Teste rápido: apague o hook e o close — o meio ainda explica a ideia? Se não, reescreva.

Regras de voz: [references/voice.md](references/voice.md).

### 4. Produzir imagem (craft > gen AI)

**Preferência obrigatória:** artboard **humano** em HTML/CSS ou SVG (`artboard.html`), depois screenshot 1080×1080. Só usar geração de imagem se o usuário pedir ilustração específica — e mesmo assim evitar cara de template.

Craft (default):
1. Criar `artboard.html` com tipografia real (ex.: Fraunces + IBM Plex), hierarquia clara, assimetria leve intencional, English-only.
2. **Altura acompanha o conteúdo** — não forçar 1080×1080 se sobrar vazio. Largura típica 1080; altura = conteúdo + padding justo. Se após o layout ainda houver faixa morta embaixo, **cortar** no export.
3. Screenshot do `#artboard` / `.frame` (clip ao conteúdo) via Edge/Chrome headless ou Playwright → `imagem.png` / `imagem.jpg`.
4. Inspecionar: se legibilidade ou metáfora falhar, **editar o HTML** (não regenerar prompt). Sem “mar de padding” abaixo do diagrama.

Se usar gen AI (exceção):
- Exigir no prompt: English-only, idea in 3s, **no AI-infographic look**.
- Proibir no prompt: identical pill rows, perfect centering, fake handwriting fonts, glossy orbs, stock sticky-notes, Midjourney template chrome.
- Inspecionar; se parecer IA → abandonar e voltar ao artboard HTML.

Anti “cara de IA” (vale para craft e gen):
- Sem alinhamento matemático de tudo no centro
- Sem 5 cards idênticos na mesma linha com gaps iguais
- Sem tipografia “handwritten” sintética uniforme
- Composição com peso tipográfico real e decisões de layout explícitas

### 5. Atualizar índices

- `estudos/linkedin/README.md` — tabela de posts
- `estudos/linkedin/posts/NNN-slug/notas.md` — fontes + ideias seguintes
- `estudos/README.md` — linha do post, se existir índice

### 6. Entregar ao usuário

- Caminho da pasta do post
- Preview: metáfora visual + estilo + tese em 1 linha
- Textos PT-BR e EN prontos para colar (não esconder só no arquivo)

## Checklist final

- [ ] `post.md` tem seção **PT-BR** e **English**
- [ ] Texto ensina a ideia (definição/mecanismo + bullets concretos); não é vago nem puro CTA
- [ ] Imagem: zero texto em português
- [ ] Imagem: tese óbvia em ~3s; sem cara de template de IA (preferir artboard HTML)
- [ ] Imagem: tipografia/layout com decisões humanas (não gen AI default)
- [ ] Imagem: sem faixa vazia grande embaixo (altura/crop no conteúdo)
- [ ] Metáfora espacial alinhada ao conceito
- [ ] Estilo ≠ post anterior **ou** variação justificada pela clareza
- [ ] Hashtags em ambos os textos
- [ ] Índices atualizados
