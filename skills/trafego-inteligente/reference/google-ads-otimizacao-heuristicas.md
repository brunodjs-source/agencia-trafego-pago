# Heurísticas de Otimização Google Ads — Equipe TI

Formato SE/ENTÃO copiável. Cada regra cita o repo-fonte. Princípio geral (google-ads-audit-checklist, coreyhaines31/marketingskills): "Do not grade what you couldn't see, don't invent negative keywords, and draft every change before touching a live account." E do AgriciDaniel/claude-ads: preferir a menor mudança reversível (pausar > deletar; mover 20% de budget > dobrar). **Nunca deletar campanha** (regra gestor, e o FGRibreau/mcp-google-ads nem implementa delete: só pause/archive/export).

## H1 — Negativação e mineração de search terms (ritual semanal)

- **SE** termo com `clicks >= 5` E `custo >= R$ 50` E `conversions == 0` **ENTÃO** candidato a negativa (thresholds do 02_search_terms_analyzer.py, itallstartedwithaidea, adaptados de $10/5 clicks pra BR; calibrar pelo CPC da conta: régua alternativa = custo >= 1x CPA-alvo).
- **SE** termo irrelevante com `clicks >= 3` e 0 conversões **ENTÃO** negativar mesmo abaixo do piso de custo (mesmo repo).
- **SE** termo contém intenção-lixo ("grátis", "gratuito", "curso", "apostila", "vaga", "emprego", "salário", "baixar", "pdf", "modelo", "o que é", "como fazer", "significado", "reclame aqui" quando não for brand defense) **ENTÃO** negativa imediata sem esperar threshold de custo (lista starter do google-search-playbook, coreyhaines31, traduzida: universal junk + research intent).
- **SE** termo converteu E `search_term_view.status = 'NONE'` **ENTÃO** adicionar como keyword Exact/Phrase no ad group temático certo (passada Winners).
- **SE** broad/phrase puxando significado adjacente errado (Drift) **ENTÃO** apertar match type ou negativar o desvio.
- **NUNCA** gerar negativas sem o search terms report em mãos, e SEMPRE revisar overblocking contra as negativas existentes (Q18) antes de aplicar (hard stops de coreyhaines31 e AgriciDaniel).

## H2 — N-gram (o multiplicador da negativação)

- **SE** um n-gram (1-3 palavras) acumula `custo >= 3x CPA-alvo` com 0 conversões ATRAVÉS de múltiplos search terms diferentes **ENTÃO** negativar o n-gram em phrase match (pega a família inteira de termos ruins de uma vez).
- **SE** um n-gram concentra conversões com CPA abaixo do alvo **ENTÃO** verificar se existe ad group/keyword dedicado a ele; se não, criar (é um tema comprador sem casa própria).
- Janela mínima: 30 dias; ideal 90. Nunca decidir n-gram com < 100 cliques agregados no n-gram.

## H3 — Pausa de keyword e de ad

- **SE** keyword gastou `>= 5-10x CPA-alvo` com 0 conversões **ENTÃO** pausar (kill imediato do optimization-playbook, ivangfalco/ads-skills).
- **SE** keyword com QS <= 4 e gasto relevante **ENTÃO** antes de pausar, diagnosticar pelo componente (Q6): creative_quality BELOW_AVERAGE → reescrever RSA; post_click BELOW_AVERAGE → landing (Lex); só pausar se o problema for a keyword em si (genérica demais).
- **SE** ad em rotação com CTR estruturalmente inferior E amostra >= 100 cliques por variante **ENTÃO** pausar o perdedor e criar variante nova (nunca ficar com 1 RSA só: padrão da casa é 2 por ad group, +6,6% conv segundo dado Google).
- **ANTES de aplicar qualquer regra fixa de kill por CPA**: mostrar o que a regra teria destruído considerando lag de conversão e tamanho de amostra (hard stop do audit-guardrails, coreyhaines31). Conta jurídica B2B tem lag de dias entre clique e WhatsApp: kill em janela de 7 dias mente.

## H4 — Realocação de budget

- **SE** campanha com CPA <= alvo E `search_budget_lost_impression_share > 20%` **ENTÃO** é a escala mais barata da conta: aumentar budget +20% e reavaliar em 5-7 dias (ritmo de escala do ivangfalco/ads-skills: +20% a cada 5 dias, NUNCA > 30% de uma vez, que reseta learning).
- **SE** CPA > 1,5x alvo por 2 semanas após aumento **ENTÃO** rollback: cortar 20-30% imediato, estabilizar 2 semanas, retomar +10%/semana (protocolo de rollback do mesmo repo).
- **SE** 20% das campanhas geram o pipeline real **ENTÃO** pausar os 80% INTEIROS (não reduzir budget deles) e reinvestir nos winners (regra 80/20 do ivangfalco/ads-skills).
- **SE** for mover verba entre campanhas com curva de saturação conhecida **ENTÃO** o ponto de corte é onde o retorno marginal cai abaixo de `1/CPA_alvo`; com curva Michaelis-Menten ajustada por campanha (`f(x) = alpha*x/(lam+x)`, derivada `alpha*lam/(lam+x)^2`), maximizar a soma sujeita ao budget total com scipy SLSQP são ~15 linhas de Python (pymc-labs/pymc-marketing, budget_optimizer). Ajustar as curvas via `scipy.optimize.curve_fit` em (spend semanal, conversões semanais) do histórico já serve pro Buck sem MCMC.
- Guardrail de aprovação (CEP do itallstartedwithaidea + regra GO do gestor): **QUALQUER aumento de budget > 20% exige aprovação explícita**; aumento > 50% exige dupla confirmação (FGRibreau: double-confirm em budget > 50% e em qualquer operação com "remove/delete" no nome).

## H5 — Escada de intenção (onde o próximo real entra)

Do google-search-playbook (coreyhaines31), gasto desbloqueia sequencialmente, degrau novo SÓ depois do degrau de baixo provar conversão em pipeline:
1. Brand → 2. Non-brand high-intent → 3. Competitor → 4. Problem-aware → 5. Demand-gen (Display/YouTube, só sobra).
- **SE** a conta mistura brand e non-brand na mesma campanha **ENTÃO** separar JÁ: misturar infla o ROAS blended e esconde a fraqueza do non-brand (FAIL de auditoria no google-ads-audit-checklist).
- **SE** termos genéricos estão vazando pra campanha brand **ENTÃO** migrar (brand ROAS artificialmente inflado, mesmo checklist).
- Regra-núcleo verbatim: "Don't skip rungs. Broad spend before high-intent proof is how B2B accounts burn budgets."

## H6 — Match types (progressão)

- Começar high-intent em **Phrase + Exact** com Manual CPC ou Max Conversions.
- **SE** quiser ligar Broad **ENTÃO** só com as 3 condições juntas: 30+ conv/mês E smart bidding ativo E lista de negativas apertada (google-search-playbook).
- **PROIBIDO** (combo killer do FGRibreau/mcp-google-ads, bloqueado em código no repo): **Broad match + Manual CPC**. "This combination burns budget." Alternativas: Smart Bidding com Broad, ou Exact/Phrase com Manual.
- Não assumir que o Broad de hoje se comporta como o legado: verificar via change history + search terms (AgriciDaniel, gaql-notes).

## H7 — Consolidação (alimentar o smart bidding)

- **SE** campanha com < 15-30 conv/mês **ENTÃO** ela não alimenta smart bidding: fundir com irmã temática antes de qualquer troca de estratégia (google-search-playbook).
- **SE** Shopping/PMax sobre-segmentado com buckets < 30-50 conv/mês **ENTÃO** consolidar antes de reestruturar (buckets de fome não aprendem, google-ads-audit-checklist).

## H8 — Migração de bid strategy (régua por volume, refina a régua atual do Buck)

Do google-search-playbook (coreyhaines31), compatível com a escada Manual→MaxConv→tCPA da casa:
- **0-15 conv/mês** → Manual CPC ou Max Conversions SEM target.
- **15-30 conv/mês** → Max Conversions.
- **30+ conv/mês estável** → tCPA, setado NO CPA real trailing 30d ou um pouco acima (nunca no CPA desejado-sonho: estrangula a entrega).
- **Receita real voltando (OCT/Enhanced)** → tROAS.
- **Ajuste de target**: passos de ±10-15%, esperando 1-2 semanas entre ajustes (cada mudança reinicia learning).
- **SE** campanha brand em tROAS/tCPA solto **ENTÃO** mover pra manual/portfolio com target apertado (tROAS básico paga caro por volume que ganharia de qualquer jeito, google-ads-audit-checklist).
- **SE** um target único cobre economics diferentes (brand + non-brand, hero + long-tail) **ENTÃO** FAIL: separar portfolios.
- Guardrail: aumento de bid > 30% exige aprovação explícita (CEP, itallstartedwithaidea); > 100% de aumento é bloqueado por default (guard do FGRibreau).

## H9 — Auditoria PMax (checklist de veredito)

Formato de veredito por check: pass / fail / unknown / not_applicable, e NUNCA nota única com cobertura < 60% de evidência (sistema de scoring do AgriciDaniel/claude-ads: health e coverage são números SEPARADOS, unknown não conta contra health).
- **SE** PMax rodando sem Search maximizado antes **ENTÃO** questionar a existência dela (ordem do playbook).
- **SE** asset group sem audience signals E sem search themes **ENTÃO** FAIL (alavanca desperdiçada).
- **SE** brand exclusions não configuradas a nível de conta **ENTÃO** FAIL (PMax canibaliza brand e o ROAS mente).
- **SE** asset com `performance_label = LOW` há 30+ dias **ENTÃO** trocar o asset.
- **SE** conversão offline (lead real do CRM) não importada **ENTÃO** não escalar PMax: ela vai otimizar pra lead lixo.
- **SE** PMax converte à metade da taxa do Search em pipeline real **ENTÃO** "cheap-looking and expensive-in-reality": cortar e devolver pro Search (coreyhaines31).
- Não-adoção de feature nova NUNCA conta contra a conta (regra dos dois catálogos de auditoria, AgriciDaniel: "non-adoption is never a failure").

## H10 — Dayparting

- **SE** matriz 7×24 (Q14) mostra célula com CPA > 2x alvo E amostra >= 30 cliques na célula **ENTÃO** bid adjustment negativo ou exclusão do horário.
- **SE** conta B2B sem dados **ENTÃO** partir do schedule padrão da casa (Seg-Sex 8-19h, Sáb 9-13h) e deixar a Q14 confirmar em 30-60 dias.
- **NUNCA** dayparting agressivo em cima de tCPA maduro sem checar: smart bidding já modula por hora; dayparting duplo pode sufocar (mudança = draft com efeito esperado + rollback, padrão AgriciDaniel).

## H11 — Tracking primeiro, otimização depois (ordem de diagnóstico)

Ordem fixa quando a campanha vai mal, parar no primeiro culpado (performance-diagnosis, realkimbarrett/advertising-skills): **1. Oferta → 2. Audiência/keyword → 3. Criativo → 4. Funil/LP → 5. Vendas**. Anti-pattern nomeado: trocar criativo (passo 3) sem checar oferta (passo 1).
- **SE** conversões duplicadas (GA4 import + tag nativa no mesmo evento) **ENTÃO** FAIL imediato, consertar antes de qualquer decisão de bid (google-ads-audit-checklist).
- **SE** upload offline com rejeição (Q17) **ENTÃO** resolver antes de julgar CPA: o CPA visível está mentindo.
- **NUNCA** somar conversões entre plataformas com janelas de atribuição diferentes: reportar lado a lado com a janela anotada (hard stop, coreyhaines31).

## H12 — Testes de LP com tráfego pago (importado do GrowthBook)

- **SE** teste A/B de LP rodando **ENTÃO** antes de ler qualquer métrica: checar SRM (chi-quadrado no split de tráfego; p < 0.001 = teste QUEBRADO, invalida tudo) e exposições múltiplas > 1% (growthbook/growthbook, função `check_srm` copiável em 10 linhas de scipy).
- **SE** decidir quando parar **ENTÃO** critério bayesiano: Chance to Beat Control >= 95% = winner; expected loss <= 0.25% = seguro shippar mesmo sem 95%; loss >= 1.25% = não shippa. Mínimo 150 conversões por variante antes de pintar qualquer cor (constants.ts do GrowthBook). Com mídia paga queimando por dia, shippar com 90% de chance e risk 0.1% ganha de esperar significância clássica.

## H13 — Contrato de execução do Volt (gates mecânicos, não disciplina)

Formalização dos gates do AgriciDaniel/claude-ads + FGRibreau pra Fase 4:
1. Toda mutação nasce como **draft** com diff humano: estado atual → mudança → efeito esperado → blast radius → plano de rollback (é o STOP da Fase 3 virando contrato de função).
2. Execução exige flag explícito de GO (padrão `dry_run=false` explícito + env var de modo, tipo `META_KIT_MODE=live-approved` do TheMattBerman/meta-ads-kit, portado: `TI_MODE=live-approved` + approval ID).
3. Toda criação nasce **PAUSED** e a resposta do script SEMPRE imprime o status real + o comando exato pra ativar (lição do ADR-0001 do FGRibreau: "created ✓" sem status fez ads ficarem dias parados invisíveis; "segurança silenciosa é bug").
4. Pós-aplicação: verificar que o estado remoto casa com o esperado + registrar rollback + janela de verificação 3-7 dias.
5. `partial_failure` da API é ERRO, nunca sucesso silencioso com metade aplicada (FGRibreau).
6. Recommendations do Google: apply/dismiss SÓ via RPC dedicado `recommendations:apply` / `recommendations:dismiss`, nunca via mutate genérico (retorna 400 opaco; bug documentado no ADR do FGRibreau). E recomendação que aumenta gasto NUNCA se aplica sem aprovação (CEP).

## Apêndice — Lições de API que somam às 11 já registradas no CLAUDE.md

12. Recommendation apply/dismiss: sempre RPC dedicado, nunca mutate (FGRibreau ADR-0001).
13. Validar operation keys contra whitelist local antes do POST: a API devolve 400 genérico que não explica (FGRibreau).
14. `keyword_view` segmentado por data DUPLICA linhas por match type/dia: dedup obrigatório por (ad_group_id + keyword_text + match_type) (AgriciDaniel).
15. RSA PODE ser editado in-place via update preservando histórico de performance em v23+ (FGRibreau implementa `update_responsive_search_ad`); revisar a lição 10 antiga ("edição impossível") quando migrar de versão.
16. `change_event` exige LIMIT <= 10000 e janela <= 30 dias (googleads/google-ads-mcp).
17. `PARAMETERS omit_unselected_resource_names=true` em toda query economiza payload/token (googleads/google-ads-mcp).
