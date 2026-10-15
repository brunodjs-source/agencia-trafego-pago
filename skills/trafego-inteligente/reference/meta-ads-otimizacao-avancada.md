# Meta Ads — Otimização Avançada (Pacote TI v1.0)

> Referência avançada da Equipe Tráfego Inteligente. Complementa `meta-api-v22-endpoints` e `benchmarks-cpl-por-nicho`. Cada regra cita o repo-fonte. Thresholds em EUR/USD nos originais valem como PRIOR calibrável — recalibrar com 30 dias de dados da conta (regra do `AgriciDaniel/claude-ads`: nunca tratar benchmark amplo como veredito).

---

## 1. Âncora única de decisão: TCPL (Target Cost Per Qualified Lead)

Fonte: `coreyhaines31/marketingskills` (meta-decision-system.md) + `ivangfalco/ads-skills` (meta-ads-operating-system.md)

Toda decisão de kill/keep/scale ancora num único número. 3 formas de cravar:

- **(A)** TCPL = custo-alvo por conversa qualificada × taxa lead→qualificado (ex: R$ 40 de alvo por lead que responde × 28% = TCPL sobre o lead bruto)
- **(B)** TCPL = CPL qualificado trailing 30 dias × 0,80 (meta de melhoria de 20%)
- **(C)** Conta nova: CAC-alvo × taxa lead→cliente; substituir por (B) após 30 dias

**Adaptação ClienteB**: TCPL = custo-alvo do Contact CAPI delivery-confirmed; "qualificado" = lead que RESPONDEU o welcome no WhatsApp (o dado já existe no workflow `ll3nFTzTZL5jzhmr`).

Todos os gatilhos abaixo são múltiplos de TCPL:

| Múltiplo | Significado |
|---|---|
| < 3× TCPL gasto | Amostra insuficiente → **WAIT**, não kill (95% de confiança exige 3×) |
| 1,0–1,5× TCPL de CPQL | Variância normal → **MONITOR** |
| > 1,5× TCPL de CPQL | Problema estrutural → **SWAP** |
| ≥ 3× TCPL gasto com ZERO leads | Abandonar o conceito, não iterar |

## 2. Estrutura 80/20: campanha Scaling + campanha Testing

Fonte: `ivangfalco/ads-skills` + `coreyhaines31/marketingskills`

- **NUNCA testar criativo novo na mesma CBO dos vencedores** — ads provados sugam o budget e matam testes por inanição, não por qualidade.
- Campanha **Scaling** ~80% do budget (só ads graduados) + campanha **Testing** ~20% (budget protegido). Targeting IDÊNTICO nas duas; a separação é controle de verba, não segmentação.
- **Teto de ads ativos** = (budget diário × 14) / (2 × TCPL). Operar em 6–10. O teto é onde PARA de adicionar, não meta.
- **Throughput de testes/mês** = (budget mensal × 0,20) / (3 × TCPL); na prática 1,5–2× isso por kills precoces liberarem verba.

## 3. Decision tree de 2 estágios (Delivery ANTES de Quality)

Fonte: `ivangfalco/ads-skills` (thresholds idênticos em `coreyhaines31/marketingskills`)

**Estágio 1 — Delivery Check (dia 7, veredito do algoritmo):**
- Gasto mínimo justo = (budget diário da campanha / nº ads ativos) × 7 × 0,5. Abaixo disso o Meta despriorizou → **KILL sem olhar conversão** e substituir.
- Check contínuo: gastou ≥ 1× TCPL lifetime E média < ~R$ 50/dia nos últimos 7d → o CBO deu chance justa → KILL.
- Ao iterar ad morto por delivery: mudar **SÓ hook/visual/formato** — nunca o conceito inteiro.

**Estágio 2 — Quality Check (toda segunda, janela ROLANTE de 14 dias, executar EM ORDEM e parar no primeiro gatilho):**
1. Gasto < 3× TCPL → WAIT
2. Zero leads de pixel com ≥ 3× TCPL → SWAP com ângulo/formato completamente diferente (48h)
3. Qualidade via CRM/WhatsApp: leads mas zero qualificados → SWAP mudando a dor; taxa qualificada < 40% → SWAP adicionando linguagem filtro-ICP; 40–60% → monitorar +1 semana; ≥ 60% → segue
4. Custo: CPQL ≤ TCPL → candidato a winner; 1–1,5× → monitorar; > 1,5× → SWAP (mesmo ângulo, hook novo)

**Graduação Testing → Scaling (TODAS obrigatórias):** ≥ 5 leads qualificados · taxa qualificada ≥ 60% · CPQL ≤ TCPL · rodando ≥ 14 dias · ≥ 1 qualificado nos últimos 7 dias.

## 4. Regra inviolável de SWAP: nunca pausar sem substituto

Fonte: `ivangfalco/ads-skills`

Pausar sem substituir = encolhimento de budget (o CBO redistribui e a conta perde volume). Manter 2–3 iterações prontas; substituto no ar em ≤ 7 dias. O que mudar depende do MOTIVO do kill:

| Motivo do kill | O que trocar | Prazo |
|---|---|---|
| Falha de delivery (Estágio 1) | Hook/visual/formato diferente (NÃO iterar) | imediato |
| Zero leads de pixel | Ângulo completamente novo | 48h |
| Zero qualificados | Mesmo formato, outra dor | 48h |
| Taxa qualificada < 40% | Iterar adicionando termos de ICP | 48h |
| Custo > 1,5× TCPL | Mesmo ângulo, hook novo | 48h |
| Fadiga crítica | Execução nova do mesmo conceito | imediato |

**NUNCA editar criativo performando** (reseta learning) — lançar ad novo ao lado. Exceção documentada em `TheMattBerman/meta-ads-kit`: refresh de COPY em ad existente deve atualizar o creative do MESMO ad (preserva social proof), nunca criar ad novo só pra trocar texto.

## 5. Detecção de fadiga criativa — bandas numéricas

Fonte: `ivangfalco/ads-skills` + `coreyhaines31/marketingskills` + `TheMattBerman/meta-ads-kit`

**Bandas de frequência por tipo de audiência:**

| Audiência | OK | Warning | Crítico |
|---|---|---|---|
| Prospecção fria | 1,0–2,5 | 2,5–4,0 | > 4,0 |
| Retargeting | 2,0–4,0 | 4,0–6,0 | > 6,0 |
| ABM/lista | 2,0–5,0 | 5,0–8,0 | > 8,0 |

**Sinais por TENDÊNCIA, nunca por snapshot de 1 dia** (`meta-ads-kit`, evita falso positivo de flutuação diária):
- CRÍTICO: CTR caindo 3+ dias consecutivos E > 20% abaixo do pico
- WARNING: frequência > 3,5 OU CPC subindo 3+ dias consecutivos E > 15% acima do baseline
- MONITOR: impressões em declínio (perda de delivery)
- Adicionais: CPM +30% em 2 semanas; Ad Relevance "Below Average"; CPA subindo com targeting estável

**Ads na campanha Scaling têm barra mais dura:** warning em freq 3,0–3,5 OU custo +20% (produzir 2 iterações, prontas em 14 dias); swap em freq > 3,5, custo +40%, ou > 1,5× TCPL por 2 semanas.

**Vida útil esperada por formato:** estática 14–28d · vídeo curto/carrossel 21–35d · UGC/depoimento 28–42d. **APOSENTAR** (não iterar) quando CTR cai 30%+ do pico OU frequência passa a banda crítica. Bleeder (`meta-ads-kit`): CTR < 1% OU frequency > 3,5 com gasto relevante.

## 6. Matemática de produção criativa

Fonte: `ivangfalco/ads-skills` + `coreyhaines31/marketingskills`

- **Win rates calibrados:** iteração de winner ~25% (1 em 4) · conceito novo ~10% (1 em 10) · blended ~1 em 6. Pra N winners, planejar ~6N testes.
- **Mix de produção 50/30/20:** 50% iterações dos top performers, 30% iterações dos demais winners, 20% conceitos novos.
- **Hierarquia de iteração por impacto:** (1) hook/3 primeiros segundos (90% da decisão de engajar) → (2) tratamento visual (rosto humano +38% engajamento; aspect 4:5 +18% CTR) → (3) formato → (4) body copy/CTA (marginal).
- **Regra image-first:** todo conceito novo lança como ESTÁTICA primeiro; só produz vídeo/UGC depois de passar os gates do dia 7. Ad estático que morre no delivery = zero produção de vídeo desperdiçada.
- **Inventário mínimo de winners** = budget mensal / ~R$ 25k (cada winner absorve ~R$ 25k/mês antes de fadigar — original €5k, calibrar BR). NUNCA escalar budget à frente do supply criativo.

## 7. Scaling e rollback — protocolo mecânico

Fonte: `ivangfalco/ads-skills` + `coreyhaines31/marketingskills`

**Escalar SÓ quando TODAS:** inventário de provados atende o próximo tier · frequência da conta < 3,0 · CPQL ≤ TCPL por 2+ semanas consecutivas · 3+ substitutos prontos na Testing.

- **Taxa: +20% a cada 5 dias. NUNCA +30% de uma vez** (reseta learning phase).
- **Rollback:** CPQL > 1,5× TCPL pós-escala → cortar 20–30% imediato, estabilizar 2 semanas, retomar +10%/semana.
- **Parede de audiência (freq da conta > 3,5):** expandir lookalike 1% → 2–3%, seeds novos, broad, reabrir remarketing — nesta ordem.
- Budget mínimo pra sair da learning phase = (CPA-alvo × 50) / 7 (regra dos 50 optimization events/adset/semana).
- Sazonalidade: Q1 = CPM mais baixo do ano (escalar agressivo); Q4 = CPM +60–80% do baseline.

## 8. Cadência semanal fixa (automatizável em 3 crons n8n)

Fonte: `ivangfalco/ads-skills`

"Meta pensa em semanas, não em dias" — nunca decidir com < 7 dias de dados; o erro mais destrutivo é mexer a cada 2–4 dias.

- **Segunda (DECISÃO):** dados 14d rolling → Estágio 2 nos ads de Testing → fatigue check nos de Scaling → swaps/graduações → atualizar pipeline criativo
- **Quarta (LANÇAMENTO):** subir testes novos → Estágio 1 (delivery) dos ads com 7+ dias
- **Sexta (BUDGET):** critérios de scaling → +20% se cumpridos → gatilhos de rollback → pacing
- **Mensal:** auditoria tracking/CAPI + frescor de audiência + biblioteca criativa + revisão do TCPL + reach único MoM (reach rolante caindo = acionar creators/partnership ads antes da fadiga forçar)

**Briefing diário "5 perguntas"** (`TheMattBerman/meta-ads-kit`, formato fixo pro Trace): (1) Estou no ritmo? (spend vs esperado, alerta se desvio > 15%) · (2) O que está rodando? (roster vs esperado) · (3) Performance 7d por campanha · (4) Winners e bleeders ad-level · (5) Tem fadiga? O valor está na CONSTÂNCIA do formato — anomalia detectada por contraste dia-a-dia.

## 9. Análise de breakdown sem cair na armadilha do mix

Fonte: `pipeboard-co/meta-ads-mcp` (core/insights.py) + `AgriciDaniel/claude-ads` (política de evidência) + skill `meta-ads-analyzer` interna

- **Armadilha do mix (Simpson):** CPA blended piorando pode ser só MUDANÇA DE MIX (mais % de spend num segmento caro) sem nenhum segmento ter piorado. REGRA: antes de declarar "piorou", decompor Δ CPA em (a) mudança dentro de cada segmento e (b) mudança de participação de spend entre segmentos. Se (b) domina, o problema é alocação/leilão, não criativo.
- **Nunca comparar breakdown entre janelas de atribuição diferentes** — hard stop do `marketingskills` (audit-guardrails): reportar lado a lado com a janela anotada, nunca somar.
- **Gotchas técnicos de breakdown** (`pipeboard-co/meta-ads-mcp`): `platform_position` EXIGE par com `publisher_platform`; `media_type` COLIDE com `action_breakdowns=['action_type']` (zerar action_breakdowns). `action_attribution_windows` vai como string `"['1d_click,7d_click,1d_view']"`.
- **Parsing de conversões** (`ivangfalco/ads-skills`): filtrar o array `actions` por action_type explícito — pra ClienteB incluir `contact` e `offsite_conversion.fb_pixel_contact`; somar `value` só dos types listados. Nunca somar o array inteiro.
- **Flag de amostra obrigatória** (`itallstartedwithaidea/google-ads-api-agent`, padrão Simba): todo relatório marca segmentos com < 100 cliques como "amostra insuficiente" — breakdown com 12 cliques não é insight, é ruído.

## 10. CAPI / Tracking QA — checklist numérico

Fonte: `TheMattBerman/meta-ads-kit` (pixel-capi) + `stape-io/facebook-tag` (template canônico)

**Dedup (a regra nº 1):** pixel browser E CAPI devem disparar com o MESMO `event_id` + `event_name` — sem isso, conversão conta em dobro. Fallback do stape: `event_id || transaction_id`. Se não dá pra compartilhar event_id (eventos de momentos diferentes, caso ClienteB clique-site vs entrega-WhatsApp), escolher UM como evento de otimização e documentar.

**user_data — o que hashear (SHA256 hex) vs mandar RAW** (`stape-io/facebook-tag`, normalizar ANTES do hash ou match = zero):

| Hashear SHA256 | RAW (nunca hashear) |
|---|---|
| em (lowercase+trim), ph (SÓ dígitos COM DDI 55), fn, ln, ct (sem espaços), st, zp, ge, db, country, external_id | fbp, fbc, client_ip_address (1º IP do X-Forwarded-For), client_user_agent |

- Telefone BR: resolver a variação do 9º dígito ANTES do hash — hashear a forma canônica `55DDD9XXXXXXXX`.
- Detecção de já-hasheado: regex `^[A-Fa-f0-9]{64}$` → não re-hashear.
- **Reconstrução do fbc a partir do fbclid** (ouro pra funil Typebot→WhatsApp): `fbc = 'fb.1.' + timestamp_ms_do_clique + '.' + fbclid`. Salvar o fbclid na attribution do lead e reconstruir na hora do CAPI. fbc presente liga o evento ao CLIQUE específico — sem ele o Meta atribui só por fuzzy match.
- Geração de fbp quando não há cookie: `'fb.1.' + timestamp_ms + '.' + random(1000000000, 2147483647)`.

**Alvos de Event Match Quality** (`meta-ads-kit`): Purchase 9,3+ excelente / 8,0–9,2 bom / < 6,0 crítico · Lead 8,0+ excelente / < 5,0 crítico. Diagnósticos verbatim: EMQ ~4,0 = quase sempre email não passado ou fbc/fbp faltando; double-counting = falta event_id. Fórmula ótima de matching: email + phone + fn + ln + fbp + fbc + IP + UA + 1 campo geográfico. Validar EMQ REAL no Events Manager, não o estimado.

**action_source:** `website` pra evento de página; pra conversa WhatsApp o correto é `chat` (`stape-io/facebook-tag`). `event_time` em segundos UNIX, máximo 7 dias no passado. `test_event_code` vai no ROOT do body, irmão de `data`.

**CAPI por estágio de funil** (`ivangfalco/ads-skills`): um evento por estágio que importa (Lead → qualified → customer). Upgrade natural ClienteB: segundo evento CAPI "qualified" quando o lead responde o welcome; migrar a otimização pra ele quando houver ~50 eventos/semana.

## 11. Batch requests, rate limits e retry

Fonte: `facebook/facebook-python-business-sdk` (verificado no código) + `pipeboard-co/meta-ads-mcp` (core/api.py)

**Batch:** `api.new_batch()` + qualquer método CRUD com `batch=` → até **50 sub-requests por POST** (a API rejeita acima; o SDK NÃO valida — fatiar no seu código). `batch.execute()` retorna um NOVO batch só com as falhas (ou None) → loop de retry idiomático: `while retry_batch is not None: sleep(5); retry_batch = retry_batch.execute()`. Cada sub-request conta individualmente pro rate limit — batch economiza latência, NÃO quota.

**Insights grandes SEM timeout:** `get_insights(params=..., is_async=True)` → AdReportRun → polling de `async_status` a cada 10s até 'Job Completed' → `async_job.get_insights(params={'limit': 500})`. Usar pra qualquer relatório 30–90d com breakdown.

**Retry (correção de mito: o SDK NÃO tem retry embutido):** `FacebookRequestError` expõe `.api_error_code()` e `.api_transient_error()`. Wrapper de produção: retryable = transient OU code em {4, 17, 32, 613, 80000, 80003, 80004}; backoff exponencial `(2^attempt) × 30s + jitter`. Erro 613/família 80000 (Business Use Case limit do ad account) → **pausar a fila daquele act_ por 5+ min**, não só re-tentar. Monitorar header `X-Business-Use-Case-Usage` e pausar preventivamente acima de ~90%.

**Matriz de decisão de token** (`pipeboard-co/meta-ads-mcp`): código 190/102 = auth → invalidar token e re-auth · **368 = bloqueio de política (o erro típico ClienteB) → PRESERVAR o token, o problema é a conta/Page** · 4 = rate limit → recuar · subcode 459 = checkpoint de segurança → o USUÁRIO limpa manualmente no Facebook, nenhum retry resolve. Sempre enviar `appsecret_proof` = HMAC-SHA256(token, app_secret) e REDIGIR token de qualquer log.

## 12. Guardrails de escrita (formaliza o STOP da Fase 3)

Fonte: `TheMattBerman/meta-ads-kit` + `FGRibreau/mcp-google-ads` (ADR-0001) + `AgriciDaniel/claude-ads`

- **PAUSED-only:** toda campanha/ad nasce `status: 'PAUSED'`. E COM VISIBILIDADE (lição do ADR do FGRibreau): o script termina imprimindo o status REAL + o comando exato pra ativar — nunca só "created ✓" (agentes acham que o ad está no ar e ele fica dias com zero impressão).
- **Dry-run-first:** toda mutação tem modo dry-run que mostra o payload exato sem chamar a API. Modo live exige env var (`META_KIT_MODE=live-approved`) + approval ID — o GO do gestor vira MECÂNICA, não disciplina.
- **Pós-GO (gap atual da skill trafego-inteligente):** idempotency key + ação de rollback + janela de verificação — verificar que o estado remoto casa com a pré-condição ANTES de aplicar e re-verificar depois (`claude-ads`, gate 5).
- **Pre-flight de adset** (`pipeboard-co/meta-ads-mcp`): targeting custom → `targeting_automation: {advantage_audience: 0}` EXPLÍCITO (senão a Meta expande a audiência cirúrgica); campanha CBO + adset com budget próprio → ABORTAR; campanha com BID_CAP e adset sem bid_amount → abortar antes do POST; `attribution_spec` NÃO é atualizável pós-criação (erro 1504040 — adset novo).
- **Anti-palavra-gatilho no DOF:** ao usar `asset_feed_spec`/Advantage+ Creative, OPT-OUT dos retoques: `degrees_of_freedom_spec: {creative_features_spec: {image_touchups: {enroll_status: 'OPT_OUT'}, text_optimizations: {enroll_status: 'OPT_OUT'}}}` — a Meta "melhorar" o texto pode inserir palavra-gatilho (crítico ClienteB). Gotcha: DOF com múltiplos image_hashes é aceito sem erro mas entrega SÓ UMA imagem.
- **Rejeição de política:** logar `ad_review_feedback` inteiro e PARAR — nunca re-submeter variações automaticamente (`FGRibreau`, análogo do exempt_policy_violations opt-in).
- **Scoring de relatório** (`claude-ads`): todo audit do Recon/Trace com veredito por item (pass/fail/unknown/not_applicable), health e coverage SEPARADOS, e coverage < 60% = só findings, sem nota. Unknown nunca conta contra health. Adotar IDs M01–M40 como checklist da Fase 0.

---
