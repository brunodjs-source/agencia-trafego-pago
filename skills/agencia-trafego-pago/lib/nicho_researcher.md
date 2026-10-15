# lib/nicho_researcher — Pesquisa Profunda de Nicho

Esta lib é invocada pelo `SKILL.md` no Passo 2.

## Objetivo

Gerar `_BRIEFING-NICHO.md` (3-10MB) com conhecimento mundial denso sobre o nicho que o usuário pediu.

## Estratégia: 8 pesquisadores em paralelo

Cada pesquisador é um sub-agent disparado VIA Agent tool com prompt específico. Todos rodam em **uma única mensagem** com múltiplos Agent calls em paralelo.

### Pesquisador 1 — GitHub Mining
**Prompt template:**
```
Você é arqueólogo de GitHub. Encontre os 50 melhores repositórios públicos relacionados ao nicho "{nicho}":
- Tools open-source usadas pelo nicho
- Templates / boilerplates
- Datasets de domínio público
- READMEs de empresas referência
- Issues com padrões / problemas comuns reportados

Pra cada repo, traga: URL, stars, descrição 1-linha, por que é relevante pro nicho.
Output em markdown estruturado, mínimo 8KB.
```

### Pesquisador 2 — YouTube Mining
**Prompt template:**
```
Você é arqueólogo de YouTube. Encontre os 30 melhores canais de "{nicho}":
- 5 canais BR (português) tier-1
- 15 canais EN tier-1
- 10 canais especialistas (nicho técnico profundo)

Pra cada canal: nome, URL, inscritos aprox., 3 vídeos icônicos com link + duração, especialidade.

Identifique também: top 20 vídeos mais influentes (10M+ views) sobre o tópico.

Output em markdown estruturado, mínimo 10KB.
```

### Pesquisador 3 — Podcasts Mining
**Prompt template:**
```
Você é arqueólogo de podcasts. Encontre os 20 melhores podcasts em "{nicho}" (Spotify/Apple):
- Pra cada: nome, URL, host(s), 3 episódios canônicos, descrição

Inclua também: top 10 episódios isolados (de qualquer podcast) que viralizaram falando sobre o tema.

Output em markdown, mínimo 6KB.
```

### Pesquisador 4 — Reddit + Fóruns
**Prompt template:**
```
Você é etnógrafo digital. Pesquise o nicho "{nicho}" em:
- Reddit: subreddits relevantes (top 10) + threads icônicas (top 30 all-time)
- Fóruns especializados (Stack Overflow, fóruns próprios do nicho)
- Discord servers públicos top 5
- Slack communities relevantes

Identifique: dores recorrentes (o que pergunta-se mil vezes), gírias do nicho, oposições internas (clusters de opinião), heróis comunitários, vilões/cancelados.

Output em markdown estruturado, mínimo 10KB.
```

### Pesquisador 5 — Papers + Cases Acadêmicos
**Prompt template:**
```
Você é pesquisador acadêmico. Encontre evidências científicas e empíricas em "{nicho}":
- Google Scholar: 20 papers mais citados (com link)
- ResearchGate: estudos aplicados
- SSRN (se business)
- Tese MBA / dissertações relevantes
- White papers de consultoria (McKinsey/BCG/Bain/Gartner)
- Cases Harvard Business Review / Wharton

Pra cada: título, autores, ano, link, finding principal em 1 linha.

Output em markdown, mínimo 8KB.
```

### Pesquisador 6 — Concorrentes + Cases de Mercado
**Prompt template:**
```
Você é analista competitivo. Pesquise no nicho "{nicho}":
- Top 20 empresas mundiais (com market share)
- Top 10 empresas BR (líderes locais)
- Top 5 startups disruptoras (recém-criadas)
- Anúncios ativos no Facebook Ad Library (top performers)
- Cases Foreplay.co / MagicBrief.com / Pipiads se aplicável

Pra cada: posicionamento, USP, modelo de negócio, ticket médio, formato de anúncio dominante.

Identifique também: hooks vencedores específicos do nicho.

Output em markdown, mínimo 12KB.
```

### Pesquisador 7 — Compliance + Regulamentação
**Prompt template:**
```
Você é compliance officer. Identifique regulamentações que afetam "{nicho}" no Brasil:
- Órgão regulador principal (CONAR / ANVISA / OAB / Bacen / ANS / CFM / CFC / etc.)
- Leis específicas aplicáveis
- Termos PROIBIDOS na publicidade
- Termos PERMITIDOS substitutos
- Certificações obrigatórias
- Restrições de plataforma (Google Ads policies, Meta Ads policies)
- Casos recentes de multas / cancelamentos

Output em markdown, mínimo 8KB. Crítico: erro aqui pode levar cliente a processo.
```

### Pesquisador 8 — Tools + Stack do Nicho
**Prompt template:**
```
Você é stack analyst. Liste TODAS as ferramentas que profissionais top do nicho "{nicho}" usam:
- CRMs específicos
- Plataformas de venda / pagamento
- Tracking / Analytics
- Automação / n8n / Zapier templates
- IAs específicas do nicho
- Marketplaces
- Apps mobile críticos

Pra cada: nome, URL, custo, free tier, caso de uso ideal.

Output em markdown, mínimo 8KB.
```

## Consolidação final

Após os 8 pesquisadores retornarem, gerar `_BRIEFING-NICHO.md` consolidado com:

1. **Sumário executivo** (1 página)
2. **Os 8 dossiês** completos (cada um numa seção)
3. **Síntese cross-cutting** (padrões que aparecem em múltiplos dossiês)
4. **5 insights mais ricos** pra acionar nos agentes da agência
5. **Anti-patterns documentados** (o que NÃO fazer no nicho)
6. **Glossário do nicho** (termos técnicos + gírias)

Tamanho final: **3-10MB** dependendo da complexidade do nicho.

## ⚠️ ATENÇÃO

- Pesquise em **FONTES REAIS** — não invente links
- Cite URLs reais (validar com WebFetch quando possível)
- Em PT-BR pro corpo, termos técnicos no original
- SEM fluff genérico — denso e operacional
- Mínimo 30 fontes únicas citadas por pesquisador
