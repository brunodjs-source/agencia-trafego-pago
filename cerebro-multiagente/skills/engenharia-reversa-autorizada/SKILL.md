---
name: engenharia-reversa-autorizada
description: Engenharia reversa do Cérebro, via REA, SOMENTE sob direito de analisar o alvo (posse, autorização por escrito, CTF/pesquisa, ou interoperabilidade lícita). Entende o QUE um sistema/referência faz — comportamento, fluxos, modelo aparente — com evidência e limitações, para que os times construam uma implementação ORIGINAL. Use quando o gestor aponta uma referência e quer um sistema equivalente autorizado, ou quer entender um artefato que tem direito de analisar. NUNCA reproduz código/assets proprietários, NUNCA burla DRM/licença/autenticação, NUNCA analisa alvo sem direito. Espelha o DISCLAIMER.md do REA.
metadata:
  display_name: Engenharia Reversa Autorizada
  version: "0.1.0"
---

# 🔍 Engenharia Reversa Autorizada

Subfuncionamento do **Cérebro**. Usa o **[REA](https://github.com/morluto/rea)**
quando instalado (ver `cerebro/references/integracao-rea.md`). A política em
`cerebro/references/politica-de-seguranca-autorizada.md` manda nesta skill.

## O portão (antes de tocar em qualquer alvo)

A skill só prossegue se pelo menos uma for verdadeira — **e grava qual no
output**:

- O artefato foi **construído pelo Cérebro** ou é **comprovadamente do gestor**.
- Há **autorização por escrito** do dono do alvo (escopo + janela).
- É **CTF / pesquisa / aprendizado** sobre alvo que o gestor tem direito de analisar.
- É **interoperabilidade lícita**: entender comportamento para construir algo original.

Se nenhuma for verdadeira → **a skill não analisa** e explica o que falta.

## O que extrai (e o que não extrai)

| Extrai (o QUE o sistema faz) | NÃO extrai / NÃO faz |
|---|---|
| Funcionalidades e fluxos observáveis | Cópia do código-fonte proprietário |
| Modelo de dados aparente | Reprodução de assets/conteúdo protegidos |
| Contratos e formatos públicos | Burla de DRM, licença, autenticação, anti-bot |
| Comportamento com evidência e limitações | Exploração de alvo sem direito |

## Fluxo (referência → entendimento → reconstrução original)

```text
1. Confirmar o direito de analisar (portão acima) e registrá-lo.
2. Preferir a via mais lícita e barata: docs públicas, API pública,
   comportamento observável.
3. Só então, com direito confirmado, usar o REA para o que faltar
   (binário, Electron/JS, .NET, Android, firmware, rede).
4. Registrar comportamento + evidência + o que ficou desconhecido.
5. Entregar o entendimento aos times de construção (construtor-fullstack),
   que implementam algo ORIGINAL que resolve o mesmo problema.
```

## REA ausente

Se o REA não estiver instalado, a skill avisa e usa as vias lícitas que não o
exigem (docs, API pública, comportamento observável). Para uma árvore JS/Electron
ou ASAR que o gestor tem direito de analisar, o fallback de CLI é
`npx -y rea-agents@latest analyze-javascript-application <caminho> --json` — leia
a evidência e as limitações com o mesmo cuidado.

## Entregável

Relatório de engenharia reversa: o que o sistema faz, a evidência e o nível
dela, os desconhecidos, o registro do direito de analisar, e a ponte para a
reconstrução original (não a cópia).
