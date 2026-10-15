# templates/compliance_checklist — Auditoria de Compliance por Nicho

Toda agência criada pela skill passa por esta checklist ANTES de ser entregue.

## Matriz de Compliance Brasil

| Nicho | Órgão | Lei/Resolução | Risco se descumprir |
|---|---|---|---|
| **Advocacia** | OAB | Provimento 205/2021 | Suspensão OAB do sócio |
| **Saúde / Clínicas** | CFM + ANS | Resolução CFM 1.974/2011 | Multa + cassação CRM |
| **Suplementos** | ANVISA + CONAR | RDC 243/2018 | Recall + multa |
| **Financeiro / Crédito** | Bacen + CDC | Resolução 4.658 + Lei 8.078 | Multa Bacen + ação cível |
| **Educação** | MEC (cursos regulamentados) | Lei 9.394/1996 | Suspensão curso |
| **Alimentação infantil** | CONANDA + CONAR | Resolução 163/2014 | Multa + retirada ad |
| **Bebidas alcoólicas** | CONAR + ANVISA | RDC 16/2014 | Restrição horário publicidade |
| **Imóveis** | CRECI | Lei 6.530/1978 | Multa CRECI |
| **Influenciador** | CONAR | Guia CONAR 2021 | Cancelamento publicidade |
| **Apostas/Bets** | SECAP + Bacen | Lei 14.790/2023 | Bloqueio +R$ 500k multa |
| **Cripto/Web3** | CVM + Bacen | Resolução CVM 175/2022 | Sanção CVM |
| **Plano de saúde** | ANS | RN 195/2009 | Multa ANS |
| **Cigarro/Cannabis** | ANVISA + RDC | RDC 14/2012 | Proibição total publicidade |
| **Pet** | (geralmente livre) | Lei do Bem-Estar Animal | Multa CRMV se vet |
| **Imigração** | OAB (advisores migração) | (caso a caso) | Variável |

## Checklist por agência criada

### Step 1 — Identificar regulamentações aplicáveis

Cruzar nicho do briefing com matriz acima. Listar TODAS as regulamentações:
- Primária (afeta diretamente — ex: ClienteF suplementos → CONAR + ANVISA)
- Secundária (afeta tangencialmente — ex: ClienteF vendendo via Mercado Livre → políticas ML)
- Terciária (de plataforma — ex: Meta Ads policies, Google Ads policies)

### Step 2 — Listar termos PROIBIDOS

Pra cada regulamentação, listar palavras/expressões vetadas. Exemplo (suplementos):
- ❌ "Cura"
- ❌ "Trata"
- ❌ "Previne doença"
- ❌ "Substitui medicamento"
- ❌ "Aprovado por médicos" (sem CRM)
- ❌ "Garantido em X dias"

### Step 3 — Listar substitutos PERMITIDOS

Pra cada termo proibido, dar alternativa segura. Exemplo:
- ❌ "Cura ansiedade" → ✅ "Apoia o equilíbrio emocional como parte de uma rotina saudável"
- ❌ "Reduza dívida" → ✅ "Análise contratual" (advocacia)
- ❌ "Garantia 100%" → ✅ "Política de troca em 7 dias (CDC art. 49)"

### Step 4 — Injetar nos agentes da agência

Cada agente criativo (Copywriter, Roteirista, Visual AI, Editor) recebe na seção "Compliance do Nicho" do seu system prompt:

1. Tabela completa de termos proibidos vs. permitidos
2. Lista de regulamentações com link pra texto legal
3. Avisos críticos ("usar este termo = APPROVED LIMITED no Google Ads")
4. Casos de multa recentes (precedentes do nicho)

### Step 5 — Crítico Criativo audita

O agent "Crítico de Criativo" da agência criada recebe checklist específica:

```
Pré-aprovação de qualquer peça:
[ ] Zero termos proibidos
[ ] Disclaimer presente (se aplicável)
[ ] Promessa de resultado: sem garantia, sem prazo específico
[ ] Antes/depois: tem "resultados podem variar"
[ ] Testimonials: tem consent escrito documentado
[ ] Comparação com concorrente: indireta (não nominal)
[ ] Avatar IA: declarado "ilustrativo" se aparecer
[ ] Voice clone: precisa consent escrito do dono da voz
[ ] B-roll: sem logos proibidos, sem propriedade intelectual de terceiros
[ ] Compliance plataforma: Meta + Google + TikTok policies
```

### Step 6 — Compliance Officer (extra condicional)

Pra nichos de alta complexidade regulatória (saúde, advocacia, financeiro, apostas, suplementos), gerar EXTRA agent específico:

- `oab-compliance-officer` (advocacia)
- `cfm-compliance-officer` (saúde)
- `bacen-compliance-officer` (financeiro)
- `anvisa-compliance-officer` (suplementos/cosmético)
- `secap-compliance-officer` (apostas)

Esse agent tem ÚNICA função: auditar tudo que sai da agência ANTES de ir pro cliente/plataforma.

## Output da auditoria

Arquivo `_BRIEFING-COMPLIANCE.md` na pasta da agência criada, contendo:

1. Regulamentações aplicáveis identificadas
2. Tabela termos proibidos/permitidos completa
3. Avisos críticos (escritos em CAPS + emoji 🚨)
4. Casos de multa recentes (precedentes)
5. Checklist final do Crítico Criativo

Esse arquivo é referenciado por **TODOS** os agents criativos da agência criada como leitura obrigatória.

## Casos canônicos pra referência

### Caso 1 — ClienteA (advocacia)
- ❌ "Renegocie sua dívida" (4 violações: OAB + Debt Services Google)
- ✅ "Análise técnica do seu contrato bancário (Tema 1.378 STJ)"

### Caso 2 — ClienteF (suplementos)
- ❌ "Whey Protein — Ganhe 5kg em 30 dias garantido"
- ✅ "Whey Protein — Fonte de proteínas que apoia recuperação muscular pós-treino. Combinado com alimentação equilibrada. *Este produto não substitui uma alimentação variada e equilibrada."

### Caso 3 — Clínica de Estética
- ❌ "Resultado garantido em 1 sessão de Botox"
- ✅ "Tratamento orientado pela Dra. {nome, CRM XXXXX}. Resultados podem variar conforme avaliação individual."

### Caso 4 — Curso de Trading
- ❌ "Aprenda a operar e ganhar R$ 5.000/mês"
- ✅ "Educação financeira aplicada à renda variável. Resultado depende de conhecimento + disciplina + capital + risco do mercado. Operações em renda variável envolvem perdas."

### Caso 5 — Plataforma Cripto
- ❌ "Compre Bitcoin e fique rico"
- ✅ "Plataforma autorizada CVM (Resolução 175/2022). Operações em criptoativos envolvem alta volatilidade. Risco de perda total do capital investido."

## ⚠️ Recurso obrigatório

Cada agência criada que tocar nicho regulado **DEVE incluir** disclaimer fixo nos materiais:

```
*Este conteúdo é informativo e não constitui {recomendação médica/jurídica/financeira específica}.
Resultados podem variar conforme análise individual. Consulte profissional habilitado antes de
tomar decisão. Operação sujeita a riscos. {Empresa/Cliente} é parceira credenciada de
{Órgão/Plataforma} sob CNPJ XX.XXX.XXX/XXXX-XX.
```
