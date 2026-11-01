# Integração com o ECC

O **[ECC](https://github.com/affaan-m/ECC)** (MIT, de Affaan Mustafa) traz 71
agentes, 314 skills e padrões de harness. Quando instalado ao lado, o Cérebro
delega a ele o trabalho especializado em vez de reinventar.

> Instale só de canais oficiais: `/plugin marketplace add affaan-m/ECC` →
> `/plugin install ecc@ecc`, ou `npx ecc-universal@2.2.3 setup`.

## Quando o Cérebro delega ao ECC

| Necessidade | Famílias do ECC a usar |
|---|---|
| Planejar antes de construir | `planner`, `architect`, `code-architect`, skills `plan-*`, `blueprint`, `spec-miner` |
| Revisar código por linguagem | `*-reviewer` (typescript, python, go, rust, java, swift, php, ruby, vue, react, c#, c++, …) |
| Consertar build quebrado | `*-build-resolver` (react, go, rust, java, kotlin, swift, django, pytorch, …) |
| Segurança (defensiva) | `security-reviewer`, skills `security-review`, `security-scan`, `threat`-* |
| Arquitetura de rede/infra | `network-architect`, `homelab-architect`, skills `kubernetes-patterns`, `docker-patterns`, `deployment-patterns` |
| Qualidade e TDD | `tdd-guide`, `silent-failure-hunter`, `code-reviewer`, skills `tdd-workflow`, `e2e-testing`, `verification-loop` |
| Dados e ML | `mle-reviewer`, `rag-pipeline-reviewer`, skills `mle-workflow`, `recsys-pipeline-architect`, `ml-adoption-playbook` |
| Performance | `performance-optimizer`, skills `react-performance`, `latency-critical-systems`, `data-throughput-accelerator` |
| Documentação | `doc-updater`, `docs-lookup`, skills `docs-governance`, `living-docs-governance` |
| Orquestração de time | skills `team-agent-orchestration`, `dev-team`, `orch-*`, `parallel-execution-optimizer` |

## Regra de precedência

1. Se o ECC tem um agente/skill específico para a tarefa, **use o do ECC** — ele
   é mais especializado que o roster genérico deste plugin.
2. Use o roster próprio (`agents/`) quando o ECC não estiver instalado, ou para
   os papéis que são do Cérebro (o conselho e a camada de orquestração).
3. **Não** duplique: não rode o revisor do Cérebro e o do ECC na mesma coisa.
   Escolha um.

## Como descobrir o que o ECC tem instalado

Peça ao Claude Code a lista de skills/agentes disponíveis (ou use a skill
`ecc-guide`/`configure-ecc` do próprio ECC). O Cérebro **não** assume que uma
skill existe só porque este documento a cita — confirma na lista real antes de
delegar.

## Prompt defense

Os agentes do ECC já trazem um "Prompt Defense Baseline". O roster deste plugin
traz um equivalente. Ao delegar, o Cérebro mantém essas proteções — não pede a
um agente que ignore as próprias regras.
