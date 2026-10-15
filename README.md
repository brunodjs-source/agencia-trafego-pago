# 🏢 Agência Tráfego Pago — Plugin para Claude

Plugin com a skill-mãe **Agência Tráfego Pago** e dois subfuncionamentos:

| Módulo | O que faz | Como chamar |
|---|---|---|
| 🏢 **Agência Tráfego Pago** (mãe) | Cria uma agência completa (14–20 subagentes) para qualquer nicho | `Agência tráfego pago, crie uma agência pra [nicho]` |
| 🎯 **Tráfego Inteligente** | Auditoria + plano + execução de Meta Ads e Google Ads em 5 fases, com pausa obrigatória para o seu GO | `equipe TI [cliente]` · `TI google [cliente]` · `TI full [cliente]` |
| 🧠 **Agência Reunida Modo GOD** | Conselho de estrategistas que vota, debate e entrega um Decision Brief | `modo god` · `agência reunida` · `chama o conselho` |

Inclui os 7 agentes da equipe Tráfego Inteligente: Recon, Trace, Probe, Scout, Plan, Volt e Buck.

## 📦 Instalação

### Claude Code
```
/plugin marketplace add brunodjs-source/agencia-trafego-pago
/plugin install agencia-trafego-pago@agencia-trafego-pago
```

### claude.ai (app/web)
Compacte cada pasta dentro de `skills/` em um `.zip` e envie em **Settings → Capabilities → Skills → Upload skill**.

## 🔑 Suas credenciais (cada usuário usa as próprias)

Nada de credencial vem no plugin. Crie a pasta `~/.agencia-trafego-pago/` e copie os modelos:

```bash
mkdir -p ~/.agencia-trafego-pago
cp config/config.exemplo.yaml      ~/.agencia-trafego-pago/config.yaml
cp config/meta-ads.exemplo.yaml    ~/.agencia-trafego-pago/meta-ads.yaml
cp config/google-ads.exemplo.yaml  ~/.agencia-trafego-pago/google-ads.yaml
```

Preencha com seus tokens, MCC e contas. As skills leem dali — se faltar algo, elas param e avisam.

## 🛡️ Segurança
- Tudo que é criado nas contas de anúncio nasce **PAUSED**.
- Nenhuma execução acontece sem o seu **GO** explícito.
- Nunca faça commit dos seus `.yaml` preenchidos (já estão no `.gitignore`).

## ⚖️ Licença
All Rights Reserved © Bruno Israel Gonçalves — ver [LICENSE.md](LICENSE.md).

---

🌐 **Agenciatrafegopago.com.br**
