# 📋 PLANO TRÁFEGO INTELIGENTE — [CLIENTE] — [DATA]

> Versão final que o Plan apresenta antes do GO gestor

## 📊 Estado atual (Fase 0+1 - Recon+Trace)

- **Ad Account**: `act_XXX`
- **Saldo**: R$ XXX,XX (autonomia ≈ X dias com budget atual)
- **Status conta**: ATIVA / SUSPENSA
- **Page**: XXX | **Pixel**: XXX | **BM**: XXX

### Performance histórica (últimos 90 dias)
- Total gasto: R$ XXX
- Impressões: XXX | Cliques: XXX | CTR: X.X%
- Leads: XXX | CPL médio: R$ XXX
- Por placement (top): FB Reels (X%) / FB Feed (X%) / IG (X%)
- Por device: Mobile (X%) / Desktop (X%)

### Diagnóstico (Trace)
- ✅ / ⚠️ / 🚨 [Causa raiz identificada]
- Próximo passo crítico antes de mais R$: [ação]

---

## 🔍 Pesquisa de inteligência (Fase 2)

### Probe (interesses validados via API live)
- Testados: XX termos | Validados: XX | Mortos: XX
- Cidades-âncora: XX validadas

### Scout (cases reais + benchmarks)
- **CPL alvo nicho**: R$ XXX-XXX (conservador-agressivo)
- **CAC esperado**: R$ XXX (conversão lead→cliente XX%)
- **Honorário médio**: R$ XXX
- **LTV/CAC alvo**: XXx
- **Gap competitivo**: [janela aberta? concorrentes ativos? quais?]
- **Dor mensagem-chave 2026**: "..."

---

## 🎯 MUDANÇAS PROPOSTAS

### Campanha 1 — [Nome]
**Ad Set proposto**: `[Nome cirúrgico]`

| Item | Valor |
|---|---|
| Geo | XX cidades-âncora raio XXkm |
| Idade | XX-XX |
| Gênero | Todos |
| Advantage+ Audience | LIGADO / desligado |
| Devices | Mobile / Mobile+Desktop |
| Placements | FB Feed + Marketplace + Reels + Stories + IG Feed + Reels + Stories + Explore |
| Budget | R$ XX/dia (CBO/ABO) |
| Optimization | QUALITY_LEAD |
| **CPL projetado** | R$ XXX |

**Interesses validados (3 camadas)**:
- CORE setor: [N interesses]
  - [Nome] (id XXX)
  - ...
- SINAIS RIQUEZA: [N interesses]
  - ...
- LASER ultra: [N interesses]
  - ...

**Geo (cidades-âncora)**:
- [Cidade 1] (XX km)
- [Cidade 2] (XX km)
- ...

[REPETIR pra cada Ad Set proposto]

---

## 🔀 DIFF POR MUTAÇÃO (obrigatório v2.0 — sem os 5 campos o Plan não pede GO)

| # | Mutação | Estado atual | Mudança proposta | Efeito esperado | Blast radius | Rollback |
|---|---|---|---|---|---|---|
| 1 | [ex: Ad Set X targeting] | [geo/interesses hoje] | [novo targeting] | [CPL R$ X → R$ Y] | [reseta learning do adset; irmãos no CBO perdem X% share] | [payload antigo salvo em <arquivo>; 1 POST reverte] |
| 2 | ... | ... | ... | ... | ... | ... |

**Regras**: menor mudança reversível sempre (pausar > deletar; mover 20% > dobrar). GO explícito extra pra: budget +20%, bid +30%, >10 entidades, reativação, remoção, mudança de conversão. Budget +50% ou delete = confirmação DUPLA.

## ✅ VERIFICAÇÃO PÓS-GO (o Volt roda depois de aplicar)

| # | Mutação | Checagem pós-aplicação | Janela de monitoramento |
|---|---|---|---|
| 1 | ... | [GET no recurso = estado esperado; status_after_apply impresso] | 3-7 dias |

---

## ⚠ RISCOS DETECTADOS

- [ ] **Compliance OAB Provimento 205/2021** — copies têm palavras-gatilho? `[verificado]`
- [ ] **Debt Services Google/Meta** — termos proibidos? `[verificado]`
- [ ] **Tema 1.061 STJ** (PF, não PJ) — citado em algum criativo? `[verificado]`
- [ ] **Pixel + CAPI** — configurado? `[verificado]`
- [ ] **Saldo suficiente** pra X dias mínimo
- [ ] **Erro 3858634 Anunciante BR** pode aparecer? `[risco: alto/médio/baixo]`
- [ ] **Frequency cap** definido 3x/7d?
- [ ] **Learning Phase** — budget suficiente pra 50+ conv/sem?

---

## 🚀 PRÓXIMOS NÍVEIS (não executar agora — só com GO especial)

| Ad Set adicional | Status | Como ativar | ETA |
|---|---|---|---|
| Custom Audience CNPJ (CNAE alvo) | ⏸ | "GO ranger" libera | 24-48h |
| Lookalike 1% CRM ClienteA | ⏸ | gestor exporta CRM hashado | 30min após receber |
| Retargeting visitantes site 180d | ✅ disponível | "GO retargeting" | imediato |
| Engajamento IG/FB 365d | ✅ disponível | "GO engajamento" | imediato |

**Estrutura ideal final** (split 40/30/20/10 por campanha):
- A (40%) Geo Âncora + Interesses cirúrgicos ← O que tô propondo aplicar AGORA
- B (30%) CNAE Custom Audience ← Próximo nível
- C (20%) Lookalike CRM ← Próximo nível
- D (10%) Retargeting ← Próximo nível

---

## ⛔ AGUARDANDO GO DO GESTOR

[USAR AskUserQuestion COM 4 OPÇÕES:]

1. **GO TOTAL** — executa tudo proposto (Volt sobe via API agora)
2. **GO PARCIAL** — executa só X (gestor especifica)
3. **AJUSTES** — gestor pede ajuste antes (interesse extra, geo diferente, copy nova, etc)
4. **CANCELAR** — pausa, fica em standby

---

> Plano gerado por Plan (Equipe Tráfego Inteligente) · v2.0
> Calibrado: sessão ClienteA (mai/2026) + varredura GitHub (ago/2026)
> Trilho google: organizar mudanças pela escada de intenção (Brand → high-intent → Competitor → Problem-aware → Demand gen); gasto não pula degrau sem o inferior provar conversão
