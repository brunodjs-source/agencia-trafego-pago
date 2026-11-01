---
name: construtor-fullstack
description: Construção de sistemas completos de ponta a ponta — frontend, backend, banco de dados, integrações, infra e deploy. Subfuncionamento do Cérebro para a fase de execução. Use quando a decisão de stack e arquitetura já passou e é hora de construir, ou quando o gestor disser "constrói", "implementa", "monta o sistema". Trabalha contract-first (contrato de API antes de frontend e backend), paraleliza módulos independentes, testa onde é crítico, e nada vai a produção sem GO do gestor.
metadata:
  display_name: Construtor Full-Stack
  version: "0.1.0"
---

# 🏗️ Construtor Full-Stack

Subfuncionamento do **Cérebro** (`cerebro`) para a fase de **construção**. Assume
depois que o discovery e as decisões caras passaram (conselho, §1 do cérebro).

## Ordem que respeita dependências

```text
1. Domínio + Requisitos  (arquiteto-de-dominio, requisitos-analista)
2. Contratos             (api-designer: OpenAPI/GraphQL — fonte da verdade)
3. Dados                 (database-architect: esquema + migrações reversíveis)
   ── a partir daqui, em PARALELO onde é independente ──
4a. Backend              (backend-architect → implementação → testes)
4b. Frontend             (frontend-architect → ui-engineer → a11y)
4c. Integrações          (integracao-engineer: terceiros, filas, pagamentos)
5. Integração            (tech-lead-orquestrador junta as partes)
6. Verificação           (code-reviewer, silent-failure-hunter, testes)
7. Infra + Deploy        (devops-engineer, sre — nasce staging, GO para produção)
```

O contrato de API (passo 2) é o que destrava 4a e 4b em paralelo: ambos
programam contra o mesmo contrato sem esperar um ao outro.

## Regras de construção

1. **Contract-first.** Nenhum frontend consome endpoint que não existe no
   contrato; nenhuma mudança breaking sem versão nova.
2. **Migração reversível.** Todo passo de banco tem rollback.
3. **Estados de borda.** Toda tela trata carregando/vazio/erro; todo backend
   trata falha parcial e idempotência.
4. **Segredo fora do código.** Variáveis/cofre, nunca no repo nem no cliente.
5. **Testes onde dói.** Lógica crítica nasce com teste (TDD); e2e nas jornadas
   principais usando o browser pré-instalado quando necessário.
6. **Verificar de contexto limpo.** Quem revisa não é quem construiu.
7. **GO para produção.** Staging automático; produção só com aprovação explícita
   e rollback pronto.

## Paralelização

Instancie construtores por módulo independente (ver
`cerebro/references/orquestracao-paralela.md`). Serialize onde há dependência
real. Reporte ao gestor quantas instâncias pretende usar antes de um fan-out
grande.

## Construir a partir de referência

Se há uma referência, siga a §5 do `cerebro/SKILL.md` e a
`engenharia-reversa-autorizada`: entenda **o que** o sistema faz por via lícita,
e construa uma implementação **original**. Não copie código/assets proprietários
nem burle proteção.

## Entregável

- Código do sistema, testado, com README e guia de setup.
- Contrato de API versionado.
- Migrações com rollback.
- Pipeline de CI e deploy com rollback.
- Relatório de verificação (revisão + segurança) antes do GO.
