# Integração com o REA — engenharia reversa autorizada

O **[REA](https://github.com/morluto/rea)** (MIT, de morluto) conecta o agente a
ferramentas de inspeção de binários nativos, apps JavaScript/Electron,
assemblies .NET, Android, firmware e navegador, via servidor MCP. O Cérebro usa
o REA para **entender como uma referência funciona** quando a análise é
**lícita e autorizada**.

> Instale só de canais oficiais: `/plugin marketplace add morluto/rea` →
> `/plugin install rea@rea`, ou `npx rea-agents setup`.

## O portão de autorização (antes de qualquer uso do REA)

O Cérebro só usa o REA quando pelo menos uma destas é verdadeira, **e** registra
qual no output:

- O artefato/sistema foi **construído pelo próprio Cérebro** ou pertence
  comprovadamente ao gestor.
- Há **autorização por escrito** do dono do alvo, com escopo e janela.
- É contexto de **CTF, pesquisa de segurança ou aprendizado** sobre alvo que o
  gestor tem direito de analisar.
- É **interoperabilidade lícita**: entender comportamento para construir uma
  implementação original e independente.

Se nenhuma for verdadeira, o Cérebro **não usa o REA** e explica por quê.

## O que o REA entrega (e o que o Cérebro faz com isso)

1. **Comportamento e evidência.** O REA explica o que um artefato faz e mostra a
   evidência e as limitações de cada conclusão. O Cérebro usa isso para entender
   **o que o sistema faz**, não para copiar o **código** dele.
2. **Reconstrução original.** A partir do entendimento, o Cérebro constrói uma
   implementação **nova** que resolve o mesmo problema. Não reproduz código,
   assets ou conteúdo proprietário.
3. **Registro de limitações.** Toda conclusão de engenharia reversa entra no
   relatório com a evidência e o que ficou desconhecido.

## Linha dura — o que o Cérebro recusa (espelha o DISCLAIMER.md do REA)

O REA e o Cérebro **não** ajudam a:

- burlar DRM, verificação de licença, anti-cheat ou outra proteção técnica;
- desenvolver cheats de jogo ou adulterar serviços online;
- burlar autenticação, proteção anti-bot ou controle de acesso em sistemas sem
  autorização;
- copiar software proprietário ou reproduzir seu código/assets protegidos;
- evadir políticas de uso ou medidas de segurança de provedores de modelo de IA;
- revender ou compartilhar contas/acesso de API.

## Fluxo típico (referência → sistema equivalente autorizado)

```text
1. Confirmar direito de analisar a referência (portão acima).
2. Preferir a via mais lícita e barata: docs públicas, API pública,
   comportamento observável.
3. Só então, com autorização, usar o REA para o que faltar.
4. Extrair o QUE o sistema faz (fluxos, funcionalidade, modelo aparente).
5. Projetar e construir uma implementação ORIGINAL (times de construção).
6. Documentar evidência e limitações.
```

## CLI de fallback (sem MCP)

Para uma árvore JavaScript/Electron ou ASAR que o gestor forneceu e tem direito
de analisar:

```bash
npx -y rea-agents@latest analyze-javascript-application /caminho/absoluto/do/app --json
```

Leia a evidência, o grafo, as limitações e os desconhecidos com o mesmo cuidado
de um resultado MCP.
