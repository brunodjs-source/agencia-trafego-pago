# 05 · Pontuação e confiança

Tudo aqui está implementado no script `workflow-modo-god.js` (funções `reescala`, `estatistica`, `estavel`, `validarMudancas`, `rotular`). Os limiares são parâmetros de desenho, herdados de projetos abertos (R10) ou escolhidos aqui; nenhum foi validado em decisão de marketing. Ajuste em `args.limiares` depois das primeiras 20 decisões conferidas no diário.

## 1. Níveis de evidência

| Nível | O que é | Exemplo |
|---|---|---|
| 1 | Dado primário do próprio cliente | GAQL da conta do cliente, analytics, auditoria do site feita pela Agência |
| 2 | Ferramenta oficial ou fonte primária de terceiro | Planejador de Palavras-chave, documentação oficial do Google ou da Meta, `ads.txt` de um portal |
| 3 | Fonte pública secundária | Estudo de mercado, artigo, site de concorrente |
| 4 | Palpite do modelo ([PRIOR]) | "Donos de escritório decidem em grupo", sem fonte |

Nível 4 nunca fecha uma objeção sozinho (regra do `calibrate`).

## 2. Nota de cada plano (R3)

Cada juiz dá 0 a 5 em cada critério (total de 25) e uma nota de 0 a 10 pela própria lente.

| Critério | 0 | 5 |
|---|---|---|
| Base em dados | Só opinião | Cada número com fonte de nível 1 ou 2 |
| Especificidade para o cliente | Serviria para qualquer empresa | Usa preço, região, nicho, conta e concorrentes reais |
| Diversidade de opções e efeitos | Um caminho só | 3 ou mais opções, efeitos de 2ª ordem, alternativa fora do óbvio |
| Ponto cego assumido | Nenhum | Declara o próprio ponto cego e responde a pergunta contrária |
| Executabilidade | Sem primeiro passo | Primeiro passo, dono, prazo e custo |

Regra fixa: número sem fonte e sem etiqueta de estimativa zera "base em dados" daquele plano.

**Ranking:** para cada juiz, os planos são ordenados pela nota da lente; a posição média entre juízes dá o ranking (método do `llm-council`). O script também mostra a média da rubrica e a média da lente. Juiz nunca é cadeira, então não existe autoavaliação.

## 3. Matriz de decisão das opções (R6)

Pesos fixados na R0, antes de qualquer opção detalhada, para evitar racionalização depois.

| Critério | Peso padrão | Como medir |
|---|---|---|
| Impacto esperado | 25 | Leads qualificados, receita mensal ou outra métrica do critério de sucesso, sempre com faixa |
| Probabilidade de funcionar | 20 | Faixa [mín, máx] das cadeiras, sem média simples |
| Força da evidência | 20 | Nível 1 a 4 |
| Custo e reversibilidade | 15 | R$ e o que se perde se der errado |
| Tempo até o primeiro sinal | 10 | Dias até ter dado para confirmar ou matar |
| Risco (regras, marca, operação) | 10 | Nota do Guardião, revisada pelo Red Team |

Cada opção recebe 0 a 5 por critério; a nota final é a soma de (nota × peso) dividida por 5, de 0 a 100. Exemplo de cálculo com números fictícios: notas 4, 3, 2, 5, 4, 3 dão (4×25 + 3×20 + 2×20 + 5×15 + 4×10 + 3×10) / 5 = 345 / 5 = 69.

A matriz orienta o Presidente; ela não decide sozinha e não entra no rótulo de confiança.

## 4. Voto e convergência

### 4.1 Peso do voto

A confiança autodeclarada é sinal fraco (modelos superestimam). O peso de cada voto é a confiança reescalada como no ReConcile (`trans_confidence`):

| Confiança declarada | Peso |
|---|---|
| até 0,6 (ou inválida) | 0,1 |
| acima de 0,6 e abaixo de 0,8 | 0,3 |
| de 0,8 a menos de 0,9 | 0,5 |
| de 0,9 a menos de 1 | 0,8 |
| 1 | 1 |

### 4.2 Estatística por decisão

- `share` = peso da opção líder ÷ peso total.
- `amplitudeTop` = maior menos menor probabilidade de sucesso entre quem votou na líder.
- `dispersao` = maior menos menor probabilidade entre todas as cadeiras.
- `pool_logodds` = média dos logit das probabilidades de quem votou na líder, devolvida à escala de probabilidade. Comparado à média simples, puxa menos para o meio quando os votantes estão confiantes; continua dentro do intervalo dos votos. Mostrado sempre ao lado da faixa.
- `faixa` = menor `faixa_min` e maior `faixa_max` de quem votou na líder.

### 4.3 Gatilho de debate (decisão rachada)

Basta um: `share` abaixo de 2/3; `amplitudeTop` acima de 0,30; objeção de severidade alta, com observação verificável, ainda sem resposta (marcada pelo consolidador a partir das falhas fatais dos juízes).

### 4.4 Checagens de diversidade

- Média da `dispersao` entre decisões abaixo de 0,10: carimbo DIVERSIDADE BAIXA (limiar do `autodecision`).
- Menos de 3 opções fora do óbvio no total: alerta de diversidade.
- Todas as decisões unânimes na primeira votação: carimbo CONVERGÊNCIA PERFEITA NA 1ª VOTAÇÃO (suspeita).
- Decisão unânime, sem debate e com dispersão abaixo de 0,10: alerta só para ela.

Qualquer alerta impede o rótulo Alta. A saída recomendada é rodar de novo as cadeiras com outro modelo (`agentType` de agente Sonnet ou `model`) ou com pacotes de evidência diferentes.

### 4.5 Mudança de posição

No voto final e na rodada Delphi, mudança de opção sem `fato_novo` escrito é anulada: vale o voto anterior. O brief mostra quantas mudanças valeram e quantas foram anuladas.

### 4.6 Convergência

| Situação da decisão | Convergiu quando |
|---|---|
| Não foi debatida | Não estava rachada no primeiro voto |
| Debatida, modo Padrão (sem voto final) | O moderador registrou preferência clara em alguma rodada |
| Debatida, GOD, com voto final | O voto final não está rachado |
| Debatida, GOD, com rodada Delphi | O voto Delphi não está rachado, ou ficou estável em relação ao voto final (mesma líder e `share` variando no máximo 0,10) |

Decisão que não convergiu ganha o carimbo NÃO CONVERGIU e fica no máximo com rótulo Baixa. "Estável e rachada" conta como convergida: o conselho estabilizou na discordância, e o rótulo cai por falta de maioria.

## 5. Rótulo de confiança

Calculado nesta ordem (função `rotular`):

1. **Abster** se o Presidente se absteve, ou se não há maioria (`share` abaixo de 0,5), a `amplitudeTop` passa de 0,30 e o moderador disse que nenhum dado disponível resolve agora.
2. **Baixa** se houver qualquer um: carimbo grave (SEM BASE, QUÓRUM NÃO ATINGIDO, VERIFICAÇÃO NÃO RODOU, RODADA SEM ARQUIVO, RASTROS NÃO CONFERIDOS); não convergiu; `share` abaixo de 0,5; premissa de alto impacto FRÁGIL no red team.
3. **Alta** se todas: `share` de 2/3 ou mais; red team rodou; existe premissa de alto impacto avaliada e todas são SÓLIDAS com evidência nível 1 ou 2; nenhuma premissa vira dentro da faixa plausível; o crítico contou zero número sem fonte; nenhum alerta de diversidade ou unanimidade; todas as lentes de verificação retornaram.
4. **Média** no resto, com a lista do que faltou para Alta.

O relator pode rebaixar com motivo escrito. Nunca sobe.

| Rótulo | Como ler |
|---|---|
| Alta | Evidência forte, conselho com maioria clara, premissas sólidas e testadas. Ainda pode dar errado; o diário mostra com que frequência |
| Média | Recomendação utilizável, com ponto fraco nomeado nos motivos |
| Baixa | Decidir só se o custo de reverter for baixo; preferir o teste mais barato antes |
| Abster | Não decidir agora; rodar o teste indicado, com prazo e custo |

## 6. Calibração com o diário

1. Cada decisão recomendada vira uma linha em `diario-decisoes.jsonl` (esquema em `diario-decisoes.schema.json`) com métrica, previsão, faixa, probabilidade e data de conferência.
2. Na data, o Liquidador grava `resultado_real`, `acertou` (pelo critério de sucesso) e o Brier da linha: (probabilidade menos acertou)², com acertou valendo 1 ou 0.
3. **Brier médio**: média das linhas liquidadas. Zero é perfeito. Quem sempre diz 50% tem Brier 0,25 em qualquer cenário, porque (0,5 menos 1)² e (0,5 menos 0)² valem 0,25; um método útil precisa ficar abaixo disso.
4. Com 20 ou mais linhas, tabela por faixa de probabilidade (0 a 0,2; 0,2 a 0,4; 0,4 a 0,6; 0,6 a 0,8; 0,8 a 1): número de previsões, probabilidade média, taxa real de acerto. Se o conselho diz 80% e acerta 50%, ele é confiante demais; reveja a reescala e os limiares.
5. Até 20 linhas liquidadas, todo brief diz: "calibração do método ainda não medida".

## 7. Parâmetros ajustáveis

| Parâmetro (`args.limiares`) | Padrão | Origem |
|---|---|---|
| `share` | 2/3 | R10 seção 6.7 (escolha de desenho) |
| `faixa` | 0,30 | R10 seção 6.7 (escolha de desenho) |
| `diversidade` | 0,10 | `autodecision` |
| `estabilidade` | 0,10 | Escolha desta skill |
| `quorum` | 2/3 | Escolha desta skill |

| Parâmetro (`args.limites`) | Rápido | Padrão | GOD |
|---|---|---|---|
| frentes | 0 | 6 | 10 |
| cadeiras | 3 | 6 | 14 |
| juizes | 1 | 3 | 5 |
| debates | 0 | 5 | 10 |
| rodadas por debate | 0 | 2 | 3 |
| votoCego | não | sim | sim |
| votoFinal (e Delphi) | não | não | sim |
| iteracoes | 1 | 1 | 2 |
| lentes de verificação | 1 | 3 | 3 |
| redTeam | não | sim | sim |
| lacunasMax | 1 | 3 | 4 |
| ideias mínimas por cadeira | 15 | 30 | 75 |
| revisaoCruzada | não | não | não (ligar com `revisao_cruzada: true`) |
