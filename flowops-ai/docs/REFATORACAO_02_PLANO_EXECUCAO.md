# Refatoração FlowOps AI — Plano de Execução
**Documento:** `REFATORACAO_02_PLANO_EXECUCAO.md`  
**Data:** 2026-09-24  
**Referência:** `REFATORACAO_01_DECISOES.md`  
**Status:** Lote 1 concluído (commit b1759f3) · Lote 2 concluído (commit 6404235) · Lote 3 aguardando autorização

---

## Visão geral

A refatoração está dividida em **4 lotes sequenciais**. Cada lote é implementado, testado e aprovado antes do próximo iniciar. Nenhum lote contém evolução funcional — apenas CSS e estrutura de layout.

| Lote | Decisões | Região afetada | Sequência |
|------|----------|----------------|-----------|
| **Lote 1** | D1 | Wrapper sticky `.site-header` | 1º — pré-requisito |
| **Lote 2** | D3 + D5 absorvida | Topbar mobile (filtros + `.btn-export`) | 2º — mesma região |
| **Lote 3** | D2 | Navegação mobile (nav icon-only) | 3º — separado para rollback independente |
| **Lote 4** | D4 | Overflow de tabelas | 4º — independente |

**Critério de progressão:** implementar → testar → revisar diff → aprovar → commit → próximo lote.

---

## Lote 1 — Estrutura sticky (D1)

### Objetivo

Eliminar o acoplamento `main-nav → top:52px` criando um wrapper `.site-header` que agrupa `.topbar` e `.main-nav` em um único contexto sticky. Esta é a mudança estrutural que torna todas as demais mudanças de responsividade seguras.

### Arquivos afetados

| Arquivo | Região | Tipo de mudança |
|---------|--------|-----------------|
| `flowops-ai/flowinsight.html` | HTML: `<header>` + `<nav>` | Adicionar `<div class="site-header">` envolvendo os dois |
| `flowops-ai/flowinsight.html` | CSS: `.topbar` | Remover `position:sticky`, `top:0`, `z-index:100` |
| `flowops-ai/flowinsight.html` | CSS: `.main-nav` | Remover `position:sticky`, `top:52px`, `z-index:99` |
| `flowops-ai/flowinsight.html` | CSS: `.site-header` | Adicionar nova regra |

### Mudanças previstas

**HTML** — envolver os dois elementos existentes:
```html
<!-- ANTES -->
<header class="topbar"> ... </header>
<nav class="main-nav"> ... </nav>

<!-- DEPOIS -->
<div class="site-header">
  <header class="topbar"> ... </header>
  <nav class="main-nav"> ... </nav>
</div>
```

**CSS** — nova regra + remoção de propriedades:
```css
/* ADICIONAR */
.site-header {
  position: sticky;
  top: 0;
  z-index: 100;
}

/* ALTERAR .topbar — remover: */
/* position: sticky → remover */
/* top: 0 → remover */
/* z-index: 100 → remover */
/* height: 52px → manter por enquanto (será tratado em Lote 2) */

/* ALTERAR .main-nav — remover: */
/* position: sticky → remover */
/* top: 52px → remover */
/* z-index: 99 → remover ou reduzir */
```

### O que NÃO pode ser alterado

- Nenhuma regra de responsividade
- Nenhuma mudança nos filtros, botões ou elementos do topbar
- Nenhum JavaScript
- `height:52px` do topbar (será tratado no Lote 2)
- `overflow-x:auto` do main-nav
- IDs e classes dos elementos internos
- Qualquer propriedade não relacionada ao sticky

### Riscos

| Risco | Probabilidade | Ação de mitigação |
|-------|--------------|-------------------|
| `.metric-modal` com z-index insuficiente fica atrás do header | Baixa | Auditar z-index do modal antes de implementar; ajustar se necessário |
| CSS inline com `position` nos filhos do topbar | Muito baixa | Grep por `style=` com `position` no topbar antes de implementar |
| Scroll âncora (links internos `href="#id"`) deslocado pelo header | Muito baixa | Não há links âncora internos no FlowOps AI |
| Export HTML captura estrutura diferente | Muito baixa | Clone é do DOM completo — wrapper é incluído naturalmente |

### Critérios de aceite

- [ ] Header (topbar + nav) permanece fixo no topo ao rolar a página
- [ ] Em desktop: nenhuma diferença visual perceptível em relação ao estado anterior
- [ ] Nenhum modal fica atrás do site-header
- [ ] O botão de cada aba continua funcionando (ativação + conteúdo correto)
- [ ] `upd()` continua funcionando normalmente após troca de filtros
- [ ] Export HTML gera arquivo funcional
- [ ] `overflow-x:auto` do nav ainda funciona em telas estreitas

### Cenários de teste

| Cenário | Largura | Esperado |
|---------|---------|----------|
| Desktop padrão | 1280px | Header visualmente idêntico ao antes |
| Scroll longo | 1280px | Header permanece no topo, nav não some |
| Troca de aba | qualquer | `tab()` funciona, conteúdo correto é exibido |
| Troca de filtro | qualquer | `upd()` recalcula, KPIs atualizam |
| Abertura de modal de métrica | desktop | Modal aparece acima do header |
| Export HTML | desktop | Arquivo gerado funciona ao abrir no browser |
| Tela estreita (pré-Lote 2) | 480px | Header pode quebrar para 2 linhas — **aceitável neste lote**; o problema visual de mobile é resolvido nos Lotes 2–3 |

### Resultado

**Status: CONCLUÍDO**

Commit `b1759f3` — "refactor(lote-1): implementa wrapper sticky .site-header (D1)"

Este commit é o **baseline da refatoração** — inclui também os três documentos de diagnóstico e planejamento criados antes da implementação (`REFATORACAO_00_DIAGNOSTICO.md`, `REFATORACAO_01_DECISOES.md`, `REFATORACAO_02_PLANO_EXECUCAO.md`). Não é um commit exclusivo da D1, mas marca a linha de partida da refatoração.

### Regra de pré-staging — válida a partir do Lote 2

Antes de qualquer `git add` nos próximos lotes, documentar obrigatoriamente:

```bash
git status --short
git diff
git diff --cached
```

Revisar o diff completo antes de staged. Não fazer staging antes da revisão.

---

## Lote 2 — Topbar mobile — D3 + D5 absorvida

### Objetivo

Tornar o topbar funcional em telas mobile. Os filtros se empilham em coluna de forma controlada. O `.btn-export` fica dentro da região sticky com texto preservado, `aria-label` e área de toque adequada.

**Escopo corrigido:** o topbar contém apenas `.brand`, `.filters` (3 selects) e `.btn-export`. Não há Exportar MD, não há botão Status no topbar.

**D5 absorvida aqui:** `aria-label` e ajuste de área de toque do `.btn-export` são implementados neste lote, dentro das mudanças de D3.

**Fora do escopo deste lote:**
- Exportar Markdown (`.btn-md`, `exportMd()`) — pertence ao conteúdo das abas
- Status Reports — aba de conteúdo normal (`#ps`)
- Claude API / Gerador IA — sub-painel dentro da aba Status Reports

### Arquivos afetados

| Arquivo | Região | Tipo de mudança |
|---------|--------|-----------------|
| `flowops-ai/flowinsight.html` | CSS: `.topbar` | Remover `height:52px`; adicionar `height:auto`; media query para `flex-direction:column` |
| `flowops-ai/flowinsight.html` | CSS: `.filters` | Media query para `width:100%` + ajuste dos selects individuais |
| `flowops-ai/flowinsight.html` | CSS: `.btn-export` | Media query para padding vertical (área de toque ≥ 40px) — manter texto |
| `flowops-ai/flowinsight.html` | HTML: `.btn-export` | Adicionar `aria-label="Exportar HTML"` |

### Mudanças previstas

**CSS — topbar mobile:**
```css
@media (max-width: 600px) {
  .topbar {
    flex-direction: column;
    align-items: flex-start;
    height: auto;
    padding: 8px 16px;
    gap: 8px;
  }
  .filters {
    width: 100%;
  }
  .flt-wrap select {
    /* flex:1 ou max-width para dividir espaço — a definir na implementação */
  }
}
```

**CSS — área de toque do botão (D5 absorvida):**
```css
@media (max-width: 600px) {
  .btn-export {
    padding: 10px 12px;  /* aumenta área de toque sem remover texto */
  }
}
```

**HTML — acessibilidade do botão (D5 absorvida):**
```html
<!-- Adicionar aria-label ao elemento existente — sem alterar texto ou ícone -->
<button class="btn-export" onclick="exportHTML(this)"
  title="Baixar snapshot HTML para apresentar offline"
  aria-label="Exportar HTML">
  <i class="ti ti-download"></i> Exportar HTML
</button>
```

### O que NÃO pode ser alterado

- `applyTeamColor()` e CSS custom properties `--flt-c`, `--flt-bg`, `--flt-bgh`
- `exportHTML(this)` — função JS e atributo onclick
- Texto "Exportar HTML" e ícone `ti-download` no `.btn-export`
- Os 3 selects de filtro (`#sTeam`, `#sQ`, `#sS`) e seus eventos `onchange`
- Comportamento desktop do topbar
- `exportMd()` — não pertence ao topbar; não será tocada
- Quaisquer elementos fora do `<header class="topbar">` e `<div class="site-header">`

### Riscos

| Risco | Probabilidade | Mitigação |
|-------|--------------|-----------|
| Selects causando overflow em 320px | Média | Testar com `flex:1; min-width:0` nos selects individuais; tratar `#sQ` se largura mínima causar problema |
| Ícone custom do select (`.flt-wrap` com ícone posicionado) desalinhando em coluna | Baixa | Testar posicionamento após mudança de layout |
| `height:auto` quebrando visual em desktop | Muito baixa | Media query limitada a `max-width:600px` — desktop não é afetado |

### Critérios de aceite

- [ ] Em 600px+: topbar visualmente idêntico ao estado pós-Lote 1
- [ ] Em ≤ 600px: topbar em coluna, sem overflow horizontal da página
- [ ] Nenhum filho do topbar ultrapassa verticalmente o box do `.site-header`
- [ ] Os 3 selects de filtro visíveis e operáveis em mobile
- [ ] Troca de time altera cores corretamente (`applyTeamColor()`)
- [ ] `.btn-export` visível, clicável, com texto e ícone preservados
- [ ] `aria-label="Exportar HTML"` presente no `.btn-export`
- [ ] Área de toque do `.btn-export` ≥ 40px de altura em mobile
- [ ] `exportHTML()` funciona normalmente após as mudanças

### Cenários de teste

| Cenário | Largura | Status | Esperado |
|---------|---------|--------|----------|
| Desktop | 1280px | Validar na implementação | Topbar igual ao Lote 1 |
| Mobile padrão | 600px | Validar na implementação | Topbar em coluna, sem scroll horizontal |
| Mobile padrão | 390px | **VALIDAÇÃO PENDENTE APÓS IMPLEMENTAÇÃO** | Filtros visíveis, botão acessível |
| Mobile estreito | 360px | **VALIDAÇÃO PENDENTE APÓS IMPLEMENTAÇÃO** | Filtros operáveis, sem overflow |
| Mobile muito estreito | 320px | **VALIDAÇÃO PENDENTE APÓS IMPLEMENTAÇÃO** | Filtros operáveis, sem overflow |
| Troca de time | 600px | Validar na implementação | Cores mudam corretamente |
| Clique em Exportar HTML | 600px | Validar na implementação | Export funciona, `aria-label` presente |

> **Nota:** Larguras 390px, 360px e 320px não puderam ser validadas no ambiente de investigação pré-Lote 2 — o viewport da pane ficou limitado a aproximadamente 557px. Esses cenários devem ser testados após a implementação usando ferramentas externas (DevTools do navegador ou dispositivo físico). Não registrar sucesso por inferência.

### Estratégia de rollback

Commit atômico. `git revert HEAD` reverte apenas o Lote 2. Lote 1 permanece intacto.

---

## Lote 3 — Navegação mobile (D2)

### Objetivo

Tornar a navegação por abas utilizável em mobile com apresentação icon-only, mantendo a funcionalidade completa de `tab()` e a navegação por scroll horizontal como fallback.

### Arquivos afetados

| Arquivo | Região | Tipo de mudança |
|---------|--------|-----------------|
| `flowops-ai/flowinsight.html` | CSS: `.ntab` | Media query para icon-only |
| `flowops-ai/flowinsight.html` | HTML: `<nav>` botões | Adicionar `aria-label` em cada botão |

### Mudanças previstas

**HTML — `aria-label` nos botões** (ex):
```html
<button class="ntab on" onclick="tab(this,'v')" aria-label="Visão geral" title="Visão geral">
  <i class="ti ti-layout-dashboard"></i> Visão geral
</button>
```

`aria-label` é obrigatório e garante o nome acessível para tecnologias assistivas. `title` é recomendado para ambientes com hover. Nenhum dos dois torna automaticamente um ícone visualmente autoexplicativo em touch — o risco residual de aprendizado dos ícones é aceito para este produto.

**CSS — icon-only mobile:**
```css
@media (max-width: 600px) {
  .ntab {
    font-size: 0;        /* oculta texto */
    padding: 0 10px;
    min-width: 40px;
    min-height: 40px;
  }
  .ntab i {
    font-size: 16px;     /* restaura ícone */
  }
  .ntab.on {
    border-bottom: 2px solid var(--flt-c, #185FA5); /* fallback caso --flt-c não esteja definido */
  }
}
```

O `overflow-x:auto` do `.main-nav` já existente garante que mesmo com icon-only, telas de 390px, 360px e 320px ainda podem rolar para encontrar abas menos visíveis.

**Estado da aba ativa em mobile:**
- Cor diferenciada do ícone (herdada de `.ntab.on{color:#185FA5}`)
- Borda inferior: `var(--flt-c, #185FA5)` — o fallback `#185FA5` evita perda da borda caso `applyTeamColor()` ainda não tenha definido `--flt-c`
- Indicação visível mesmo sem texto ✓

### O que NÃO pode ser alterado

- Função `tab(el, id)` — nenhuma mudança em JS
- Classe `.on` e seu uso em `.ntab` e `.fp`
- IDs dos painéis (`pv`, `pf`, `pd`, `pc`, `pr`, `pas`, `ps`, `pm`, `pcal`, `pg`, `pab`)
- Ícones Tabler existentes em cada botão
- Comportamento desktop do nav

### Riscos

| Risco | Probabilidade | Mitigação |
|-------|--------------|-----------|
| `font-size:0` afetando o ícone `<i>` | Baixa | `font-size:16px` explícito no `<i>` restaura o ícone |
| Aba ativa não visível ao carregar em mobile | Baixa | Scroll automático pode ser adicionado como JS mínimo se necessário |
| 11 ícones ainda não cabem em 390px, 360px, 320px | Baixa | `overflow-x:auto` garante scroll; icon-only é ~460px (11 × ~40px + gaps) — cabe em 600px; scroll esperado e aceitável em 390px/360px/320px |

### Critérios de aceite

- [ ] Em 600px+: nav visualmente idêntico ao estado pós-Lote 2
- [ ] Em ≤ 600px: apenas ícones visíveis, sem texto
- [ ] Em 320px: nav com scroll horizontal funcional
- [ ] Aba ativa destacada visualmente por cor/borda
- [ ] Troca de aba funciona corretamente (conteúdo correto exibido)
- [ ] `aria-label` presente em todos os 11 botões
- [ ] `tab()` não foi alterada

### Cenários de teste

| Cenário | Largura | Status | Esperado |
|---------|---------|--------|----------|
| Desktop | 1280px | Validar na implementação | Nav com ícone + texto — idêntico ao Lote 2 |
| Mobile | 600px | Validável no ambiente | Nav icon-only, todas as 11 abas visíveis sem scroll |
| Mobile | 390px | **VALIDAÇÃO MANUAL PENDENTE** | Nav icon-only, aba ativa com destaque |
| Mobile estreito | 360px | **VALIDAÇÃO MANUAL PENDENTE** | Nav rola horizontalmente, abas operáveis |
| Mobile muito estreito | 320px | **VALIDAÇÃO MANUAL PENDENTE** | Nav rola horizontalmente, abas operáveis |
| Troca de aba | 390px | **VALIDAÇÃO MANUAL PENDENTE** | Conteúdo correto; 8 lazy renders funcionando |
| Troca de time | 390px | **VALIDAÇÃO MANUAL PENDENTE** | Cor da aba ativa segue `var(--flt-c, #185FA5)` |
| Acessibilidade | qualquer | Validar na implementação | `aria-label` e `title` presentes em todos os 11 botões |

> **Nota:** Larguras 390px, 360px e 320px não são validáveis no ambiente de desenvolvimento atual (viewport mínimo da pane ~550px). Esses cenários devem ser testados após a implementação com DevTools do navegador ou dispositivo físico. Estimativas matemáticas não substituem validação executada.

### Estratégia de rollback

Commit atômico. `git revert HEAD` reverte apenas o Lote 3. Lotes 1 e 2 permanecem.

---

## Lote 4 — Overflow de tabelas (D4)

### Objetivo

Evitar que tabelas e elementos com largura fixa causem overflow horizontal da página em mobile, adicionando wrappers com `overflow-x:auto` nos containers pai estáticos.

### Arquivos afetados

| Arquivo | Região | Tipo de mudança |
|---------|--------|-----------------|
| `flowops-ai/flowinsight.html` | HTML: containers de DORA, Métricas de fluxo, CFD, Health bars | Adicionar classe `tbl-scroll` nos wrappers adequados |
| `flowops-ai/flowinsight.html` | CSS | Adicionar `.tbl-scroll { overflow-x:auto; -webkit-overflow-scrolling:touch; }` |

### Elementos com largura fixa (a preservar internamente)

| Classe | Largura | Seção | Função de render |
|--------|---------|-------|-----------------|
| `.dbname` | 180px | DORA benchmark | `renderDora()` linha ~4785 |
| `.wip-stage` | 105px | Métricas de fluxo | `renderFlowMetrics()` linha ~5139 |
| `.cf-orig-lbl` | 150px | CFD origem | `renderFlowMetrics()` |
| `.hs-label` | 170px | Health bars Multi-time | `renderMultiTeam()` linha ~4002 |

### Mudanças previstas

**CSS — nova regra:**
```css
.tbl-scroll {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}
```

**HTML — adicionar classe nos wrappers pai** (sem alterar elementos internos):
```html
<!-- DORA benchmark rows -->
<div class="tbl-scroll">
  <!-- renderDora() injeta aqui — não alterar -->
</div>

<!-- Métricas de fluxo — WIP/Aging table -->
<div class="tbl-scroll">
  <!-- renderFlowMetrics() injeta aqui — não alterar -->
</div>
```

### O que NÃO pode ser alterado

- Nenhuma das 4 classes com largura fixa (`.dbname`, `.wip-stage`, `.cf-orig-lbl`, `.hs-label`)
- `renderDora()`, `renderFlowMetrics()`, `renderMultiTeam()` — nenhuma mudança em JS
- Templates HTML dentro das funções de renderização
- Estrutura interna das tabelas
- Comportamento desktop

### Riscos

| Risco | Probabilidade | Mitigação |
|-------|--------------|-----------|
| Wrapper `tbl-scroll` quebrando seletor CSS pai existente | Baixa | Usar classe genérica nova; verificar se há seletores com `>` direto |
| Gráfico Chart.js dentro de wrapper com `overflow:auto` causando resize incorreto | Muito baixa | Gráficos ficam em containers `.cc`, não dentro de tabelas |
| Wrapper adicionado em seção errada (dentro de elemento dinâmico) | Baixa | Verificar que o wrapper é elemento **estático** no HTML, externo à área de inject do JS |

### Critérios de aceite

- [ ] Em mobile: tabelas das 4 seções afetadas rolam horizontalmente sem vazar a página
- [ ] Em desktop: nenhuma diferença visual
- [ ] `renderDora()`, `renderFlowMetrics()`, `renderMultiTeam()` continuam funcionando
- [ ] Gráficos Chart.js não apresentam deformação
- [ ] Larguras internas (`.dbname`, `.wip-stage`, `.cf-orig-lbl`, `.hs-label`) preservadas

### Cenários de teste

| Cenário | Largura | Esperado |
|---------|---------|----------|
| Aba DORA em mobile | 390px | Tabela benchmark rola horizontalmente |
| Aba Métricas de fluxo em mobile | 390px | Tabela WIP/Aging rola horizontalmente |
| Aba Multi-time em mobile | 390px | Health bars roláveis |
| Desktop — todas as abas | 1280px | Nenhuma diferença visual |
| Troca de filtro em mobile | 390px | Re-render das seções mantém funcionamento |

### Estratégia de rollback

Commit atômico. `git revert HEAD` reverte apenas o Lote 4. Lotes 1–3 permanecem.

---

## Regras de proteção — válidas para todos os lotes

Durante toda a refatoração, os seguintes elementos **nunca podem ser alterados**:

| Categoria | Elementos protegidos |
|-----------|---------------------|
| Métricas e cálculo | `calcHealth()`, `calcHealthFrom()`, `upd()`, `updateKpi()` |
| Dados | `D`, `DB`, `DG`, `METAS`, `SPRINT_DATES`, `STAGES`, `CURRENT_Q`, `CURRENT_S` |
| Filtros | `gq()`, `gi()`, `gd()`, `applyTeamColor()`, `--flt-c`, `--flt-bg`, `--flt-bgh` |
| KPIs DOM | IDs `k0`–`k10`, `ks0`–`ks10`, `kd0`–`kd10`, `km0`–`km10`, `kc0`–`kc10` |
| Painéis | IDs `pv`, `pf`, `pd`, `pc`, `pr`, `pas`, `ps`, `pm`, `pcal`, `pg`, `pab` |
| DORA | `renderDora()`, `setTier()`, benchmarks |
| Gráficos | `drawCharts()`, `buildCFD()` |
| Export | `exportHTML()`, `exportMd()` |
| IA | Claude API, `openStatusModal()`, geração de Status Report |
| Multi-time | `renderMultiTeam()` |
| Comportamento desktop | Qualquer breakpoint deve usar `max-width` para não afetar desktop |

---

## Escopo explicitamente fora desta refatoração

- Atualização de Q3/Q4, sprint atual, datas
- OKRs, forecast, IA preditiva
- Integrações Jira / Azure DevOps
- Modularização em múltiplos arquivos
- Frameworks, TypeScript, bundler, backend
- Limpeza geral de CSS (css inline, comentários, organização)
- Padronização de todos os breakpoints (D6 — adiada)
- Modais em mobile (`.metric-modal`)
- Fallback para dependências CDN offline

---

*Documento atualizado após investigação factual pré-Lote 3 (2026-09-29). Lote 2 concluído em commit 6404235. Cálculo de largura icon-only corrigido: ~460px (não ~220px). Documentação de tab() corrigida: 8 lazy renders (não 2). CSS do estado ativo atualizado com fallback de cor. Nenhum arquivo de código foi alterado.*
