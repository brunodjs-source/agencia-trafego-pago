# Roster de agentes do Cérebro

Papéis próprios deste plugin, em `agents/`. Quando o ECC está instalado, prefira o agente especializado dele para linguagens, build-repair e revisão (ver `integracao-ecc.md`); use este roster para o conselho, a orquestração e quando o ECC não estiver presente.

**Total de papéis próprios:** 43. Os "milhares de agentes" vêm de instanciar esses papéis (mais o catálogo ECC/REA) em paralelo — ver `orquestracao-paralela.md`.

## Conselho

| Agente | Papel | Modelo |
|---|---|---|
| `presidente-sintese` | Presidente — Síntese do Conselho — Preside o conselho de decisão do Cérebro: enquadra a decisão, coleta votos às cegas, abre  | opus |
| `estrategista-independente` | Estrategista Independente (cadeira) — Cadeira do conselho que pesquisa e vota de forma isolada, sem ver as outras cadeiras, para | opus |
| `red-team-adversarial` | Red Team Adversarial — Assume o papel de 'por que isto falha': escreve o cenário de fracasso mais plausível de um | opus |
| `verificador-evidencia` | Verificador de Evidência (3 lentes) — Verificação adversarial em três lentes: premissa, evidência e custo | opus |
| `critico-de-lacunas` | Crítico de Lacunas — Lista o que NÃO foi considerado: dados faltando, alternativas não exploradas, stakeholders | opus |
| `juiz-viabilidade` | Juiz de Viabilidade — Avalia planos anônimos pela lente da viabilidade técnica e de prazo: dá para construir ist | opus |
| `juiz-risco` | Juiz de Risco e Conformidade — Avalia planos pela lente de risco: segurança, dados pessoais (LGPD/GDPR), dinheiro, reputa | opus |

## Produto

| Agente | Papel | Modelo |
|---|---|---|
| `product-strategist` | Estrategista de Produto — Transforma uma intenção vaga em problema bem definido: usuários, jobs-to-be-done, hipótese | opus |
| `ux-researcher` | Pesquisador(a) de UX — Entende usuários e valida usabilidade: entrevistas, jornadas, testes de usabilidade e sínt | sonnet |
| `requisitos-analista` | Analista de Requisitos (spec) — Extrai e formaliza requisitos e regras de negócio a partir de conversas, código legado e r | opus |
| `arquiteto-de-dominio` | Arquiteto(a) de Domínio (DDD) — Modela o domínio: contextos delimitados, linguagem ubíqua, agregados e invariantes | opus |

## Frontend

| Agente | Papel | Modelo |
|---|---|---|
| `frontend-architect` | Arquiteto(a) de Frontend — Define a arquitetura do frontend: framework, roteamento, gestão de estado, fronteiras de c | opus |
| `ui-engineer` | Engenheiro(a) de UI — Implementa interfaces: componentes, telas, responsividade e integração com a API | sonnet |
| `design-system-engineer` | Engenheiro(a) de Design System — Cria e mantém o design system: tokens de cor/tipografia/espaçamento, componentes base, tem | sonnet |
| `acessibilidade-a11y` | Especialista em Acessibilidade (a11y) — Audita e corrige acessibilidade (WCAG 2.1 AA): contraste, navegação por teclado, leitores  | sonnet |
| `frontend-performance` | Otimizador(a) de Performance Frontend — Melhora Core Web Vitals: bundle, carregamento, renderização, imagens e cache | sonnet |

## Backend

| Agente | Papel | Modelo |
|---|---|---|
| `backend-architect` | Arquiteto(a) de Backend — Define a arquitetura do backend: estilo (monólito modular/serviços), limites, comunicação, | opus |
| `api-designer` | Designer de API (contract-first) — Projeta contratos de API primeiro (OpenAPI/GraphQL/protobuf): recursos, versionamento, err | opus |
| `database-architect` | Arquiteto(a) de Banco de Dados — Modela dados: escolha do banco, esquema, índices, migrações, integridade e estratégia de c | opus |
| `integracao-engineer` | Engenheiro(a) de Integrações — Integra serviços externos e internos: APIs de terceiros, webhooks, filas, pagamentos, e-ma | sonnet |
| `backend-performance` | Otimizador(a) de Performance Backend — Melhora latência e throughput do backend: queries N+1, índices, cache, pool de conexões, c | sonnet |

## Infra

| Agente | Papel | Modelo |
|---|---|---|
| `devops-engineer` | Engenheiro(a) de DevOps — CI/CD, containers, IaC e automação de deploy | sonnet |
| `cloud-architect` | Arquiteto(a) de Nuvem — Desenha a topologia de nuvem: computação, rede, armazenamento, custo e disponibilidade | opus |
| `sre-reliability` | SRE — Confiabilidade — Define SLOs, orçamentos de erro, estratégia de resiliência, backups e plano de incidente | opus |
| `observabilidade` | Engenheiro(a) de Observabilidade — Instrumenta logs, métricas e traces; cria dashboards e alertas acionáveis | sonnet |
| `network-architect` | Arquiteto(a) de Rede — Desenha rede e segmentação: VPC, sub-redes, firewall, VPN, DNS e zero-trust | opus |

## Dados/ML

| Agente | Papel | Modelo |
|---|---|---|
| `data-engineer` | Engenheiro(a) de Dados — Pipelines de dados: ingestão, transformação, qualidade, versionamento e modelagem analític | sonnet |
| `ml-engineer` | Engenheiro(a) de ML — Modelagem de ML de ponta a ponta: dados, features, treino, avaliação honesta e baseline | opus |
| `mlops-engineer` | Engenheiro(a) de MLOps — Operacionaliza modelos: serving, versionamento de modelo/dados, monitoramento de drift e r | sonnet |

## Qualidade

| Agente | Papel | Modelo |
|---|---|---|
| `tech-lead-orquestrador` | Tech Lead Orquestrador(a) — Coordena os times de construção do Cérebro: quebra o trabalho, decide o que paralelizar, i | opus |
| `qa-lead` | Líder de QA — Define a estratégia de testes: pirâmide, cobertura por risco, dados de teste e critérios d | sonnet |
| `tdd-guide` | Guia de TDD — Conduz desenvolvimento guiado por testes: vermelho → verde → refatora | sonnet |
| `test-automation` | Engenheiro(a) de Automação de Testes — Implementa testes automatizados: unidade, integração e e2e (incl | sonnet |
| `code-reviewer` | Revisor(a) de Código — Revisa diffs em busca de bugs de correção, a partir de contexto limpo | sonnet |
| `silent-failure-hunter` | Caçador(a) de Falhas Silenciosas — Caça erros engolidos, catch vazios, promessas não aguardadas, retornos ignorados e estados | sonnet |
| `refactor-cleaner` | Refatorador(a) / Simplificador(a) — Reduz complexidade e duplicação sem mudar comportamento, com testes verdes | sonnet |
| `doc-writer` | Redator(a) Técnico(a) — Escreve documentação que as pessoas usam: README, guia de setup, decisões (ADR), API e run | sonnet |
| `release-manager` | Gerente de Release — Prepara o lançamento: versionamento, changelog, checklist de release, feature flags e plan | sonnet |

## Segurança

| Agente | Papel | Modelo |
|---|---|---|
| `security-auditor` | Auditor(a) de Segurança (defensivo) — Audita o que o Cérebro construiu: OWASP Top 10, segredos, injeção, authz/authn, SSRF, crip | opus |
| `threat-modeler` | Modelador(a) de Ameaças — Modela ameaças (STRIDE/attack trees) sobre a arquitetura: o que um atacante quer, por onde | opus |
| `dependency-auditor` | Auditor(a) de Dependências — Audita a cadeia de dependências: CVEs conhecidas, licenças, pacotes abandonados, typosquat | sonnet |
| `pentest-autorizado` | Pentester Autorizado(a) — Testa segurança de forma ofensiva SOMENTE dentro de escopo autorizado e registrado | opus |
| `engenharia-reversa-autorizada-agent` | Analista de Engenharia Reversa (autorizado) — Conduz engenharia reversa via REA SOMENTE sob autorização ou direito de analisar o alvo | opus |
