# templates/agency_blueprint — Blueprint de Agência

Template canônico de uma agência criada pela skill. Inclui 14 cargos base + extras condicionais.

## 14 Cargos Base (sempre presentes)

| # | Slug | Cargo | Especialidade | Tier |
|---|---|---|---|---|
| 1 | `art-director` | Diretor(a) de Arte | Big Idea, brief criativo, Cannes-level | Estratégico |
| 2 | `traffic-analyst` | Analista de Tráfego | Performance Google Ads + Meta Ads | Tático |
| 3 | `budget-optimizer` | Otimizador(a) Orçamento | Escalar, pausar, redistribuir, bid strategy | Tático |
| 4 | `creative-reviewer` | Crítico(a) de Criativo | Saturação, fadiga, QA | QA |
| 5 | `report-writer` | Redator(a) Relatórios | Relatórios semanais/mensais cliente | Operacional |
| 6 | `copywriter` | Redator(a) de Copy | Headlines + corpo + CTA, A/B/C | Tático |
| 7 | `script-director` | Diretor(a) de Roteiro | Roteiros Reels/TikTok/Shorts/VSL | Tático |
| 8 | `automation-engineer` | Engenheiro(a) Automação | n8n, CAPI, GCLID, Evolution, GTM | Infra |
| 9 | `cro-specialist` | Especialista CRO | Landing page, hero, A/B test, mobile | Tático |
| 10 | `visual-ai-creator` | Gerador(a) Imagem/Vídeo IA | Veo/Flux/Hailuo/Higgsfield/Pollinations | Tático |
| 11 | `account-director` | Account Director | Cliente, brief, QBR, churn prevention | Estratégico |
| 12 | `commercial-prospector` | Caçador(a) Comercial | Prospecção, cold outreach, pipeline | Estratégico |
| 13 | `transcriber-analyst` | Transcritor(a) & Audiovisual | Whisper, Vision, auto-clone concorrência | Apoio |
| 14 | `video-editor` | Editor(a) de Vídeo | Pós-produção (Remotion/MoviePy/DaVinci) | Tático |

## Agentes EXTRAS por Nicho (regra 2b)

### E-commerce Marketplace (Mercado Livre / Shopee / Amazon)

| Slug | Cargo | Foco |
|---|---|---|
| `marketplace-algo-whisperer` | Algoritmo Whisperer | BuyBox, ranking, Mercado Pago, Envios |
| `reviews-manager` | Reviews Manager | Resposta a reviews, recuperação reputação |
| `marketplace-seo` | SEO Marketplace | Title optimization, ficha técnica, fotos |

### SaaS B2B

| Slug | Cargo | Foco |
|---|---|---|
| `demo-specialist` | Demo Specialist | Demos qualificadas, sales engineering |
| `abm-strategist` | ABM Strategist | Account-Based Marketing, named accounts |
| `outbound-sdr` | Outbound SDR Caller | Cold call, voice notes WhatsApp |
| `product-marketing` | Product Marketing | Positioning April Dunford, naming, launch |

### Saúde / Clínicas

| Slug | Cargo | Foco |
|---|---|---|
| `cfm-compliance-officer` | Compliance CFM/ANS | Resolução CFM 1.974/2011 ad de saúde |
| `patient-journey-architect` | Patient Journey | Funil paciente, NPS, fidelização |
| `local-seo-saude` | SEO Local Saúde | Google Business, mapas, especialidades |

### Advocacia / Jurídico

| Slug | Cargo | Foco |
|---|---|---|
| `oab-compliance-officer` | Compliance OAB | Provimento 205/2021, termos vetados |
| `jurisprudencia-analyst` | Jurisprudência Analyst | STF/STJ, súmulas, casos paradigmáticos |

### Food / Restaurante / Delivery

| Slug | Cargo | Foco |
|---|---|---|
| `delivery-apps-optimizer` | Delivery Apps Optimizer | iFood, Rappi, 99Food, ranking apps |
| `food-photography-director` | Food Photography | Direção visual de prato, mock-ups, IA |
| `local-seo-food` | SEO Local Food | Google Business, Maps, raio entrega |

### Fitness / Wellness

| Slug | Cargo | Foco |
|---|---|---|
| `trainer-persona-architect` | Trainer Persona | Construção de figura do treinador IA |
| `transformation-manager` | Transformation Manager | Antes/depois ético, compliance ANVISA |
| `community-coach` | Coach Comunidade | WhatsApp groups, Discord engajamento |

### Educação / Cursos

| Slug | Cargo | Foco |
|---|---|---|
| `course-architect` | Course Architect | Estrutura currículo, gamificação |
| `student-success` | Student Success | Onboarding, retenção, NPS, certificação |
| `webinar-director` | Webinar Director | Captação webinar, conversão, replay |

### Imóveis

| Slug | Cargo | Foco |
|---|---|---|
| `crm-imobiliario` | CRM Imobiliário | Sienge, Apto, follow-up cadência |
| `tour-virtual-director` | Tour Virtual Director | Drone, 360°, IA staging |
| `imovel-match` | Match Inteligente | Filtros, algoritmo perfil-imóvel |

### Fintech / Crédito

| Slug | Cargo | Foco |
|---|---|---|
| `bacen-compliance` | Compliance Bacen | Resolução 4.658, LGPD financeira |
| `score-credit-analyst` | Score Analyst | Análise de crédito, SCR, Serasa |
| `funnel-fintech` | Funil Fintech | KYC, esteira aprovação, otimização |

### Moda / Beauty

| Slug | Cargo | Foco |
|---|---|---|
| `influencer-curator` | Influencer Curator | Discovery, contracts, performance |
| `seasonal-calendar` | Seasonal Calendar | Coleções, drops, sazonalidade fashion |
| `try-on-architect` | Try-On IA Architect | Virtual try-on, body type adaptation |

## Como decidir AGENTS EXTRAS

```pseudo
function decideExtras(nicho_briefing):
  base = 14
  extras = []

  # Mapear nicho → set de extras
  if "marketplace" in nicho.tags: extras += marketplace_extras
  if "saas" in nicho.tags or "b2b" in nicho.tags: extras += saas_extras
  if "saude" in nicho.tags or "clinica" in nicho.tags: extras += saude_extras
  ...

  # Limite máximo razoável
  extras = extras[:6]  # max 6 extras = total 20 agentes

  return base_agents + extras
```

## Mínimo viável: 14 / Máximo: 20

A skill nunca cria menos de 14 (poderia faltar função crítica) nem mais de 20 (vira ingovernável). Sweet spot: 14-18 dependendo do nicho.
