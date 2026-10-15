# 08 · Caso de origem: o conselho da ClienteG (06/10/2026)

O método desta skill foi usado pela primeira vez no plano de crescimento da ClienteG (TI gerenciada para escritórios de contabilidade em São Paulo). Este arquivo registra o que rodou, o que os arquivos mostram e o que mudou na skill por causa disso. Nada aqui avalia se o plano da ClienteG vai dar certo; isso só o diário vai dizer.

## Fontes

- Script da corrida: `<pasta local>`
- Registro da corrida: `...\workflows\wf_2e6d26bb-b0f.json` (campos `agentCount`, `totalTokens`, `totalToolCalls`, `durationMs`, `logs`)
- Pacote de contexto: `<PASTA_DO_PROJETO>\ClienteG\plano-crescimento\00-contexto.md`
- Pesquisas: `<PASTA_DO_PROJETO>\ClienteG\plano-crescimento\pesquisa\R1` a `R10`
- Planos das cadeiras: `<PASTA_DO_PROJETO>\ClienteG\plano-crescimento\conselho\P01-plano.md` a `P14-plano.md`

## 1. Forma da corrida

| Fase | O que rodou | Agentes |
|---|---|---|
| Pesquisa | 10 frentes em paralelo (concorrentes de contabilidade e de advocacia, demanda e palavras, auditoria do Google Ads, canais do YouTube, portais de Display, funil e LP, chatbot, orçamento, repositórios do GitHub) | 10 |
| Conselho | 14 estrategistas independentes, cada um com persona, 8 perguntas obrigatórias e mínimo de 75 ideias numeradas | 14 |
| Julgamento | 5 juízes com lentes distintas (retorno financeiro, comprador real, execução rápida, técnica de Google Ads e mensuração, diferenciação da oferta) | 5 |
| Debate | 10 temas fixos, cada um com defensor e opositor em paralelo e moderador depois | 30 |
| Síntese | Redator-chefe do plano final e esta skill, em paralelo | 2 |
| Verificação | 3 verificadores adversariais (números; fatos externos; viabilidade e regras), corretor, crítico de lacunas com creative pre-flight | 5 |
| Total | | 66 |

Regras repetidas em todo prompt: ler o `00-contexto.md` primeiro, zero travessão, sem cadência de IA, número com fonte, somente leitura, nada instalado.

## 2. O que os arquivos mostram

| Item | Valor | Fonte |
|---|---|---|
| Planos entregues | 14 | log da corrida |
| Ideias ou hipóteses declaradas pelas cadeiras | 1.405 | log: "Conselho entregou 14 planos e 1405 ideias/hipóteses" |
| Tamanho dos planos | de 40.597 bytes (P03) a 62.645 bytes (P10) | listagem da pasta `conselho` |
| Tamanho das pesquisas | de 25.954 bytes (R6) a 89.714 bytes (R8) | listagem da pasta `pesquisa` |
| Agentes no script | 66 | `agentCount` |
| Tokens até a parada | 9.786.773 | `totalTokens` |
| Chamadas de ferramenta | 1.502 | `totalToolCalls` |
| Duração até a parada | 5.519.056 ms (cerca de 92 minutos) | `durationMs` |

### Sinais de que a independência rendeu

- Duas cadeiras, sem ler uma à outra, acharam o mesmo fato novo que nenhuma pesquisa tinha listado: a aplicação automática de recomendações ligada na conta, inclusive para tipos que trocam a estratégia de lance (P01, item 2 do veredito; P14, item 2 do veredito, com consulta GAQL `recommendation_subscription`).
- Três cadeiras registraram que a campanha ativa gastava em Parceiros e Display em vez da Busca do Google (P01, P07 e P14).
- O Advogado do diabo (P14) mostrou que os dois modelos de funil da própria Agência divergiam 5,6 vezes (R7 com 1 contrato a cada cerca de 1.600 cliques, R9 com 286). Contradição entre pesquisas é exatamente o que o conselho existe para achar.
- Várias cadeiras fizeram consultas próprias somente leitura (P01 e P14 rodaram GAQL; P02 consultou o Planejador; P09 usou curl com intervalo de 2 s).

Esses sinais mostram que o conselho achou coisa nova. Não mostram que o plano final é bom.

## 3. O que falhou

1. **Limite de sessão no meio.** Depois de pesquisa, conselho e julgamento, todos os agentes de debate, síntese e verificação falharam com a mensagem de limite de sessão (log). O GOD inteiro num workflow só passou do que o plano de uso aguentava naquela janela.
2. **Falha lida como "nada encontrado".** Com os 3 verificadores sem retorno, o script filtrou os nulos e registrou "Verificação: 0 problemas apontados". Um leitor apressado entenderia que o plano passou limpo.
3. **Debate em temas fixos.** Os 10 temas foram escritos antes de qualquer voto. Não havia como saber se o conselho já concordava num tema (debate desnecessário) ou rachava em outro que não estava na lista.
4. **Opositor sem freio.** O prompt mandava "ataque a posição dominante". Sem steelman, sem preço da objeção e sem regra de discordância moderada.
5. **Juízes com nomes e ordem fixa.** O resumo dos planos ia sempre de P01 a P14, com a persona de cada um. Viés de ordem e de autoridade possíveis.
6. **Confiança autodeclarada.** O moderador escolhia "alta, média ou baixa" sem regra.
7. **Sem voto antes e depois.** Não havia como medir se o debate mudou alguma posição.
8. **Crítico de lacunas em uma passada.** Sem loop até passadas vazias.
9. **Sem prova de rodada nem diário.** Nenhum auditor de arquivos; nenhuma previsão gravada para conferir depois.

## 4. O que a skill mudou por causa disso

| Problema observado | Mudança na skill |
|---|---|
| Limite de sessão no meio | Modos com teto de chamadas declarado; recomendação de rodar Padrão antes do GOD; parada segura com status INTERROMPIDO e retomada por `resumeFromRunId` |
| "0 problemas" vindo de falha | `chamar` com uma nova tentativa e registro de falha; quórum por rodada; verificação sem nenhuma lente para a corrida; carimbos VERIFICAÇÃO PARCIAL e NÃO RODOU |
| Debate em temas fixos | Voto cego primeiro (R4); debate só nas decisões rachadas; temas obrigatórios continuam possíveis via `temas_debate` |
| Opositor sem freio | Steelman na rodada 1, objeção com observação e custo, discordância moderada, orçamento simétrico, moderador apaga objeção sem preço |
| Juízes com nomes e ordem fixa | Letras e ordem sorteadas com semente, ordem diferente para cada juiz |
| Confiança autodeclarada | Rótulo calculado no código; relator só rebaixa |
| Sem voto antes e depois | Voto cego, voto final cego e rodada Delphi; mudança sem fato novo anulada |
| Crítico em uma passada | Loop até 2 passadas vazias, com teto por modo |
| Sem prova nem diário | Arquivo por rodada, auditor de rastros, diário com Brier |

## 5. O que vale reaproveitar como está

- O formato do `00-contexto.md`: fatos verificados com fonte, estado do cliente, regras para todos os agentes. É o primeiro arquivo de qualquer corrida.
- O bloco de regras repetido em todo prompt.
- As 10 missões de pesquisa como molde de frente (meta numérica, o que confirmar, como verificar, onde gravar, fecho comparativo).
- As 8 perguntas obrigatórias do plano, adaptadas ao cliente.
- O pedido de 75 ideias numeradas com confiança e evidência por ideia, como ferramenta de divergência (não de prova).
- As 3 lentes de verificação e a junção do crítico de lacunas com o creative pre-flight.
