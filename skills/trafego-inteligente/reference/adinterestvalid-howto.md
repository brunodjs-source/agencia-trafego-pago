# Como usar `/search?type=adinterestvalid` — o endpoint que salva vidas

> O endpoint que descobrimos tardio na sessão ClienteA (deveria ser primeiro passo de toda criação). Distingue interesse VIVO de interesse MORTO pós-purga Andromeda 15/01/2026.

## Por que esse endpoint é OURO

- `/search?type=adinterest&q=X` retorna interesse **mesmo morto** (existe no banco mas bloqueado)
- `/search?type=adinterestvalid&interest_list=[...]` é o ÚNICO que diz se ainda RODA em campanha

Sem ele:
- Você cria Ad Set com 10 interesses, paga budget, mas Meta entrega zero porque 8/10 estão "soft-deprecated"
- Pior: pode dar erro `1870247` na criação Ad Set e quebrar pipeline

## Como chamar

```python
import requests, json
r = requests.get('https://graph.facebook.com/v22.0/search', params={
    'type': 'adinterestvalid',
    'interest_list': json.dumps(['Sebrae', 'Cyrela', 'Agronegócio', 'Toyota Hilux']),
    'access_token': TOKEN
})
data = r.json()['data']
# data[0] = {'name': 'Sebrae', 'valid': False, 'audience_size': 0}
# data[3] = {'name': 'Toyota Hilux', 'valid': True, 'audience_size': 37051139}
```

## Regras

1. **Batch máximo 25 itens** por chamada (limite Meta)
2. **Por NOME, não ID** — passa lista de strings de nomes
3. **Retorna na MESMA ORDEM** que enviou
4. Sempre incluir `access_token`

## Quando chamar

- ✅ Antes de toda criação/edição Ad Set
- ✅ Diariamente em campanhas ativas (cron job)
- ✅ Em audit semanal (Trace fase 5)

## Quando NÃO chamar

- Pra interesses cached < 24h (acabou de validar)
- Pra termo super-genérico (já sabe que é válido)

## Workflow integrado (Probe + Volt)

```
1. Probe descobre interesse via /adinterest?q=X (encontra)
2. Probe valida via /adinterestvalid (filtra mortos)
3. Probe salva JSON: {id, name, valid:true, validated_at:NOW}
4. Volt aplica via POST /<adset_id> (já garantido válido)
5. Trace re-valida em 7 dias (cron)
6. Se Trace acha interesse morto em campanha ativa, alerta gestor
```

## Erros típicos

| Erro | Causa | Fix |
|---|---|---|
| `valid: false` pra ID antigo | Meta tirou desde última validação | Buscar substituto via /adinterest com termo similar |
| Batch > 25 itens rejeitado | Limite endpoint | Quebrar em batches de 25 |
| `OAuthException 100` invalid keys | Passou "interest_list" sem JSON | Sempre `json.dumps(list)` |
| Endpoint não retorna nada | Token sem permissão `ads_read` | Confirmar System User permissions |

## Caso real (sessão ClienteA mai/2026)

Manhã: validei 13 IDs agro com `/adinterest` (todos apareceram).
Tarde: validei mesmos 13 IDs com `/adinterestvalid` → **9 morreram em 6h**:
- Sebrae morto
- Canal Rural morto
- Globo Rural morto
- AGCO morto
- Case IH morto
- Aprosoja morto
- Plano Safra morto
- BB Agro morto
- Conselho OAB morto

Sobreviveram só: Agronegócio, Pecuária, Maquinário agricultura, Deere & Company (ID novo).

**Lição cravada**: Meta tira interesses de hora em hora. Re-validar SEMPRE.

Versão doc: 1.0 · 2026-05-29
