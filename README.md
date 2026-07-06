# FlowOps AI

**Do dado ao diagnóstico em segundos — sem relatórios manuais, sem planilhas, sem reuniões de alinhamento evitáveis.**

FlowOps AI é uma plataforma de inteligência operacional para gestão ágil de times de desenvolvimento de produto. Consolida métricas de fluxo, métricas DORA (DevOps Research and Assessment) e indicadores de qualidade em um único painel interativo, com diagnósticos automáticos, insights em linguagem natural e geração de relatórios executivos por IA.

## O problema

Scrum Masters, Agile Coaches e Squad Leaders perdem entre **3 e 6 horas por semana** coletando, formatando e interpretando dados espalhados em ferramentas como Jira, Azure DevOps ou GitHub — sem consolidação automática de indicadores de saúde do time.

FlowOps AI elimina esse trabalho manual: dashboards automáticos, interpretação inteligente dos dados e relatórios prontos para compartilhar em segundos.

## Funcionalidades

| Módulo | O que entrega |
|---|---|
| **Visão Geral** | Health Score do time com leitura executiva de 10 segundos |
| **Métricas de Fluxo** | Lead Time (P50/P85/P95), Cycle Time, WIP, Aging e CFD, com identificação automática de gargalo |
| **Eficiência de Deploys** | Deployment Frequency, Lead Time for Changes, MTTR e Change Failure Rate com benchmark de mercado |
| **Saúde da Qualidade** | Bugs abertos/fechados, retrabalho e origem de defeitos |
| **Relatório de Quarter** | Consolidação automática da performance do quarter |
| **Acompanhamento da Sprint** | Burndown, distribuição por status, bloqueios ativos e capacidade do time em tempo real |
| **Status Reports via IA** | Geração de status report a partir da transcrição de reuniões, usando a API do Claude (Anthropic) |
| **Multi-time** | Comparação de Health Score e métricas entre squads |
| **Calendário Corporativo** | Planejamento anual de Quarters, Sprints, ritos ágeis e janelas de implantação |
| **Glossário** | Dicionário integrado de métricas e conceitos ágeis |

## Como executar

Não há instalação, build ou servidor — o FlowOps AI é uma aplicação web em arquivo único.

1. Baixe ou clone este repositório
2. Abra o arquivo [`flowinsight.html`](flowinsight.html) diretamente no navegador
3. Pronto — a aplicação carrega imediatamente

**Requisitos:** navegador moderno (Chrome, Edge, Firefox 115+ ou Safari 17+) e conexão com internet apenas para carregar Chart.js, Tabler Icons e Google Fonts via CDN, e para o módulo de Status Report via IA (Claude API).

> A chave da API do Claude, quando usada no módulo de Status Report, é inserida diretamente na interface e permanece apenas na memória da sessão do navegador — nunca é enviada a nenhum servidor externo do FlowOps AI.

## Stack técnica

- **HTML5 + CSS3** — interface e estilos responsivos
- **JavaScript (ES2020)** — lógica de cálculo, filtros e renderização
- **Chart.js 4.4** — todos os gráficos (barras, linhas, doughnut, CFD)
- **Tabler Icons** — ícones de interface
- **Claude API (Anthropic)** — geração de Status Report por IA
- **Inter (Google Fonts)** — tipografia

Zero backend, zero banco de dados, zero processo de build.

## Documentação

A documentação completa do produto — contexto, personas, arquitetura, regras de negócio, definição de métricas e roadmap — está em [`FlowOps_AI_Documentacao_Oficial_MVP.md`](FlowOps_AI_Documentacao_Oficial_MVP.md).

## Roadmap

- **Fase 2:** integrações automáticas com Jira, Azure DevOps e GitHub; importação via CSV
- **Fase 3:** IA preditiva de throughput, forecast de quarter, detecção de anomalias
- **Fase 4:** benchmark entre squads, alertas inteligentes, planejamento assistido por IA

## Autora

Desenvolvido por **Ana Pacheco** — Scrum Master / Agilista.
