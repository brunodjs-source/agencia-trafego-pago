# Arquitetura do Cérebro

## Visão em uma frase

O Cérebro é um **orquestrador**: ele não constrói sozinho, ele **coordena** um
catálogo de papéis especializados, decide o que paralelizar, submete decisões
caras a um conselho adversarial, e verifica tudo antes de entregar.

## Camadas

```text
┌─────────────────────────────────────────────────────────────┐
│  CÉREBRO (skill `cerebro`)                                    │
│  Roteia a intenção → escolhe modo → monta o plano de fan-out  │
└───────────────┬───────────────────────────────┬──────────────┘
                │                               │
      ┌─────────▼──────────┐         ┌──────────▼─────────────┐
      │ CONSELHO            │         │ TIMES DE CONSTRUÇÃO     │
      │ (decisão)           │         │ (execução)              │
      │ presidente-sintese  │         │ frontend / backend /    │
      │ juízes + red team   │         │ infra / dados / ML      │
      └─────────┬──────────┘         └──────────┬─────────────┘
                │                               │
      ┌─────────▼───────────────────────────────▼─────────────┐
      │ CATÁLOGO DE PAPÉIS                                      │
      │ • agents/ deste plugin (roster próprio)                │
      │ • ECC: 71 agentes + 314 skills (se instalado)          │
      │ • REA: engenharia reversa autorizada (se instalado)    │
      └────────────────────────────────────────────────────────┘
                │
      ┌─────────▼──────────┐
      │ VERIFICAÇÃO +       │
      │ SEGURANÇA           │
      │ (adversarial +      │
      │ autorizada)         │
      └────────────────────┘
```

## Os dois números (de novo, porque é o mal-entendido central)

- **Papéis ≈ 430.** É o catálogo de *tipos* de agente disponíveis. Finito e
  versionado.
- **Instâncias = centenas a milhares.** É quantas *execuções* o Cérebro dispara.
  Uma decisão GOD pode usar 14 estrategistas + 5 juízes + 10 debates; uma
  construção pode usar N construtores (um por módulo) rodando ao mesmo tempo.

Prometer "1000 agentes únicos" é vender contagem de arquivo. O que entrega
resultado é: **papéis certos × instâncias paralelas × verificação**.

## Princípios de projeto

1. **Contexto limpo para verificar.** Quem verifica não é quem construiu, e
   verifica a partir de contexto novo — para não herdar o viés de quem fez.
2. **Paralelo onde é independente, sequencial onde há dependência.** O Cérebro
   paraleliza módulos independentes e serializa onde um depende do outro
   (contrato de API antes de frontend e backend, por exemplo).
3. **Para em decisão cara.** Stack, arquitetura, build-vs-buy, escopo, verba,
   deploy em produção: tudo passa por GO explícito.
4. **Evidência > opinião.** Toda recomendação carrega a evidência e o nível
   dela, a faixa de incerteza, e o dado que mudaria a decisão.
5. **Segurança autorizada.** Nenhuma ação ofensiva fora de escopo autorizado
   (ver `politica-de-seguranca-autorizada.md`).

## Degradação graciosa

- **Sem ECC/REA instalados:** usa o roster de `agents/` e avisa quais
  capacidades extras ganharia instalando-os.
- **Sem a ferramenta Workflow/Agent (fan-out):** executa o pipeline de forma
  sequencial, com menos instâncias, e diz ao gestor que está em modo reduzido.
- **Orçamento de tokens apertado:** cai para o modo Rápido e reduz o número de
  instâncias, preservando as fases de decisão e verificação.
