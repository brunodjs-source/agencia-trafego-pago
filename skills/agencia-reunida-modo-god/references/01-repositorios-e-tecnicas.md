# 01 · Os 10 repositórios estudados e as técnicas absorvidas

Fonte única deste arquivo: `<PASTA_DO_PROJETO>\ClienteG\plano-crescimento\pesquisa\R10-github-skills-conselho.md` (pesquisa Scout de 06/10/2026, somente leitura via API do GitHub e arquivos brutos; nada clonado, nada instalado). Estrelas e datas foram medidas uma vez, em 06/10/2026 por volta de 21h45, e mudam rápido. Estrela mede popularidade, não qualidade de decisão.

Critérios do ranking da R10 (1 a 5 cada, julgamento qualitativo do pesquisador, não métrica): Rel = serve para decisão estratégica; Evid = tem paper ou medição; Mat = adoção e manutenção; Port = dá para levar para skill do Claude Code sem infraestrutura nova; Lic = licença permite reaproveitar.

| # | Repositório | Estrelas | Licença | Técnica central | Total R10 |
|---|---|---|---|---|---|
| 1 | harshilmathur/autodecision | 102 | MIT | Loop de decisão com 5 personas isoladas, crítica anônima, adversário, sensibilidade e juiz de convergência mecânico | 20 |
| 2 | karpathy/llm-council | 25.229 | sem licença | Respostas independentes, ranking anônimo cruzado, presidente sintetiza | 15 |
| 3 | aiwithremy/claude-skills-llm-council (skill de Ole Lehmann) | 2.388 | sem licença | 5 lentes de pensamento em subagentes, revisão anônima de 3 perguntas, veredito em 5 blocos | 15 |
| 4 | coreyhaines31/marketingskills, skill `marketing-council` | 53.486 | MIT | Banco de 12 conselheiros de marketing com dossiê, dissidente obrigatório, mapa de desacordos | 21 |
| 5 | TauricResearch/TradingAgents | 109.992 | Apache-2.0 | Analistas, debate touro x urso, gerente, debate de risco em 3 perfis, memória que confere o resultado real | 19 |
| 6 | Skytliang/Multi-Agents-Debate (MAD) | 614 | GPL-3.0 | Afirmativo x negativo e moderador com parada adaptativa | 14 |
| 7 | composable-models/llm_multiagent_debate (Du et al.) | 552 | sem licença | N agentes, R rodadas, cada um revisa lendo os outros; voto da maioria | 14 |
| 8 | dinobby/ReConcile | 207 | MIT | Mesa redonda de modelos diferentes com voto ponderado por confiança reescalada | 16 |
| 9 | princeton-nlp/tree-of-thought-llm | 6.076 | MIT | Gerar ramos, avaliar, manter os melhores | 20 |
| 10 | bmad-code-org/BMAD-METHOD (party mode + advanced elicitation) | 53.863 | MIT com marca registrada | Mesa redonda em 4 modos e catálogo de 71 métodos de elicitação | 17 |

A R10 ordena de 1 a 10 pela utilidade direta para esta skill, não pelo total: o `marketing-council` tem o maior total, mas roda numa mente só; o `autodecision` é o esqueleto de processo mais completo.

---

## 1. harshilmathur/autodecision

- **URL:** https://github.com/harshilmathur/autodecision (release v0.4.0 de 16/04/2026, segundo a R10).
- **O que faz:** plugin do Claude Code que leva uma decisão de negócio por SCOPE, GROUND, ELICIT, HYPOTHESIZE, SIMULATE (5 personas), CRITIQUE, ADVERSARY, SENSITIVITY e CONVERGE, repetindo 2 vezes por padrão, e fecha com um Decision Brief de 16 seções. O README estima cerca de 15 minutos no modo completo.
- **Técnicas absorvidas e onde entram:**
  - Persona como subagente separado, em paralelo; a conversa principal orquestra porque subagente não cria subagente. **Regra 3 do SKILL.md e R2.**
  - Cada persona com OTIMIZA PARA, PONTO CEGO e PERGUNTA CONTRÁRIA. **Formato de toda cadeira.**
  - Efeito com faixa [mín, máx]; "a discordância entre personas é o sinal de incerteza; nunca tire a média". **Probabilidade em faixa, seção 10 do SKILL.md.**
  - Juiz de convergência mecânico, sem opinião. **Cálculo no código (funções `estatistica`, `estavel`, `rotular`).**
  - Dispersão média abaixo de 0,10 = diversidade baixa; convergência perfeita = alerta. **Carimbos DIVERSIDADE BAIXA e CONVERGÊNCIA PERFEITA.**
  - Regra 15: sem os arquivos intermediários, o brief é fabricado. **Arquivo por rodada e auditor de rastros.**
  - Seção "Minority-View Winners" e diário de decisões (`journal.jsonl`). **Minoria vencedora e `diario-decisoes.jsonl`.**
- **Fraquezas que a skill corrige:** probabilidades autodeclaradas sem calibração (aqui: reescala e diário com Brier); um modelo só (aqui: `agentType` de agentes com outro modelo); crítica com 1 revisor só (aqui: 3 a 5 juízes e 3 lentes de verificação).

## 2. karpathy/llm-council

- **URL:** https://github.com/karpathy/llm-council (5 commits; o autor escreveu que foi "99% vibe coded" e que não vai manter).
- **O que faz:** manda a mesma pergunta para 4 modelos via OpenRouter, cada um ranqueia as respostas anonimizadas ("Response A, B, C", formato `FINAL RANKING:`), e um presidente sintetiza.
- **Técnicas absorvidas:** sequência independente, avaliação anônima, síntese; ranking agregado por posição média (`calculate_aggregate_rankings`); diversidade de modelos. **R2, R3 e R6.**
- **Correções aplicadas:** no original cada modelo ranqueia também a própria resposta e o presidente vê os nomes dos modelos. Aqui juiz nunca é cadeira, a revisão cruzada exclui o próprio plano e o Presidente só vê letras.
- **Licença:** sem licença declarada. Só a ideia foi usada; nenhum código.

## 3. aiwithremy/claude-skills-llm-council (skill de Ole Lehmann)

- **URL:** https://github.com/aiwithremy/claude-skills-llm-council (o README credita a skill a Ole Lehmann e a metodologia ao Karpathy).
- **O que faz:** 5 lentes em subagentes paralelos (Contrário, Primeiros Princípios, Expansionista, Forasteiro, Executor), formando 3 tensões; lê CLAUDE.md e memória antes de enquadrar; revisão anônima com letras sorteadas.
- **Técnicas absorvidas:**
  - As 3 perguntas da revisão: qual a mais forte; qual o maior ponto cego; **o que todas deixaram passar**. **Revisão cruzada opcional da R3 e prompt do Crítico de Lacunas.**
  - Veredito em blocos (concorda, diverge, pontos cegos, recomendação, "a única coisa a fazer primeiro"). **Estrutura do Presidente e campo `primeira_acao`.**
  - O presidente pode ficar com o dissidente. **Minoria vencedora.**
  - Lentes Forasteiro, Primeiros Princípios e Executor. **Cadeiras C08, C07 e C06.**
- **Fraquezas:** mesmo modelo nas 5 lentes; sem dado, fonte ou confiança. **Licença:** sem licença; só ideias.

## 4. coreyhaines31/marketingskills · skill `marketing-council`

- **URL:** https://github.com/coreyhaines31/marketingskills/tree/main/skills/marketing-council (MIT, mantido; 12 dossiês em `references/advisors/`).
- **O que faz:** conselho simulado de 12 nomes do marketing, em 3 modos (1, 3 a 5, ou 12), com tabela de "quem senta" por tipo de pergunta e dissidente natural.
- **Técnicas absorvidas:**
  - "Sempre sente pelo menos um dissidente designado. Um conselho que concorda é um espelho, não um conselho." **Cadeira Advogado do diabo e regra do dissidente na seção 6.1 do SKILL.md.**
  - Mapa de desacordos com o trade-off e **o que resolveria** cada conflito. **Campo `o_que_resolveria` do Presidente.**
  - Regras de aterramento: rotular como simulação, nenhuma citação inventada, nada de opinião atribuída a pessoa real sobre a empresa do usuário. **Regra 11 do SKILL.md.**
  - "Gatilho de alerta" (tripwire) na síntese. **Critério de abandono com data.**
  - Formato de dossiê (lente, frameworks, posições com fonte, perguntas-assinatura, pontos cegos). **Modelo para criar cadeiras de especialista, em `03-papeis-e-prompts.md`.**
- **Fraquezas:** uma mente só; não pontua nem mede confiança; os 12 nomes são de marketing americano de consumo e infoproduto.

## 5. TauricResearch/TradingAgents

- **URL:** https://github.com/TauricResearch/TradingAgents (Apache-2.0; v0.6.0 em outubro de 2026, segundo a R10).
- **O que faz:** imita uma mesa de investimento: analistas em paralelo, debate touro x urso, Research Manager decide, Trader monta a ordem, time de risco com perfis agressivo, conservador e neutro debate, Portfolio Manager aprova.
- **Técnicas absorvidas:**
  - Debatedor recebe o histórico e o último argumento do adversário e refuta ponto a ponto, "em estilo de conversa". **Prompt do debatedor a partir da rodada 2.**
  - Gerente: conflito sozinho não é motivo para manter; escolher o lado mais forte, dimensionado pela força da vitória; não fabricar direção para parecer decidido; pesar os lados sem importar quem falou primeiro ou por último. **Prompt do Moderador final e do Presidente.**
  - Memória que confere o retorno realizado e escreve uma reflexão; backtest porque "uma execução dá uma decisão, o que não diz se o sistema decide bem". **Diário e liquidação em D+30, D+60, D+90.**
  - Debate de risco em perfis. **Red Team com piores casos e atores irracionais.**
- **Fraquezas:** feito para ações; infraestrutura Python e LangGraph; o README admite que duas execuções podem divergir.

## 6. Skytliang/Multi-Agents-Debate (MAD, Liang et al., arXiv 2305.19118)

- **URL:** https://github.com/Skytliang/Multi-Agents-Debate (GPL-3.0).
- **O que faz:** afirmativo, negativo e moderador; o moderador responde em JSON se já existe preferência clara e encerra.
- **Técnicas absorvidas:**
  - "Degeneration-of-Thought": autorreflexão não gera ideia nova quando o modelo já confia na própria resposta; a saída é feedback externo. **Por que o debate usa dois agentes e não autocrítica.**
  - Parada adaptativa do moderador em JSON e juiz de desempate quando estoura o limite (padrão 3 rodadas). **Campo `preferencia_clara` e teto de rodadas.**
  - O paper indica que um nível **moderado** de discordância é necessário e que LLM pode não ser juiz justo entre modelos diferentes. **Discordância moderada no prompt e `modelo_juiz` separado.**
- **Licença:** GPL-3.0; nenhum código ou texto copiado.

## 7. composable-models/llm_multiagent_debate (Du et al., arXiv 2305.14325)

- **URL:** https://github.com/composable-models/llm_multiagent_debate (sem licença).
- **O que faz:** cada agente responde; nas rodadas seguintes recebe as respostas dos outros "como conselho adicional" e atualiza; no fim, voto da maioria. Aritmética com 2 agentes e 3 rodadas; GSM8K, MMLU e biografias com 3 agentes e 2 rodadas.
- **Resultado (Tabela 1, versão HTML):** aritmética de 67,0% para 81,8%; GSM8K de 77,0% para 85,0%; retorno decrescente depois de 4 rodadas; consenso às vezes errado e confiante.
- **Técnicas absorvidas:** atualização "como conselho, não como ordem"; teto de 2 ou 3 rodadas; registrar o voto antes e depois para saber se o debate mudou algo. **Voto cego da R4, voto final cego e campo `mudancas_de_posicao`.**

## 8. dinobby/ReConcile (arXiv 2309.13007)

- **URL:** https://github.com/dinobby/ReConcile (MIT; último push em 03/10/2023).
- **O que faz:** mesa redonda entre modelos de fornecedores diferentes com voto ponderado por confiança.
- **Técnicas absorvidas:**
  - A confiança declarada é reescalada porque modelos superestimam (função `trans_confidence`): até 0,6 vira 0,1; acima de 0,6 e abaixo de 0,8 vira 0,3; de 0,8 a menos de 0,9 vira 0,5; de 0,9 a menos de 1 vira 0,8; 1 fica 1. **Função `reescala` do script, peso do voto.**
  - Agrupar posições ("N agentes acham X") no prompt seguinte. **Resumo estatístico da rodada Delphi.**
- **Fraquezas:** abandonado; usava Claude por cookies de navegador; tarefas de quiz.

## 9. princeton-nlp/tree-of-thought-llm (Yao et al., arXiv 2305.10601)

- **URL:** https://github.com/princeton-nlp/tree-of-thought-llm (MIT).
- **O que faz:** em vez de raciocínio linear, gera vários pensamentos por passo, avalia (`value` ou `vote`) e mantém os melhores (busca em largura).
- **Resultado:** Game of 24 com GPT-4, cadeia de pensamento 4% e ToT 74% (abstract); o README admite reexecução com 69%.
- **Técnicas absorvidas:** cada decisão abre pelo menos 3 ramos, um fora do óbvio; avaliação por voto entre ramos; poda com critério declarado; reexecutar para medir variação antes de confiar. **R2, consolidador de opções e recomendação de rodar Padrão antes do GOD.**

## 10. bmad-code-org/BMAD-METHOD (party mode + advanced elicitation)

- **URL:** https://github.com/bmad-code-org/BMAD-METHOD (MIT com aviso de marca registrada "BMad").
- **O que faz:** mesa redonda em 4 modos (`session`, `auto`, `subagent`, `agent-team`) e catálogo de 71 métodos de elicitação em 12 categorias (contagem da R10 no `methods.csv`).
- **Técnicas absorvidas:**
  - Regra do modo `auto`: criar agente independente quando o pensamento divergente sem contaminação é o valor da rodada (revisão, red team, avaliação de plano). **Critério de quando gastar subagente; o modo Rápido economiza aí.**
  - Rodada em paralelo para primeira opinião; em sequência quando se quer reação às palavras do outro. **Conselho em paralelo, debate em rodadas.**
  - Catálogo de métodos por rodada: Six Thinking Hats, Delphi, Red Team vs Blue Team, Pre-mortem, Steelmanning, Source Triangulation, Assumption Audit, Second-Order Thinking, Inversion. **`09-catalogo-de-tecnicas.md`.**
- **Fraquezas:** o party mode foi feito para não resolver ("eles brigam e você não resolve"); depende da instalação do BMAD. Aqui sempre há veredito ou abstenção justificada.

---

## Lidos fora do top 10 e o que veio de cada um

| Repositório | Ideia aproveitada | Onde entra |
|---|---|---|
| obra/superpowers (skill `brainstorming`) | Portão rígido: nada se executa sem aprovação humana; propor 2 a 3 abordagens com recomendação | Regra 2 (GO do gestor) |
| wan-huiyan/agent-review-panel | Voto final cego, auditoria de completude, verificação de cada alegação, portão pós-juiz; aviso de que revisores do mesmo modelo fazem autocrítica estruturada; auditoria do autor com 50 de 51 execuções sem debate | Aviso fixo, voto final cego, auditor de rastros |
| limelights-amsterdam/mckinsey-strategy-team | Opções por mandatos opostos; loop de riscos até duas rodadas voltarem vazias | Crítico de lacunas em loop até 2 passadas vazias |
| yogevr76er/calibrate | Decisão como afirmação falsificável; objeção diz a observação que a confirma ou mata e quanto custa; portão de refutação; orçamento igual entre chapéus; etiqueta "checado em fonte" x "prior"; gatilhos de escalada | R0, regra 5, orçamento simétrico, etiqueta [PRIOR], gatilhos do GOD |
| NotEl1gible/llm-debate-engine | Delphi com estimativa isolada e rodadas anônimas; pooling em log-odds; abstenção quando a discordância é alta | Rodada Delphi, `pool_logodds`, rótulo Abster |
| hex/claude-council | Quando todos concordam, nomear a premissa em que a resposta se apoia | Campo `premissa_da_concordancia` |
| togethercomputer/MoA | Agregador instruído a avaliar criticamente porque parte das respostas pode estar errada | Prompt do Presidente |
| thunlp/ChatEval | Papéis iguais não superam um agente só; pico com 3 e 4 papéis; mais turnos não melhoram | Debate com 2 vozes e moderador; teto de rodadas |
| stanford-oval/storm (Co-STORM) | Moderador que puxa informação recuperada e ainda não usada | Moderador confere números contra arquivos |
| microsoft/autogen (Magentic-One) | Livro de fatos em 4 títulos: dados ou verificados, a buscar, a derivar, palpites | Curador de Fatos, R1 |
| FoundationAgents/MetaGPT | Papéis com procedimento padrão e saída estruturada | Esquemas de saída por papel; o `examples/debate.py` ("forte e emocional em 80 palavras") serve de antiexemplo |
| camel-ai/camel | Role-playing com inception prompting; comitê de juízes | Persona da cadeira no topo do prompt |
| anthropics/skills | Nenhuma skill oficial de conselho; `discernment-nudge` (perguntas para o usuário checar fatos e contexto) | Checklist da seção 11.1 |

## Regras de licença usadas na skill

- Ideias de qualquer repositório podem ser usadas; o texto foi reescrito em português.
- Trechos de projetos MIT ou Apache-2.0 podem ser adaptados com crédito (aqui: a regra numérica de reescala do ReConcile, MIT).
- Nenhum código do MAD (GPL-3.0) nem de repositórios sem licença (llm-council, skill do Ole Lehmann, Du et al.).
- O script `workflow-modo-god.js` foi escrito do zero para a ferramenta Workflow do Claude Code.
