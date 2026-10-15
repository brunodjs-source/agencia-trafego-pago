# Medição de Incrementalidade — MMM leve, GeoLift e atribuição de plataforma (Pacote TI v1.0)

> Quando o número do Ads Manager mente, quanto mente, e o que usar no lugar — calibrado pro porte de cliente da Agência (PME BR, R$ 3k–50k/mês de mídia). Fontes: `pymc-labs/pymc-marketing`, `coreyhaines31/marketingskills`, `growthbook/growthbook`, `AgriciDaniel/claude-ads`.

---

## 1. Régua de decisão por porte de cliente (a regra mais importante do arquivo)

| Investimento/mês | Ferramenta de medição | Por quê |
|---|---|---|
| < R$ 10k, 1 canal | Atribuição de plataforma + MER como sanity check | MMM não tem dado; teste geo não tem potência estatística |
| R$ 10k–30k, 1–2 canais | Plataforma + MER mensal + **1 teste de holdout geo por trimestre** na dúvida grande (ex: "o remarketing é incremental?") | Teste barato responde a pergunta cara |
| R$ 30k–100k, 2–3 canais | Curvas de saturação por canal via curve_fit (MMM "leve", sem MCMC) + holdout geo semestral pra calibrar | 15 linhas de Python respondem "até onde escalar" |
| > R$ 100k, 3+ canais | MMM bayesiano completo (pymc-marketing / Robyn / Meridian) calibrado com lift tests | Só aqui o MMM completo paga o próprio custo |

Regra do `claude-ads` que governa tudo: **nunca somar conversões entre plataformas com janelas de atribuição diferentes** — reportar lado a lado com a janela anotada. O "total de conversões" Meta+Google somado é sempre > que a realidade (dupla contagem de view-through e de janelas sobrepostas).

## 2. Fórmulas de bolso — MER, Payback, marginal ROAS

**MER (Marketing Efficiency Ratio)** — o sanity check que a atribuição não engana:

```
MER = receita TOTAL do negócio no período / spend TOTAL de mídia no período
```

- Vem do financeiro/CRM, não do pixel. Se o ROAS da plataforma sobe e o MER fica flat, a plataforma está **roubando crédito do orgânico** (típico de remarketing e brand).
- Acompanhar MER mensal em TODO cliente, mesmo o de R$ 3k. É grátis e imune a iOS/atribuição.
- Filosofia `marketingskills` (ads skill): **"Net cash over ROAS%"** — escalar até bater o teto de CAC break-even, não até um ROAS arbitrário.

**Payback Period como gate de canal** (`marketingskills`, payback-period.md — substitui ROAS como critério de escala em negócio recorrente tipo ClienteB R$ 35,90/mês):

```
Payback = CAC / ARPU mensal
Payback descontado = CAC / (ARPU mensal × retenção anual)
```

- Ligar/escalar canal SOMENTE onde payback descontado ≤ 12 meses (alvo 3–12). < 3 meses = crescimento na mesa, escalar mais agressivo. > 12 = insustentável pra caixa de PME.
- Exemplo ClienteB: CAC R$ 60, ARPU R$ 35,90, retenção 70% → 60/(35,90×0,70) = 2,4 meses = excelente, escalar.
- Re-rodar MENSALMENTE: CAC deriva durante a escala.

**Marginal ROAS (o número que decide "escalo mais?"):**

O ROAS médio esconde que o próximo real rende menos que o anterior. Com curva de saturação Michaelis-Menten ajustada (seção 3), a derivada tem forma fechada (`pymc-marketing`, transformers.py):

```
f(x) = alpha·x/(lam + x)          # conversões em função do spend
f'(x) = alpha·lam/(lam + x)²      # conversões MARGINAIS por real
Regra de parada: escalar até f'(x) < 1/CPA_alvo
```

- alpha = teto de conversões que o canal entrega; lam = spend de meia-saturação ("com gasto = lam você já capturou 50% do potencial").
- Isso responde com número exato "até quanto escalar antes do CPA marginal estourar" — coisa que nem o Ads Manager nem o MER dizem.

## 3. MMM "leve" sem MCMC — adstock + saturação em NumPy/SciPy (o nível certo pra PME)

Fonte: `pymc-labs/pymc-marketing` (fórmulas canônicas de transformers.py, copiáveis sem rodar o modelo bayesiano)

**Passo 1 — Adstock geométrico** (corrige o viés de correlacionar spend do dia com lead do dia):

```python
import numpy as np
def adstock_geometrico(spend, alpha, l_max=8):
    w = np.power(alpha, np.arange(l_max)); w /= w.sum()
    return np.convolve(spend, w)[:len(spend)]
# alpha via meia-vida: alpha = 0.5**(1/h). Efeito cai pela metade em 3 dias -> alpha ≈ 0.794
```

- Direct response (ClienteB, lead jurídico): geometric com decay rápido (prior Beta(1,3) do pymc = puxa pra alpha baixo). Awareness/TV: delayed/Weibull-PDF (pico atrasado).
- **Uso imediato no Trace:** aplicar adstock na série de spend ANTES de correlacionar com leads — correlação same-day subestima canal com carryover.

**Passo 2 — Ajustar saturação com o histórico da conta:**

```python
from scipy.optimize import curve_fit
def mm(x, alpha, lam): return alpha*x/(lam+x)
# x = spend semanal por campanha/canal, y = leads semanais (12+ semanas)
params, _ = curve_fit(mm, x_adstocked, y, p0=[y.max()*1.5, x.mean()], maxfev=10000)
```

**Passo 3 — Alocador de verba entre campanhas (Buck):** maximizar `Σ alpha_i·x_i/(lam_i+x_i)` sujeito a `Σ x_i = B` com `scipy.optimize.minimize(method='SLSQP')` + bounds por canal (ex: piso no canal âncora) — ~15 linhas, mesmo desenho do budget_optimizer.py do pymc-marketing. Versão avessa a risco (cliente com caixa apertado): otimizar o quantil pessimista, não a média.

**Detecção de fadiga de CANAL (proxy sem MCMC):** regressão em janela móvel de 4 semanas `leads ~ spend_adstocked` por canal; coeficiente caindo com spend estável = fadiga estrutural (o mesmo que o `time_varying_media` do pymc detecta com Gaussian process — multiplicador latente caindo de 1,0 pra 0,7 = mesmo real rendendo 30% menos).

## 4. Teste de holdout geo simples (GeoLift de pobre, potência real)

Fonte: desenho de lift test do `pymc-marketing` (lift_test.py) + health checks do `growthbook/growthbook`

O teste que PME consegue rodar com as cidades-âncora que a equipe TI já usa em geo `cities`:

1. **Parear cidades** por volume histórico de leads (ex: Campinas ~ Ribeirão Preto; São Luís ~ João Pessoa). Mínimo 2 pares; ideal 3–4.
2. **Desligar (ou dobrar) a mídia** em UMA cidade de cada par por **2–4 semanas** (mínimo 2 — menos que isso o ruído semanal come o sinal).
3. **Medir no CRM/WhatsApp**, nunca no pixel: leads da cidade tratada vs cidade controle, comparando com a razão histórica entre elas (diff-in-diff simples: `lift = (tratada_durante/controle_durante) − (tratada_antes/controle_antes)`).
4. **Registrar SEMPRE as 4 colunas do schema de lift test do pymc-marketing** — `x` (spend base), `delta_x` (mudança de spend), `delta_y` (lift observado em conversões), `sigma` (incerteza) — mesmo que hoje não rode o modelo. É o formato que calibra qualquer MMM futuro e vira histórico permanente do cliente.
5. **Interpretar:** incrementalidade = delta_y real / conversões que a plataforma reivindicou no mesmo geo. Ex: Meta reportou 80 Contacts em Campinas no período, o diff-in-diff mostra +50 leads reais → fator de incrementalidade 0,62 → descontar o ROAS de plataforma por esse fator nas decisões de budget.

**Armadilhas (health checks do GrowthBook adaptados):**
- Split quebrado invalida TUDO: se as cidades pareadas divergem historicamente mais de ~20% em tendência, o par é ruim — re-parear (análogo do SRM check p < 0.001: teste com randomização quebrada não se interpreta, se conserta).
- Amostra mínima: não interpretar antes de ~**150 conversões somadas** no teste (DEFAULT_MIN_SAMPLE_SIZE do GrowthBook).
- Não mudar targeting/criativo no meio do teste (re-randomização silenciosa).
- Rodar fora de sazonalidade atípica (nunca em dezembro/Black Friday).

## 5. Quando confiar em cada número — tabela de arbitragem

| Pergunta | Fonte confiável | Fonte que MENTE |
|---|---|---|
| Qual criativo/adset performa melhor DENTRO do canal? | Atribuição de plataforma (viés é igual pra todos os ads) | — |
| O canal está gerando negócio de verdade? | MER + CRM | ROAS de plataforma (rouba crédito do orgânico) |
| Remarketing/brand é incremental? | Holdout geo | Plataforma (remarketing é o rei da conversão roubada) |
| Até onde escalar o canal? | Curva de saturação + marginal ROAS | ROAS médio (esconde o retorno decrescente) |
| Vale abrir canal novo? | Payback descontado ≤ 12 meses | CPL comparado entre canais com janelas diferentes |
| A LP nova venceu? | Teste A/B com SRM check + 150 conv/variante (GrowthBook) | "Conversão subiu depois que trocamos" (confunde com sazonalidade) |

**Decisão bayesiana de parar teste queimando mídia** (GrowthBook, constants.ts): vencedor com Chance to Beat Control ≥ 95% OU expected loss ≤ 0,25% (seguro shippar mesmo sem 95%); nunca shippar com expected loss ≥ 1,25%. Com CPL alto, variante com 90% de chance e risk 0,1% DEVE ser shippada — esperar o 95% clássico é queimar verba. Se for espiar o resultado todo dia, usar sequential testing (IC mais largo, mas válido a qualquer momento; N* = 5000).

## 6. Robyn / Meridian / pymc-marketing — quando indicar o MMM completo

- **Robyn (Meta, R)**: gratuito, ridge + evolutionary; bom pra quem já vive em R. **Meridian (Google, Python)**: sucessor do LightweightMMM, bayesiano, geo-hierárquico. **pymc-marketing (PyMC Labs, Python)**: o mais transparente/hackeável, stack já compatível com a Agência (Python 3.14), e o único dos três cujo desenho de calibração por lift test está documentado na seção 3–4 deste arquivo.
- Pré-requisitos mínimos ANTES de propor MMM a cliente: **2+ anos de série semanal** (ou 1 ano com dados diários limpos), 3+ canais com variação real de spend (MMM não aprende com spend flat), receita/leads confiáveis fora do pixel, e ≥ 1 lift test pra calibrar (MMM observacional puro herda o viés da correlação).
- Pra carteira atual da Agência (ClienteB, ClienteC, ClienteA, ClienteF): **nenhum cliente atinge o pré-requisito hoje**. O caminho certo é: MER mensal desde já + registrar todo teste de escala no schema de 4 colunas + curvas de saturação leves quando houver 12+ semanas de série — e o MMM completo vira upgrade natural em 12–18 meses com o dado já organizado.

---
