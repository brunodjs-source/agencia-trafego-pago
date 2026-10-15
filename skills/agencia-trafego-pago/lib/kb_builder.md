# lib/kb_builder — Construtor de Knowledge Base por Agente

Esta lib é invocada pelo `SKILL.md` no Passo 6.

## Objetivo

Pra cada um dos 14-20 agentes da agência criada, construir um diretório `knowledge/{nome}/` contendo **1-3MB** (200-600 páginas equivalentes) de KB densa adaptada ao nicho.

## Estrutura por agente

```
knowledge/
└── {nome-do-agente}/                    ← Ex: knowledge/Sage/
    ├── _index.md                        ← Sumário + roteiro de leitura
    ├── 01-fundamentos-do-cargo.md       ← O que esse cargo faz (~80KB)
    ├── 02-best-practices-nicho.md       ← Melhores práticas mundiais (~150KB)
    ├── 03-cases-canonicos.md            ← 20-30 cases (~120KB)
    ├── 04-frameworks-modelos.md         ← Frameworks que o cargo usa (~100KB)
    ├── 05-tools-stack.md                ← Tools específicas (~80KB)
    ├── 06-erros-comuns.md               ← Anti-patterns (~60KB)
    ├── 07-comunidades-recursos.md       ← Onde aprender mais (~40KB)
    ├── 08-compliance-nicho.md           ← Regulamentações aplicáveis (~80KB)
    ├── 09-vocabulario-tecnico.md        ← Glossário (~30KB)
    └── 10-scripts-templates.md          ← Snippets prontos (~80KB)
```

Total: **~820KB-1MB** denso por agente (multiplicar quando o cargo for crítico — Spielberg=tipo recebe 2-3MB).

## Algoritmo de Construção

```pseudo
function buildAgentKB(agent_role, agent_name, agent_personality, nicho_briefing):
  for arquivo_kb in 10_arquivos_template:
    prompt = compose(
      "Construa o arquivo {arquivo_kb} pro agent {agent_name} ({role})",
      "no nicho {nicho}.",
      "Personalidade: {personality}.",
      "Voz: {voice}. Heróis: {heroes}.",
      "USE briefing do nicho como fonte primária: {nicho_briefing.relevant_section}",
      "Mínimo {N}KB de conteúdo denso, em PT-BR.",
      "Inclua: comandos prontos, código se aplicável, frameworks com pseudo-code, cases reais com nomes/datas, links de fontes verificadas."
    )
    chamar_subagente_pesquisador_com(prompt)
    salvar_em(knowledge/{agent_name}/{arquivo_kb})
```

## Pesquisadores paralelos

Pra otimizar tempo, disparar 5 pesquisadores em paralelo por agente. Cada um pega 2 arquivos da lista de 10. Total: **N agentes × 5 pesquisadores em paralelo** (com cap de simultaneidade pra não estourar rate limits).

## Estratégia de Profundidade

### Camada 1 — Conhecimento Universal (40% do KB)
Best practices universais do cargo (independente do nicho). Ex: Diretor de Arte sempre usa Big Idea, sempre brifa em camadas (insight → conceito → execução).

### Camada 2 — Adaptação ao Nicho (40% do KB)
Como o cargo é EXECUTADO especificamente naquele nicho. Ex: Diretor de Arte pra Mercado Livre foca em thumbnail otimizada pra mobile-app, não outdoor.

### Camada 3 — Cases + Operacional (20% do KB)
Cases reais do nicho + comandos/snippets/templates prontos pra usar.

## Fontes mandatórias por arquivo

| Arquivo | Mínimo de fontes citadas |
|---|---|
| 01-fundamentos | 10 fontes (livros + cursos + papers) |
| 02-best-practices | 20 fontes (blogs tier-1 + YouTube + podcasts) |
| 03-cases | 25 cases reais (com link / nome empresa / ano) |
| 04-frameworks | 8 frameworks com inventor + paper original |
| 05-tools | 15 tools (URL + custo + free tier) |
| 06-erros | 15 erros documentados |
| 07-comunidades | 10 comunidades (URL + tamanho aproximado) |
| 08-compliance | 5 leis/regulamentos citados (artigo específico) |
| 09-vocabulario | 50 termos definidos |
| 10-templates | 10 templates/snippets prontos |

## Garantia de Qualidade

Cada arquivo deve passar 3 checks antes de gravar:

1. **Densidade** — tamanho mínimo atingido (KB)
2. **Citações** — N fontes mínimas + URLs validáveis
3. **Adaptação** — pelo menos 30% do conteúdo é específico do nicho (não genérico)

Se falhar: re-executar prompt com instrução de aprofundar a parte fraca.

## Pra agentes EXTRAS por nicho (regra 2b)

Quando o nicho exigir agents extras (ex: "Algoritmo Whisperer" pro Mercado Livre), o KB desses extras é **inteiramente focado no nicho** (sem camada universal de 40%) — eles SÃO o nicho.

Estrutura extras:
```
knowledge/Cipher/                      ← Algoritmo Whisperer
├── _index.md
├── 01-anatomia-buybox-ml.md           ← Como funciona BuyBox Mercado Livre
├── 02-ranking-fatores.md              ← 50 fatores documentados
├── 03-mercado-pago-impacto.md
├── 04-mercado-envios-otimizar.md
├── 05-reviews-impact.md
├── 06-publicidade-paga-ml.md          ← Product Ads ML
├── 07-cases-sellers-top.md
├── 08-anti-banimento.md
└── 09-scripts-monitoramento.md        ← Scripts Python/n8n
```

Total: 1-2MB densa, 100% específica.

## Output

Pasta `knowledge/` da agência criada deve ter:
- N agentes × ~1MB = N MB total
- Manifest `knowledge/_index.md` com mapa de leitura cruzada
- Cada agente CONSULTA seu próprio `knowledge/{nome}/` antes de cada decisão técnica (igual o Spielberg faz hoje)
