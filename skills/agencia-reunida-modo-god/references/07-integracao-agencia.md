# 07 · Integração com os agentes da Agência

Ferramentas e modelo de cada agente conferidos no frontmatter dos arquivos em `<pasta local>` em 06/10/2026 (cópia espelhada em `<pasta local>`). Agente sem linha `tools:` herda todas as ferramentas da sessão.

## 1. Mapa

| Agente | `agentType` | Ferramentas declaradas | Modelo declarado | Onde entra no Modo GOD |
|---|---|---|---|---|
| Recon | `recon-campaign-scout` | Read, Glob, Grep, Bash, WebSearch, WebFetch | (herda) | R1: inventário da conta de anúncios (campanhas ativas e pausadas, histórico de mudanças). Sem Write: grava por Bash ou devolve o texto para o orquestrador gravar |
| Trace | `trace-traffic-analyst` | (herda) | sonnet | R1: auditoria e post-mortem; R7: lente de números; liquidação do diário |
| Probe | `probe-meta-interests-validator` | Read, Write, Bash, Glob, Grep | (herda) | R1: demanda (interesses Meta via `adinterestvalid`; no trilho Google, palavras via Planejador por script). Sem WebSearch e WebFetch |
| Scout | `scout-niche-researcher` | WebSearch, WebFetch, Read, Write, Bash, Grep | (herda) | R1: concorrentes, canais, portais, benchmarks, repositórios; R7: lente de fatos externos; C03 |
| Plan | `plan-strategy-presenter` | Read, Write, AskUserQuestion, Glob, Grep | (herda) | Depois da R9, na conversa principal: apresenta o brief e colhe GO, NO-GO ou nova rodada. Dentro do Workflow não há humano para responder |
| Buck | `buck-budget-optimizer` | (herda) | sonnet | R1: economia unitária e verba; C02 Pessimista de risco; J1; Red Team |
| Lex | `lex-cro-landing-page` | (herda) | sonnet | R1: funil e LP; C10; C05 na parte de página; execução de LP depois do GO |
| Volt | `volt-automation-engineer` | (herda) | sonnet | R1: automação, mensuração, CAPI, conversões offline; C11; C05 técnico; C06; execução de API depois do GO |
| Vega | `vega-art-director` | (herda) | sonnet | C01, C07, C13; J5; conceito criativo fica com a skill `creative-director` |
| Ada | `ada-ad-creator` | (herda) | sonnet | Pre-flight de copy (R8); passada final no brief para tirar cadência de IA |
| CRAFT | `craft-anti-ai-master` | (herda) | opus | Pre-flight visual obrigatório; veto abaixo de 50/50 |
| Atlas | `atlas-account-director` | (herda) | sonnet | C04 Voz do comprador; C12; J2 |
| Rex | `rex-report-writer` | (herda) | sonnet | Relator do Decision Brief (R9); relatório de calibração |
| Cris | `cris-creative-reviewer` | (herda) | sonnet | Juiz de criativo performático quando a decisão envolve peças; C14 |
| Ranger | `ranger-commercial-prospector` | (herda) | sonnet | C01 e C12 quando a decisão é comercial; só com pedido de "próximo nível" do gestor para extração de CNPJ |

Outros agentes da pasta (Iris, Cleo, Spielberg, Scribe, Argos, Aegis, Strix e os demais) entram como frente de pesquisa ou cadeira de especialista quando a decisão é do domínio deles.

## 2. Por que usar o `agentType` dos agentes

1. **Conhecimento de casa.** Cada agente carrega o contexto e as regras da Agência para a sua função (compliance OAB e Debt Services, regras da ClienteB, padrões de conta).
2. **Diversidade de modelo de graça.** Dez agentes declaram `model: sonnet` e o CRAFT declara `model: opus`. Uma cadeira com `agentType` de agente Sonnet pensa com outro modelo que a sessão principal. A literatura indica que modelos diferentes melhoram todos os métodos de debate testados (arXiv 2502.08788). Isso não foi medido nesta Agência.
3. **Cuidado com o custo de contexto.** Agente com prompt de sistema longo gasta mais por chamada. Nas cadeiras do GOD (14 em paralelo), vale testar antes num Padrão.

Exemplo de cadeira com `agentType` no `args`:

```json
{ "id": "C02", "nome": "Pessimista de risco", "persona": "guardião de caixa no papel de CFO", "otimiza": "preservar caixa e cortar desperdício", "ponto_cego": "o custo de não agir", "pergunta": "quanto custa não fazer nada?", "agentType": "buck-budget-optimizer" }
```

## 3. Como foi na missão ClienteG

Frentes de pesquisa e `agentType` usados no script original (`agencia-reunida-ClienteG-wf_2e6d26bb-b0f.js`):

| Frente | Tema | `agentType` |
|---|---|---|
| R1 | Concorrentes que atendem contabilidade | `scout-niche-researcher` |
| R2 | Concorrentes que atendem advocacia | `scout-niche-researcher` |
| R3 | Demanda e palavras-chave (trilho Google, Probe+Keyword) | `probe-meta-interests-validator` |
| R4 | Auditoria somente leitura do Google Ads | `trace-traffic-analyst` |
| R5 | Canais do YouTube para posicionamento | `scout-niche-researcher` |
| R6 | Portais e elegibilidade para Display | `scout-niche-researcher` |
| R7 | Funil e LP de prevenção | `lex-cro-landing-page` |
| R8 | Chatbot e automação | `volt-automation-engineer` |
| R9 | Orçamento e economia unitária | `buck-budget-optimizer` |
| R10 | Repositórios de conselho e debate no GitHub | (padrão) |

As 14 cadeiras e os 5 juízes rodaram com o agente padrão do Workflow e persona no prompt.

## 4. Encaixe na skill `trafego-inteligente`

A `trafego-inteligente` tem 5 fases com parada obrigatória na Fase 3. O Modo GOD entra como motor da decisão quando há gatilho de escalada:

| Fase TI | O que faz | Relação com o Modo GOD |
|---|---|---|
| 0 Recon | Inventário da conta | Vira frente da R1 |
| 1 Trace | Post-mortem | Vira frente da R1 |
| 2 Probe + Scout | Interesses ou palavras validados ao vivo, cases e benchmarks | Viram frentes da R1 |
| 3 Plan | Plano e parada para GO | **O Modo GOD roda aqui** (R0 a R9) e o Plan apresenta o brief |
| 4 Volt | Execução via API, tudo nascendo pausado | Só depois do GO; nunca dentro do conselho |
| 5 Trace + Buck | Monitoramento 48 h e decisão de escala | Alimenta a liquidação do diário (D+30, D+60, D+90) |

Regra de ouro das duas skills: nada vai para a Meta API ou para a Google Ads API sem GO do gestor. Interesses Meta e palavras Google são revalidados ao vivo na execução, mesmo que o conselho já tenha olhado.

## 5. Regras visuais e de texto da Agência que o conselho herda

1. Peça visual: o agente que cria (Iris, Vega, Lex, Volt) entrega; o CRAFT audita contra 50 pontos; só passa com 50/50. Criativo performático passa também pela Cris.
2. Texto: zero travessão, sem cadência de IA, português falado do Brasil. Ada reescreve quando preciso.
3. Imagem ou vídeo de IA: auditoria de 5 pontos da memória da Agência (coerência, texto, alucinação, pessoas brasileiras, compliance do nicho) antes de mostrar ao gestor, e o criativo é sempre exibido.
4. Regras de cliente (exemplos registrados na memória): ClienteB com palavras proibidas e preço exato; advocacia com Provimento 205/2021 da OAB; Google Ads com a política de Serviços de Débito. O Guardião de Regras (C05) e a lente 3 de verificação carregam essas regras via `args.regras_extra`.
