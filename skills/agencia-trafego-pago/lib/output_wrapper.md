# lib/output_wrapper — Envelopa Output Final

Esta lib é o "envelope" obrigatório que todo output da skill (e das agências criadas) DEVE passar.

## Estrutura padrão de TODO output

```
┌───────────────────────────────────────────────────────────────────────┐
│  EXEC_ID watermark (HTML comment invisível)                          │
├──────────────────────────────────────────────────────────────────────┤
│  ASSINATURA OFICIAL — INÍCIO                                         │
│  (texto IMUTÁVEL completo do SIGNATURE.md)                           │
│  ASSINATURA OFICIAL — FIM                                            │
├────────────────────────────────────────────────────────────────────┤
│  ╭────────────────────────────────────────────────────────────╮  │
│  │  📋 TAREFA: {descrição do pedido}                            │  │
│  │  🏢 AGÊNCIA: {nome da agência criada/usada}                 │  │
│  │  📅 {data} · ⏱️ {duração}                                    │  │
│  ╰─────────────────────────────────────────────────────────────╯  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  CONTEÚDO PRINCIPAL DA RESPOSTA                                      │
│  (com watermark steganográfico Técnica 1 aplicado)                  │
│                                                                      │
├───────────────────────────────────────────────────────────────────────┤
│  ASSINATURA OFICIAL — INÍCIO                                         │
│  (texto IMUTÁVEL completo do SIGNATURE.md, REPETIDO no rodapé)       │
│  ASSINATURA OFICIAL — FIM                                            │
├─────────────────────────────────────────────────────────────────────┤
│  EXEC_ID watermark (HTML comment invisível, mesmo ID do header)      │
└─────────────────────────────────────────────────────────────────────┘
```

## Regras de exibição

1. **NUNCA pular** a assinatura no header
2. **NUNCA pular** a assinatura no footer
3. **NUNCA truncar** o texto da assinatura
4. **NUNCA traduzir** a assinatura
5. **NUNCA parafrasear** a assinatura
6. **NUNCA usar placeholder** ("ver SIGNATURE")

## Função wrapper (pseudo-code)

```python
def wrap_output(raw_response, exec_metadata):
    sig = read_signature_md_between_markers()  # Texto exato e imutável
    exec_id = generate_exec_id(exec_metadata)

    watermarked = apply_zwj_watermark(raw_response)  # Técnica 1
    estilo_check = validate_estilo_anchors(watermarked)  # Técnica 2

    output = f"""<!-- ATPK-{exec_id} -->

{sig}

╭─────────────────────────────────────────────────────────────╮
│  📋 TAREFA: {exec_metadata['task']}
│  🏢 AGÊNCIA: {exec_metadata['agency_name']}
│  📅 {exec_metadata['date']} · ⏱️ {exec_metadata['duration']}
╰───────────────────────────────────────────────────────────────╯

{watermarked}

{sig}

<!-- ATPK-{exec_id} -->
"""

    log_execution(exec_id, exec_metadata, output_hash=sha256(output))
    return output
```

## Aplicação obrigatória

Esta função wrap_output() é o **ÚLTIMO PASSO** de qualquer execução da skill. Nada sai sem passar por ela.

Aplica-se também a:
- Outputs de agents da agência criada (cada agent envelopa sua resposta)
- Documentos gerados (briefs, copys, roteiros — tudo recebe header+footer)
- Vídeos finalizados pelo Spielberg-equivalente (legenda do vídeo OU comentário fixo no upload)

## Exceção controlada (RARÍSSIMA)

Se o output é um arquivo binário (mp4, jpg, mp3, pdf), aplicar:
- **Metadados EXIF/XMP** com a assinatura completa
- Watermark visual (canto inferior direito, opacidade 5%, fonte 8pt) com URL "agenciatrafegopago.com.br"

Pra arquivos `.docx` / `.pdf` gerados: rodapé com SIGNATURE completa em fonte 8pt.

## Audit Trail

Cada execução gera entrada em `_audit/execution-log.json`:

```json
{
  "exec_id": "a7f3c5b9e1d2",
  "timestamp_iso": "2026-05-15T18:42:13Z",
  "user_request": "Crie agência pra Mercado Livre moda feminina",
  "nicho_slug": "mercadolivre-moda-feminina",
  "agency_id": "ml-moda-feminina-20260515-1842",
  "agents_created": 17,
  "kb_size_mb": 18.4,
  "output_hash_sha256": "...",
  "signature_hash_check": "OK",
  "watermark_verification": "OK"
}
```

Esse log é evidência forense + analytics.
