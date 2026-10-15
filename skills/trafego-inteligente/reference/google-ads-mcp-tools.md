# MCP Servers e Repos de Google Ads — Avaliação e Recomendação

Avaliados no ciclo de pesquisa GitHub da equipe TI (ago/2026). Ordem: do recomendado pro complementar.

## 🥇 TOP 1 — googleads/google-ads-mcp (OFICIAL do Google)

- **URL**: https://github.com/googleads/google-ads-mcp
- **Licença**: Apache 2.0 · Google Ads API **v25** · Python/FastMCP
- **O que faz**: 3 tools (search com GAQL slot-filling, get_resource_metadata, list_accessible_customers) + 4 resources (discovery-document da API viva, metrics, segments, release-notes). **100% read-only** (readOnlyHint=True em tudo): impossível mutar conta por acidente. O LLM não escreve GAQL livre: passa listas (fields, resource, conditions) e o servidor monta a query determinística; erros da API voltam com request_id pro modelo se autocorrigir. A lista completa de 272 resources válidos é injetada na descrição da tool (anti-alucinação de FROM).
- **Por que é o TOP 1**: mantido pelo próprio Google (a versão da API nunca fica órfã), é o par perfeito pro papel do Trace/Recon (análise sem risco), e o `tools_config.yaml` permite allowlist declarativa por instância.
- **Instalação (local, 1 comando via pipx)**:
```
claude mcp add google-ads-mcp -- pipx run --spec git+https://github.com/googleads/google-ads-mcp.git google-ads-mcp
```
Env vars: `GOOGLE_ADS_DEVELOPER_TOKEN=<SEU_DEVELOPER_TOKEN>`, `GOOGLE_ADS_LOGIN_CUSTOMER_ID=<SEU_MCC_ID>`, e auth via ADC (`gcloud auth application-default login --scopes https://www.googleapis.com/auth/adwords,https://www.googleapis.com/auth/cloud-platform --client-id-file=<client.json>`). Requer developer token com no mínimo Explorer access (o do gestor é Basic, OK).
- **Ressalva**: exige o passo ADC do gcloud (não lê o google-ads.yaml direto). Se o atrito do ADC incomodar, o TOP 2 pluga na hora.

## 🥈 TOP 2 — cohnen/mcp-google-ads (menor caminho HOJE, reusa credenciais existentes)

- **URL**: https://github.com/cohnen/mcp-google-ads
- **Licença**: MIT · API v19 via REST puro (sem SDK: elimina as dores de versão do SDK Python já documentadas) · **read-only** (12 tools, nenhuma de mutação: seguro nas contas ClienteA/ClienteF)
- **O que faz**: list_accounts, run_gaql (com formatter de tabela pipe otimizado pra LLM), queries prontas de campanha/ad/RSA, get_image_assets + analyze_image_assets (auditoria de assets de imagem), get_account_currency, list_resources.
- **Por que instalar**: NÃO precisa refazer OAuth. O `google-ads.yaml` do gestor já tem tudo; basta criar um `credentials.json` no formato authorized_user:
```json
{"type": "authorized_user",
 "client_id": "<SEU_CLIENT_ID>.apps.googleusercontent.com",
 "client_secret": "<SEU_CLIENT_SECRET> yaml)",
 "refresh_token": "<SEU_REFRESH_TOKEN>"}
```
- **Instalação**:
```
git clone https://github.com/cohnen/mcp-google-ads
cd mcp-google-ads && uv venv && uv pip install -r requirements.txt
claude mcp add googleAdsServer -- <path>\.venv\Scripts\python.exe <path>\google_ads_server.py
```
Env: `GOOGLE_ADS_CREDENTIALS_PATH=<path>\credentials.json`, `GOOGLE_ADS_DEVELOPER_TOKEN=<SEU_DEVELOPER_TOKEN>`, `GOOGLE_ADS_LOGIN_CUSTOMER_ID=<SEU_MCC_ID>`. Paths SEMPRE absolutos; rodar `test_token_refresh.py` antes de plugar (valida credencial fora do MCP). Refresh de token é automático.
- **Gotchas**: mostra `cost_micros` cru (dividir por 1e6); moeda vem por tool separada (`get_account_currency`, cachear: BRL); `API_VERSION='v19'` hardcoded no topo do arquivo, trocar a constante quando deprecar.

**Recomendação prática**: instalar o TOP 2 agora (5 minutos, zero OAuth novo) pro Trace começar a auditar ClienteA/ClienteF hoje; migrar/adicionar o TOP 1 quando quiser a validação de campos via discovery document e a manutenção oficial de versão.

## 🥉 Referência de escrita segura — FGRibreau/mcp-google-ads (Rust)

- **URL**: https://github.com/FGRibreau/mcp-google-ads
- **O que faz**: 19 tools de leitura + run_gaql escape hatch + 39 write-ops com o protocolo **draft → preview → confirm_and_apply** (dry_run=true por default, hard guard pré-HTTP com `GOOGLE_ADS_REQUIRE_DRY_RUN=true`), 6 guards com thresholds em env var (budget cap, bid increase > 100%, combo killer Broad+ManualCPC, double-confirm em delete e budget +50%, blocklist, validação de formato), tudo PAUSED por default com `status_after_apply` + `next_action_hint`, audit log JSON. 322 testes, ADR público documentando os próprios bugs.
- **Uso na Agência**: NÃO instalar como executor primário (o Volt já executa via SDK Python). Usar como **blueprint de design**: os gates do H13 das heurísticas vêm daqui. O par `GOOGLE_ADS_READ_ONLY` + `GOOGLE_ADS_BLOCKED_OPS` é o modelo pra implementar `AGENCIA_READ_ONLY_ACCOUNTS` por cliente nos scripts próprios.

## Menções (não instalar, minerar)

- **itallstartedwithaidea/google-ads-api-agent** (https://github.com/itallstartedwithaidea/google-ads-api-agent, MIT, v23): 28 action files Python + 6 sub-agents + orquestrador Anthropic completo. Minerar: Query Planner filter-first (estimar linhas antes de queryar, limite 500, completude 99%), thresholds CEP de aprovação (budget > 20%, bid > 30%, > 10 entidades), formato de output Simba ("summarize, don't dump", máx 15 linhas, flag de amostra < 100 cliques). ⚠️ Vulnerabilidade real: `date_range` interpolado direto na GAQL sem whitelist; ao portar, validar contra lista fechada de presets.
- **coreyhaines31/marketingskills** (https://github.com/coreyhaines31/marketingskills, MIT, 46k stars): não é MCP, é skill pack. Os arquivos `google-search-playbook.md`, `google-ads-audit-checklist.md` (32 checks, veredito pass/fail/unknown/not_applicable) e `audit-guardrails.md` são a espinha das heurísticas H1, H5-H9 e H11.
- **AgriciDaniel/claude-ads** (https://github.com/AgriciDaniel/claude-ads): catálogo G01-G95 de controles de auditoria Google + sistema de scoring health/coverage separados + gaql-notes (dedup keyword_view, pré-registro de query, proibição de falha silenciosa). Adotar os IDs como checklist da Fase 0 Recon; os thresholds numéricos do marketingskills entram como prior calibrável por conta.
- **Zie619/n8n-workflows** (https://github.com/Zie619/n8n-workflows): workflow 1905 = esqueleto pronto do relatório semanal Google Ads + Meta + GA4 com IA via n8n (o VPS já roda n8n); GAQL via HTTP Request com header developer-token, endpoint `googleAds:searchStream`. Sanitizado: restaurar URL real e reconstruir nodes LangChain (viraram noOp com parâmetros preservados).
