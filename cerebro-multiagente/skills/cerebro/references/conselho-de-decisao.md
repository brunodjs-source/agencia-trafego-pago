# Conselho de decisão (adversarial)

O conselho é o subsistema do Cérebro para decisões caras ou difíceis de
reverter. Herda o método da skill `agencia-reunida-modo-god` da Agência Tráfego
Pago e o generaliza para decisões de engenharia (stack, arquitetura,
build-vs-buy, escopo, migração, deploy).

## Por que adversarial

Um conselho de instâncias do **mesmo modelo** é **autocrítica estruturada**, não
verificação independente. Concordância unânime pode refletir **viés comum**.
Por isso o conselho é desenhado para **divergir de propósito** antes de
convergir.

> Du et al. (arXiv 2305.14325) registram agentes chegando a consenso confiante
> em resposta errada. O conselho reduz esse risco; não o elimina.

## O fluxo

1. **Enquadramento.** O presidente escreve a decisão em uma frase, lista as
   opções e o critério de sucesso. Decisão mal enquadrada é a maior fonte de
   erro — isto vem primeiro.
2. **Pesquisa isolada.** Cada cadeira pesquisa **sem ver** as outras, para não
   convergir cedo.
3. **Voto às cegas.** Cada cadeira vota antes de qualquer debate. Registra-se a
   distribuição.
4. **Debate só onde racha.** Só as opções em que o voto divergiu vão a debate,
   com **defensor**, **opositor** e **moderador**.
5. **Síntese.** O presidente integra, sem apagar a minoria: a posição vencida
   fica registrada com o motivo.
6. **Verificação adversarial (3 lentes).** Um time tenta **derrubar** a
   recomendação por três ângulos distintos (ex.: premissa frágil, evidência
   fraca, custo escondido).
7. **Red team.** Alguém assume o papel de "por que isto falha" e escreve o
   cenário de fracasso mais plausível.
8. **Crítico de lacunas.** Lista o que **não** foi considerado e qual dado está
   faltando.

## A saída: Decision Brief

Todo brief traz:

- **Recomendação** em uma frase.
- **Confiança** calculada por regra: Alta, Média, Baixa ou **Abster**.
- **Faixa de probabilidade** (não um número único falsamente preciso).
- **Evidência e o nível dela** para cada ponto.
- **O dado que mudaria a decisão** (o teste mais barato para reduzir a dúvida).
- **Critério de abandono com data** (quando desistir se não funcionar).
- **A posição minoritária** e por que perdeu.

> Aviso fixo no topo de todo brief:
>
> > Conselho de instâncias do mesmo modelo é autocrítica estruturada, não
> > verificação independente. Este documento reduz risco e explicita premissas;
> > não garante resultado.

## Quando escalar para GOD

Basta um gatilho:

- Decisão irreversível ou cara de reverter (migração, troca de stack, contrato).
- Divergência forte no voto às cegas (sem maioria clara).
- Alto impacto em segurança, dados pessoais, ou dinheiro.
- O gestor pediu "modo god".

## Integração com a Agência Tráfego Pago

Se a skill `agencia-reunida-modo-god` estiver instalada, o Cérebro delega a ela
a condução do conselho e consome o Decision Brief. Caso contrário, conduz o
conselho com o roster próprio (`presidente-sintese`, `red-team-adversarial`,
`verificador-evidencia`, `critico-de-lacunas`, `juiz-viabilidade`,
`juiz-risco`, `estrategista-independente`).
