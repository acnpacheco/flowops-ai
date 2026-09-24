# FlowOps AI
## Documentação Oficial do MVP
### Product Requirements Document · Especificação Funcional · Arquitetura Técnica

---

**Versão:** 1.0  
**Data:** Junho de 2026  
**Classificação:** Documento Oficial de Produto  
**Audiência:** Banca Avaliadora de Certificação — Agile Product Management  
**Autor:** Candidato ao Programa de Certificação  
**Status:** Aprovado para submissão

---

## Sumário

1. [Resumo Executivo](#1-resumo-executivo)
2. [Contexto e Problema](#2-contexto-e-problema)
3. [Proposta de Valor e Diferenciais](#3-proposta-de-valor-e-diferenciais)
4. [Público-Alvo e Personas](#4-público-alvo-e-personas)
5. [Arquitetura Técnica](#5-arquitetura-técnica)
6. [Fluxo Funcional do Sistema](#6-fluxo-funcional-do-sistema)
7. [Regras Gerais de Negócio](#7-regras-gerais-de-negócio)
8. [Módulos e Funcionalidades](#8-módulos-e-funcionalidades)
   - 8.1 Visão Geral — Dashboard Executivo
   - 8.2 Métricas de Fluxo
   - 8.3 DORA — Métricas de Engenharia
   - 8.4 Carga de Falhas — Saúde da Qualidade
   - 8.5 Acompanhamento da Sprint
   - 8.6 Relatório de Quarter
   - 8.7 Status Reports Inteligentes
   - 8.8 Multi-time
   - 8.9 Calendário de Sprints
   - 8.10 Glossário Integrado
9. [Definição das Métricas](#9-definição-das-métricas)
10. [Insights Automatizados por IA](#10-insights-automatizados-por-ia)
11. [Critérios de Aceite do MVP](#11-critérios-de-aceite-do-mvp)
12. [Instruções de Uso](#12-instruções-de-uso)
13. [Roadmap Futuro](#13-roadmap-futuro)
14. [Glossário de Termos](#14-glossário-de-termos)

---

## 1. Resumo Executivo

O **FlowOps AI** é uma plataforma de inteligência operacional para gestão ágil de times de desenvolvimento de produto. O sistema consolida métricas de fluxo, métricas DORA (DevOps Research and Assessment) e indicadores de qualidade em um único painel interativo, com geração automática de diagnósticos, insights em linguagem natural e relatórios executivos por inteligência artificial.

O MVP foi desenvolvido como uma aplicação web standalone (arquivo HTML único), eliminando qualquer dependência de infraestrutura de backend, banco de dados ou servidor web. O produto funciona localmente no navegador, com dados simulados representando três times de desenvolvimento ao longo de quatro quarters e seis sprints cada — totalizando 72 pontos de dados históricos por métrica por time.

**Problema central resolvido:** profissionais de gestão ágil (Scrum Masters, Agile Coaches, Squad Leaders) perdem entre 3 e 6 horas semanais coletando, formatando e interpretando dados de ferramentas distintas. O FlowOps AI elimina esse trabalho manual com dashboards automáticos e interpretação inteligente dos dados.

**Resultado esperado:** redução do tempo de preparação de relatórios de horas para segundos, com aumento da qualidade dos diagnósticos e da frequência de acompanhamento das métricas.

---

## 2. Contexto e Problema

### 2.1 Cenário Atual

Organizações que adotam metodologias ágeis frequentemente operam com times que utilizam ferramentas como Jira, Azure DevOps ou GitHub para rastreamento de atividades. Contudo, essas ferramentas não consolidam automaticamente os indicadores de saúde do time — geração de métricas, tendências e relatórios executivos exige trabalho adicional e manual.

O fluxo típico de um Scrum Master ou Agile Master antes do FlowOps AI:

1. Exportar dados do Jira para planilha
2. Calcular manualmente Lead Time, Throughput e WIP
3. Preparar gráficos para reunião de Review
4. Elaborar status report executivo em PowerPoint ou Google Slides
5. Identificar visualmente onde estavam os gargalos e bloqueios
6. Repetir esse processo a cada sprint para cada time acompanhado

### 2.2 Lacunas Identificadas

| Lacuna | Descrição |
|--------|-----------|
| Métricas de fluxo ausentes | Lead Time, Cycle Time e Aging raramente são monitorados em tempo real |
| DORA desconhecida | DF, MTTR e CFR não são implementados na prática da maioria dos times |
| Visão retrospectiva, não preditiva | Relatórios são gerados após o fato, sem capacidade de antecipar riscos |
| Sem padrão entre times | Cada Scrum Master usa critérios próprios para interpretar métricas |
| IA ausente | Nenhuma ferramenta de uso corrente oferece interpretação automática de tendências |

### 2.3 Dimensionamento do Problema

- **Volume de trabalho manual:** 3 a 6 horas semanais por gestor ágil em atividades de consolidação e reporte
- **Impacto em escala:** uma organização com 5 Scrum Masters perde entre 15 e 30 horas semanais em trabalho de baixo valor estratégico
- **Custo de oportunidade:** tempo que poderia ser investido em coaching, facilitação e melhoria contínua

---

## 3. Proposta de Valor e Diferenciais

### 3.1 Proposta de Valor Central

> *"Do dado ao diagnóstico em segundos — sem relatórios manuais, sem planilhas, sem reuniões de alinhamento evitáveis."*

O FlowOps AI posiciona-se como o primeiro produto da organização que integra **métricas de fluxo + métricas DORA + carga de falhas + acompanhamento de sprint** em um único painel com diagnóstico automático e geração de relatórios por inteligência artificial. Não é apenas um dashboard — é um copiloto de gestão ágil.

### 3.2 Benefícios Esperados

| Benefício | Descrição |
|-----------|-----------|
| Redução de esforço | Geração automática de relatórios e insights, eliminando trabalho manual repetitivo |
| Diagnóstico em tempo real | Identificação automática de gargalos, riscos e anomalias sem análise manual |
| Padronização | Cálculos e interpretações consistentes entre times e quarters |
| Apoio à decisão | Recomendações acionáveis baseadas em dados reais do sprint |
| Visibilidade executiva | Health Score e dashboards prontos para apresentação a stakeholders |
| Previsibilidade | Projeção de capacidade e risco de transbordo com base em histórico |

### 3.3 Diferenciais em Relação às Alternativas

| Alternativa | Limitação | FlowOps AI |
|-------------|-----------|------------|
| Jira Reports | Não calcula Lead Time P85, Aging nem DORA | Tudo integrado |
| Planilhas Excel | Trabalho manual, sem automação, sem insights | Totalmente automatizado |
| Power BI | Requer configuração de ETL e licença cara | Sem servidor, zero configuração |
| Linear Analytics | Apenas métricas de issue, sem DORA | Fluxo + DORA + Qualidade + IA |
| Dashboards customizados | Meses de desenvolvimento | Entrega imediata como arquivo único |

---

## 4. Público-Alvo e Personas

### Persona 1 — Scrum Master Operacional

**Perfil:** Carla, 32 anos

**Responsabilidades:** Facilitar cerimônias, remover impedimentos, acompanhar 1 a 2 squads.

**Dores:** Gasta muito tempo preparando relatórios; não tem ferramenta que consolide automaticamente o que precisa apresentar na Review; identifica gargalos tarde demais.

**Como usa o FlowOps AI:** Acessa diariamente para verificar WIP, Aging e gargalos. Usa o Acompanhamento da Sprint para monitorar progresso day-by-day. Gera o Status Report de sprint com 1 clique. Compartilha o Alerta de Fluxo no canal de WhatsApp do time.

---

### Persona 2 — Agile Master / Coach

**Perfil:** Rafael, 41 anos

**Responsabilidades:** Coaching de times, evolução de maturidade ágil, acompanhamento de múltiplos squads.

**Dores:** Falta de visão comparativa entre times; dificuldade de evidenciar evolução ou regressão de métricas ao longo do quarter; precisa de dados para embasar conversas de coaching.

**Como usa o FlowOps AI:** Usa o painel Multi-time para comparar Health Scores. Acessa DORA para identificar quais times têm problemas de qualidade ou frequência de entrega. Usa insights automáticos como ponto de partida para conversas de coaching.

---

### Persona 3 — Squad Leader / Tech Lead

**Perfil:** Bruno, 37 anos

**Responsabilidades:** Liderança técnica e gestão do squad, alinhamento com stakeholders de negócio.

**Dores:** Precisa apresentar status para a diretoria sem ter tempo de preparar uma apresentação completa; quer identificar rapidamente se o time vai cumprir o planejamento do quarter.

**Como usa o FlowOps AI:** Acessa o Health Score e o Relatório de Quarter. Usa o painel DORA para evidenciar qualidade técnica. Compartilha o Status Report gerado por IA com stakeholders.

---

### Persona 4 — Gerente / Diretor de Tecnologia

**Perfil:** Mariana, 48 anos

**Responsabilidades:** Gestão de múltiplos times, alinhamento estratégico, reporte para C-level.

**Dores:** Recebe relatórios inconsistentes de cada Scrum Master; não tem visão consolidada da saúde da área; perde tempo em reuniões longas que poderiam ser resolvidas com dados.

**Como usa o FlowOps AI:** Consome o painel Multi-time para visão executiva. Usa o Health Score como KPI de área. Acessa o Relatório de Quarter para preparar apresentações à diretoria.

---

### Persona 5 — Product Owner

**Perfil:** Fernanda, 35 anos

**Responsabilidades:** Priorização do backlog, alinhamento de entrega com o negócio, gestão de expectativas de stakeholders.

**Dores:** Falta de previsibilidade sobre o que será entregue; dificuldade de entender o impacto de bugs reabertos e retrabalho no ritmo de entrega.

**Como usa o FlowOps AI:** Consulta Throughput e % de Entrega para calibrar expectativas. Usa Carga de Falhas para entender o impacto da dívida técnica. Discute Transbordo com o time na retrospectiva.

---

## 5. Arquitetura Técnica

### 5.1 Modelo de Entrega

O FlowOps AI MVP é uma **Single-File Web Application**: toda a lógica de negócio, estilos, dados e componentes visuais estão encapsulados em um único arquivo HTML (`flowinsight.html`). Não há dependência de servidor, banco de dados, processo de build, ou conexão de rede (exceto para o módulo de IA via Claude API).

**Vantagens do modelo standalone:**
- Zero de infraestrutura — funciona abrindo o arquivo no navegador
- Portabilidade total — pode ser distribuído por e-mail, USB, repositório ou link direto
- Sem latência de rede — todas as operações são instantâneas
- Sem risco de downtime — não há servidor para falhar

### 5.2 Stack Tecnológica

| Camada | Tecnologia | Versão | Função |
|--------|-----------|--------|--------|
| Interface | HTML5 + CSS3 | — | Estrutura e estilos responsivos |
| Lógica | JavaScript (ES2020) | — | Cálculos, filtros, navegação, geração de conteúdo |
| Gráficos | Chart.js | 4.4 | Todos os gráficos (barras, linhas, doughnut, CFD) |
| Ícones | Tabler Icons | 3.x | Ícones de interface via CDN |
| IA Generativa | Claude API (Anthropic) | Sonnet 4 | Status Report de Sprint por transcrição |
| Fontes | Inter (Google Fonts) | — | Tipografia principal |
| Deploy | Arquivo local / servidor estático | — | Sem dependência de backend |

### 5.3 Estrutura de Dados

Os dados de sprint são organizados em três objetos principais:

```
D   → Time Alpha  (7 membros, Q1–Q4, S1–S6 por quarter)
DB  → Time Beta   (7 membros, Q1–Q4, S1–S6 por quarter)
DG  → Time Gamma  (6 membros, Q1–Q4, S1–S6 por quarter)
```

Cada entrada de sprint contém as seguintes séries de dados:

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `pl[]` | Array (6) | Itens planejados por sprint |
| `en[]` | Array (6) | Itens entregues por sprint |
| `tr[]` | Array (6) | Itens transbordados por sprint |
| `lt[]` | Array (6) | Lead Time P50 por sprint (dias) |
| `p85[]` | Array (6) | Lead Time P85 por sprint (dias) |
| `p95[]` | Array (6) | Lead Time P95 por sprint (dias) |
| `wip[]` | Array (6) | WIP médio por sprint |
| `blk[]` | Array (6) | Dias bloqueados por sprint |
| `df[]` | Array (6) | Deployment Frequency por sprint |
| `ltc[]` | Array (6) | Lead Time for Changes por sprint |
| `mttr[]` | Array (6) | MTTR em horas por sprint |
| `cfr[]` | Array (6) | Change Failure Rate % por sprint |
| `bo[]` | Array (6) | Bugs abertos por sprint |
| `bc[]` | Array (6) | Bugs fechados por sprint |
| `rw[]` | Array (6) | Retrabalho % por sprint |
| `dates[]` | Array (6) | Objeto com datas de início/fim por sprint |
| `members` | Array | Lista de membros do time com nome e papel |

### 5.4 Modelo de Seleção e Filtragem

O sistema mantém três filtros globais sincronizados:

```
Time (Alpha / Beta / Gamma)  →  gd() retorna o objeto de dados correto
Quarter (Q1 / Q2 / Q3 / Q4)  →  gq() retorna o índice do quarter
Sprint (S1 a S6)  →  gi() retorna o índice 0–5 dentro do quarter
```

A função `upd()` é o ponto central de atualização: ao mudar qualquer filtro, ela recalcula e re-renderiza todos os KPIs, gráficos e textos de insight simultaneamente.

### 5.5 Integração com Claude API (IA)

O módulo de Status Report usa a API do Claude (Anthropic) para processar transcrições de reuniões. A chave de API é fornecida pelo usuário via campo de texto na interface e não é armazenada externamente — permanece apenas na memória da sessão do navegador.

```
Fluxo:
1. Usuário insere transcrição + chave de API
2. JavaScript monta payload com transcrição + dados da sprint
3. Fetch POST para api.anthropic.com/v1/messages
4. Resposta do Claude renderizada na interface
```

### 5.6 Modelo de Renderização de Gráficos

Todos os gráficos usam Chart.js. O registro de instâncias é gerenciado por `const ch = {}` e a função `kc(id)` destrói a instância anterior antes de recriar, prevenindo sobreposição de canvas ao navegar entre abas.

---

## 6. Fluxo Funcional do Sistema

```
┌─────────────────────────────────────────────────────────────────┐
│                    FLUXO FUNCIONAL DO SISTEMA                   │
└─────────────────────────────────────────────────────────────────┘

 1. ENTRADA DE DADOS
    ├── Seleção de Time (Alpha / Beta / Gamma)
    ├── Seleção de Quarter (Q1 / Q2 / Q3 / Q4)
    └── Seleção de Sprint (S1 a S6)

          ↓

 2. VALIDAÇÃO E CONTEXTUALIZAÇÃO
    ├── Verificação de consistência das datas de Sprint
    ├── Identificação do Sprint atual vs. histórico
    └── Aplicação das metas configuradas (objeto METAS)

          ↓

 3. PROCESSAMENTO E CÁLCULO DAS MÉTRICAS
    ├── Métricas de Fluxo: Lead Time P50/P85/P95, Throughput, WIP, Aging
    ├── Métricas DORA: DF, LT for Changes, MTTR, CFR
    ├── Métricas de Qualidade: Bugs, Retrabalho, Defeitos Escapados
    └── Health Score: consolidação multidimensional ponderada

          ↓

 4. DASHBOARDS E VISUALIZAÇÕES
    ├── Visão Geral — KPIs com semáforos, deltas e Health Score
    ├── Métricas de Fluxo — CFD, Cycle Time, WIP por estágio, Aging
    ├── DORA — 4 métricas com benchmarks de mercado
    ├── Carga de Falhas — bugs, retrabalho, origem, causas, ações
    ├── Acompanhamento da Sprint — timeline e progresso day-by-day
    ├── Relatório de Quarter — gerado automaticamente dos dados
    └── Multi-time — comparação entre squads

          ↓

 5. GERAÇÃO DE INSIGHTS AUTOMÁTICOS
    ├── Detecção de gargalos por Cycle Time
    ├── Alertas de WIP acima do limite
    ├── Interpretação de tendências DORA
    ├── Análise de causas da carga de falhas
    └── Recomendações de ações para a próxima sprint

          ↓

 6. STATUS REPORTS INTELIGENTES
    ├── Relatório de Quarter (automático a partir dos dados calculados)
    ├── Status Report de Sprint (gerado por IA a partir de transcrição)
    └── Alerta de Fluxo (formatado para WhatsApp/Slack)

          ↓

 7. SUPORTE À DECISÃO
    ├── Priorização de ações por criticidade
    ├── Identificação do principal risco da sprint
    └── Recomendação de ajuste de capacidade e planejamento
```

---

## 7. Regras Gerais de Negócio

### 7.1 Estrutura Temporal

**Quarter**
- Cada Quarter é composto por exatamente **6 Sprints** numeradas de S1 a S6.
- Os Quarters seguem a nomenclatura Q1, Q2, Q3 e Q4, representando os trimestres do ano fiscal.
- O sistema considera o Quarter corrente e mantém histórico dos anteriores para análise comparativa.

**Sprint**
- Duração: **10 dias úteis** (duas semanas de trabalho).
- Reserva obrigatória: **1 dia útil** dedicado exclusivamente às cerimônias ágeis (Planning, Review e Retrospective).
- Capacidade efetiva de desenvolvimento: **9 dias úteis por Sprint**.
- As datas de início e fim de cada Sprint são pré-configuradas e utilizadas como referência temporal nas análises.

**Jornada Produtiva**
- Jornada diária bruta: **8 horas** por colaborador.
- Reserva para pausas, reuniões não planejadas e atividades não produtivas: **1 hora**.
- Capacidade produtiva considerada pelo sistema: **7 horas úteis por dia por colaborador**.

### 7.2 Critérios de Consolidação de Dados

- Os dados de cada Sprint são armazenados como série histórica dentro do Quarter correspondente.
- O sistema processa os dados do Sprint selecionado como referência principal e utiliza o Sprint imediatamente anterior para cálculo de variações (delta).
- Quando o Sprint selecionado é S1, comparações de delta não são exibidas (não há Sprint anterior no mesmo Quarter).
- Comparações entre Quarters utilizam a última Sprint disponível de cada Quarter como dado representativo do período.

### 7.3 Metas de Referência

O sistema opera com metas-padrão configuradas globalmente (objeto `METAS`), utilizadas como critério de semáforo e meta progress em todos os dashboards:

| Métrica | Meta | Critério |
|---------|------|----------|
| Lead Time P85 | ≤ 20 dias | Menor é melhor |
| Throughput | ≥ 17 itens/sprint | Maior é melhor |
| % de Entrega | ≥ 90% | Maior é melhor |
| Transbordo | ≤ 1 item/sprint | Menor é melhor |
| Bugs Abertos | ≤ 3 bugs | Menor é melhor |
| WIP | ≤ 9 itens simultâneos | Menor é melhor |
| Aging | ≤ 2 itens acima do P85 | Menor é melhor |

### 7.4 Sistema de Semáforo

Todos os indicadores seguem uma escala de três níveis:

| Status | Cor | Critério |
|--------|-----|----------|
| Saudável | Verde | Métrica dentro da meta ou melhor |
| Atenção | Amarelo | Métrica entre 80% e 100% da meta (zona de alerta) |
| Crítico | Vermelho | Métrica fora da meta, acima do limite aceitável |

### 7.5 Cálculo do Health Score

O Health Score consolida a saúde geral do time em uma nota de 0 a 100, composta por 5 dimensões:

| Dimensão | Peso | Critério |
|----------|------|----------|
| Throughput | 25% | Percentual de atingimento da meta de itens entregues |
| Lead Time | 25% | Inverso proporcional ao desvio do P85 em relação à meta |
| Transbordo | 20% | Penalidade por cada item transbordado acima da meta |
| Bloqueios | 15% | Penalidade por dias bloqueados na sprint |
| DORA | 15% | Média ponderada das 4 métricas DORA classificadas por tier |

| Faixa | Classificação |
|-------|---------------|
| 90–100 | Excelente — time em alto desempenho |
| 70–89 | Bom — time estável com pontos de melhoria |
| 50–69 | Atenção — time com risco de degradação |
| < 50 | Crítico — intervenção necessária |

### 7.6 Comparações entre Sprints

- O sistema exibe o **delta** (variação) de cada indicador em relação ao Sprint imediatamente anterior.
- A variação é exibida em valor absoluto e percentual.
- A direção da variação (↑ ou ↓) é colorida de acordo com o impacto: verde para melhora, vermelho para piora.
- O **streak histórico** (sequência de melhoras consecutivas) é exibido quando o indicador melhora por 2 ou mais sprints seguidos.

### 7.7 Identificação de Tendências

O sistema identifica tendências observando os últimos 3 sprints disponíveis:

- **Tendência positiva:** métrica melhora por 2 ou mais sprints consecutivos
- **Tendência estável:** variações dentro de ±5% do valor anterior
- **Tendência negativa:** métrica piora por 2 ou mais sprints consecutivos

---

## 8. Módulos e Funcionalidades

### 8.1 Visão Geral — Dashboard Executivo

**Propósito:** Oferecer uma leitura de 10 segundos sobre a saúde do time. Público principal: gestores, diretores e stakeholders que precisam de síntese sem profundidade técnica.

**Componentes:**

1. **Banner de Health Score** — nota geral do time com texto interpretativo automático e classificação visual (Excelente / Bom / Atenção / Crítico)
2. **Grupo 1 — Saúde do Fluxo** (causa): Lead Time P85, WIP Atual, Aging > P85
3. **Grupo 2 — Capacidade de Entrega** (efeito intermediário): Throughput, % de Entrega, Transbordo
4. **Grupo 3 — Qualidade** (efeito final): Bugs Abertos
5. **Card MTTR - Mean Time to Restore** — indicador DORA em planejamento para integração futura

**Comportamento dos cards:**
- Valor atual em destaque
- Delta vs. sprint anterior (% e direção com seta)
- Streak de tendência quando aplicável ("▲ 3 sprints melhorando")
- Barra de progresso em relação à meta
- Semáforo de cor na borda do card (verde / amarelo / vermelho)

---

### 8.2 Métricas de Fluxo

**Propósito:** Diagnóstico operacional do fluxo de trabalho. Público: Scrum Masters, Agile Coaches.

**Componentes:**

| Componente | Descrição |
|-----------|-----------|
| 5 KPI cards | P50, Cycle Time do gargalo, P85, Throughput, WIP |
| CFD — Cumulative Flow Diagram | Acúmulo de itens por estágio ao longo das sprints do quarter |
| Cycle Time por estágio | Identifica visualmente o gargalo (estágio mais lento destacado) |
| Lead Time — evolução de percentis | Evolução de P50, P85, P95 sprint a sprint |
| WIP por estágio | Barras comparativas WIP atual vs. WIP Limit configurado |
| Tabela de Aging | Lista itens em andamento classificados como ALTO / MÉDIO / BAIXO risco |
| Banner de Gargalo | Destaque automático do estágio mais lento com recomendação de ação |
| Botão "Gerar Alerta de Fluxo" | Formata e copia o alerta para WhatsApp/Slack |

---

### 8.3 DORA — Métricas de Engenharia

**Propósito:** Evidenciar a performance de engenharia do time com base em benchmarks de mercado do DORA Institute (Google Cloud).

**Componentes:**

| Componente | Descrição |
|-----------|-----------|
| 4 cards DORA | DF, LTC, MTTR, CFR — cada um com valor, delta, meta, badge de tier (Elite/High/Medium/Low) |
| Painel de Benchmarks | Barras visuais mostrando a posição de cada métrica na escala de mercado |
| Seletor de gráfico | Visualizar qualquer métrica individual ou todas simultâneas em linha do tempo |
| Painel de Impacto no Negócio | Traduz as métricas DORA em termos de impacto operacional compreensível para não-técnicos |
| Insight Automático | Texto interpretativo contextualizando o estado atual do time |

**Benchmarks DORA de Mercado:**

| Tier | Deployment Frequency | Lead Time for Changes | MTTR | Change Failure Rate |
|------|---------------------|----------------------|------|---------------------|
| Elite | Múltiplos deploys/dia | < 1 hora | < 1 hora | < 5% |
| High | Entre 1x/dia e 1x/semana | Entre 1 dia e 1 semana | Entre 1h e 1 dia | 5% a 15% |
| Medium | Entre 1x/semana e 1x/mês | Entre 1 semana e 1 mês | Entre 1 dia e 1 semana | 15% a 45% |
| Low | Menos de 1x por mês | Mais de 1 mês | Mais de 1 semana | > 45% |

---

### 8.4 Carga de Falhas — Saúde da Qualidade

**Propósito:** Monitorar a saúde da qualidade do produto e o impacto de bugs e retrabalho na capacidade de entrega.

**Componentes:**

| Componente | Descrição |
|-----------|-----------|
| 5 cards de qualidade | Bugs Abertos, Bugs Fechados, Retrabalho%, Defeitos Escapados, Bugs Reabertos |
| Histórico de bugs | Barras de bugs abertos/fechados + linha de retrabalho% sprint a sprint |
| Origem dos defeitos | Distribuição por ambiente de detecção (Desenvolvimento / Homologação / Produção / Reaberto) |
| Causas Identificadas | Análise automática das causas mais prováveis com base nos indicadores atuais |
| Ações Recomendadas | Lista de ações para a próxima sprint, contextualizadas pelos dados |

---

### 8.5 Acompanhamento da Sprint

**Propósito:** Visão operacional detalhada da sprint em andamento — o módulo de acompanhamento day-by-day do Scrum Master.

Este módulo oferece três visões integradas via sub-abas:

#### 8.5.1 Progresso Atual

Painel de acompanhamento em tempo real da sprint corrente:

- **KPIs da sprint:** itens concluídos vs. planejados, % de progresso, projeção de entrega, dias restantes
- **Burndown chart:** evolução dia a dia dos itens restantes vs. linha ideal
- **Distribuição por status:** doughnut chart mostrando a proporção de itens por estágio (Done / In Progress / To Do / Blocked)
- **WIP por estágio:** barras com limite visual de WIP
- **Itens em risco:** lista de itens próximos ou acima do P85 com classificação de risco
- **Bloqueios ativos:** registro de impedimentos com tempo bloqueado
- **Capacidade do time:** horas disponíveis, cerimônias reservadas, capacidade efetiva e % utilizada

#### 8.5.2 Membros do Time

Visão individual de cada membro do time na sprint:

- Cards com nome, papel e avatar de inicial
- Itens atribuídos: concluídos vs. em progresso vs. planejados
- Barra de carga individual (capacidade utilizada em %)
- Status visual: Disponível / Moderado / Sobrecarregado
- Horas estimadas x realizadas por membro
- Dias bloqueados registrados por membro

#### 8.5.3 Timeline de Eventos

Linha do tempo visual da sprint com os principais eventos e marcos:

- **Eixo temporal:** dias D1 a D10 (dias úteis da sprint)
- **Eventos mapeados:**
  - D1: Sprint Planning — abertura formal e comprometimento
  - D3: Daily Sync — verificação de progresso inicial e identificação de riscos precoces
  - D5: Mid-Sprint Review — ponto de controle do meio da sprint com análise de burndown
  - D7: Technical Checkpoint — validação técnica e code review
  - D9: Sprint Review — apresentação de itens prontos para stakeholders
  - D10: Retrospectiva + Planning prep — encerramento e preparação do próximo sprint
  - Dia D (Forecast) — projeção probabilística de entrega com % de confiança
- **Detalhes por evento:** descrição, ícone representativo, data e cor por tipo
- **Destaque automático** do evento mais próximo ou em andamento

---

### 8.6 Relatório de Quarter

**Propósito:** Relatório executivo automático do quarter completo, gerado a partir dos dados calculados pelo sistema — sem intervenção manual.

**Seções do relatório:**

| # | Seção | Conteúdo |
|---|-------|---------|
| 1 | Saúde Geral do Quarter | Health Score, tendência e classificação de risco |
| 2 | Planejado vs. Entregue | Itens por sprint, % de aderência, variância acumulada |
| 3 | Capacidade de Entrega | Throughput médio, comparação com capacidade planejada |
| 4 | Transbordo e Carryover | Total transbordado, recorrências, impacto no planejamento |
| 5 | Bloqueios e Impedimentos | Número de bloqueios, dias perdidos, categorização |
| 6 | Métricas DORA do Quarter | Tabela consolidada com tier e variação vs. quarter anterior |
| 7 | Qualidade e Carga de Falhas | Bugs, retrabalho médio, defeitos escapados |
| 8 | Análise de Lead Time | Evolução do P85, fatores identificados de impacto |
| 9 | Recomendações | Ações prioritárias, metas sugeridas, buffer de capacidade |

---

### 8.7 Status Reports Inteligentes

**Propósito:** Centralizar a geração de relatórios executivos via IA e a comunicação de status do time.

#### 8.7.1 Status Report de Sprint via IA

Utiliza a API do Claude (Anthropic) para gerar um documento executivo completo a partir da transcrição de daily meeting ou reunião de acompanhamento.

**Fluxo:**
1. Usuário insere a transcrição no campo de texto
2. Sistema envia transcrição + dados da sprint atual para a API do Claude
3. Claude gera o relatório estruturado
4. Relatório exibido na interface, pronto para copiar e compartilhar

**Conteúdo gerado:**
- Resumo executivo (3 a 5 linhas)
- Status atual da sprint (entregues, em progresso, bloqueados)
- Riscos identificados na transcrição
- Dependências e bloqueios mencionados
- Previsão de entrega para o final da sprint
- Itens em risco de transbordo
- Recomendações de ação imediata
- Próximos passos acordados

#### 8.7.2 Alerta de Fluxo (WhatsApp/Slack)

Gerado automaticamente a partir dos dados de Métricas de Fluxo, formatado para comunicação rápida via mensageiros corporativos.

**Conteúdo:** cabeçalho com time/quarter/sprint, status dos 5 KPIs principais com semáforo emoji, gargalo identificado, itens de Aging com risco alto, WIP acima do limite, recomendação principal.

---

### 8.8 Multi-time

**Propósito:** Visão comparativa entre múltiplos times para gestores e coaches que acompanham mais de um squad.

**Componentes:**
- Cards de Health Score por time com classificação e variação vs. quarter anterior
- Tabela comparativa de métricas principais (Lead Time P85, Throughput, WIP, CFR)
- Gráficos comparativos por métrica (barras lado a lado)
- Tabela DORA por time com tier de classificação

---

### 8.9 Calendário de Sprints

**Propósito:** Visão do calendário de sprints do quarter selecionado, com datas de início e fim de cada sprint e identificação do sprint corrente.

**Componentes:**
- Grid mensal com marcação das sprints do quarter ativo
- Identificação visual do sprint atual
- Datas de início e fim por sprint
- Contagem de dias úteis remanescentes

---

### 8.10 Glossário Integrado

**Propósito:** Base de conhecimento integrada na aplicação para educação e nivelamento da equipe sobre termos e conceitos utilizados no produto.

O Glossário contém **41 entradas** organizadas por categoria:

| Categoria | Termos |
|-----------|--------|
| Métricas de Fluxo | Lead Time, Cycle Time, Throughput, WIP, Aging, CFD, P85, P50, P95, Takt Time, Capacidade |
| Métricas DORA | Deployment Frequency, Lead Time for Changes, MTTR, Change Failure Rate |
| Qualidade | Bugs Abertos, Bugs Fechados, Retrabalho, Defeitos Escapados, Bugs Reabertos |
| Gestão Ágil | Sprint, Quarter, Transbordo, Bloqueio, Cerimônias, Health Score, Velocity, DoR, DoD |
| Conceitos IA | Insights Automáticos, Status Report IA, Alerta de Fluxo, Análise Preditiva |

Cada entrada do glossário contém:
- Nome e categoria do termo
- Definição clara em português
- Fórmula ou critério de cálculo (quando aplicável)
- Meta de referência
- Dica de interpretação e ação

---

## 9. Definição das Métricas

### 9.1 Lead Time

**Definição:** Tempo total desde o comprometimento do time com um item até a sua conclusão e disponibilização para o usuário.

**Fórmula:**
```
Lead Time = Data de Conclusão − Data de Início (comprometimento)
```

**Percentis:**
- **P50 (mediana):** metade dos itens entregues neste prazo ou menos
- **P85:** 85% dos itens entregues dentro deste prazo — **referência oficial de previsibilidade**
- **P95:** referência para comprometimentos com alta confiança

**Meta:** P85 ≤ 20 dias

**Por que P85 e não média?** A média é distorcida por outliers. O P85 é resistente a casos extremos e representa um compromisso realista com 85% de cobertura histórica.

---

### 9.2 Cycle Time

**Definição:** Tempo que um item permanece ativo em cada etapa específica do processo (A Fazer → Em Progresso → Revisão → Teste → Pronto).

**Fórmula:**
```
Cycle Time por Estágio = Soma do tempo dos itens no estágio ÷ Quantidade de itens
```

**WIP Limits por estágio:**

| Estágio | Limite de WIP |
|---------|--------------|
| A Fazer | Sem limite (backlog) |
| Em Progresso | 5 itens |
| Revisão | 3 itens |
| Teste | 4 itens |
| Pronto | Sem limite |

---

### 9.3 Throughput

**Definição:** Quantidade de itens de trabalho concluídos pelo time em uma Sprint.

**Meta:** ≥ 17 itens/sprint

**Limitação importante:** Throughput não mede o tamanho ou valor dos itens. O produto adota a premissa de homogeneidade razoável de tamanho dentro do mesmo time.

---

### 9.4 Work In Progress (WIP)

**Definição:** Número total de itens iniciados mas ainda não concluídos em qualquer estágio ativo do fluxo.

**Princípio da Lei de Little:**
```
Lead Time = WIP ÷ Throughput
```

Mantendo Throughput constante, qualquer aumento de WIP resulta diretamente em aumento do Lead Time.

**Meta:** ≤ 9 itens simultâneos

---

### 9.5 Aging

**Definição:** Tempo que cada item em andamento já passou no fluxo desde que foi iniciado.

**Classificação:**

| Aging vs. P85 | Classificação | Ação |
|--------------|---------------|------|
| < 70% do P85 | Baixo Risco | Monitoramento padrão |
| 70% a 99% do P85 | Médio Risco | Atenção proativa |
| ≥ 100% do P85 | Alto Risco | Escalada imediata |

---

### 9.6 Deployment Frequency (DF)

**Definição:** Número de deploys realizados em produção por semana.

**Fórmula:**
```
DF = Número de deploys em produção ÷ Número de semanas da Sprint
```

**Meta:** ≥ 2 deploys/semana

---

### 9.7 Lead Time for Changes (LTC)

**Definição:** Tempo entre o commit de código e o deploy em produção.

**Meta:** ≤ 3 dias

---

### 9.8 Mean Time to Recovery (MTTR)

**Definição:** Tempo médio entre a detecção de uma falha em produção e a restauração completa do serviço.

**Fórmula:**
```
MTTR = Soma do tempo de recuperação de todos os incidentes ÷ Número de incidentes
```

**Meta:** ≤ 4 horas

---

### 9.9 Change Failure Rate (CFR)

**Definição:** Percentual de deploys que resultaram em falhas, rollbacks ou necessidade de hotfix.

**Fórmula:**
```
CFR (%) = (Deploys com falha ÷ Total de deploys) × 100
```

**Meta:** ≤ 10%

---

### 9.10 Métricas de Qualidade

| Métrica | Definição | Meta |
|---------|-----------|------|
| Bugs Abertos | Bugs não corrigidos ao final da sprint | ≤ 3 |
| Bugs Fechados | Bugs corrigidos e validados na sprint | ≥ 8 (quando há backlog) |
| Retrabalho% | Itens entregues reabertos para correção | ≤ 10% |
| Defeitos Escapados | Bugs em produção detectados pelo usuário final | 0 (meta ideal) |
| Bugs Reabertos | Bugs "corrigidos" que voltaram por correção inadequada | ≤ 2 |

**Princípio Shift-Left:** Corrigir defeitos em desenvolvimento custa até 10× menos do que corrigi-los em produção.

---

## 10. Insights Automatizados por IA

O sistema gera interpretações automáticas em linguagem natural para cada painel. Os insights seguem a hierarquia:

**diagnóstico → evidência → impacto → recomendação**

### Exemplos de Padrões de Insight

**Tendência de piora do Lead Time:**
> "O Lead Time P85 cresceu X dias nas últimas 3 sprints. A causa mais provável é o aumento de WIP de Y para Z itens simultâneos (Lei de Little: maior WIP = maior Lead Time). Recomendação: estabelecer limite de WIP em [estágio mais carregado] e priorizar a conclusão de itens em progresso antes de iniciar novos."

**WIP acima do limite:**
> "WIP atual de N itens está X acima do limite recomendado (9). O estágio [nome] concentra Z itens simultâneos (limite: W). Considere pausar novos inícios em [estágio] até reduzir o inventário."

**Crescimento da Carga de Falhas:**
> "Bugs abertos cresceram X% em 3 sprints consecutivas. Taxa de correção insuficiente: apenas N bugs fechados para M abertos. O retrabalho de Y% sugere que os critérios de aceite precisam ser revisados antes da próxima sprint."

**Aumento do MTTR:**
> "MTTR aumentou de Ah para Bh — variação de X%. Combinado com uma CFR de Y%, indica que os deploys estão gerando mais incidentes e o time está demorando mais para se recuperar. Verificar disponibilidade de runbooks atualizados e automação de rollback."

### Regras de Geração

- Insights são gerados no momento do carregamento do painel
- Cada insight é contextualizado com os valores reais do sprint atual e anterior
- Quando múltiplos problemas coexistem, o insight prioriza o de maior impacto no Health Score
- Insights positivos também são gerados: *"Terceira sprint consecutiva com Lead Time dentro da meta — o time demonstra maturidade de fluxo crescente."*

---

## 11. Critérios de Aceite do MVP

### 11.1 Funcionalidades Obrigatórias

| # | Critério de Aceite | Status |
|---|-------------------|-|
| CA-01 | Ao selecionar um time e uma sprint, todos os KPIs da Visão Geral atualizam automaticamente | ✅ Implementado |
| CA-02 | O semáforo de cada card muda de cor de acordo com o valor em relação à meta configurada | ✅ Implementado |
| CA-03 | O delta vs. sprint anterior é exibido corretamente, com direção e percentual | ✅ Implementado |
| CA-04 | O Health Score é calculado e exibido com texto interpretativo na Visão Geral | ✅ Implementado |
| CA-05 | O gráfico de Cycle Time identifica e destaca o estágio com maior tempo acumulado como gargalo | ✅ Implementado |
| CA-06 | As barras de WIP por estágio exibem alerta quando o limite é ultrapassado | ✅ Implementado |
| CA-07 | A tabela de Aging classifica os itens como ALTO/MÉDIO/BAIXO risco com base no P85 | ✅ Implementado |
| CA-08 | Os 4 cards DORA exibem valor, delta, meta e tier de classificação corretamente | ✅ Implementado |
| CA-09 | O seletor de gráfico DORA permite visualizar qualquer métrica individualmente ou todas | ✅ Implementado |
| CA-10 | Os benchmarks DORA são exibidos com a posição do time em cada escala | ✅ Implementado |
| CA-11 | Os 5 cards de Carga de Falhas exibem status, delta e meta de referência | ✅ Implementado |
| CA-12 | As causas identificadas e as ações recomendadas são geradas automaticamente | ✅ Implementado |
| CA-13 | O Relatório de Quarter é gerado automaticamente com dados reais | ✅ Implementado |
| CA-14 | O botão "Gerar Alerta de Fluxo" produz texto formatado e o copia para o clipboard | ✅ Implementado |
| CA-15 | A entrada de transcrição + botão de análise via IA gera o Status Report com Claude API | ✅ Implementado |
| CA-16 | A troca de time (Alpha/Beta/Gamma) atualiza todos os painéis com os dados correspondentes | ✅ Implementado |
| CA-17 | O painel Multi-time exibe comparativo de Health Score e métricas entre os times | ✅ Implementado |
| CA-18 | O produto funciona como arquivo standalone HTML sem dependência de servidor | ✅ Implementado |
| CA-19 | O Acompanhamento da Sprint exibe progresso, membros e timeline de eventos | ✅ Implementado |
| CA-20 | O Glossário exibe 41 entradas organizadas por categoria com definições e exemplos | ✅ Implementado |

### 11.2 Critérios de Qualidade da Experiência

| # | Critério | Status |
|---|---------|--------|
| CQ-01 | Todos os gráficos são responsivos e legíveis em tela com largura ≥ 1024px | ✅ |
| CQ-02 | A navegação entre abas não causa perda de contexto (time/quarter/sprint selecionados) | ✅ |
| CQ-03 | Gráficos são recriados corretamente ao trocar de aba, sem sobreposição ou dimensão zero | ✅ |
| CQ-04 | O idioma padrão da interface é português brasileiro | ✅ |
| CQ-05 | Os insights automáticos são contextualizados com os valores reais (não textos genéricos fixos) | ✅ |

---

## 12. Instruções de Uso

### 12.1 Pré-requisitos

- Navegador moderno: Google Chrome 115+, Microsoft Edge 115+, Firefox 115+, ou Safari 17+
- Nenhuma instalação, servidor ou conexão de internet necessários (exceto para a funcionalidade de Status Report via IA)
- Conexão com internet necessária apenas para: Tabler Icons (CDN), Chart.js (CDN), Inter font (Google Fonts) e Claude API

### 12.2 Como Executar

1. Baixar ou acessar o arquivo `flowinsight.html`
2. Abrir o arquivo diretamente no navegador (duplo clique ou arrastar para o navegador)
3. A aplicação carrega imediatamente — nenhuma configuração adicional necessária

### 12.3 Navegação

**Filtros globais (barra superior):**
- **Time:** selecionar Alpha, Beta ou Gamma
- **Quarter:** selecionar Q1, Q2, Q3 ou Q4
- **Sprint:** selecionar S1 a S6

Todos os filtros atualizam todos os painéis simultaneamente ao ser alterados.

**Abas de navegação:**
| Aba | Ícone | Conteúdo |
|-----|-------|---------|
| Visão Geral | chart-dots-3 | Dashboard executivo com Health Score e KPIs |
| Métricas de Fluxo | wave-sine | CFD, Lead Time, Cycle Time, WIP, Aging |
| DORA | rocket | 4 métricas DORA com benchmarks de mercado |
| Carga de Falhas | bug | Bugs, retrabalho, origem de defeitos |
| Acompanhamento | activity | Progresso, membros e timeline da sprint |
| Relatório de Quarter | file-analytics | Relatório automático do quarter |
| Status Reports | clipboard-text | Geração de relatórios via IA |
| Multi-time | users-group | Comparativo entre times |
| Calendário | calendar-stats | Calendário de sprints do quarter |
| Glossário | books | Dicionário de métricas e conceitos |

### 12.4 Usando o Status Report via IA

1. Acessar a aba **Status Reports**
2. Inserir a chave de API do Claude (Anthropic) no campo correspondente
3. Selecionar o time, quarter e sprint no filtro global
4. Colar a transcrição da daily meeting ou reunião no campo de texto
5. Clicar em **"Analisar com IA"**
6. Aguardar a geração do relatório (tipicamente 5–15 segundos)
7. Copiar o relatório gerado para compartilhamento

> **Nota:** A chave de API é utilizada diretamente no navegador via chamada à API do Claude. Ela não é armazenada em nenhum servidor externo e persiste apenas durante a sessão do navegador.

### 12.5 Usando o Alerta de Fluxo

1. Acessar a aba **Métricas de Fluxo**
2. Selecionar o time, quarter e sprint desejados
3. Clicar em **"Gerar Alerta de Fluxo"**
4. O texto formatado com emojis de semáforo é copiado automaticamente para o clipboard
5. Colar diretamente no WhatsApp, Slack, Teams ou e-mail

---

## 13. Roadmap Futuro

### Fase 2 — Integrações e Automação de Dados

| Funcionalidade | Descrição |
|---------------|-----------|
| Integração com Jira | Importação automática de dados de sprints via API do Jira Cloud |
| Integração com Azure DevOps | Coleta de work items e bugs diretamente do Azure Boards |
| Integração com GitHub | Coleta de Deployment Frequency e LTC via GitHub Actions |
| Importação via CSV | Upload de planilhas exportadas de qualquer ferramenta de gestão |
| Sincronização automática | Atualização de dados em tempo real via webhooks |

### Fase 3 — Inteligência Avançada

| Funcionalidade | Descrição |
|---------------|-----------|
| IA Preditiva de Throughput | Projeção das próximas sprints com base em séries históricas |
| Forecast de Quarter | Estimativa probabilística de entrega com intervalos de confiança |
| Monte Carlo Simulation | Simulação de capacidade com múltiplos cenários |
| Detecção de Anomalias | Alertas quando uma métrica desvia mais de 2 desvios padrões |
| Análise de Correlação | Painel mostrando correlações entre WIP × Lead Time, CFR × MTTR |

### Fase 4 — Gestão em Escala

| Funcionalidade | Descrição |
|---------------|-----------|
| Benchmark entre Squads | Comparação anônima de métricas entre times da mesma organização |
| Alertas Inteligentes | Notificações via Slack/Teams/e-mail em limites críticos |
| Simulação de Capacidade | Impacto de adicionar/remover membros nas métricas projetadas |
| Planejamento Assistido por IA | Sugestão de metas para o próximo quarter baseada no histórico |
| Relatórios Agendados | Envio automático de status reports ao final de cada sprint |
| Multi-tenancy | Múltiplas organizações com isolamento de dados |

### Fase 5 — Produto Enterprise

| Funcionalidade | Descrição |
|---------------|-----------|
| SSO e autenticação corporativa | Login via Google Workspace, Azure AD ou Okta |
| API pública | Endpoints para integração com Power BI e Tableau |
| White-label | Personalização visual para consultorias e implementadores |
| Auditoria e rastreabilidade | Log de acesso e histórico de alterações para compliance |

---

## 14. Glossário de Termos

| Termo | Definição |
|-------|-----------|
| **Aging** | Tempo que um item de trabalho já passou no fluxo desde que foi iniciado, sem ser concluído |
| **Burndown** | Gráfico que mostra a redução de itens restantes ao longo dos dias da sprint |
| **CFD** | Cumulative Flow Diagram — gráfico de acúmulo de itens por estágio ao longo do tempo |
| **CFR** | Change Failure Rate — taxa de deploys que causaram falhas em produção |
| **Cycle Time** | Tempo que um item permanece ativo em um estágio específico do processo |
| **Delta** | Variação percentual de uma métrica em relação ao sprint anterior |
| **Deployment Frequency** | Frequência com que o time realiza deploys em produção |
| **DoD** | Definition of Done — critérios que definem quando um item está concluído |
| **DoR** | Definition of Ready — critérios para um item entrar no sprint |
| **DORA** | DevOps Research and Assessment — framework de métricas de performance de engenharia |
| **Health Score** | Nota geral de saúde do time calculada a partir de múltiplas dimensões (0 a 100) |
| **Lead Time** | Tempo total de um item desde o comprometimento até a entrega ao usuário |
| **Lead Time for Changes** | Tempo entre o commit de código e o deploy em produção |
| **Lei de Little** | Princípio que relaciona Lead Time = WIP ÷ Throughput |
| **MTTR** | Mean Time to Recovery — tempo médio de recuperação de falhas em produção |
| **P85** | Percentil 85 do Lead Time — 85% dos itens são entregues dentro deste prazo |
| **Shift-Left** | Princípio de detectar defeitos o mais cedo possível no ciclo de desenvolvimento |
| **Sprint** | Ciclo fixo de desenvolvimento de 10 dias úteis (2 semanas) |
| **Streak** | Sequência de sprints consecutivos com melhora em uma métrica |
| **Throughput** | Quantidade de itens concluídos em uma sprint |
| **Tier DORA** | Classificação de maturidade DevOps: Elite / High / Medium / Low |
| **Transbordo** | Itens planejados para a sprint que não foram concluídos e precisaram ser movidos |
| **WIP** | Work In Progress — número de itens em andamento simultaneamente |
| **WIP Limit** | Limite máximo de itens simultâneos em um estágio do fluxo |

---

*Documento elaborado como parte da documentação oficial do FlowOps AI MVP*  
*Programa de Certificação em Agile Product Management · Junho de 2026*  
*Versão 1.0 · Confidencial · Uso restrito à banca avaliadora e stakeholders autorizados*

---

**Contato:** acnpacheco@gmail.com
