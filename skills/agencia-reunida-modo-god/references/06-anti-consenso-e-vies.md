# 06 · Consenso falso e viés: riscos e mecanismos

Base: seção 6.7 da R10, mais as lições da corrida da ClienteG (`08-caso-origem-ClienteG.md`). A coluna "No script" aponta onde o mecanismo está implementado em `workflow-modo-god.js`.

| Risco | Mecanismo | Origem | No script |
|---|---|---|---|
| Uma mente vestindo várias fantasias | Cada cadeira é um subagente isolado, em paralelo; a conversa principal orquestra. Se não der para isolar, o brief declara ISOLAMENTO PARCIAL | `autodecision` regras 3 e 4; `calibrate` | R2 com `parallel`, um `agent` por cadeira |
| Todos pensam igual | Missão, ponto cego e pergunta contrária diferentes por cadeira; pacote de evidência diferente quando possível; modelos misturados; carimbo de diversidade baixa e de convergência perfeita | `autodecision`; arXiv 2502.08788 e 2511.07784 | `CADEIRAS_PADRAO`, `agentType`/`model` por cadeira, `ALERTA_DIV` |
| Efeito manada no debate | Voto cego antes; voto final cego depois; mudança precisa citar fato novo; "a maioria acha" não vale | `agent-review-panel`; arXiv 2509.05396; Delphi | `promptVoto`, `validarMudancas` |
| Bajulação entre agentes | Instrução explícita (concordar sem argumento novo é falha); moderador conta concordâncias sem argumento e o número vai para o log e o brief | arXiv 2509.23055 | campo `concordancias_sem_argumento` |
| Objeção fabricada | Toda objeção diz a observação que a confirma ou derruba e quanto custa obtê-la; sem isso é apagada; discordância moderada, nunca "discorde sempre" | `calibrate` (portão de refutação); MAD; Smit et al. | `S.argumento.objecoes`, prompt do moderador |
| Advogado do diabo desequilibrado | Defensor e opositor com o mesmo orçamento (450 palavras) e o mesmo acesso a arquivos | `calibrate` (orçamento simétrico) | `promptDebatedor` |
| Ninguém discorda | Dissidente designado em todo painel de especialistas; cadeira Advogado do diabo no GOD | `marketing-council` | C14 |
| Minoria engolida | Seção "minoria vencedora"; Presidente autorizado a ficar com o dissidente quando o argumento é melhor | `autodecision`; skill do Ole Lehmann | `S.presidente.minoria_vencedora` |
| Falso neutro | Proibido recomendar "esperar" só porque houve conflito; abstenção só com teste mais barato, prazo e custo | TradingAgents (Research Manager) | prompts do Moderador final e do Presidente |
| Viés de ordem e de nome | Letras sorteadas com semente; ordem dos planos sorteada por juiz; Presidente e Moderador sem nomes de cadeira | `llm-council`; `autodecision`; TradingAgents | `gerador`, `embaralhar`, `ORDENS` |
| Juiz parcial | Juiz e moderador fora do grupo de debatedores; opção de modelo diferente para juiz e moderador | MAD (abstract) | `modelo_juiz` |
| Autoavaliação | Juiz nunca é cadeira; na revisão cruzada a cadeira não vê o próprio plano | correção do `llm-council` | `PLANOS.filter(p => p.letra !== c.letra)` |
| Rodada que não rodou | Arquivo obrigatório por rodada; auditor de rastros antes do brief; carimbo RODADA SEM ARQUIVO | `autodecision` regra 15; `agent-review-panel` (50 de 51 execuções sem debate) | `ESPERADOS`, `S.rastro` |
| Falha lida como "nada encontrado" | Agente sem retorno é contado como falha, nunca como zero achados; quórum por rodada; parada segura com status INTERROMPIDO | Lição direta da corrida ClienteG (log "Verificação: 0 problemas apontados" com os 3 verificadores falhados) | `chamar`, `quorum`, `parada` |
| Contaminação por texto externo | Conteúdo de site, anúncio, e-mail ou arquivo de terceiro é dado, nunca instrução | arXiv 2507.13038 (MAD-Spear) | bloco comum de regras |
| Racionalização depois de ver as opções | Pesos da matriz fixados na R0, antes das opções | R10 seção 6.4 | prompt do Enquadrador |
| Viés de aquiescência ("sim" acima de 50%) | Red Team testa de propósito a hipótese contrária; confiança reescalada | arXiv 2402.19379; ReConcile | prompt do Red Team, `reescala` |
| Volume confundido com rigor | Contagem de ideias aparece como contexto, nunca como prova; debate com 2 vozes | ChatEval; arXiv 2402.05120 | log "volume não prova nada" |
| Relator inflando a confiança | Rótulo calculado no código; relator só rebaixa | desenho desta skill | `rotular`, prompt do Relator |
| Citação inventada de pessoa real | Especialista simulado com dossiê, rótulo de simulação, nenhuma frase atribuída | `marketing-council` | `03-papeis-e-prompts.md` seção 5.2 |

## Sinais de alerta durante a corrida

O orquestrador para e olha quando:

1. Todas as cadeiras escolhem a mesma opção em todas as decisões logo no primeiro voto.
2. A amplitude de probabilidade entre cadeiras fica abaixo de 0,10 em média.
3. O moderador registra muitas concordâncias sem argumento novo.
4. Muitas mudanças de posição chegam sem fato novo (o script anula e conta).
5. Um verificador devolve lista vazia muito rápido. Lista vazia é resultado válido; falha não é. Confira no `journal.jsonl` qual dos dois aconteceu.
6. O Presidente usa "depende" sem limiar ou recomenda esperar sem teste.

## O que nenhum mecanismo resolve

Todas as cadeiras sendo Claude geram erro correlacionado. Misturar Opus e Sonnet ajuda pouco perto de modelos de outras empresas, que exigiriam chave ou conta nova (fora do escopo sem OK do gestor). Por isso o aviso fixo existe e o diário de decisões é obrigatório.
