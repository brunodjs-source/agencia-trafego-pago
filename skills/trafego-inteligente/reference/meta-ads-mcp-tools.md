# MCP Servers e Repos de Meta Ads — Catálogo Avaliado (Pacote TI v1.0)

> Resultado da varredura GitHub 08/2026. Veredito honesto por repo, o que roubar, como instalar. Google Ads incluído no fim porque o MCC do gestor (~20 contas) é a lacuna do pipeline.

---

## ⭐ TOP 2 RECOMENDADOS

### 1. pipeboard-co/meta-ads-mcp — o MCP de Meta Ads pra plugar no Claude Code

- **URL:** https://github.com/pipeboard-co/meta-ads-mcp
- **O que é:** 42 tools sobre Graph API **v24.0** (BSL 1.1), código em `meta_ads_mcp/core/` (20 módulos: api.py, targeting.py, adsets.py, campaigns.py, ads.py, insights.py, duplication.py, budget_schedules.py...). NÃO é wrapper raso: validação pre-flight, matriz de erros da Graph API, payloads completos de creative/adset.
- **Por que é o nº 1 pra equipe TI:** as 6 tools de targeting search/validação espelham EXATAMENTE o fluxo do Probe — `adinterestvalid`, `adinterestsuggestion` (expandir seeds pra gerar os 70–100 termos), `adTargetingCategory` (behaviors/demographics), `adgeolocation` (city keys BR) — e confirma que `adinterestvalid` segue vivo na v24.0. Bônus que o Probe não faz hoje: `GET /act_{id}/reachestimate` com o targeting completo ANTES de criar o adset (users_lower/upper_bound).
- **Instalação:** `uvx meta-ads-mcp` (ou `pip install meta-ads-mcp`) + `claude mcp add meta-ads -- uvx meta-ads-mcp` com env `PIPEBOARD_API_TOKEN` (auth via pipeboard.co) OU modo direto com `--app-id` + token próprio (usar os System User tokens do `meta-ads.yaml` — never-expire, sem OAuth flow).
- **Mesmo sem instalar o server:** os padrões de `core/adsets.py` (matriz bid_strategy, `advantage_audience: 0` obrigatório em targeting custom, campos imutáveis) e `core/api.py` (matriz de erro 190/102/368/4/459, appsecret_proof) são copiáveis direto pros scripts Python do Volt. Já absorvidos em `meta-ads-otimizacao-avancada.md` §9, §11, §12.

### 2. googleads/google-ads-mcp — o MCP OFICIAL do Google pra cobrir a lacuna do MCC

- **URL:** https://github.com/googleads/google-ads-mcp
- **O que é:** repo oficial Google (Apache 2.0), Google Ads API **v25**. 3 tools (search com GAQL slot-filling, get_resource_metadata, list_accessible_customers) + 4 resources (discovery-document, metrics, segments, release-notes). **100% read-only** (readOnlyHint=True) — seguro plugar nas 20 contas do MCC sem risco de mutação.
- **Por que é o nº 2:** é o caminho mais curto pra dar Google Ads ao Trace com garantia mecânica de leitura. O `tools_config.yaml` permite allowlist declarativa por namespace (perfil restrito por agente). O search é slot-filling (o LLM passa fields/resource/conditions em listas, nunca escreve GAQL livre — zero erro de sintaxe) e injeta a lista completa de 272 resources válidos na descrição da tool (anti-alucinação de FROM).
- **Instalação (local, 1 comando):** `pipx run --spec git+https://github.com/googleads/google-ads-mcp.git google-ads-mcp` com env `GOOGLE_ADS_DEVELOPER_TOKEN=<SEU_DEVELOPER_TOKEN>`, `GOOGLE_ADS_LOGIN_CUSTOMER_ID=<SEU_MCC_ID>`, auth via ADC (`gcloud auth application-default login --scopes .../adwords,.../cloud-platform`).
- **Alternativa mais simples (reusa o refresh_token existente, sem gcloud):** `cohnen/mcp-google-ads` abaixo.

---

## Demais repos avaliados (em ordem de utilidade)

### coreyhaines31/marketingskills
- **URL:** https://github.com/coreyhaines31/marketingskills · 46k stars, MIT, v2.0
- **O que é:** 60+ skills de marketing pra Claude. O ouro está em `skills/ads/references/`: meta-decision-system.md (sistema TCPL completo), google-search-playbook.md (escada de intenção 5 degraus), payback-period.md, google-ads-audit-checklist.md (32 checks), audit-guardrails.md (hard stops do auditor).
- **Uso:** NÃO instalar como skill — extrair os sistemas de decisão (já absorvidos nos outros 2 arquivos deste pacote). Padrão de interconexão copiável: arquivo canônico `.agents/product-marketing.md` (12 seções + changelog versionado) que toda skill lê primeiro → virar `clientes/<cliente>/client-brief.md` na Agência.

### ivangfalco/ads-skills
- **URL:** https://github.com/ivangfalco/ads-skills · 263 stars, MIT+Commons Clause
- **O que é:** 16 arquivos de KB Meta com thresholds de operador real + 12 scripts Python Marketing API v22.0 (create_campaign, get_campaign_performance, create_custom_audience, ad_scheduler, get_active_ads_copy). Arquivo central: meta-ads-operating-system.md.
- **Uso:** fonte primária dos números do `meta-ads-otimizacao-avancada.md`. O padrão organizacional "SKILL.md router enxuto + api-reference.md + scripts/ + knowledge-base/" é superior ao formato atual das skills internas — adotar na v2 da trafego-inteligente.

### TheMattBerman/meta-ads-kit
- **URL:** https://github.com/TheMattBerman/meta-ads-kit · MIT, Graph API v22.0 (mesma versão da Agência)
- **O que é:** "sistema operacional" de Meta Ads pra agente: 6 skills (daily-check 5 perguntas, ad-creative-monitor, budget-optimizer, ad-copy-generator, ad-upload, pixel-capi), thresholds centralizados em `ad-config.json`, guardrail `META_KIT_MODE=live-approved` + approval ID.
- **Uso:** copiar (a) o ad-config.json por cliente (target_cpa BRL, timezone America/Sao_Paulo), (b) os scripts pixel-audit.sh / capi-test.sh / emq-check.sh pra auditar o Contact da ClienteB, (c) o padrão workspace/brand/ (stack.md, creative-kit.md, audience.md, learnings.md append-only) — memória por cliente pro Trace/Buck/Cris.

### facebook/facebook-python-business-sdk
- **URL:** https://github.com/facebook/facebook-python-business-sdk · oficial Meta, `pip install facebook_business`
- **Uso:** substituir requests puro nos scripts do Volt quando precisar de: batch de 50 (`api.new_batch()`), insights async (`AdReportRun`), Custom Audience com hashing SHA256 automático (`CustomAudience.add_users` — pronto pro fluxo CNPJ do Ranger; telefone BR sempre com 55+DDD antes, a normalização só remove não-dígitos). Doc viva = pasta `/examples/` (um .py por endpoint). ATENÇÃO: NÃO tem retry embutido — usar o wrapper do §11 do arquivo de otimização.

### stape-io/facebook-tag
- **URL:** https://github.com/stape-io/facebook-tag · Apache 2.0, template oficial Stape pra GTM server-side, Graph API v26.0
- **Uso:** o `template.tpl` é o mapa aberto do payload CAPI perfeito — lista definitiva de campos hashear-vs-raw, reconstrução de fbc a partir do fbclid, tabela GA4→eventos padrão Meta. Já destilado no §10 do arquivo de otimização. Consultar o .tpl direto quando o EMQ de algum cliente travar.

### AgriciDaniel/claude-ads
- **URL:** https://github.com/AgriciDaniel/claude-ads
- **O que é:** skill multi-plataforma (12 canais) com core Python tipado, catálogo de 250+ controles de auditoria (Meta M01–M72, Google G01–G95), scoring com evidência datada, 5 gates de mutação, tiers de automação T0–T4.
- **Uso:** filosofia OPOSTA ao marketingskills (zero thresholds fixos, tudo evidence-based). Combinar: **claude-ads dá a estrutura** (vereditos pass/fail/unknown/not_applicable, health vs coverage separados, IDs M01–M40 como checklist da Fase 0 do Recon) e **marketingskills/ads-skills dão os números de partida**. Rotular cada conta com tier T0–T4 + mapa das 9 dimensões no client-brief.

### cohnen/mcp-google-ads
- **URL:** https://github.com/cohnen/mcp-google-ads
- **O que é:** MCP Google Ads em Python via REST puro (sem SDK — elimina as dores de versão já documentadas), 12 tools read-only, formatter de resultado GAQL em tabela pipe pro LLM.
- **Instalação (o mais rápido pro stack atual):** clone + uv venv + criar `credentials.json` formato authorized_user com client_id/client_secret/refresh_token do `google-ads.yaml` existente (SEM refazer OAuth) + `claude mcp add googleAdsServer` com env DEVELOPER_TOKEN e LOGIN_CUSTOMER_ID=<SEU_MCC_ID>. Rodar `test_token_refresh.py` antes de plugar. Gotchas: API v19 hardcoded (trocar constante se deprecar); micros crus (dividir por 1e6); moeda via tool separada.

### FGRibreau/mcp-google-ads
- **URL:** https://github.com/FGRibreau/mcp-google-ads · Rust, 322 testes
- **Uso:** NÃO instalar (gestor já tem SDK Python) — roubar o DESIGN: protocolo draft→preview→confirm com `dry_run=true` default e hard guard pré-HTTP (`GOOGLE_ADS_REQUIRE_DRY_RUN`), 6 guards com thresholds em env var (budget cap, bid increase > 100%, combo BROAD+ManualCPC bloqueado, double-confirm pra delete e budget +50%), `READ_ONLY` + `BLOCKED_OPS` por conta (implementa a regra "só ClienteB editável" como config enforçável), audit log JSON. Lições do ADR-0001: guard que só avisa é teatro; PAUSED sem `next_action_hint` gera ads fantasma; recommendations só via RPC dedicado, nunca mutate genérico.

### itallstartedwithaidea/google-ads-api-agent
- **URL:** https://github.com/itallstartedwithaidea/google-ads-api-agent · MIT, v2.0.0, Google Ads API v23
- **Uso:** blueprint de agente PPC completo (28 actions + 6 sub-agents + orquestrador Anthropic). Roubar: protocolo CEP Confirm→Execute→Post-check com thresholds (budget +20%, bid +30%, 10+ entidades = aprovação obrigatória), Query Planner filter-first (estimar volume antes de queryar, validação de completude 99%), padrão de output "SUMMARIZE, DON'T DUMP" (máx 15 linhas/tabela, flag amostra < 100 cliques). AVISO: tem vulnerabilidade real de injeção GAQL no date_range — ao portar, whitelist fechada de presets.

### Zie619/n8n-workflows
- **URL:** https://github.com/Zie619/n8n-workflows · 56k stars, 2.057 workflows
- **Uso:** mineração pontual (índice em `docs/api/search-index.json`, 2,2MB, grepar local). Destaques: workflow **1905** (relatório semanal Meta+Google+GA4 com IA → adaptar trocando Telegram por Evolution API), **1058** (comentários de Page via Graph API — monitorar ads da concorrência/Multplay), **1603** (vision scraper → base pro analisador de Ad Library do Cris), **1884** (long-lived tokens pra cliente sem BM verificada). AVISOS: workflows sanitizados (nodes LangChain viraram noOp — reconstruir; URLs viraram env vars); NÃO existe scraper pronto de Ad Library nem pasta Google Ads.

### pymc-labs/pymc-marketing e growthbook/growthbook
- **URLs:** https://github.com/pymc-labs/pymc-marketing · https://github.com/growthbook/growthbook
- **Uso:** não são tools de Meta Ads — são a base científica do arquivo `medicao-incrementalidade.md` (adstock/saturação/lift test/budget optimizer; SRM check, sequential testing, decisão bayesiana com expected loss). Consultar lá.

### realkimbarrett/advertising-skills
- **URL:** https://github.com/realkimbarrett/advertising-skills
- **Veredito:** PARCIALMENTE RASO — README anuncia ~24 skills mas só 12 existem; o claim-checker prometido NÃO existe; zero thresholds. Salvável: formato de skill (frontmatter com triggers + Role/Process/Output/Rules/Avoid, SKILL.md < 30 linhas + references/), regra de orquestrador "Do not invent missing inputs", references de Schwartz awareness-stages e mechanism-patterns (3 componentes + 4 tipos). Não instalar; usar como esqueleto.

---

## Resumo executivo de adoção

1. **Instalar já:** `pipeboard-co/meta-ads-mcp` (Meta, targeting/pre-flight que o Probe usa) + `cohnen/mcp-google-ads` (Google read-only em 30 min com credenciais existentes; migrar pro oficial `googleads/google-ads-mcp` quando quiser o slot-filling e o tools_config).
2. **Absorver sem instalar:** thresholds do ivangfalco/marketingskills/meta-ads-kit (→ `meta-ads-otimizacao-avancada.md`), estrutura de scoring do claude-ads (→ Fase 0 do Recon), guardrails do FGRibreau (→ scripts do Volt).
3. **Não adotar:** advertising-skills (raso), deploy Cloud Run do google-ads-mcp (overkill pra 1 operador).
