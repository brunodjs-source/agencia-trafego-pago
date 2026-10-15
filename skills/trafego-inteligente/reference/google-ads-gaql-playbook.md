# GAQL Playbook — Equipe TI (Google Ads)

Queries prontas pra auditoria e otimização via Google Ads API. Todas rodam no endpoint `POST https://googleads.googleapis.com/v{VERSAO}/customers/{customer_id}/googleAds:search` (REST puro, padrão do repo cohnen/mcp-google-ads) ou via SDK Python já instalado. Headers REST: `Authorization: Bearer {token}`, `developer-token: <SEU_DEVELOPER_TOKEN>`, `login-customer-id: <SEU_MCC_ID>` (MCC).

## Regras invioláveis antes de qualquer query

1. **Nunca chutar campo.** Validar via `GoogleAdsFieldService`: `SELECT name, selectable, filterable, sortable, selectable_with FROM google_ads_field WHERE name LIKE 'campaign.%'` (padrão do repo oficial googleads/google-ads-mcp: "Do not guess the fields. Use the tool to look them up.").
2. **Custo sempre em micros.** `custo_reais = cost_micros / 1_000_000`. `average_cpc` também vem em micros (gotcha documentado no cohnen/mcp-google-ads).
3. **Datas**: `LAST_7_DAYS` / `LAST_14_DAYS` / `LAST_30_DAYS` são os presets válidos; janela arbitrária usa `segments.date BETWEEN 'YYYY-MM-DD' AND 'YYYY-MM-DD'` com início E fim.
4. **Customer ID** só dígitos, sem hífen (`123-456-7890` → `1234567890`).
5. **Adicionar `PARAMETERS omit_unselected_resource_names=true`** no fim de toda query: encolhe o JSON de resposta e economiza contexto (técnica do repo oficial googleads/google-ads-mcp).
6. **Erro nunca vira dado vazio.** `INVALID_ARGUMENT` exige reparo da query; todo fetch falho entra explícito no diagnóstico do relatório, nunca `except: return []` (regra do AgriciDaniel/claude-ads, gaql-notes: "Never silently skip checks; always explain why data is unavailable"). Isso corrige bug real dos scripts `verify_account.py`.
7. **Dedup em `keyword_view`**: com `segments.date`, a MESMA keyword aparece várias vezes por dia entre match types. Deduplicar pela chave `(ad_group_id + keyword_text + match_type)` e agregar métricas antes de analisar, senão os totais inflam (AgriciDaniel/claude-ads).
8. **Default de leitura = últimos 30 dias** (evita query all-time acidental, padrão FGRibreau/mcp-google-ads).

---

## Q1 — Baseline da conta (rodar SEMPRE primeiro)

Padrão "filter-first" do Query Planner (itallstartedwithaidea/google-ads-api-agent): estimar antes de detalhar.

```sql
SELECT customer.id, customer.descriptive_name, customer.currency_code,
  metrics.impressions, metrics.clicks, metrics.cost_micros,
  metrics.conversions, metrics.conversions_value, metrics.ctr,
  metrics.average_cpc
FROM customer
WHERE segments.date DURING LAST_30_DAYS
```

**O que fazer**: este é o total de referência. Depois de qualquer drill-down, somar o custo das linhas detalhadas e comparar: se cobrir < 99% do total da conta, o relatório está incompleto e tem que declarar quanto falta (threshold de completude 99% do mesmo repo). Limite prático: 500 linhas por query detalhada; acima disso, fatiar.

## Q2 — Performance de campanha 30 dias

```sql
SELECT campaign.id, campaign.name, campaign.status,
  campaign.advertising_channel_type, campaign.bidding_strategy_type,
  metrics.impressions, metrics.clicks, metrics.cost_micros,
  metrics.conversions, metrics.conversions_value,
  metrics.cost_per_conversion, metrics.average_cpc
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
ORDER BY metrics.cost_micros DESC
LIMIT 50
```

(base: cohnen/mcp-google-ads). **O que fazer**: mapa de onde o dinheiro vai. Campanha com gasto e zero conversão vai direto pra fila do Q3/Q6. Anotar `bidding_strategy_type` pra régua de migração de bid (ver heurísticas H8).

## Q3 — Search terms: gasto sem conversão (wasted spend)

```sql
SELECT search_term_view.search_term, search_term_view.status,
  campaign.id, campaign.name, ad_group.id, ad_group.name,
  segments.keyword.info.text, segments.keyword.info.match_type,
  metrics.impressions, metrics.clicks, metrics.cost_micros,
  metrics.conversions, metrics.conversions_value
FROM search_term_view
WHERE segments.date DURING LAST_30_DAYS
  AND metrics.impressions > 0
ORDER BY metrics.cost_micros DESC
```

(query completa do `02_search_terms_analyzer.py`, itallstartedwithaidea). **O que fazer**: aplicar as duas heurísticas do mesmo repo:
- **Wasted spend**: `conversions == 0 AND custo >= R$ 50 AND clicks >= 5` (adaptação BR do default $10/5 clicks; calibrar por conta).
- **Candidato a negativa**: termo contém indicador de intenção ruim ("grátis", "curso", "vaga", "salário", "baixar", "como fazer", "o que é") OU `clicks >= 3` com 0 conversões.

**NUNCA inventar negativas sem este report em mãos** (hard stop do coreyhaines31/marketingskills, audit-guardrails: sem search terms report o veredito é UNKNOWN, não uma lista de negativas chutada).

## Q4 — Search terms: winners sem keyword (mineração)

```sql
SELECT search_term_view.search_term, search_term_view.status,
  campaign.name, ad_group.name,
  metrics.clicks, metrics.cost_micros, metrics.conversions,
  metrics.conversions_value
FROM search_term_view
WHERE segments.date DURING LAST_30_DAYS
  AND metrics.conversions > 0
  AND search_term_view.status = 'NONE'
ORDER BY metrics.conversions DESC
```

**O que fazer**: `status = 'NONE'` significa que o termo converteu mas NÃO está adicionado como keyword. Adicionar em Exact/Phrase no ad group temático certo. É a passada 2 do ritual semanal de 3 passadas do google-search-playbook (coreyhaines31): (1) Waste → negativar; (2) Winners → adicionar; (3) Drift → broad puxando significado adjacente errado → apertar match ou negativar.

## Q5 — Base pra análise n-gram

```sql
SELECT search_term_view.search_term,
  metrics.clicks, metrics.cost_micros, metrics.conversions,
  metrics.conversions_value, metrics.impressions
FROM search_term_view
WHERE segments.date BETWEEN '{INICIO}' AND '{FIM}'
  AND metrics.clicks > 0
```

**O que fazer**: exportar cru e processar em Python: tokenizar cada search term em 1-grams, 2-grams e 3-grams, agregar custo/cliques/conversões por n-gram, ranquear por `custo com 0 conversões`. N-grams com gasto alto e conversão zero em MUITOS termos diferentes são o padrão que uma negativa por termo individual nunca pega (ex: todo termo com "manual" ou "de graça" sangrando). O Trace já tem a heurística n-gram; esta é a query de alimentação. Janela mínima: 30-90 dias pra ter massa estatística.

## Q6 — Quality Score por keyword (com componentes)

```sql
SELECT campaign.name, ad_group.name,
  ad_group_criterion.keyword.text, ad_group_criterion.keyword.match_type,
  ad_group_criterion.quality_info.quality_score,
  ad_group_criterion.quality_info.creative_quality_score,
  ad_group_criterion.quality_info.post_click_quality_score,
  ad_group_criterion.quality_info.search_predicted_ctr,
  metrics.impressions, metrics.clicks, metrics.cost_micros,
  metrics.conversions
FROM keyword_view
WHERE segments.date DURING LAST_30_DAYS
  AND ad_group_criterion.status != 'REMOVED'
ORDER BY metrics.cost_micros DESC
```

**O que fazer**: lembrar do dedup (regra 7). Triagem pelos componentes, não pela nota agregada: `creative_quality_score` BELOW_AVERAGE → problema de aderência anúncio-keyword (reescrever RSA do ad group); `post_click_quality_score` BELOW_AVERAGE → problema de landing page (acionar Lex); `search_predicted_ctr` BELOW_AVERAGE → keyword genérica demais ou anúncio fraco. QS <= 4 com gasto alto = candidata a mover pra ad group mais específico ou pausar.

## Q7 — Impression Share perdido: verba vs rank

```sql
SELECT campaign.id, campaign.name,
  metrics.search_impression_share,
  metrics.search_budget_lost_impression_share,
  metrics.search_rank_lost_impression_share,
  metrics.search_top_impression_share,
  metrics.search_absolute_top_impression_share,
  metrics.cost_micros, metrics.conversions
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
  AND campaign.advertising_channel_type = 'SEARCH'
  AND campaign.status = 'ENABLED'
```

**O que fazer**: diagnóstico do scorecard semanal do google-search-playbook (coreyhaines31): `budget_lost_IS` alto → o gargalo é verba (decisão do Buck: escalar ou aceitar); `rank_lost_IS` alto → o gargalo é Ad Rank (QS baixo ou bid baixo, ver Q6 antes de subir bid). Campanha convertendo bem com `budget_lost_IS > 20%` é a escala mais barata disponível na conta.

## Q8 — RSAs completos pra auditoria de copy (sem abrir UI)

```sql
SELECT ad_group_ad.ad.id, ad_group_ad.ad.type,
  ad_group_ad.ad.final_urls, ad_group_ad.status,
  ad_group_ad.ad.responsive_search_ad.headlines,
  ad_group_ad.ad.responsive_search_ad.descriptions,
  ad_group_ad.ad_strength,
  ad_group.name, campaign.name
FROM ad_group_ad
WHERE ad_group_ad.status != 'REMOVED'
ORDER BY campaign.name, ad_group.name
LIMIT 50
```

(query do cohnen/mcp-google-ads, "ouro pra auditar RSAs OAB-compliant da ClienteA sem abrir a UI"). **O que fazer**: rodar o texto de TODOS os headlines/descriptions contra as listas de compliance (OAB Provimento 205/2021 e gatilhos Debt Services do CLAUDE.md). Checar 2 RSAs por ad group (padrão da casa). `ad_strength` POOR/AVERAGE com gasto alto = fila de reescrita da Ada.

## Q9 — Disapprovals e limitações de política

```sql
SELECT campaign.name, ad_group.name, ad_group_ad.ad.id,
  ad_group_ad.policy_summary.approval_status,
  ad_group_ad.policy_summary.review_status,
  ad_group_ad.policy_summary.policy_topic_entries
FROM ad_group_ad
WHERE ad_group_ad.policy_summary.approval_status != 'APPROVED'
  AND ad_group_ad.status != 'REMOVED'
```

**O que fazer**: `APPROVED_LIMITED` em conta jurídica quase sempre = Debt Services (ver tabela de substitutos no CLAUDE.md). O `policy_topic_entries` traz a política exata e o texto ofensor. REGRA (do FGRibreau/mcp-google-ads): exemption de política NUNCA é automática; reportar política + texto pro gestor decidir. Auto-exemptar em conta com histórico sensível (OAB/Debt) é risco de suspensão.

## Q10 — Change history (quem mexeu na conta)

```sql
SELECT change_event.change_date_time, change_event.change_resource_type,
  change_event.changed_fields, change_event.user_email,
  change_event.client_type, change_event.resource_change_operation,
  campaign.name
FROM change_event
WHERE change_event.change_date_time DURING LAST_14_DAYS
LIMIT 9999
```

**O que fazer**: OBRIGATÓRIO `LIMIT <= 10000` (limite hard da API, documentado no googleads/google-ads-mcp: "Requests to resource change_event must specify a LIMIT of less than or equal to 10000") e janela máxima de ~30 dias. Uso na Fase 0 Recon: detectar edição do cliente/terceiro antes de atribuir queda de performance a criativo ou leilão. Todo post-mortem do Trace começa aqui.

## Q11 — Pacing de budget (campanha limitada por verba)

```sql
SELECT campaign.name, campaign.status,
  campaign_budget.amount_micros,
  campaign_budget.recommended_budget_amount_micros,
  campaign_budget.explicitly_shared,
  metrics.cost_micros
FROM campaign
WHERE segments.date DURING LAST_7_DAYS
  AND campaign.status = 'ENABLED'
```

**O que fazer**: `gasto_diario_medio = cost_micros/1e6/7` vs `amount_micros/1e6`. Gasto >= 95% do budget diário por 5+ dias = campanha estrangulada (cruzar com Q7: se `budget_lost_IS` alto E CPA no alvo, recomendação de aumento pro Buck). `recommended_budget_amount_micros` preenchido = o próprio Google sinaliza estrangulamento. Alerta de pacing: desvio > 15% do esperado no período (threshold do TheMattBerman/meta-ads-kit, vale igual aqui).

## Q12 — PMax: asset groups e saúde dos assets

```sql
SELECT campaign.name, asset_group.id, asset_group.name,
  asset_group.status, asset_group.ad_strength
FROM asset_group
WHERE campaign.status = 'ENABLED'
```

E por asset:

```sql
SELECT asset_group.name, asset_group_asset.field_type,
  asset_group_asset.performance_label, asset_group_asset.status
FROM asset_group_asset
WHERE asset_group_asset.status = 'ENABLED'
```

**O que fazer**: `performance_label = 'LOW'` = trocar o asset (não acumular LOW rodando). Checar cobertura de field_type: asset group sem vídeo próprio recebe vídeo auto-gerado do Google (quase sempre feio, o CRAFT reprovaria). Guardrail do google-ads-audit-checklist (coreyhaines31): asset groups sem audience signals e sem search themes = alavanca desperdiçada, FAIL de auditoria. Signals via `asset_group_signal`.

## Q13 — PMax guardrails de spend (a query de desconfiança)

```sql
SELECT campaign.name, segments.ad_network_type,
  metrics.impressions, metrics.clicks, metrics.cost_micros,
  metrics.conversions, metrics.conversions_value
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
  AND campaign.advertising_channel_type = 'PERFORMANCE_MAX'
```

**O que fazer**: a API não abre PMax por canal com precisão total, mas `ad_network_type` + comparação com Search dá o sinal. Regra do coreyhaines31/marketingskills: PMax só depois de Search maximizado; se PMax converte à metade da taxa do Search em pipeline REAL (CRM), é "cheap-looking and expensive-in-reality". Exigir: brand exclusions a nível de conta, negativas desde o dia 1, offline conversions importadas ANTES de escalar.

## Q14 — Dayparting: hora × dia da semana

```sql
SELECT segments.day_of_week, segments.hour,
  metrics.clicks, metrics.cost_micros, metrics.conversions,
  metrics.cost_per_conversion
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
  AND campaign.id = {CAMPAIGN_ID}
```

**O que fazer**: montar matriz 7×24 de CPA. Só agir com amostra: célula com < 30 cliques não decide nada (flag obrigatória de amostra pequena, padrão Simba do itallstartedwithaidea: "FLAG quando amostra < 100 cliques" no agregado). Cruzar com o schedule padrão B2B da casa (Seg-Sex 8-19h, Sáb 9-13h): a query confirma ou refuta o padrão por conta, nunca aplicar cego.

## Q15 — Device performance (validar os bid adjustments padrão)

```sql
SELECT segments.device, campaign.name,
  metrics.clicks, metrics.cost_micros, metrics.conversions,
  metrics.cost_per_conversion, metrics.conversions_value
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
```

**O que fazer**: validar o padrão da casa (Mobile -30%, Tablet -10%, Desktop 0%) contra dados reais da conta. Se mobile converte no mesmo CPA que desktop, o -30% está queimando volume bom.

## Q16 — Conversion actions: fonte única de verdade

```sql
SELECT conversion_action.id, conversion_action.name,
  conversion_action.status, conversion_action.type,
  conversion_action.category, conversion_action.primary_for_goal,
  conversion_action.include_in_conversions_metric,
  conversion_action.attribution_model_settings.attribution_model
FROM conversion_action
WHERE conversion_action.status = 'ENABLED'
```

**O que fazer**: caçar double-counting: GA4 import + tag nativa medindo o MESMO purchase/lead = FAIL de auditoria (check exato do google-ads-audit-checklist, coreyhaines31). Só UMA conversão primária por objetivo. Na ClienteA: confirmar que "Clique WhatsApp" (`<ID_CONVERSAO>`) é a única primária de lead.

## Q17 — Diagnóstico de conversão fantasma / offline upload

```sql
SELECT offline_conversion_upload_conversion_action_summary.conversion_action_name,
  offline_conversion_upload_conversion_action_summary.status,
  offline_conversion_upload_conversion_action_summary.total_event_count,
  offline_conversion_upload_conversion_action_summary.successful_event_count,
  offline_conversion_upload_conversion_action_summary.pending_event_count
FROM offline_conversion_upload_conversion_action_summary
```

**O que fazer**: resource pouco conhecido, dica embutida na descrição da tool do repo OFICIAL googleads/google-ads-mcp: "For Conversion issues try looking in offline_conversion_upload_conversion_action_summary". É o primeiro lugar pro Volt olhar quando Enhanced Conversions/GCLID upload "sumiu": mostra eventos recebidos vs aceitos vs pendentes, com motivo de rejeição.

## Q18 — Negativas existentes (antes de propor novas)

```sql
SELECT campaign.name, campaign_criterion.keyword.text,
  campaign_criterion.keyword.match_type, campaign_criterion.negative
FROM campaign_criterion
WHERE campaign_criterion.negative = TRUE
  AND campaign_criterion.type = 'KEYWORD'
```

E listas compartilhadas: `SELECT shared_set.name, shared_set.type, shared_set.member_count FROM shared_set WHERE shared_set.status = 'ENABLED'`.

**O que fazer**: obrigatório ANTES de propor negativas novas (revisão de overblocking, hard stop do AgriciDaniel/claude-ads: "nenhuma negativa broad sem evidência de search-term + revisão de overblocking"). Lembrar da mecânica: negativa broad exige TODAS as palavras em qualquer ordem; phrase bloqueia a frase em ordem; exact só a query exata.

## Q19 — Validação de geo target (lição Bali)

```sql
SELECT geo_target_constant.resource_name, geo_target_constant.id,
  geo_target_constant.name, geo_target_constant.country_code,
  geo_target_constant.target_type, geo_target_constant.canonical_name
FROM geo_target_constant
WHERE geo_target_constant.name = '{CIDADE}'
  AND geo_target_constant.country_code = 'BR'
```

**O que fazer**: SEMPRE rodar antes de usar qualquer geo ID (lição já paga: 20436 é Bali, não SP; SP estado = 20106, Campinas = 1001729). O FGRibreau/mcp-google-ads promoveu isso a tool dedicada (`search_geo_targets`) exatamente porque geo errado derruba o adset em silêncio. Conferir `canonical_name` inteiro, não só o nome.

## Q20 — Relatório semanal enxuto (o feed do Rex)

```sql
SELECT campaign.name, metrics.impressions, metrics.average_cpm,
  metrics.clicks, metrics.ctr, metrics.conversions,
  metrics.cost_per_conversion, metrics.cost_micros,
  metrics.conversions_value
FROM campaign
WHERE segments.date >= '{startDate}' AND segments.date <= '{endDate}'
```

(query verbatim do workflow n8n 1905 "Online Marketing Weekly Report", Zie619/n8n-workflows; rodar 2x: período atual + mesmo período do ano anterior pra YoY). **O que fazer**: alimentar o scorecard semanal de 8 números do google-search-playbook: spend, leads, CPL, lead→SQL, SQLs, custo/SQL, Search IS, top termos desperdiçados. Formato de saída (padrão Simba, itallstartedwithaidea): máximo 15 linhas por tabela, métricas já calculadas (nunca dump cru), sempre com date range e sample size.
