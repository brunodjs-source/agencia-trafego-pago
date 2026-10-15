# Automações a propor pro gestor (com fonte e esforço)

**1. Daily-check "5 perguntas" no WhatsApp do gestor** (fonte: `TheMattBerman/meta-ads-kit` + esqueleto do workflow 1905 do `Zie619/n8n-workflows`)
Cron n8n diário 8h: puxa insights Meta (e GAQL Google) das contas ativas, responde as 5 perguntas no formato fixo, manda via Evolution API (`/message/sendText`). Alerta inline se pacing desviar >15% ou bleeder novo (CTR<1% ou freq>3,5). **Esforço: 1 dia** (o workflow 1905 já tem a puxada `facebookGraphApi` com fields prontos; trocar Telegram por Evolution).

**2. Relatório semanal multi-fonte com comparação YoY** (fonte: workflow 1905, `Zie619/n8n-workflows`, arquivo `workflows/Telegram/1905_...json`)
Schedule semanal → subworkflows-como-tools por fonte (Meta insights + GAQL de campanha + GA4) → Summarize → e-mail HTML + WhatsApp. Nota: os nodes LangChain vêm sanitizados como noOp, tem que reconstruir o Agent (os prompts originais estão preservados dentro do noOp). **Esforço: 1-2 dias.**

**3. Cadência seg/qua/sex como 3 crons** (fonte: `ivangfalco/ads-skills`)
Segunda: dados 14d rolling + Stage 2 + fatigue check, entrega DIFF de decisão pro gestor aprovar (nunca executa). Quarta: Stage 1 (delivery check dos ads dia-7). Sexta: checklist de scaling/rollback + pacing. Cada cron termina em mensagem com o diff no formato do Plan. **Esforço: meio dia por cron reaproveitando o item 1.**

**4. QA de tracking ClienteB: dedup + fbc + EMQ** (fonte: `stape-io/facebook-tag` + `TheMattBerman/meta-ads-kit` pixel-capi)
Três correções no pipeline atual: (a) event_id compartilhado browser/CAPI; (b) fbclid salvo na attribution do Typebot e fbc reconstruído no n8n; (c) ip+user_agent capturados no webhook e enviados raw no evento de entrega. Depois, script emq-check estilo meta-ads-kit validando no Events Manager (alvo Lead/Contact ≥8,0). Cada campo raw sobe EMQ, e EMQ alto = CPL menor. **Esforço: 0,5-1 dia, ROI direto no Pixel <ID_CONTA>.**

**5. Alarme de tracking quebrado (Zeus adaptado)** (fonte: `google-marketing-solutions/gps-sgtm-pantheon`, padrões Zeus/Chaos)
Espelhar cada Contact/CAPI enviado numa tabela do Postgres do VPS (em vez de BigQuery) e um cron n8n comparando taxa de sucesso 24h vs baseline; queda >30% dispara WhatsApp. Resolve o modo de falha "descobrir pixel caído 2 semanas depois pelo CPL". **Esforço: 1-2 dias.**

**6. `workspace/brand/` por cliente com learnings.md append-only** (fonte: `TheMattBerman/meta-ads-kit`)
Pasta por cliente (ClienteB/, removaltech/, ClienteA/, ClienteF/) com stack.md, creative-kit.md, audience.md, learnings.md. Trace/Buck/Cris leem antes de analisar e anexam padrões descobertos depois (ex: "depoimento aguenta freq 4+, estático morre em 3"). **Esforço: 2h de template + disciplina de pipeline.**

**7. `client-brief.md` de 12 seções versionado** (fonte: `coreyhaines31/marketingskills`, padrão `.agents/product-marketing.md`)
Documento canônico por cliente (Product, Audience, Personas, Pain Points, Competitive, Differentiation, Objections, Customer Language, Brand Voice, Proof Points, Goals, Changelog datado no topo). Todos os subagents recebem a instrução "leia primeiro". Elimina re-briefing e divergência entre agentes. **Esforço: 2h por cliente, auto-draft a partir das memórias existentes.**

**8. Wrapper de segurança com READ_ONLY/BLOCKED_OPS + audit log JSON** (fonte: `FGRibreau/mcp-google-ads`)
Módulo Python comum aos scripts do Volt: `AGENCIA_READ_ONLY_ACCOUNTS` (ClienteC/ClienteD/ClienteF/Tzur nos n8n, contas Google que não são alvo) + `AGENCIA_BLOCKED_OPS` + log JSON de toda mutação (timestamp, operação, diff, dry_run) em arquivo por conta. Transforma a "REGRA gestor" de memória em garantia mecânica. **Esforço: meio dia.**

**9. Espião de Ad Library com vision** (fonte: workflow 1603 do `Zie619/n8n-workflows`)
Screenshot da Meta Ad Library do concorrente (página pública renderizada via JS) → agent vision com schema {hook_3s, promessa, oferta, CTA, formato, ângulo} → planilha. Braço de inteligência pro Cris/Scout. Reconstruir os 3 nodes LangChain sanitizados. **Esforço: 1-2 dias.**

**10. Registro padrão de lift test** (fonte: `pymc-labs/pymc-marketing`, lift_test.py)
Todo teste de escala (dobrar budget numa cidade 2 semanas) registra 4 colunas: x (gasto base), delta_x, delta_y (lift observado em leads), sigma. Vira calibração de ROAS real vs ROAS do pixel, e insumo futuro de MMM. **Esforço: 1h (template CSV + regra no Buck).**

**11. SRM check pra testes de LP com tráfego pago** (fonte: `growthbook/growthbook`, gbstats/utils.py)
Função chi-quadrado de 12 linhas (scipy) no toolkit do Lex: split observado vs esperado com p<0,001 = teste INVÁLIDO, não interpretar nada. Todo report de teste abre com a linha "Health: SRM p=X OK". **Esforço: 2h.**

**12. LTV por coorte da base ClienteB** (fonte: `pymc-labs/pymc-marketing`, clv/utils.py)
Pipeline RFM → BG/NBD → Gamma-Gamma sobre o histórico de assinantes (R$ 35,90/59,90 recorrente): LTV previsto por coorte de aquisição alimenta CPA-alvo real por campanha e o value do CAPI pra value-based bidding, em vez de otimizar pra R$ 35,90 flat. Depende do gestor exportar o log de transações do cliente. **Esforço: 1 dia após ter os dados.**

**Ordem sugerida de implementação** (impacto ÷ esforço): 4 → 8 → 1 → 6 → 7 → 3 → 11 → 5 → 2 → 10 → 9 → 12. Os itens 4 e 8 protegem dinheiro e conta já na primeira semana; 1/6/7 mudam a rotina diária; o resto é acumulação de inteligência.
