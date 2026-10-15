# Meta Purge 2026 — Interesses descontinuados (Andromeda 15/01/2026)

> Atualizado: 2026-05-29 (sessão ClienteA — testado live via `adinterestvalid`)

## ⚠ Contexto

- **23/06/2025** Meta começou consolidação massa de interesses granulares
- **31/03/2025** Removida exclusões de detailed targeting em novos ad sets
- **15/12/2025** Bloqueio de SELEÇÃO de interesses descontinuados
- **15/01/2026** Ad Sets antigos com interesses mortos PARARAM DE RODAR
- **Andromeda IA** (out/2025 100% rollout) — virou creative-first, targeting = hint

## ❌ Interesses MORTOS confirmados (não usar)

### Jurídico / B2B Empresarial
- Sebrae (id 6003099842040) — morreu hoje (estava ok manhã)
- Empresário
- Empreendedorismo
- MBA
- Conselho Federal OAB (era 6003297737530)
- Bolsa de valores
- B3
- Business networking
- Valor Econômico
- FIESP / CIESP / ACSP / IBEF
- Mattos Filho, Demarest, BMA, Pinheiro Neto, Galvão Villani (marcas escritório)

### Agro
- Agronegócio (id 6002898157162) — morre/volta — REVALIDAR sempre
- Canal Rural (id 6003075125293) — invalid 15/01/2026
- Globo Rural (id 6003137334500) — invalid
- Aprosoja
- Famasul / Coopercitrus
- Plano Safra
- BB Agro / Banco do Brasil Agronegócio
- John Deere (id 6003232600533 — solta) — substituto: Deere & Company (id 6003092796617 — VÁLIDO)
- AGCO, Case IH, Massey Ferguson, New Holland (marcas)
- Festa do Peão de Barretos
- Pioneer Sementes / Syngenta Brasil
- Plantation, Family farming, No-till farming

### Incorporadora / Construção
- MRV Engenharia (id 6004139649906) — morto hoje
- Cyrela (id 6003092402016) — morto hoje
- Tenda
- Helbor Empreendimentos
- Eztec Empreendimentos
- Even Construtora
- Tecnisa
- Gafisa
- SECOVI / Sinduscon / ABRAINC / CBIC / Construbusiness
- Expo Revestir / Brain Inteligência Imobiliária
- Real Estate Investing (behavior)
- Engenharia civil
- Construction management
- Mortgage bank / Fixed-rate mortgage
- Imóveis (indústria) — id 6003578086487 (estava ok, morreu)

### Sinais riqueza
- Rolex (geralmente invalid)
- Louis Vuitton (designer) — só "marca de moda" às vezes válido
- Ferrari / Lamborghini / Porsche modelos (genéricos restantes)
- Bentley / Rolls-Royce
- Private bank / Private banking
- Bancos de investimento
- Gestão de fortunas (às vezes válida — REVALIDAR)
- Hedge fund
- Estate planning
- Resorts de luxo
- Cessna 172 (id 6003320087218) — morto, sugestão Meta: "Airplane" (id 6009200312620)

### Categorias inteiras consolidadas
- Veículos de mídia regional (Canal Rural, Globo Rural, revistas nicho)
- Associações setoriais (Aprosoja, FIESP, ABRAS)
- Marcas específicas B2B (John Deere, Case IH → "Agricultural Machinery")
- Bancos BR (BB Agro, Bradesco Empresas)
- Modelos de carro específicos → "SUVs", "Pickup truck"
- Health/medical (desde 2022)
- Política / religião / etnia / income específico (desde 2022)
- Detailed Targeting EXCLUSIONS (desde 31/03/2025)

## ✅ Interesses que SOBREVIVERAM (mai/2026)

### Categorias amplas
- Real Estate
- Property development (alguns IDs)
- Construction
- Civil engineering (alguns IDs)
- Architecture
- Small business
- Business owner (demographic — sob Demographics > Work)
- Job titles: CEO, Founder, Diretor, Sócio (sob Demographics > Work)
- Comportamentos: Real estate investors, Frequent business travelers

### Jurídico
- Escritório de advocacia (lei e serviços) — id 6003057724244

### Agro (validados 2026-05-29)
- Pecuária (id 6003135809808)
- Colheitadeira (id 6003224959745)
- Deere & Company (id 6003092796617) — NÃO o ID antigo 6003232600533
- Maquinário de agricultura (id 6003221195667)
- Ford F-Series (id 6002920759555)
- Toyota Hilux (id 6003143214166)
- Ram Trucks (id 6003498508425)
- Quarto de milha (id 6003174990849) — LASER cavalo
- Universidade de São Paulo (id 6003342653587) — proxy Esalq
- Fazenda, Agricultura (indústria), Gado

### Empresarial PJ (poucos sobreviveram)
- Pequenas e médias empresas (id 6003136069408)
- Pequena empresa (id 6002884511422)

### SUVs decisor B2B (top filtros riqueza)
- BMW X5 (id 6003218506076)
- Audi Q5 (id 6003280496610)
- Land Rover Range Rover (id 6003669147318)
- Porsche Cayenne (id 6003446343280)
- Patek Philippe & Co. (id 6003306942514)

### Imobiliário (5 sobreviveram dia 2026-05-28)
- Desenvolvimento imobiliário (id 6003332796032)
- Desenvolvedores de imóveis (id 6836751095756)
- Condomínio (id 6003435139283)
- Fundo de investimento imobiliário (id 6002975543459)

## 🔑 Regra de ouro

> **Nenhum ID acima é confiável por mais de 24h.** Meta tira IDs sem aviso. Sempre revalidar via `/search?type=adinterestvalid` ANTES de aplicar.

## Alternativas quando Meta sugere troca

Quando POST falha com `1870247` "deprecated_interest_id", Meta retorna `alternative_interest_id` no erro. SEMPRE usar a sugestão (já é o que sobreviveu).

Exemplos vistos:
- Cessna 172 → Airplane
- Festa Peão Boiadeiro → Rodeo (depende contexto)
- John Deere → Agricultural machinery

## Estratégia 2026 cravada

Se interesse virou loteria → **Advantage+ Audience + Custom Audience (CRM/CNPJ) + Geo cirúrgico + Criativo forte > Interesses específicos**.

Os interesses viram seed/hint. O algoritmo Andromeda acha quem converte pelo CRIATIVO + sinal de conversão (Lead Form qualificado).

Versão doc: 1.0 · 2026-05-29 (ClienteA)
