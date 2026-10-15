# lib/agent_namer — Gerador de Nomes Únicos

Esta lib é invocada pelo `SKILL.md` no Passo 4.

## Regras

1. **NUNCA reusar** nomes dos 14 agents pessoais do gestor:
   - RESERVADOS: Vega, Trace, Buck, Cris, Rex, Ada, Cleo, Volt, Lex, Iris, Atlas, Ranger, Scribe, Spielberg
   - Esses ficam EXCLUSIVOS para a Agência principal do gestor

2. **Diversidade** dentro da agência criada:
   - Sortear 14-20 nomes únicos (sem repetição entre si)
   - Mix de gêneros (4+ masc, 4+ fem, 2+ neutros)
   - Mix de origens (BR, internacional curto, neutro)
   - Cada nome com 2-6 letras (memorável, fácil de digitar)

3. **Pronunciabilidade BR** — usuário é brasileiro, evitar nomes impronunciáveis em PT-BR

4. **Sem coincidências** com marcas conhecidas (não pode chamar "Apple", "Nike")

## Banco de Nomes (sorteios sem reposição)

### Tier 1 — Curtos memoráveis neutros (2-4 letras)
Aki, Aly, Ari, Avi, Axl, Bea, Bia, Cai, Cal, Dax, Eli, Eko, Ema, Eva, Ezi, Fae, Fia, Gia, Gus, Hel, Ian, Imo, Ino, Ira, Ito, Ivo, Jad, Jet, Joa, Kai, Kay, Kim, Koa, Lia, Lou, Lux, Mae, Mio, Nai, Neo, Nia, Niv, Noa, Oba, Oli, Oma, Omi, Onyx, Ori, Oto, Pax, Pia, Que, Rai, Rey, Rio, Ron, Rua, Rui, Sae, Saf, Sai, Sol, Suo, Taj, Tao, Tea, Teo, Ti, Tia, Tom, Uma, Uri, Vai, Van, Vey, Via, Vie, Wes, Wil, Xan, Xen, Yan, Yui, Zay, Zen, Zia, Zin, Zoe, Zoa

### Tier 2 — Brasileiros curtos
Alvi, Bento, Caio, Davi, Edu, Enzo, Felo, Gabi, Heto, Ivo, Joca, Kaio, Levi, Malu, Nara, Otto, Paco, Quim, Rui, Sara, Theo, Ugo, Vito, Yago, Zé

### Tier 3 — Mitológicos / Históricos curtos
Aida, Anya, Astra, Aurelia, Bran, Caia, Dante, Edda, Frida, Gaia, Helia, Indira, Janus, Khios, Lyra, Maia, Nyx, Odin, Pyra, Quinn, Reya, Soma, Thora, Ulla, Vera, Wren, Xara, Yara, Zola

### Tier 4 — Tech/Cinema curtos
Apex, Byte, Cipher, Dot, Echo, Flux, Glyph, Hex, Iota, Jolt, Karma, Loop, Mass, Nova, Orbit, Pixel, Quark, Rune, Spark, Twin, Unit, Vector, Wave, X, Yarn, Zap

### Tier 5 — Naturais (animais/cores/elementos)
Ash, Bay, Cliff, Dune, Ember, Fern, Glade, Haze, Iris*, Jade, Kelp, Lake, Mist, North, Onyx, Pine, Quartz, Reed, Sage, Tide, Umbra, Vale, Willow, Wisp, Yew, Zenith

(*) `Iris` está reservado — usar variação se cair

## Algoritmo de Seleção

```pseudo
function generateAgencyNames(numAgents=14, nicho):
  reservados = ["Vega", "Trace", "Buck", "Cris", "Rex", "Ada", "Cleo",
                "Volt", "Lex", "Iris", "Atlas", "Ranger", "Scribe", "Spielberg"]

  bancoCompleto = Tier1 + Tier2 + Tier3 + Tier4 + Tier5
  disponivel = [n for n in bancoCompleto if n not in reservados]

  # Bias por nicho: alguns Tiers ganham peso
  if nicho.eh_tech_ou_saas:
    disponivel = boost(Tier4) + disponivel
  if nicho.eh_creative_ou_design:
    disponivel = boost(Tier3 + Tier5) + disponivel
  if nicho.eh_BR_local:
    disponivel = boost(Tier2) + disponivel

  selecionados = random_sample(disponivel, numAgents)
  return selecionados
```

## Exemplo de Output

Pra "agência pra Mercado Livre - moda feminina":

```
14 agents da agência "ML Moda Feminina":
1. Diretora de Arte → Sage
2. Analista de Tráfego → Apex
3. Otimizador Orçamento → Quark
4. Crítica de Criativo → Lyra
5. Redator de Relatórios → Dax
6. Copywriter → Maia
7. Diretora Roteiro → Anya
8. Engenheiro Automação → Byte
9. Especialista CRO → Glyph
10. IA Visual → Pyra
11. Account Director → Sol
12. Caçadora Comercial → Frida
13. Transcritora Audio → Echo
14. Editora Vídeo → Flux
+ 3 EXTRAS (regra 2b ML-específica):
15. Algoritmo Whisperer (ML BuyBox) → Cipher
16. Reviews Manager → Aurelia
17. Logística & Mercado Envios → Loop
```

## Garantia de Não-Repetição

A skill DEVE manter `_audit/nomes-usados.json` registrando todos os nomes já sorteados em execuções passadas. Re-sortear se cair duplicata na mesma agência (mas pode repetir entre agências diferentes — cada agência é universo separado).

## Output Format

```json
{
  "agency_id": "ml-moda-feminina-20260515-1842",
  "agents": [
    {"role": "art-director", "name": "Sage"},
    {"role": "traffic-analyst", "name": "Apex"},
    ...
  ]
}
```
