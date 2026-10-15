# 09 · Catálogo de técnicas por rodada

Menu para enriquecer prompts quando uma rodada parece rasa. A origem de cada técnica está na pesquisa R10 (principalmente o catálogo `methods.csv` do BMAD-METHOD, com 71 métodos em 12 categorias, e os repositórios `calibrate`, `marketing-council`, `autodecision`, `tree-of-thought-llm` e `llm-debate-engine`). As descrições abaixo são resumos próprios.

| Rodada | Técnica | Como usar no prompt | Origem |
|---|---|---|---|
| R0 | Afirmação falsificável | "Reescreva a decisão como: fazer X produz Y, medido por Z, até W. Se não der, marque como exploratória." | `calibrate` |
| R0 | Pesos antes das opções | Fixar a matriz de critérios antes de qualquer opção detalhada | R10 seção 6.4 |
| R1 | Livro de fatos em 4 títulos | Verificados, a buscar, a derivar, palpites | AutoGen Magentic-One |
| R1 e R7 | Triangulação de fontes | Uma afirmação importante só vira [FATO] com 3 tipos de fonte, ou com 1 fonte primária de nível 1 ou 2 | BMAD (Source Triangulation) |
| R2 | Ramos múltiplos | Pelo menos 3 opções por decisão, uma fora do óbvio; avaliar e podar com critério declarado | Tree of Thoughts |
| R2 | Seis chapéus | Na mesma cadeira, passar pelos chapéus branco (fatos), vermelho (intuição), preto (riscos), amarelo (benefícios), verde (alternativas) e azul (processo), com orçamento parecido para cada | BMAD (Six Thinking Hats); `calibrate` (orçamento igual entre chapéus) |
| R2 | Inversão | "Como garantir que isso fracasse?" e depois evitar cada item | BMAD (Inversion); `recursive-research` citado na R10 |
| R2 | Pensamento de 2ª ordem | "E depois disso, o que acontece?" para cada efeito de 1ª ordem | BMAD (Second-Order Thinking); `autodecision` |
| R2 | Pergunta contrária da cadeira | Cada cadeira responde por escrito à pergunta que ataca o próprio viés | `autodecision` |
| R2 | Pre-mortem da própria cadeira | "Como o meu plano fracassa?" antes de entregar | Klein 2007; usado nas cadeiras da ClienteG |
| R3 | Três perguntas da revisão | A mais forte; o maior ponto cego; o que todas deixaram passar | skill do Ole Lehmann |
| R3 | Ranking por posição média | Cada juiz ordena; a média das posições decide | `llm-council` |
| R4 | Delphi | Estimativa isolada, revelação anônima do resumo estatístico, revisão, parada quando estabiliza | Dalkey 1969; BMAD; `llm-debate-engine` |
| R4 | Confiança reescalada | Peso do voto pela tabela do ReConcile | ReConcile |
| R5 | Steelman | Antes de atacar, escrever a versão mais forte do outro lado | BMAD (Steelmanning) |
| R5 | Red Team x Blue Team | Um lado constrói, o outro tenta quebrar, com regras iguais | BMAD |
| R5 | Objeção com preço | Toda objeção diz a observação que a confirma ou mata e quanto custa obtê-la | `calibrate` |
| R5 | Parada adaptativa | Moderador pergunta "já existe preferência clara?" a cada rodada | MAD |
| R6 | Mapa de desacordos | 2 a 4 conflitos, o trade-off de cada um e o que resolveria | `marketing-council` |
| R6 | Premissa da concordância | Quando todos concordam, nomear a premissa em que a concordância se apoia | `hex/claude-council` |
| R6 | Gatilho de alerta | Critério de abandono com data e métrica | `marketing-council` |
| R7 | Pre-mortem | "Estamos em [data] e o plano fracassou. Por quê?" | Klein 2007; BMAD |
| R7 | Auditoria de premissas | Listar premissas, dar nota de confiança e impacto, testar as mais fracas | BMAD (Assumption Audit) |
| R7 | Sensibilidade | Para as 3 premissas de maior impacto, em que valor a recomendação vira | `autodecision` |
| R7 | Hipótese contrária | Testar de propósito o "não", porque ensembles de LLM tendem ao "sim" | arXiv 2402.19379 |
| R8 | Loop até secar | Repetir o crítico até 2 passadas seguidas sem achado | `mckinsey-strategy-team` |
| R9 | Diário e backtest | Registrar previsão e conferir com o número real; rodar o método em decisões antigas para ver se teria acertado | TradingAgents; `autodecision` |

## Quando gastar subagente

Regra do modo `auto` do BMAD, adaptada: crie agente independente quando o pensamento divergente sem contaminação é o valor da rodada (divergência das cadeiras, revisão, voto, red team, verificação). Para tarefas mecânicas (conferir arquivos, consolidar opções), um agente com esforço baixo ou o próprio orquestrador basta.
