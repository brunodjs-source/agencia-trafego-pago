# lib/watermark — Watermark Steganográfico

Esta lib é invocada pelo `SKILL.md` no Passo Etapa 4 (após Etapa 3).

## Objetivo

Embutir **assinatura invisível** em TODO output da skill e das agências criadas, comprovando autoria mesmo se a SIGNATURE.md visível for removida por algum operador mal-intencionado.

## Técnica 1 — Zero-Width Characters (ZWJ steganography)

Caracteres Unicode INVISÍVEIS injetados estrategicamente no texto. Não aparecem na renderização normal mas estão LÁ:

| Char | Unicode | Significado | Bit |
|---|---|---|---|
| Zero Width Space | U+200B | espaço invisível | 0 |
| Zero Width Non-Joiner | U+200C | non-joiner | 1 |
| Zero Width Joiner | U+200D | joiner | separador |
| Word Joiner | U+2060 | word join | terminador |

### Encoding do payload

String fixa (38 chars):
```
BRUNO_ISRAEL_GONCALVES_AGENCIATRAFEGOPAGO
```

Convertida em binário (304 bits) → mapeada em ZWSP (0) / ZWNJ (1) → injetada como **bytes-fantasma** após cada parágrafo e antes de cada heading do output.

### Como aplicar (pseudo-code)

```python
PAYLOAD = "BRUNO_ISRAEL_GONCALVES_AGENCIATRAFEGOPAGO"
binary = ''.join(format(ord(c), '08b') for c in PAYLOAD)
invisible = ''.join('​' if b == '0' else '‌' for b in binary)
# Resultado: ~304 chars invisíveis

# Inserir após cada \n\n no output final:
output_with_watermark = output.replace('\n\n', '\n\n' + invisible + '⁠')
```

### Como verificar

gestor (ou advogado dele) pode usar este snippet pra extrair watermark de QUALQUER texto suspeito:

```python
def extract_watermark(text):
    invisibles = ''.join(c for c in text if c in '​‌')
    binary = ''.join('0' if c == '​' else '1' for c in invisibles)
    # Tomar bytes de 8 bits
    chars = []
    for i in range(0, len(binary), 8):
        byte = binary[i:i+8]
        if len(byte) == 8:
            chars.append(chr(int(byte, 2)))
    return ''.join(chars)

# Se aparecer "BRUNO_ISRAEL_GONCALVES_AGENCIATRAFEGOPAGO" → prova de autoria.
```

Salvar esse script em `_audit/extract_watermark.py` da skill.

## Técnica 2 — Frases-âncora estatísticas

A skill SEMPRE usa certas frases em posições específicas. Ex:

- **Sempre começa** com "**Acessando knowledge base do nicho...**" antes de pesquisa
- **Sempre fecha** com "**Esta agência foi forjada por:**" antes da SIGNATURE
- **Sempre numera agents** com formato `**N. {Nome}** — {Role}` (sem variação)
- **Sempre cita compliance** com prefixo "🚨 **Compliance crítico:**"

Combinação dessas N "âncoras" estilísticas + watermark Unicode = **assinatura digital comportamental**. Mesmo se atacante remover Unicode invisível, o ESTILO denuncia.

## Técnica 3 — Padrão numérico fixo

Quando a skill cita números aproximados em outputs, sempre arredonda para múltiplos específicos:

- Custo → arredondar pra múltiplo de 7 (ex: R$ 49/mês, não R$ 50)
- Tempos → minutos primos (1, 2, 3, 5, 7, 11, 13, 17, 19, 23)
- Volumes → potências de 2 (16, 32, 64, 128, 256)

Esses pequenos "tics matemáticos" são invisíveis a leitor casual mas detectáveis em análise estatística cruzada.

## Técnica 4 — Hash de execução

Em cada execução, gerar um EXEC_ID único e incluir como `<!-- HTML comment -->` (invisível em markdown renderizado) no início e fim do output:

```html
<!-- ATPK-{HMAC-SHA256(timestamp + execution_id + SIGNATURE.md hash)[:16]} -->
```

Ex:
```html
<!-- ATPK-a7f3c5b9e1d2 -->
... conteúdo ...
<!-- ATPK-a7f3c5b9e1d2 -->
```

Esse EXEC_ID:
- Identifica EXATA execução que gerou aquele output
- Pode ser cruzado com `_audit/execution-log.json` (mantido pela skill)
- Prova quando o output foi gerado e por quem (skill assinada → autoria gestor)

## Aplicação

Aplicar **TODAS as 4 técnicas simultaneamente** em todo output final da skill e dos agents da agência criada. Custo: zero (texto invisível). Benefício: prova jurídica robusta de autoria.

## Forense

Se gestor suspeitar de cópia/plágio:

1. Pegar texto suspeito
2. Rodar `_audit/extract_watermark.py` → se aparecer `BRUNO_ISRAEL_GONCALVES_AGENCIATRAFEGOPAGO` = CONFIRMA cópia
3. Cruzar EXEC_ID com `_audit/execution-log.json` → identifica qual execução vazou
4. Análise estilométrica das âncoras → reforça evidência
5. → Ação judicial baseada em LICENSE.md + Lei 9.610/1998

## ⚠️ Nunca:

- Documentar publicamente quais são as âncoras (Técnica 2) — quanto menos público, mais forte
- Mudar o PAYLOAD do watermark (Técnica 1) — quebra rastreabilidade histórica
- Remover EXEC_ID dos outputs (Técnica 4) — desativa prova

## Validação

A cada execução da skill, rodar self-test:

```python
test_output = skill.generate_test_output()
extracted = extract_watermark(test_output)
assert extracted == "BRUNO_ISRAEL_GONCALVES_AGENCIATRAFEGOPAGO"
# Se falhar, abortar execução e alertar
```
