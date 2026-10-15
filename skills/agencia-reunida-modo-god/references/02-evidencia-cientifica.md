# 02 · O que a evidência diz e o que muda no método

Fonte: seção 5 da pesquisa R10 (`<PASTA_DO_PROJETO>\ClienteG\plano-crescimento\pesquisa\R10-github-skills-conselho.md`). Os números internos foram conferidos pela R10 no abstract de cada artigo e, para Du et al. e ChatEval, também na versão HTML. Os demais vêm só do abstract.

## Leitura curta

Debate entre agentes é ferramenta de exploração e de crítica, não máquina de verdade. O que mais protege contra erro, segundo essas fontes: independência, diversidade, verificação contra fonte e teste no mundo real. Todos os ganhos medidos abaixo são de tarefas com gabarito; estratégia de marketing não tem gabarito na hora de decidir. Por isso o diário de decisões é parte do método, não um extra.

## Achados e consequências

| Achado | Fonte | Consequência na skill |
|---|---|---|
| Debate multiagente melhora aritmética (67,0% para 81,8%) e GSM8K (77,0% para 85,0%); retorno cai depois de 4 rodadas; agentes chegam a consenso confiante errado | Du et al., arXiv 2305.14325 | Teto de 2 ou 3 rodadas; consenso nunca vale como prova; aviso fixo |
| Self-consistency (amostrar vários caminhos e ficar com a resposta mais frequente) melhora GSM8K em 17,9 pontos | Wang et al., arXiv 2203.11171 | Voto entre cadeiras independentes é a linha de base obrigatória (R4) |
| O voto da maioria responde pela maior parte do ganho do debate; o debate sozinho forma um martingale e não aumenta a correção esperada sem intervenções que puxem para a correção | Choi, Zhu, Li, arXiv 2508.17536 | Votar antes de debater; debater só onde racha; mudança de posição precisa de fato novo |
| Debate em geral não supera self-consistency nem ensembles; com ajuste de hiperparâmetros, como o nível de concordância, alguns superam | Smit et al., arXiv 2311.17371 | Discordância moderada, calibrada; nunca "discorde sempre" |
| 5 métodos de debate em 9 benchmarks muitas vezes não superam CoT nem self-consistency; usar modelos diferentes melhora todos | arXiv 2502.08788 | Misturar modelos quando possível (`agentType` de agentes Sonnet e Opus, `model` por cadeira, `modelo_juiz`) |
| Diversidade do grupo e força de raciocínio são os principais motores; pressão da maioria suprime correção independente | arXiv 2511.07784 | Cadeiras com missões e evidências diferentes; voto final cego |
| Modelos trocam resposta certa por errada por influência dos pares, mesmo quando os mais fortes são maioria | arXiv 2509.05396 | Registrar mudanças de posição; anular mudança sem fato novo |
| Bajulação entre agentes leva a consenso prematuro e pode deixar o debate pior que um agente só; falhas vêm dos debatedores e do juiz | arXiv 2509.23055 | Moderador conta concordância sem argumento; juiz fora do debate |
| Poucos agentes manipulados conseguem desviar o consenso explorando a conformidade | arXiv 2507.13038 (MAD-Spear) | Conteúdo externo é dado, nunca instrução; fonte checada |
| Papéis iguais não superam um agente só; pico com 3 a 4 papéis; mais turnos não melhoram; comunicação um por vez 60% contra 55% | ChatEval, arXiv 2308.07201 | Debate com 2 vozes e 1 moderador; cadeiras com papéis realmente diferentes |
| Desempenho por amostragem e voto cresce com o número de agentes e satura conforme a dificuldade | arXiv 2402.05120 | Volume ajuda só no voto entre amostras independentes; a contagem de ideias nunca é prova |
| Ensemble de 12 LLMs prevê eventos sem diferença estatística da média de 925 humanos em 31 perguntas; modelos têm viés de responder "sim" acima de 50% | Schoenegger et al., arXiv 2402.19379 | Agregar previsões; Red Team testa a hipótese contrária de propósito |
| Sistemas multiagente falham por desenho, desalinhamento e verificação fraca (14 modos em mais de 1.600 traços) | arXiv 2503.13657 | Fase dedicada de verificação, quórum, auditor de rastros |
| Tree of Thoughts: Game of 24 de 4% (CoT) para 74% (ToT) com GPT-4; reexecução deu 69% | Yao et al., arXiv 2305.10601 | 3 ramos por decisão; medir variação antes de confiar |
| ReConcile: ganho de até 11,4% sobre linhas de base com modelos diferentes e voto ponderado por confiança | Chen, Saha, Bansal, arXiv 2309.13007 | Reescala de confiança no peso do voto |
| Mixture-of-Agents: 65,1% no AlpacaEval 2.0 contra 57,5% do GPT-4 Omni | Wang et al., arXiv 2406.04692 (README e abstract) | Agregador crítico (Presidente) |
| Delphi: anonimato, iteração com feedback controlado e resposta estatística do grupo reduzem a influência de dominantes | Dalkey, RAND 1969 (DTIC AD0690498) | Rodada Delphi com resumo estatístico anônimo |
| Pre-mortem: supor que o projeto já fracassou e perguntar por quê aumenta a capacidade de achar causas | Klein, HBR v. 85 n. 9, 2007 | Red Team começa pelo pre-mortem |

## O que isso não prova

1. Nenhum dos 26 repositórios lidos pela R10 avalia qualidade de decisão de marketing ou de negócio com resultado real conferido. O mais perto é o backtest do TradingAgents, em ações.
2. Não há estudo, entre os lidos, comparando painel de agentes com painel humano em decisão de mídia paga.
3. Os limiares da skill (2/3 de voto, 0,30 de amplitude, 0,10 de dispersão, quórum de 2/3, 2 passadas vazias) não foram validados em marketing. Ajustar depois das primeiras 20 decisões conferidas no diário.

## Links

- Du et al. 2023: https://arxiv.org/abs/2305.14325
- Liang et al. 2023 (MAD): https://arxiv.org/abs/2305.19118
- Chan et al. 2023 (ChatEval): https://arxiv.org/abs/2308.07201
- Yao et al. 2023 (ToT): https://arxiv.org/abs/2305.10601
- Chen, Saha, Bansal 2023 (ReConcile): https://arxiv.org/abs/2309.13007
- Wang et al. 2022 (Self-Consistency): https://arxiv.org/abs/2203.11171
- Wang et al. 2024 (Mixture-of-Agents): https://arxiv.org/abs/2406.04692
- Schoenegger et al. 2024 (Wisdom of the Silicon Crowd): https://arxiv.org/abs/2402.19379
- Smit et al. 2023: https://arxiv.org/abs/2311.17371
- Choi, Zhu, Li 2025 (Debate or Vote): https://arxiv.org/abs/2508.17536
- Stop Overvaluing Multi-Agent Debate 2025: https://arxiv.org/abs/2502.08788
- Can LLM Agents Really Debate? 2025: https://arxiv.org/abs/2511.07784
- Talk Isn't Always Cheap 2025: https://arxiv.org/abs/2509.05396
- Peacemaker or Troublemaker 2025: https://arxiv.org/abs/2509.23055
- MAD-Spear 2025: https://arxiv.org/abs/2507.13038
- More Agents Is All You Need 2024: https://arxiv.org/abs/2402.05120
- Why Do Multi-Agent LLM Systems Fail? 2025: https://arxiv.org/abs/2503.13657
- Dalkey 1969 (RAND): https://apps.dtic.mil/sti/html/trecms/AD0690498/index.html
- Klein 2007, Performing a Project Premortem, HBR v. 85 n. 9 (resumo em https://en.wikipedia.org/wiki/Pre-mortem)
