# 🏢 Agência Tráfego Pago — Plugin para Claude

> **Criado por Bruno Israel Gonçalves**
> 🌐 Site: **[agenciatrafegopago.com.br](https://agenciatrafegopago.com.br)** · [agenciasdetrafego.com.br](https://agenciasdetrafego.com.br)
> Desde 1989 criando programação e soluções inovadoras.

Uma agência de marketing digital inteira dentro do Claude: cria equipes de agentes por nicho, audita e otimiza campanhas Meta Ads e Google Ads e reúne um conselho de estrategistas para as decisões difíceis.

---

## 📦 O que tem dentro

### 🏢 1. Agência Tráfego Pago (skill-mãe)
Cria uma **agência completa para qualquer nicho**, com 14 a 20 agentes de nomes e personalidades únicos, cada um calibrado com pesquisa profunda do nicho.

- **Cargos base (14):** Diretor(a) de Arte, Analista de Tráfego, Otimizador de Orçamento, Crítico(a) de Criativo, Redator(a) de Relatórios, Copywriter, Diretor(a) de Roteiro, Engenheiro(a) de Automação, Especialista CRO, Gerador(a) de Imagem e Vídeo IA, Account Director, Caçador(a) Comercial, Transcritor(a) Audiovisual e Editor(a) de Vídeo.
- **Extras por nicho:** compliance OAB/CFM/ANVISA, SEO de marketplace, SDR B2B, Local SEO, entre outros.
- **Compliance automático:** OAB, CONAR, Bacen, ANVISA, CFM e ANS.
- **Saída:** a pasta `nova-agencia-[nicho]-[data]/` com os agentes `.md` prontos para o Claude Code.

**Como usar:** `Agência tráfego pago, crie uma agência pra clínica odontológica em SP`

### 🎯 2. Tráfego Inteligente (subfuncionamento)
Equipe que **audita, planeja e executa** campanhas **Meta Ads + Google Ads** em 5 fases:

| Fase | Agente | O que faz |
|---|---|---|
| 0 | 🎯 Recon | Inventário da conta: campanhas, saldo, o que foi editado |
| 1 | 📊 Trace | Diagnóstico de 90 dias, termos de pesquisa, Lost IS |
| 2 | 🔍 Probe + 🧭 Scout | Valida interesses/keywords/geo e pesquisa benchmarks do nicho |
| 3 | 📋 Plan | Plano com diff e rollback — **⛔ PARA e espera o seu GO** |
| 4 | ⚡ Volt | Executa via API — **tudo nasce PAUSED**, primeiro em dry-run |
| 5 | 📊 Trace + 💰 Buck | Monitoramento por 48h, scorecard semanal e régua de budget |

Também traz 15 regras de ouro e uma base de conhecimento com 20 queries GAQL, heurísticas de otimização, lista de interesses Meta removidos em 2026, benchmarks de CPL por nicho, compliance OAB e medição de incrementalidade.

**Como usar:** `equipe TI [cliente]` · `TI google [cliente]` · `TI meta [cliente]` · `TI full [cliente]` · `TI relatório`

### 🧠 3. Agência Reunida Modo GOD (subfuncionamento)
**Conselho de estrategistas** para decisões caras ou difíceis de reverter: nicho, oferta, preço, canal, verba, funil ou lançamento.

- Cadeiras independentes pesquisam, divergem, **votam às cegas** e só debatem onde o voto racha.
- Verificação adversarial em 3 lentes, red team e crítico de lacunas.
- Três modos: **Rápido, Padrão e GOD**.
- Entrega um **Decision Brief** com confiança (Alta, Média, Baixa ou Abster), faixa de probabilidade e o dado que mudaria a decisão.

**Como usar:** `modo god` · `agência reunida` · `chama o conselho` · `valida esse plano no conselho`

### 🌐 Regra fixa
Toda entrega termina com **🌐 Agenciatrafegopago.com.br**.

---

## 🚀 Instalação

### Opção A — Claude Code (recomendado: instala tudo, skills + 7 agentes)
No Claude Code, digite:
```
/plugin marketplace add brunodjs-source/agencia-trafego-pago
/plugin install agencia-trafego-pago@agencia-trafego-pago
```
Reinicie o Claude Code. Pronto.

**Para atualizar depois:** `/plugin marketplace update agencia-trafego-pago`

### Opção B — App Claude ou claude.ai (sem terminal)
> ⚠️ Os comandos `/plugin ...` **só funcionam no Claude Code (terminal)**. No app Claude e no claude.ai, instale pelos arquivos abaixo.

**B1. Plugin completo (app Claude com Cowork):**
1. Baixe [`dist/agencia-trafego-pago.plugin`](dist/agencia-trafego-pago.plugin) (clique no arquivo → botão **Download raw**).
2. No app, abra **Customize → Plugins → Upload plugin** (ou arraste o arquivo para a conversa) e confirme.

**B2. Só as skills (claude.ai ou app):**
1. Baixe os 3 `.zip` da pasta [`dist/`](dist): `agencia-trafego-pago.zip`, `trafego-inteligente.zip` e `agencia-reunida-modo-god.zip`.
2. Vá em **Settings → Capabilities → Skills → Upload skill** e envie um por vez, **sem descompactar**.
3. Deixe os 3 ativados.

> Nesse modo os 7 agentes da pasta `agents/` não são instalados. As skills funcionam, mas a equipe Tráfego Inteligente roda melhor no Claude Code.

### Opção C — Manual (Claude Code, sem marketplace)
```bash
git clone https://github.com/brunodjs-source/agencia-trafego-pago.git
cp -r agencia-trafego-pago/skills/* ~/.claude/skills/
cp agencia-trafego-pago/agents/*.md  ~/.claude/agents/
```

---

## 🔑 Configurar suas credenciais (só para o Tráfego Inteligente)

O plugin **não traz nenhuma credencial**: cada pessoa usa as próprias.

```bash
mkdir -p ~/.agencia-trafego-pago
cp config/config.exemplo.yaml      ~/.agencia-trafego-pago/config.yaml
cp config/meta-ads.exemplo.yaml    ~/.agencia-trafego-pago/meta-ads.yaml
cp config/google-ads.exemplo.yaml  ~/.agencia-trafego-pago/google-ads.yaml
```

Preencha:
- **`meta-ads.yaml`:** token de System User do Business Manager e o ID da conta de anúncios (`act_...`).
- **`google-ads.yaml`:** developer token, client ID e secret, refresh token e o ID do seu MCC.
- **`config.yaml`:** seus clientes, contas, IDs de conversão e padrões de budget.

Se faltar algum arquivo, a skill **para e avisa** o que preencher, em vez de inventar.

---

## 🛡️ Segurança
- Nada é executado nas contas de anúncio sem o seu **GO** explícito.
- Tudo que é criado nasce **PAUSED** e passa primeiro por um dry-run.
- Os tokens nunca aparecem nas respostas.
- Nunca faça commit dos seus `.yaml` preenchidos (já estão no `.gitignore`).

---

## 📁 Estrutura
```
agencia-trafego-pago/
├── .claude-plugin/         plugin.json + marketplace.json
├── skills/
│   ├── agencia-trafego-pago/       skill-mãe
│   ├── trafego-inteligente/        subfuncionamento Meta + Google Ads
│   └── agencia-reunida-modo-god/   subfuncionamento conselho de decisão
├── agents/                 7 agentes da equipe Tráfego Inteligente
├── config/                 modelos de credenciais
└── dist/                   .zip prontos para o claude.ai
```

---

## 👤 Autor

**Bruno Israel Gonçalves**
🌐 [agenciatrafegopago.com.br](https://agenciatrafegopago.com.br) · [agenciasdetrafego.com.br](https://agenciasdetrafego.com.br)

## ⚖️ Licença
All Rights Reserved © Bruno Israel Gonçalves. Ver [LICENSE.md](LICENSE.md).

---

🌐 **Agenciatrafegopago.com.br**
