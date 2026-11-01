# 🧠 Cérebro Multiagente

> Orquestrador que recebe uma intenção (ou uma referência) e entrega um **sistema completo** — do discovery ao deploy — coordenando um **conselho de decisão adversarial**, **times de construção full-stack** (frontend, backend, infra, dados, ML) e uma camada de **auditoria de segurança autorizada**.
>
> Criado por **Bruno Israel Gonçalves** · 🌐 [agenciatrafegopago.com.br](https://agenciatrafegopago.com.br)

---

## O que é (e o que não é)

**É:** um "cérebro" que coordena muitos agentes especializados para planejar, construir e auditar software. Ele fica mais forte quando o **[ECC](https://github.com/affaan-m/ECC)** (71 agentes, 314 skills) e o **[REA](https://github.com/morluto/rea)** (engenharia reversa) estão instalados ao lado, mas funciona sozinho com o roster próprio em `agents/`.

**Não é** uma máquina "impossível de parar", "sem erro" ou "sem crítica". Isso não existe em software:

- A **crítica** (red team, verificação adversarial, crítico de lacunas) é o que torna a decisão boa — o Cérebro a **reforça**, não a remove.
- O Cérebro **para de propósito** em decisões caras ou irreversíveis e espera o seu GO.
- Ele **reduz e explicita** o risco; não promete zerá-lo.

### De onde vêm os "milhares de agentes"

| Conceito | Número | O que é |
|---|---:|---|
| **Papéis (tipos)** | ~430 | 71 agentes + 314 skills do ECC + skills do REA + o roster próprio deste plugin |
| **Instâncias (execuções)** | centenas a milhares | O Cérebro dispara muitas cópias dos papéis **em paralelo** sobre o mesmo problema |

Os "milhares" são **instâncias em paralelo** sobre algumas centenas de papéis — não milhares de arquivos. O que entrega resultado é a orquestração, não a contagem de arquivo.

---

## O pipeline

```text
Discovery → Decisão (conselho) → Projeto → Construção paralela →
Verificação adversarial → Auditoria de segurança autorizada → Entrega → Aprendizado
```

A skill `cerebro` descreve cada fase, quem lidera e onde o Cérebro para e espera GO. Veja `skills/cerebro/SKILL.md` e as referências em `skills/cerebro/references/`.

---

## Instalação

Este plugin funciona sozinho, mas foi feito para **potencializar o ECC e o REA**. Instale os três.

### 1. Este plugin (Cérebro)

No Claude Code:

```text
/plugin marketplace add brunodjs-source/cerebro-multiagente
/plugin install cerebro-multiagente@cerebro-multiagente
```

### 2. ECC — catálogo de 71 agentes + 314 skills (opcional, recomendado)

> Só canais oficiais. Fonte: [github.com/affaan-m/ECC](https://github.com/affaan-m/ECC).

```text
/plugin marketplace add affaan-m/ECC
/plugin install ecc@ecc
```

Ou pelo instalador guiado (precisa de Node 18+):

```bash
npx ecc-universal@2.2.3 setup
```

### 3. REA — engenharia reversa autorizada (opcional)

> Só canais oficiais. Fonte: [github.com/morluto/rea](https://github.com/morluto/rea) · pacote npm `rea-agents`.

```text
/plugin marketplace add morluto/rea
/plugin install rea@rea
```

Ou: `npx rea-agents setup`. O REA respeita o próprio `DISCLAIMER.md`: análise **lícita e autorizada** apenas.

Reinicie o Claude Code depois de instalar.

> **App Claude / claude.ai (sem terminal):** os comandos `/plugin ...` só rodam no Claude Code. No app, suba as skills de `skills/` por **Settings → Capabilities → Skills → Upload skill** (uma por vez, sem descompactar os `.zip` de `dist/`). O ECC e o REA têm os próprios fluxos de instalação — veja os repositórios deles.

---

## Como usar

```text
cérebro, constrói pra mim um sistema de agendamento pra clínicas, do zero ao deploy
```

```text
monta o time e me dá o plano completo de um painel de BI com login e billing
```

```text
cérebro, tenho autorização por escrito pra auditar o sistema X — faça o threat model e o pentest no escopo
```

Sem modo dito, o Cérebro usa o **Padrão**: Discovery → Conselho só nas decisões caras → Projeto (para e espera GO) → Construção → Verificação → Segurança → Entrega.

---

## 🛡️ Segurança é autorizada por padrão

O Cérebro **constrói** segurança o tempo todo (código seguro, threat model, auditoria do próprio sistema). Mas qualquer ação que **teste, sonde ou faça engenharia reversa** de um sistema passa por um portão de autorização:

- **Livre:** sistemas que o próprio Cérebro construiu e artefatos que você comprovadamente possui.
- **Exige autorização registrada:** alvos de terceiros (clientes, apps, produtos) — escopo por escrito, alvo, janela e limites. As skills `auditoria-seguranca-autorizada` e `engenharia-reversa-autorizada` **param e pedem esse registro**.
- **Sempre recusado:** acesso não autorizado, roubo de credenciais, malware, DoS, burla de autenticação/DRM/licença sem permissão, evasão de detecção para fins maliciosos, e cópia de software proprietário.

Construir funcionalidade equivalente de forma **independente e autorizada** é interoperabilidade. Copiar código/assets proprietários ou burlar proteção não é — e o Cérebro recusa. Veja `skills/cerebro/references/politica-de-seguranca-autorizada.md`.

---

## Estrutura

```text
cerebro-multiagente/
├── .claude-plugin/              plugin.json + marketplace.json
├── skills/
│   ├── cerebro/                 orquestrador (o "cérebro") + referências
│   ├── construtor-fullstack/    construção de sistemas completos
│   ├── auditoria-seguranca-autorizada/   auditoria defensiva + pentest autorizado
│   └── engenharia-reversa-autorizada/    REA sob escopo autorizado
├── agents/                      roster próprio de papéis especializados
├── config/                      modelos (autorização de pentest etc.)
└── dist/                        .zip prontos pro claude.ai
```

---

## Créditos

- **[ECC](https://github.com/affaan-m/ECC)** — Affaan Mustafa (MIT). Catálogo de agentes, skills e padrões de harness.
- **[REA](https://github.com/morluto/rea)** — morluto (MIT). Engenharia reversa multialvo via MCP.

Este projeto é original e **depende** desses dois como plugins instalados ao lado; não redistribui o código deles.

## Licença

MIT © Bruno Israel Gonçalves. Veja [LICENSE](LICENSE).

---

🌐 **Agenciatrafegopago.com.br**
