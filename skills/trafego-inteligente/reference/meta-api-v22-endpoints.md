# Meta Marketing API v22.0 — endpoints testados e payloads que FUNCIONAM (mai/2026)

> Base: `https://graph.facebook.com/v22.0`
> Token: `meta-ads.yaml` bloco do cliente

## Discovery (GET)

### Buscar interesse
```
GET /search?type=adinterest&q=<termo>&locale=pt_BR&limit=3&access_token=X
```
Retorna: `id`, `name`, `audience_size_lower_bound`, `audience_size_upper_bound`, `topic`, `path`

### Validar interesses (ESSENCIAL pós-15/01/2026)
```
GET /search?type=adinterestvalid&interest_list=["nome1","nome2"]&access_token=X
```
Retorna array com `valid: true/false` pra cada item. **Batch máx 25.**

### Buscar cidade BR
```
GET /search?type=adgeolocation&q=<cidade>&location_types=["city"]&country_code=BR&limit=3&access_token=X
```
Retorna: `key`, `name`, `region`, `country_code`

### Estado da conta
```
GET /act_<id>?fields=name,balance,currency,account_status,disable_reason,spend_cap&access_token=X
```

### Campanhas
```
GET /act_<id>/campaigns?fields=id,name,status,effective_status,objective,daily_budget,lifetime_budget,created_time,updated_time&limit=100&access_token=X
```

### Ad Sets de uma campanha
```
GET /<camp_id>/adsets?fields=id,name,status,configured_status,effective_status,optimization_goal,billing_event,destination_type,promoted_object,targeting,daily_budget,created_time,updated_time,issues_info&access_token=X
```

### Lead Forms da página
```
GET /<page_id>/leadgen_forms?fields=id,name,status,questions&access_token=X
```

## Criar (POST)

### Criar Campanha
```
POST /act_<id>/campaigns
{
  "name": "[Cliente] C1 - SP Medio Porte",
  "objective": "OUTCOME_LEADS",
  "status": "PAUSED",
  "special_ad_categories": [],   // [] = sem categoria especial (NÃO usar "NONE")
  "buying_type": "AUCTION",
  "daily_budget": 7000            // R$ 70 em centavos (CBO)
}
```

### Criar Lead Form
```
POST /<page_id>/leadgen_forms
{
  "name": "[Cliente] Q1 - Qualificador",
  "follow_up_action_url": "https://wa.me/55XXXXXXXXXX",
  "context_card": {...},
  "questions": [
    {"type": "FULL_NAME"},
    {"type": "EMAIL"},
    {"type": "PHONE"},
    {"type":"CUSTOM","key":"valor_divida","label":"Valor da dívida?","options":[...]}
  ],
  "privacy_policy": {"url":"...","link_text":"Política"},
  "thank_you_page": {...}
}
```

### Criar Ad Set (NOVO ou via API)
```
POST /act_<id>/adsets
{
  "name": "AS1 - Geo Cirúrgico",
  "campaign_id": "<id>",
  "billing_event": "IMPRESSIONS",
  "optimization_goal": "QUALITY_LEAD",     // NÃO LEAD_GENERATION
  "bid_strategy": "LOWEST_COST_WITHOUT_CAP",
  "status": "PAUSED",
  "destination_type": "ON_AD",
  "promoted_object": "{\"page_id\":\"X\",\"smart_pse_enabled\":false}",   // JSON STRING
  "targeting": "{...}",                     // JSON STRING (ver abaixo)
  "attribution_spec": "[{\"event_type\":\"CLICK_THROUGH\",\"window_days\":1}]",
  "start_time": "2026-05-29T00:00:00-0300"
  // NÃO incluir daily_budget se campanha é CBO
}
```

**ERRO 3858634 "Anunciante BR ausente"** = NÃO PODE criar via API. gestor precisa criar Ad Set MANUAL na UI (mesmo vazio), depois você edita via API.

### Editar Ad Set existente (estratégia anti-bloqueio BR)
```
POST /<adset_id>
{
  "name": "...",
  "targeting": "{...}",
  "attribution_spec": "[...]"
}
```
Quando gestor criou Ad Set vazio na UI → ID herda registro de Anunciante → você pode editar TUDO via API sem erro.

## Targeting completo (payload JSON pra Ad Set cirúrgico)

```json
{
  "age_min": 25,           // ≤ 25 se Advantage+ Audience ON
  "age_max": 65,
  "geo_locations": {
    "cities": [
      {"key":"269969","radius":40,"distance_unit":"kilometer"},
      ...
    ],
    "location_types": ["home","recent"]
  },
  "flexible_spec": [
    {
      "interests": [
        {"id":"6002898157162","name":"Agronegócio"},
        ...
      ]
    }
  ],
  "targeting_automation": {
    "advantage_audience": 1,
    "individual_setting": {"age":1,"gender":1,"geo":0}
  },
  "publisher_platforms": ["facebook","instagram"],
  "facebook_positions": ["feed","marketplace","story","instream_video"],
  "instagram_positions": ["stream","story","reels","explore"],
  "device_platforms": ["mobile","desktop"]
}
```

## Erros conhecidos + solução

| Código | Mensagem | Solução |
|---|---|---|
| 3858634 | Anunciante BR ausente | gestor cria Ad Set MANUAL na UI vazio, depois edita via API |
| 1885621 | Budget conflita com CBO | Tirar `daily_budget` do Ad Set (campanha já tem) |
| 2490562 | Posicionamento `video_feeds` descontinuado | Tirar `video_feeds` de facebook_positions |
| 1870247 | Interesse descontinuado | Re-validar via adinterestvalid, trocar pela sugestão Meta |
| 1870188 | Idade min > 25 com Advantage+ | Setar `age_min: 25` ou desligar Advantage+ |
| 1487756 | Sobreposição geo (city dentro de region) | Usar SÓ cities OU SÓ regions, nunca misturar |
| 100 (lead_gen_form_id) | "Invalid keys lead_gen_form_id in promoted_object" | Vai no nível do AD, não do Ad Set |
| `explore_home` validation | Posicionamento descontinuado | Usar `explore` (sem `_home`) |

## Limitações Advantage+ Audience

- `age_min ≤ 25` (Meta exige)
- `targeting_automation.individual_setting.geo=0` (geo é strict)
- Idade/gênero podem ser "suggestion" (`individual_setting.age=1`)
- Interesses como hint, não filtro rígido

## Versão API

- Atual: **v22.0** (jan/2025)
- v24.0 lançada out/2025 — pendente teste, manter v22.0 por estabilidade
- Quando subir pra v24.0: revisar este doc

Versão doc: 1.0 · 2026-05-29
