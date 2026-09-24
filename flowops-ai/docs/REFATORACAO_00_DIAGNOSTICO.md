# FLOWOPS AI — DIAGNÓSTICO PRÉ-REFATORAÇÃO
**Documento:** `REFATORACAO_00_DIAGNOSTICO.md`  
**Data:** 2026-09-24  
**Arquivo auditado:** `flowops-ai/flowinsight.html`  
**Linhas:** 7.283  
**Status:** Leitura completa — sem alterações de código

---

## 1. RESUMO EXECUTIVO

O FlowOps AI é uma aplicação web single-file de 7.283 linhas que consolida métricas ágeis, DORA e saúde de times em um único painel interativo. A arquitetura está funcional e bem estruturada para desktop. O ponto crítico é **responsividade**: existem apenas 7 media queries em ~700 linhas de CSS, cobrindo menos de 40% das seções que precisam de adaptação para telas menores que 768px. O problema mais grave é o sticky navigation: o `main-nav` tem `top:52px` hardcoded, o que quebra o layout quando o topbar cresce além de 52px (o que ocorre quando os filtros "quebram" para uma segunda linha em telas menores). A refatoração não exige mudanças na lógica de negócio nem na estrutura de dados — apenas intervenção no CSS e nos elementos de navegação.

**Grau de risco da refatoração:** Médio.  
**Complexidade:** Alta (arquivo único, sem separação de responsabilidades, CSS inline em vários pontos).  
**Pré-requisito crítico:** Resolver o sticky nav antes de qualquer outra mudança de layout.

---

## 2. ARQUITETURA ATUAL

### Estrutura do arquivo

```
flowinsight.html (7.283 linhas)
├── <head>          linhas 1–7       meta, viewport, título
├── <style>         linhas 8–710     TODO o CSS da aplicação
├── <body>          linhas 711–2177  TODO o HTML (topbar + nav + seções)
└── <script>        linhas 2178–7283 TODO o JavaScript
```

### Dependências externas (CDN — requer internet)

| Biblioteca        | Versão     | Função                              |
|-------------------|------------|-------------------------------------|
| Chart.js          | 4.4.1      | Todos os gráficos                   |
| Tabler Icons      | 3.19.0     | Ícones de interface                 |
| PDF.js            | 3.11.174   | Importação de PDF no Status Report  |
| Mammoth.js        | 1.6.0      | Importação de .docx no Status Report|
| Inter (Google Fonts) | —       | Tipografia base                     |
| CounterAPI.dev    | —          | Contador de visitas                 |

**Nota de risco:** A ausência de qualquer uma dessas dependências derruba funcionalidades específicas. Não há fallback local.

### Modelo de atualização

Toda interação do usuário (troca de time, quarter, sprint) chama `upd()` (linha 2330), que recalcula KPIs, reconstrói banners, e dispara `drawCharts()`, `renderDora()`, `renderMultiTeam()`, `renderFlowMetrics()` e outros renders. Não há estado global gerenciado externamente — o estado vive nos `<select>` do DOM.

---

## 3. MAPA FUNCIONAL — 11 ABAS

| ID  | Rótulo                 | Função principal                              | Linha do render |
|-----|------------------------|-----------------------------------------------|-----------------|
| `v` | Visão geral            | Health Score, KPIs, Resumo executivo, IA recs | ~2400 (via upd) |
| `f` | Métricas de fluxo      | Lead Time, WIP, Aging, CFD                    | 5139            |
| `d` | Eficiência de deploys  | DORA (DF, LT for Changes, MTTR, CFR)          | 4785            |
| `c` | Saúde da qualidade     | Bugs abertos/fechados, retrabalho, origem      | ~3500 (renderCargaFalhas) |
| `r` | Relatório de quarter   | Consolidação automática do quarter            | 2678            |
| `as`| Acompanhamento         | Burndown, distribuição por status, bloqueios  | 3262            |
| `s` | Status reports         | Geração via Claude API + importação PDF/DOCX  | ~5800           |
| `m` | Multi-time             | Comparação de saúde e métricas entre squads   | 4002            |
| `cal`| Calendário            | Calendário de sprints do quarter              | ~4600           |
| `g` | Glossário              | Dicionário de métricas e conceitos            | estático        |
| `ab`| Sobre a autora         | Perfil, foto, bio                             | estático        |

**Comportamento lazy:** `renderMultiTeam()` e `renderFlowMetrics()` só são chamadas quando a aba está visível (`.classList.contains('on')`). As demais seções são sempre renderizadas pelo `upd()`.

---

## 4. MAPA TÉCNICO — CSS

### Layout principal

```
body → display:flex; flex-direction:column; min-height:100vh
  ├── .topbar   position:sticky; top:0; z-index:100; height:52px; flex-wrap:wrap
  ├── .main-nav position:sticky; top:52px; z-index:99
  └── .content  padding:24px; max-width:1200px; margin:0 auto; flex:1
```

**BUG CRÍTICO:** `.main-nav` tem `top:52px` hardcoded. Quando `.topbar` cresce (ex: filtros "quebram" para 2a linha em telas < ~700px), o nav desce para baixo do topbar ampliado mas o `sticky` continua grudado em 52px, causando sobreposição. Não há JavaScript compensando essa mudança de altura.

### Media queries existentes (7 total)

| Linha | Breakpoint     | Classes afetadas                              |
|-------|----------------|-----------------------------------------------|
| 86    | max-width:600px | `.g2` → 1 coluna                             |
| 92    | max-width:720px | `.dora-g2x3` → 2 colunas                    |
| 93    | max-width:480px | `.dora-g2x3` → 1 coluna                     |
| 388   | max-width:600px | `.mt3` → 1 coluna                            |
| 530   | max-width:600px | `.cal-year-grid` → 2 col; `.spr-grid` → 1; `.depm-row` → 1 |
| 657   | max-width:640px | `.bd-grid` → 1 coluna                        |
| 683   | max-width:640px | `.ab-ps-grid` → 1 coluna                     |

**Cobertura estimada:** ~35% das seções com grade/tabela têm breakpoint. O restante não tem tratamento responsivo.

### Elementos com largura fixa (causam overflow no mobile)

| Classe / Elemento     | Largura fixa | Seção              |
|-----------------------|--------------|--------------------|
| `.dbname`             | 180px        | DORA benchmark     |
| `.wip-stage`          | 105px        | Métricas de fluxo  |
| `.cf-orig-lbl`        | 150px        | CFD / origem       |
| `.hs-label`           | 170px        | Health score bars  |
| Tabelas em geral      | sem `overflow-x:auto` no wrapper | múltiplas |

### Sistema de cores do filtro (CSS custom properties)

```css
:root {
  --flt-c:   cor do texto do time selecionado
  --flt-bg:  cor de fundo dos selects
  --flt-bgh: cor de hover
}
```
Definido em `applyTeamColor()` (linha 2218). Valores hardcoded por time:
- Alpha (D): `#185FA5`
- Beta (DB): `#059669`
- Gamma (DG): `#7c3aed`

**Este sistema deve ser preservado integralmente.**

---

## 5. INVENTÁRIO DE DADOS

### Constantes globais

```js
const D  = { Q1:{}, Q2:{}, Q3:{}, Q4:{} }  // Time Alpha — alto desempenho
const DB = { Q1:{}, Q2:{}, Q3:{}, Q4:{} }  // Time Beta  — desempenho médio
const DG = { Q1:{}, Q2:{}, Q3:{}, Q4:{} }  // Time Gamma — time mais novo

const CURRENT_Q = 'Q2'
const CURRENT_S = 5
const STAGES = ['Backlog','In Progress','Code Review','Testing','Done']
const METAS = { p85:20, tp:17, vl:55, pct:90, tr:1, bo:3, bc:8, reo:2, res:3, wip:9, aging:2 }
```

### Estrutura de cada quarter (por time)

Cada `{Q}` contém arrays de 6 sprints para:

| Chave | Significado                    |
|-------|-------------------------------|
| `sp`  | Labels das sprints             |
| `pl`  | Planejado (SP)                 |
| `en`  | Entregue (SP)                  |
| `vl`  | Velocity (SP)                  |
| `cap` | Capacity disponível (SP)       |
| `tr`  | Transbordo                     |
| `p50` | Lead Time P50 (dias)           |
| `p85` | Lead Time P85 (dias)           |
| `p95` | Lead Time P95 (dias)           |
| `bo`  | Bugs abertos                   |
| `bc`  | Bugs fechados                  |
| `rw`  | Retrabalho (%)                 |
| `df`  | Deploy Frequency (deploys/dia) |
| `lc`  | Lead Time for Changes (dias)   |
| `mt`  | MTTR (horas)                   |
| `cf`  | Change Failure Rate (%)        |

### Acessores de filtro

```js
gq()  → quarter atual (ex: 'Q2')
gi()  → índice da sprint (0–5)
gd()  → dataset do time selecionado (D, DB ou DG)
```

---

## 6. INVENTÁRIO DE MÉTRICAS

### Health Score (fórmula ponderada)

Calculado em `calcHealth(q)` e `calcHealthFrom(d)` (linha 2615):

| Dimensão         | Peso | Cálculo                                              |
|------------------|------|------------------------------------------------------|
| Aderência         | 25%  | `totDel / totPlan × 100`                            |
| Lead Time Score   | 20%  | `50 + ltImprove%` (melhoria de p85 S1→S6)          |
| Block Score       | 20%  | `100 - (totTr/totPlan × 100) × 2.5`               |
| Carry Score       | 20%  | `100 - (totTr/totDel × 100)`                       |
| Stability Score   | 15%  | `(dfScore + (50 + bugImprove%)) / 2`              |
| **Total**         | 100% | soma ponderada → arredondada                        |

Faixas: ≥85 = Saudável / ≥70 = Bom / ≥55 = Atenção / <55 = Crítico

### DORA — Tiers de mercado

Tiers classificados em `setTier()` (linha 4752): Elite / High / Medium / Low, para cada uma das 4 métricas DORA.

### KPIs renderizados na Visão Geral

`kc0`–`kc10` via `updateKpi()`. Inclui: Lead Time P85, Throughput, % Entrega, Transbordo, Bugs Abertos, WIP Atual, Aging >P85, Capacity, Bugs Fechados, Bugs Reabertos, Tempo de Resolução.

---

## 7. INVENTÁRIO DE INTERFACE

### Topbar (`<header class="topbar">`)

```
.brand          logo + título "FlowOps AI"
.filters        3 selects: time (D/DB/DG), quarter (Q1-Q4), sprint (S1-S6)
  └── .flt-wrap   wrapper com ícone posicionado + select com chevron custom
botão Exportar HTML
botão Exportar MD
botão Status (abre modal)
span #visitCount  contador de visitas
```

**Problema mobile:** os botões e filtros em linha não cabem em telas < ~700px. O `flex-wrap:wrap` do topbar faz os itens quebrarem para a 2ª linha — mas o nav não acompanha (ver BUG CRÍTICO na seção 4).

### Nav (`<nav class="main-nav">`)

11 `<button class="ntab">` com ícone Tabler + texto. Sem hamburger, sem modo icon-only. Usa `overflow-x:auto` para scroll horizontal em telas estreitas — funcional mas não ideal.

### Grids e layouts principais

| Classe           | Layout atual                     | Tem breakpoint? |
|------------------|----------------------------------|-----------------|
| `.kgrid`         | `repeat(auto-fit,minmax(150px,1fr))` | Não (auto-fit funciona) |
| `.g2`            | `1fr 1fr`                        | Sim (600px → 1fr) |
| `.mt3`           | `repeat(3,1fr)`                  | Sim (600px → 1fr) |
| `.dora-g2x3`     | `repeat(3,1fr)`                  | Sim (720px → 2, 480px → 1) |
| `.cal-year-grid` | `repeat(4,1fr)`                  | Sim (600px → 2 col) |
| `.spr-grid`      | múltiplas colunas                | Sim (via cal) |
| `.bd-grid`       | múltiplas colunas                | Sim (640px → 1) |
| `.ab-ps-grid`    | múltiplas colunas                | Sim (640px → 1) |
| Tabelas DORA     | linhas com `.dbname` fixo 180px  | Não |
| Tabela WIP/Aging | `.wip-stage` fixo 105px          | Não |

### Modais

Há pelo menos 1 modal de detalhe de métrica (`.metric-modal`). Comportamento mobile não auditado em profundidade — candidato a verificação.

---

## 8. DÍVIDA TÉCNICA IDENTIFICADA

### Crítico (impede uso mobile)

| # | Problema                                     | Localização         |
|---|----------------------------------------------|---------------------|
| C1 | `main-nav` com `top:52px` hardcoded — sobreposição quando topbar cresce | linha ~65 |
| C2 | Topbar sem estratégia mobile — 5+ elementos em linha em 52px de altura | linha ~46 |

### Alto (funciona, mas quebra em ≤ 480px)

| # | Problema                                     | Localização         |
|---|----------------------------------------------|---------------------|
| A1 | `.dbname` 180px fixo em tabelas DORA         | linha ~157          |
| A2 | `.wip-stage` 105px fixo em tabelas de fluxo  | linha ~247          |
| A3 | `.cf-orig-lbl` 150px fixo em tabelas CFD     | linha ~275          |
| A4 | `.hs-label` 170px fixo em health score bars  | linha ~316          |
| A5 | Tabelas sem `overflow-x:auto` no container   | múltiplas           |
| A6 | Nav com 11 abas sem fallback para mobile     | linha 759–771       |

### Médio (UX reduzida, mas não quebrado)

| # | Problema                                          |
|---|---------------------------------------------------|
| M1 | `cal-year-grid` colapsa para 2 colunas (bom) mas meses ficam apertados em 360px |
| M2 | Botões de export no topbar sem label em mobile (só ícone seria melhor) |
| M3 | Modais: `.metric-modal` sem verificação de comportamento em mobile |
| M4 | `.kgrid` com `minmax(150px,1fr)`: funciona mas 150px pode ser tight em 320px |
| M5 | Gráficos Chart.js: responsivos por padrão, mas containers sem `min-width:0` podem causar overflow |

### Baixo (qualidade, não funcionalidade)

| # | Problema                                          |
|---|---------------------------------------------------|
| B1 | Breakpoints inconsistentes: usa 480/600/640/720px sem sistema unificado |
| B2 | CSS com valores inline em `style=""` em vários elementos — dificulta manutenção |
| B3 | Dependência de CDN sem fallback — falha silenciosa se CDN cair |

---

## 9. RISCOS DE REGRESSÃO

### Riscos altos

| Área                    | Risco                                                                 |
|-------------------------|-----------------------------------------------------------------------|
| `upd()` e renderização  | Qualquer mudança de estrutura DOM pode quebrar IDs que o JS atualiza (ex: `k0`, `ks0`, `kd0`) |
| Sticky nav              | Corrigir o `top:52px` com JS dinâmico pode criar loop de reflow se não implementado com cuidado |
| `.filters` com `--flt-*`| Mudança no wrapper pode quebrar o posicionamento do ícone custom do select |
| Export HTML (`exportHTML`) | Clona o DOM inteiro — mudanças estruturais devem ser testadas no export |

### Riscos médios

| Área                    | Risco                                                               |
|-------------------------|---------------------------------------------------------------------|
| `tab(el,id)`            | Depende de IDs como `p${id}` para mostrar/ocultar seções — manter IDs intactos |
| `setGroupDot`, `updateKpiGroupDots` | Dependem de IDs específicos dos dots de sinaleira   |
| `.cgh-mt` chart headers | Estrutura com ícone + título + dots — mudança pode afetar visual dos gráficos |
| Modal de detalhe        | `openMetricDetail()` referencia IDs dentro do modal — não alterar IDs dos targets |

### Riscos baixos

| Área                    | Risco                                                               |
|-------------------------|---------------------------------------------------------------------|
| Gráficos Chart.js       | Responsivos por padrão; mudanças de container podem exigir `chart.resize()` manual |
| Glossário e Sobre       | Seções estáticas — menor risco de regressão                        |

---

## 10. PONTOS A PRESERVAR INTEGRALMENTE

Estes elementos **não devem ser alterados** durante a refatoração responsiva:

1. **Fórmula do Health Score** — `calcHealth()` e `calcHealthFrom()` (linhas 2615–2658)
2. **Estrutura de dados** — constantes `D`, `DB`, `DG`, `METAS`, `SPRINT_DATES`, `STAGES`, `CURRENT_Q`, `CURRENT_S`
3. **Sistema de cores por time** — CSS custom properties `--flt-c`, `--flt-bg`, `--flt-bgh` e função `applyTeamColor()`
4. **Acessores de filtro** — `gq()`, `gi()`, `gd()`
5. **IDs do DOM para KPIs** — `k0`–`k10`, `ks0`–`ks10`, `kd0`–`kd10`, `km0`–`km10`, `kc0`–`kc10`
6. **IDs de painel** — `pv`, `pf`, `pd`, `pc`, `pr`, `pas`, `ps`, `pm`, `pcal`, `pg`, `pab`
7. **Export HTML e Markdown** — funções `exportHTML()` e `exportMd()` (linhas 4706, 4175)
8. **Integração Claude API** — módulo de Status Reports
9. **Tiers DORA** — `setTier()` e tabela de benchmarks (linha 4752)
10. **CFD building** — `buildCFD()` (linha 2415)
11. **`.cgh-mt` chart header system** — estrutura de cabeçalhos de gráfico com ícone + dots
12. **Contador de visitas** — integração CounterAPI.dev
13. **Tabler Icons** — todos os ícones já implantados na navegação e seções
14. **Foto da autora** — `AnaPachecco.jpg` referenciada em `#pab`

---

## 11. CANDIDATOS À REFATORAÇÃO

### Prioridade 1 — Sticky nav (bloqueante)

**Problema:** `top:52px` hardcoded quebrará com qualquer mudança de altura do topbar.

**Opções:**
- (A) JS: medir altura real do topbar e ajustar dinamicamente via `ResizeObserver`
- (B) CSS: usar `top: var(--topbar-h)` e setar a variável via JS uma vez no carregamento + no resize
- (C) Estrutura: envolver topbar + nav em um wrapper `sticky` único — mais robusto, mas maior mudança

**Recomendação:** Opção B (CSS var + JS mínimo). Menor risco, não exige mudança estrutural.

### Prioridade 2 — Topbar mobile

**Problema:** Topbar com `height:52px` não comporta filtros + botões + brand em < 700px.

**Opções:**
- (A) Filtros em linha secundária, topbar com `height:auto`  
- (B) Painel de filtros colapsável (botão de funil, abre dropdown)
- (C) Filtros fora do topbar (em barra própria abaixo do nav)

**Recomendação:** Opção A com suporte a `height:auto` — menor risco, mantém todos os controles visíveis.

### Prioridade 3 — Menu de navegação mobile

**Problema:** 11 abas em scroll horizontal — funcional mas não ideal em telas de 360px.

**Opções:**
- (A) Manter scroll horizontal, mas com indicador de scroll e padding nas extremidades  
- (B) Modo icon-only (só ícone Tabler, sem texto) abaixo de 600px
- (C) Hamburger drawer que lista as abas verticalmente

**Recomendação:** Opção B (icon-only). Preserva a navegação sem hamburger, reduz espaço de ~300px para ~180px, ícones Tabler já existem.

### Prioridade 4 — Tabelas com largura fixa

**Elementos:** `.dbname` (180px), `.wip-stage` (105px), `.cf-orig-lbl` (150px), `.hs-label` (170px)

**Solução:** `width:auto; min-width:Xpx; max-width:Xpx` + container com `overflow-x:auto`.

### Prioridade 5 — Breakpoints faltantes

**Seções sem breakpoint:** tabelas de DORA inline (`.dora-row`), cards de resumo executivo (`.ai-rec-list`), cards do Quarter Report, tabela de burndown, tabela de Acompanhamento.

**Solução:** Adicionar `overflow-x:auto` nos wrappers + 1 breakpoint unificado em 600px para grids restantes.

---

## 12. DECISÕES NECESSÁRIAS ANTES DA IMPLEMENTAÇÃO

As questões abaixo precisam de decisão explícita antes de iniciar o código:

| # | Decisão                                                              | Opções                                   |
|---|----------------------------------------------------------------------|------------------------------------------|
| D1 | Estratégia para sticky nav (BUG CRÍTICO)                            | ResizeObserver / CSS var / wrapper único |
| D2 | Menu de navegação em mobile                                          | scroll horizontal / icon-only / hamburger |
| D3 | Filtros em mobile                                                    | empilhar em coluna / painel colapsável   |
| D4 | Tabelas: wrapping com overflow-x ou reformatar células em mobile     | overflow-x:auto / layout em card         |
| D5 | Botões do topbar (Exportar, Status): manter texto ou só ícone mobile | texto + ícone / só ícone com tooltip     |
| D6 | Breakpoint unificado: definir um conjunto canônico                   | 480 / 600 / 768 / 1024px sugeridos       |

---

## APÊNDICE — ESTRUTURA DE ARQUIVOS DO PROJETO

```
flowops-ai/
├── flowinsight.html          → app principal (7.283 linhas)
├── AnaPachecco.jpg           → foto da autora (referenciada em #pab)
└── docs/
    ├── REFATORACAO_00_DIAGNOSTICO.md         ← este documento
    ├── FlowOps_AI_Documentacao_Oficial_MVP.md
    ├── FlowInsight_Documentacao_Oficial_MVP.md
    ├── FlowOpsAI_Storytelling_Q2_2026.md
    └── Case_LeadTime_104_64_Estrategia.md
```

---

*Documento gerado a partir de leitura estática do código-fonte. Nenhuma alteração foi feita nos arquivos do projeto.*
