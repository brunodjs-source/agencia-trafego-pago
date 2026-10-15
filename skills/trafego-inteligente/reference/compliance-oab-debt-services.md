# Compliance OAB Provimento 205/2021 + Debt Services Google/Meta

> Aplicar em TODO criativo + copy + Lead Form de qualquer cliente ClienteA-like (jurídico bancário empresarial)

## OAB Provimento 205/2021 — palavras PROIBIDAS

### Auto-engrandecimento
- ❌ "Melhor", "líder", "renomado", "referência"
- ❌ "Especialista" sem certificado OAB
- ❌ "Expert", "experts"

### Promessas
- ❌ "Garantia", "garantido", "100%", "garantimos"
- ❌ "Resultado garantido", "vitória garantida"
- ❌ "Recupere seu dinheiro"
- ❌ "Ganhe a ação"

### Gratuidade
- ❌ "Grátis", "gratuito", "sem custo"
- ❌ "Consulta gratuita", "consultoria gratuita"
- ❌ "Sem compromisso" (cinza — Meta não rejeita mas OAB tem precedente TED-MG 2025)

### Promessas de tempo
- ❌ "24h", "imediato", "instantâneo"

### Valores
- ❌ Citar honorários
- ❌ Descontos
- ❌ Formas de pagamento

## OAB — palavras PERMITIDAS

- ✅ "Atuação em [área]"
- ✅ "Análise técnica de [tema]"
- ✅ "Inscritos na OAB-[seccional]"
- ✅ "Atendimento por [canal]" (sem prazo)
- ✅ Citar **temas jurídicos**, **leis**, **súmulas**, **jurisprudência**:
  - Súmula 297 STJ (CDC aplica-se a instituições financeiras)
  - Súmula 379 STJ (limite 12% revogado pós-1994)
  - Tema 246 STJ (capitalização mensal de juros)
  - CDC Art. 51 (cláusulas abusivas)
- ✅ Citar **cidade**, **área**, **anos de inscrição** (fato verificável)

### ⚠ ATENÇÃO TEMA 1.061 STJ

Tema 1.061 trata de **autenticidade de assinatura em contrato bancário com consumidor PF** (REsp 1.846.649/MA, jul. 24/11/2021). **NÃO é tese de revisão PJ**.

NÃO usar em qualquer criativo PJ do ClienteA. Substituir por Súmula 297/379 ou Tema 246.

## Google Ads — Debt Services (Brasil)

Ativa em headline/desc com termos de gestão de dívida. Sem certificação G2 Risk Solutions, ad fica APPROVED LIMITED ou bloqueado.

### Termos que ATIVAM
- `renegociação` / `renegocie` / `renegociar`
- `dívida` / `endividad` (cinza — depende contexto)
- `reduza` / `reduzir`
- `quitar` / `quitação` / `liquidação` / `liquidar`
- `gestão de dívida` / `alívio de dívida`
- `saldo devedor`

### Substitutos (mesmo sentido, NÃO ativam)

| ❌ Aciona | ✅ Equivalente |
|---|---|
| Renegociação Bancária | Atuação em Direito Bancário |
| Renegocie Sua Dívida | Discuta Seu Contrato |
| Empresa Endividada | Empresa em Dificuldade / em Disputa Bancária |
| Reduza Dívida | Análise Contratual |
| Recupere Capital | Proteja Capital |
| Análise da Dívida | Análise Contratual |
| Estratégia para Endividado | Estratégia Empresarial |
| Renegocie com Banco | Conteste com Banco |

## Meta Ads — Debt Services

Mesma regra do Google + extra:
- Algoritmo Meta transcreve áudio de vídeo desde 2024 — palavra falada vale como copy
- Lead Form com pergunta "valor da dívida?" é OK (não ativa classifier)
- Headline "reduzir dívida" reprova
- Headline "revisão contratual bancária" passa

## Certificação Google G2 Risk Solutions (gratuita, 5 dias)

Pra escritório jurídico bancário:
1. Formulário G2: https://greenlight.g2netview.com/applications/new?country=BR&locale=pt-BR
2. Documentos: CNPJ, OAB do escritório, OAB sócios, comprovante endereço
3. Aprovação → código → enviar Google https://support.google.com/google-ads/contact/google_ads_financial_services_verification
4. Custo: R$ 0 | Prazo: 5 dias úteis
5. Contato dúvidas: financialservicesverification@g2llc.com

## Auditoria automática (checklist do Plan)

Antes do GO gestor na Fase 3, varrer todos os textos propostos (Lead Form, copy, headline) contra esta lista. Reportar TODA palavra-gatilho encontrada com sugestão de substituto.

```python
PROIBIDOS = ['garanti','melhor','grátis','gratuit','100%','renegoci','reduza','reduzir','quitar','recupere','especialista','consulta grátis','sem custo','Tema 1.061']
def auditar(texto):
    return [p for p in PROIBIDOS if p.lower() in texto.lower()]
```

Versão doc: 1.0 · 2026-05-29
