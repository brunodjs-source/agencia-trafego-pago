# Orquestração paralela — como o Cérebro dispara muitas instâncias

O Cérebro produz os "milhares de agentes" disparando **instâncias em paralelo**
dos papéis do catálogo. Em Claude Code isso se faz com a ferramenta **Agent**
(subagentes) e, para fan-out determinístico e grande, com a ferramenta
**Workflow** (quando o gestor optou por orquestração multiagente).

## Regra de ouro do paralelismo

> Paralelize o que é **independente**. Serialize o que tem **dependência**.

- Independente → roda junto: pesquisas de nicho, revisões por dimensão,
  construção de módulos desacoplados, verificação de achados.
- Dependente → roda em ordem: contrato de API **antes** de frontend/backend;
  schema de dados **antes** de queries; build **antes** de deploy.

## Padrões de fan-out

### 1. Conselho (decisão)

```text
N estrategistas independentes  →  votam às cegas  →  debate só onde o voto racha
→ presidente sintetiza → verificação adversarial (3 lentes) → red team → brief
```

- Rápido: 3–5 cadeiras, sem debate.
- Padrão: 7–10 cadeiras, debate nas divergências.
- GOD: 12–16 cadeiras, 5 juízes, debates com defensor/opositor/moderador,
  verificação em 3 lentes, crítico de lacunas.

### 2. Construção por módulo (execução)

```text
para cada módulo independente (em paralelo):
    arquiteto define contrato → construtor implementa → testador valida
depois (sequencial): integrador junta → verificador revisa de contexto limpo
```

### 3. Revisão por dimensão (verificação)

```text
em paralelo: {correção, segurança, performance, simplicidade, testes}
cada dimensão → revisor dedicado → cada achado → verificador adversarial
só sobrevivem os achados confirmados
```

## Esqueleto de workflow (pseudo)

Quando o gestor autorizou orquestração multiagente, o Cérebro pode usar um
script de Workflow. Padrão canônico (pipeline, cada dimensão verifica assim que
sua revisão termina):

```js
// Pseudocódigo ilustrativo do padrão. O script real é gerado conforme a tarefa.
const DIMENSOES = [
  { chave: 'correcao',   prompt: '...' },
  { chave: 'seguranca',  prompt: '...' },
  { chave: 'performance',prompt: '...' },
]
const resultados = await pipeline(
  DIMENSOES,
  d => agent(d.prompt, { label: `revisao:${d.chave}`, phase: 'Revisão', schema: ACHADOS }),
  revisao => parallel(revisao.achados.map(a => () =>
    agent(`Verifique de forma adversarial: ${a.titulo}`,
          { label: `verifica:${a.arquivo}`, phase: 'Verificação', schema: VEREDITO })
      .then(v => ({ ...a, veredito: v }))
  ))
)
const confirmados = resultados.flat().filter(Boolean).filter(a => a.veredito?.real)
return { confirmados }
```

> O script de workflow só roda quando o gestor pediu orquestração multiagente
> explicitamente. Fora disso, o Cérebro usa subagentes (Agent) com moderação e
> prefere fazer o trabalho barato inline.

## Controle de custo

- Cada instância custa tokens. O Cérebro **não** dispara 1000 instâncias "porque
  pode" — dimensiona pelo valor da decisão e pelo orçamento.
- Decisão barata e reversível → poucas instâncias.
- Decisão cara/irreversível → mais instâncias + verificação adversarial.
- Sempre reporta ao gestor quantas instâncias pretende usar antes de um fan-out
  grande.

## Guarda-corpos

- Um subagente de verificação **nunca** é o mesmo que construiu o artefato.
- Entregar a um verificador o **artefato**, não a sua conclusão — senão ele só
  confirma o seu viés.
- Resultados de subagentes voltam resumidos; o Cérebro relata o que importa, não
  o despejo de arquivos.
