# Decision Brief · {Cliente} · {título curto da decisão}

> **Conselho de instâncias do mesmo modelo é autocrítica estruturada, não verificação independente. Concordância unânime pode refletir viés comum. Este documento reduz risco e explicita premissas; não garante resultado.**

**Data:** {AAAA-MM-DD} · **Modo:** {Rápido | Padrão | GOD} · **Corrida:** `{pasta da corrida}` · **Calibração do método:** {ainda não medida (menos de 20 previsões conferidas) | Brier médio X em N previsões}

**Carimbos:** {nenhum | SEM BASE | DIVERSIDADE BAIXA | CONVERGÊNCIA PERFEITA NA 1ª VOTAÇÃO | NÃO CONVERGIU: Dn | VERIFICAÇÃO PARCIAL | RED TEAM NÃO RODOU | PRE-FLIGHT COM PENDÊNCIA | RODADA SEM ARQUIVO | ISOLAMENTO PARCIAL | ...}

---

## 1. Resumo por decisão

| Decisão | Recomendação | Confiança (por regra) | Probabilidade (faixa) | Dado que mudaria a decisão | Primeira ação |
|---|---|---|---|---|---|
| D1 {pergunta} | {opção e frase curta} | {Alta, Média, Baixa ou Abster} | {mín a máx; pooling em log-odds ao lado} | {limiar da sensibilidade} | {ação, dono, prazo} |

Motivos do rótulo (copiar de `08-confianca.json`; se o relator rebaixou, dizer por quê):

- D1: {motivos}

Decisões exploratórias (sem voto nem rótulo): {lista ou "nenhuma"}

## 2. Livro de fatos

Resumo do `01-fatos.md`, com nível de evidência de cada fato usado nas recomendações. Fatos a buscar que fariam diferença.

## 3. Opções e matriz de decisão

Para cada decisão: opções canônicas (A, B, C, D), nota de cada uma nos 6 critérios, pesos fixados na R0 e nota final. A matriz orienta; não decide sozinha.

## 4. Dinâmica do conselho

- Cadeiras sentadas: {N} (letras sorteadas; mapa guardado pelo orquestrador). Ideias declaradas: {N} (contexto, não prova).
- Ranking dos juízes por posição média: {tabela}.
- Primeiro voto cego por decisão: líder, peso, amplitude.
- Debates: quais decisões, quantas rodadas, se o moderador viu preferência clara.
- Voto final e Delphi: mudanças de posição com fato novo {N}; mudanças anuladas por falta de fato novo {N}.
- Concordâncias sem argumento registradas pelos moderadores: {N}.

## 5. Onde o conselho concorda e em que premissa

## 6. Onde diverge e o que resolveria

| Divergência | Lado A | Lado B | O que resolveria (dado, teste, prazo, custo) |
|---|---|---|---|

## 7. Minoria vencedora

{Quando houver: qual posição minoritária o Presidente adotou e por quê.}

## 8. Red Team

- Pre-mortem: {causas}
- Piores casos por decisão
- Atores irracionais e cisnes negros
- Premissas: SÓLIDAS, ABALÁVEIS, FRÁGEIS, com nível de evidência e como testar

## 9. Sensibilidade

| Decisão | Premissa | Valor atual | Limiar de virada | Vira dentro da faixa plausível? |
|---|---|---|---|---|

## 10. Verificação e lacunas

- Problemas apontados pelas 3 lentes: {N}; aplicados {N}; rejeitados com motivo {N}.
- Lacunas que o crítico achou e escreveu no plano: {lista}. O que ficou sem resposta: {lista}.
- Pre-flight: peças auditadas {N}; pendências {lista}; travessões restantes {N}. Peças visuais pendentes de CRAFT: {lista}.

## 11. Recomendação final, abandono e conferência

Para cada decisão recomendada:

- **Faça:** {ação}
- **Dono e prazo:** {quem, até quando}
- **Critério de abandono:** {métrica, limiar, data}
- **Teste mais barato para reduzir a dúvida:** {teste, prazo, custo}
- **Previsão registrada no diário:** {métrica, valor, faixa, probabilidade, data de conferência}

## 12. Log de convergência e custo

Chamadas de agente: {N}. Falhas: {lista}. Quórum por rodada. Iterações. Convergência por decisão.

## 13. Fontes

Arquivos, URLs e consultas, com data de coleta.

---

**Próximo passo:** GO, NO-GO ou nova rodada, decidido pelo gestor. Nada foi executado.
