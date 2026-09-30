# Refatoração FlowOps AI — Decisões Arquiteturais
**Documento:** `REFATORACAO_01_DECISOES.md`  
**Data:** 2026-09-24  
**Referência:** `REFATORACAO_00_DIAGNOSTICO.md`  
**Revisão:** Decisões consolidadas — D1–D5 concluídas · D6 adiada · Refatoração responsiva encerrada (2026-09-30)

---

## 1. Objetivo

Tornar o FlowOps AI responsivo e sustentável para telas mobile sem introduzir complexidade desnecessária, sem quebrar as funcionalidades existentes e sem comprometer a característica fundamental do produto: **ser aberto diretamente no navegador, sem instalação, build ou backend**.

A refatoração se limita a:
- CSS e estrutura de layout
- Comportamento de navegação em mobile
- Legibilidade de tabelas e grids em telas estreitas

Ficam fora do escopo desta refatoração: lógica de negócio, dados mockados, fórmulas de métricas, integração Claude API, sistema de export, DORA tiers e qualquer evolução funcional.

---

## 2. Princípios da refatoração

**YAGNI** — Não será introduzido nada que o produto atual não exija concretamente. Sem frameworks, bundlers, TypeScript, backend, gerenciamento de estado externo ou dependências adicionais.

**Preservação de compatibilidade** — O arquivo `flowinsight.html` deve continuar sendo aberto diretamente no navegador (via `file://` ou servidor HTTP simples). Qualquer decisão que quebre essa característica é rejeitada.

**Sem duplicação de lógica** — Nenhuma mudança de CSS deve exigir mudança paralela em JavaScript para compensar, exceto onde isso for inevitável e documentado.

**Menor superfície de risco** — Preferir soluções CSS puras a soluções JavaScript. Preferir mudanças aditivas a mudanças destrutivas.

**Separação de frentes** — Esta refatoração não corrige bugs de dados, não atualiza sprints/quarters, não adiciona funcionalidades.

---

## 3. D1 — Sticky Header: Topbar + Main Navigation

### Problema

O `main-nav` usa `position:sticky; top:52px`, dependendo da altura fixa do `.topbar`. Quando o topbar cresce verticalmente (filtros "quebram" para segunda linha em mobile), o nav continua colado em `52px` e sobrepõe o final do topbar — ou sobrepõe o conteúdo.

### Estado atual

```css
/* linha ~46 */
.topbar {
  position: sticky;
  top: 0;
  z-index: 100;
  height: 52px;       /* altura fixa */
  flex-wrap: wrap;    /* permite crescimento, mas o nav não acompanha */
}

/* linha ~65 */
.main-nav {
  position: sticky;
  top: 52px;          /* hardcoded — acoplado à altura do topbar */
  z-index: 99;
}
```

### Decisão adotada

Criar um wrapper `.site-header` com `position:sticky; top:0; z-index:100` contendo `.topbar` e `.main-nav`. Os dois elementos perdem seus `position:sticky` individuais. A dependência `main-nav → top:52px` é eliminada.

```
body (flex-direction:column)
  └── .site-header  ← position:sticky; top:0; z-index:100
        ├── .topbar   (sem position próprio)
        └── .main-nav (sem position próprio; sem top:52px)
  └── .content
```

A altura do cabeçalho passa a ser determinada naturalmente pelo conteúdo.

### Justificativa

Única solução que elimina o hardcode sem criar um substituto igualmente frágil. Alternativas como `ResizeObserver + JS` ou `CSS var atualizada via JS` trocam um valor hardcoded por um mecanismo que precisa ser mantido sincronizado. O wrapper é CSS puro, sem JavaScript, compatível com `file://`.

### Impactos esperados

| Área | Impacto |
|------|---------|
| z-index | `.site-header` herda z-index:100; `main-nav` pode ter z-index próprio removido |
| Overflow horizontal do nav | Mantém `overflow-x:auto` — sem alteração |
| Desktop | Comportamento visualmente idêntico ao atual |
| Mobile | Topbar cresce livremente sem deslocar o nav |
| Scroll da página | `.content` continua abaixo do header completo |
| Modais | Verificar z-index do `.metric-modal` para garantir que fica acima de `.site-header` |
| Export HTML | `exportHTML()` clona `document.documentElement` — wrapper incluído naturalmente |

### Riscos

| Risco | Nível |
|-------|-------|
| Modal sobreposta pelo header | Baixo — verificar z-index antes de implementar |
| CSS inline com `position` nos filhos do topbar | Baixo — auditar antes |

**Status: CONCLUÍDA** — commit `b1759f3`

---

## 4. D2 — Navegação mobile (11 abas)

### Problema

O `main-nav` contém 11 `<button class="ntab">` com ícone + texto. Em telas ≤ 480px, os botões ficam muito estreitos. O scroll horizontal funciona mas não é intuitivo em mobile.

### Estado atual

```html
<!-- linha 759–771 -->
<nav class="main-nav">
  <button class="ntab on" onclick="tab(this,'v')">
    <i class="ti ti-layout-dashboard"></i> Visão geral
  </button>
  <!-- ... 10 botões adicionais -->
</nav>
```

```js
// linha ~3116 — tab() depende apenas da classe .on; não depende do texto
// Código real verificado em 2026-09-29: a função possui 8 renderizações lazy (não 2)
function tab(el,id){
  document.querySelectorAll('.ntab').forEach(t=>t.classList.remove('on'));
  el.classList.add('on');
  document.querySelectorAll('.fp').forEach(p=>p.classList.remove('on'));
  document.getElementById('p'+id).classList.add('on');
  if(id==='r') renderQuarterReport();
  if(id==='m') renderMultiTeam();
  if(id==='f') renderFlowMetrics();
  if(id==='d') renderDora();
  if(id==='c') renderCargaFalhas();
  if(id==='cal') renderCalendario();
  if(id==='g') renderGlossario();
  if(id==='as') renderAcompanhamento();
}
```

### Decisão adotada

Em telas mobile, aplicar **versão icon-only** via CSS puro:

- Ocultar o texto dos botões com `font-size:0`; restaurar ícone com `font-size:16px` no `<i>`
- Preservar `overflow-x:auto` como fallback para telas estreitas (390px, 360px, 320px)
- Não criar hamburger, não criar drawer, não adicionar JavaScript
- Preservar `tab()` integralmente — nenhuma alteração JavaScript
- Manter indicação visual da aba ativa: cor diferenciada do ícone + borda inferior com `var(--flt-c, #185FA5)` (fallback evita perda da borda caso a variável ainda não esteja definida)
- Adicionar `aria-label` obrigatório em cada botão (nome acessível para tecnologias assistivas)
- Adicionar `title` como auxílio em ambientes com hover (recomendado, não obrigatório)
- Área de toque mínima: aproximadamente 40×40px — requisito interno de usabilidade do FlowOps AI (não é declaração de conformidade WCAG)

### Justificativa

CSS puro, zero impacto em `tab()`, usa os ícones Tabler já presentes em todos os botões. A largura do nav cai de ~1591px (scrollWidth medido) para aproximadamente 460px com icon-only (11 abas × ~40px + gaps). Essa largura cabe no espaço interno disponível em 600px (~552px disponíveis). Em 390px, 360px e 320px o scroll horizontal continua necessário para parte das abas — isso é esperado e aceitável. Para o perfil do produto (demonstração, uso por profissional familiarizado), ícones sem texto são suficientes com o botão ativo destacado.

### Riscos

| Risco | Nível |
|-------|-------|
| Ícone ambíguo sem texto para usuário novo | Baixo — produto demonstrado em contexto profissional; risco residual de aprendizado dos ícones é aceito |
| `aria-label` e `title` em ambientes touch | Baixo — `aria-label` fornece nome acessível para tecnologias assistivas; `title` auxilia em hover; nenhum dos dois garante que o ícone seja visualmente autoexplicativo em touch |

**Status: CONCLUÍDA** — commit `30b6591`

---

## 5. D3 — Filtros mobile (topbar)

### Problema

O `.topbar` contém `.brand`, `.filters` (3 selects), 2 botões de export, 1 botão de status e o contador — todos em uma linha. Em telas estreitas, os elementos quebram para segunda linha de forma não controlada.

### Estado atual

```css
/* linha ~46 */
.topbar { display:flex; align-items:center; gap:16px; height:52px; flex-wrap:wrap; }
.filters { display:flex; align-items:center; gap:8px; }
```

Elementos no topbar (ordem HTML):
1. `.brand` (logo + título)
2. `.filters` (3 `.flt-wrap` com select)
3. Botão exportar HTML
4. Botão exportar MD
5. Botão Status (modal)
6. `#visitCount`

### Decisão adotada

Em mobile, `.topbar` usa `flex-direction:column` e `height:auto`. Os filtros se ajustam à largura disponível. Os três selects dentro de `.filters` precisam permitir quebra controlada ou encolhimento sem provocar overflow horizontal da página.

Requisitos adicionais de validação:
- Testar em 320px, 360px, 390px, 600px e desktop
- Não criar painel colapsável
- Não adicionar JavaScript
- Preservar todos os filtros e todos os comportamentos existentes
- `width:100%` em `.filters` não é suficiente se os três controles ainda causarem overflow — o tratamento dos selects individuais também deverá ser considerado na implementação

### Justificativa

CSS puro, layout previsível, nenhum risco para as funções de filtro (`gq()`, `gi()`, `gd()`, `applyTeamColor()`). A altura maior do topbar em mobile é aceitável após D1.

### Riscos

| Risco | Nível |
|-------|-------|
| Selects individuais causando overflow em 320px | Médio — requer validação em implementação |
| `.brand` empurrado para coluna quebra identidade visual | Baixo — layout ainda reconhecível |

**Status: CONCLUÍDA** — commit `6404235`

---

## 6. D4 — Tabelas e elementos com largura fixa

**Status: CONCLUÍDA** — commit `739b94d`

> Decisão original aprovada. Escopo corrigido após investigação factual (2026-09-30): os componentes originalmente listados não correspondem aos problemas reais encontrados. Os quatro problemas comprovados substituem as premissas anteriores.

---

### 6.1 Correção de premissas antigas

#### CSS legado — `.dbname`, `.dbrow`, `.dbval`, `.dbtrack`, `.dbfill`

As classes abaixo estão definidas no CSS do produto mas **nunca são referenciadas no HTML estático nem nos templates JavaScript**. São CSS morto — código legado substituído pelas classes `dband-*` na versão atual.

```css
.dbrow   { display:flex; ... }
.dbname  { width:180px; ... }   /* era listado como "DORA benchmark" — INATIVO */
.dbtrack { flex:1; ... }
.dbfill  { height:10px; ... }
.dbval   { width:100px; ... }
```

**Constatação:** nenhuma dessas classes causa overflow em mobile porque nenhuma delas é renderizada.  
**Decisão:** não remover esse CSS nesta refatoração. Não faz parte do Lote 4. Registrar como dívida técnica futura.

#### `.hs-label` — atribuição corrigida

A documentação anterior associava `.hs-label{width:170px}` ao Multi-time (`renderMultiTeam`). A investigação factual confirma:

- `.hs-label` é utilizada exclusivamente pela função `scoreBar()`, chamada apenas de `renderQuarterReport()`.
- Multi-time **não usa** `.hs-label`.
- `.hs-label` **não apresentou overflow** em nenhum dos viewports testados (390px, 360px, 320px).
- **Nenhuma alteração** será feita em `.hs-label` no Lote 4.

#### DORA — `.dband-row`, `.dband-info`, `.dband-right`

As classes com `min-width` na aba DORA foram medidas em todos os viewports:

| Classe | Valor | Viewports testados | Resultado |
|--------|-------|-------------------|-----------|
| `.dband-info` | `min-width:160px` | 390px, 360px, 320px | Sem overflow |
| `.dband-right` | `min-width:76px` | 390px, 360px, 320px | Sem overflow |
| `.dband-row` | — | 390px, 360px, 320px | Page overflow: 0 |

**Constatação:** DORA não apresenta problema de overflow em mobile.  
**Decisão:** nenhuma correção DORA entra no Lote 4.

---

### 6.2 Componentes já protegidos — sem ação necessária

Os seguintes componentes foram verificados e já possuem `overflow-x:auto` nos containers adequados, ou não apresentaram overflow:

| Componente | Aba | Proteção existente | Resultado |
|---|---|---|---|
| Tabela "Itens Planejados" | Status (`#ps`) | `<div style="overflow-x:auto">` linha 1498 | ✅ Protegido |
| Tabela "Transbordo sprint anterior" | Status (`#ps`) | `<div style="overflow-x:auto">` linha 1524 | ✅ Protegido |
| Tabela `.cap-tbl` "Capacidade" | Status (`#ps`) | `<div style="overflow-x:auto">` linha 1662 | ✅ Protegido |
| Tabela `.rit-tbl` "Ritos e Cerimônias" | Status (`#ps`) | `<div style="overflow-x:auto">` linha 1685 | ✅ Protegido |
| Tabela `.cap-tbl` individual | Acompanhamento (`#pas`) | `<div style="overflow-x:auto">` linha 3449 | ✅ Protegido |
| Tabela "Capacity × Velocity" | Acompanhamento (`#pas`) | `<div style="overflow-x:auto">` linha 3498 | ✅ Protegido |
| `#wipBars` (`.wip-stage{105px}`) | Métricas de fluxo | — | ✅ Sem overflow |
| `#cfOrigin` (`.cf-orig-lbl{150px}`) | Saúde da qualidade | — | ✅ Sem overflow |
| `.hs-row` / `.hs-label{170px}` | Quarter Report | — | ✅ Sem overflow |
| `.dband-*` | DORA | — | ✅ Sem overflow |

---

### 6.3 Quatro problemas comprovados — escopo real do Lote 4

#### Problema 1 — Aging (`#agingList`, aba Métricas de fluxo)

**Elemento:** `#agingList` — DIV container da tabela `.mt-tbl` de aging  
**Causa:** tabela com coluna `vs p85` (barra interna `width:90px`) excede o container em mobile  
**Medições:** overflow de página de +39px (390px), +68px (360px), +108px (320px)

**Solução aprovada — CSS puro, sem wrapper, sem JS:**

```css
#agingList {
  overflow-x: auto;
}
```

Validado em runtime: `pageOvf` passa de 39px → 0. `#agingList` é o único elemento no seu `.cc` pai que transborda. Nenhuma alteração em `renderFlowMetrics()`.

---

#### Problema 2 — Quarter Report (`.rpt-table`, aba Quarter Report)

**Elementos envolvidos:**
- `.rpt-sec{overflow:hidden}` — container de cada seção do report (linhas 236, 2827)
- `.rpt-body` — container imediato da tabela, entre a tabela e `.rpt-sec`
- `.rpt-table` — a tabela propriamente dita (~403px em viewports estreitos)

**Causa:** `.rpt-sec{overflow:hidden}` é necessário para preservar o `border-radius:12px` do card. Em mobile, o `overflow:hidden` corta silenciosamente os conteúdos que transbordam, tornando colunas da tabela inacessíveis (82px cortados a 390px, 136px cortados a 320px).

**Solução aprovada — CSS puro, sem wrapper, sem JS:**

```css
.rpt-body {
  overflow-x: auto;
}
```

**Preservar obrigatoriamente:**

```css
.rpt-sec {
  overflow: hidden;   /* NÃO remover — preserva border-radius do card */
}
```

Validado em runtime: `pageOvf` passa a 0. Todos os 20 `.rpt-body` ficam com scroll interno independente. `.rpt-sec.scrollWidth` normaliza para igual ao `clientWidth` — o `overflow:hidden` não clipa mais nada; mantém apenas o recorte visual do card.

---

#### Problema 3 — Calendário (`.cal-tbl`, aba Calendário)

**Elemento:** `<table class="cal-tbl">` — tabela de 7 colunas (Quarter, Início, Encerramento, Sprints, Dias Úteis, Semana Planejamento, Status)  
**Causa:** tabela com largura mínima de conteúdo ~597px; excede qualquer container até ≥600px  
**Medições:** overflow de +41px (600px), +250px (390px), +280px (360px), +320px (320px)

**Cadeia DOM:**
```
#pcal.fp
  div.cc (cabeçalho com abas de calendário)
  div#calContent.cc
    div (legend — responsivo, NÃO overflow)
    div.cal-year-grid (mini-calendários — responsivo, 2 cols a ≤600px, NÃO overflow)
    table.cal-tbl  ← causa do overflow
```

**Por que `#calContent{overflow-x:auto}` foi descartado:** `#calContent` contém também `legend` e `yearGrid`. Com overflow no container pai, scrollar para acessar colunas da tabela deslocaria o calendário visual inteiro para fora do viewport — UX inaceitável.

**Solução aprovada — CSS + 1 linha em `renderCalendario()`:**

```css
.cal-table-scroll {
  overflow-x: auto;
}
```

Alteração estrutural autorizada em `renderCalendario()` (linha 5997):

```js
// Envolver somente `tbl` — legend e yearGrid permanecem sem scroll
document.getElementById('calContent').innerHTML =
  legend + yearGrid + '<div class="cal-table-scroll">' + tbl + '</div>';
```

**Não usar `style="overflow-x:auto"` inline no template.** A responsabilidade de apresentação permanece no CSS.  
**Não alterar** `legend`, `yearGrid`, conteúdo da `.cal-tbl`, nem `renderCalSprints()` / `renderCalDeploys()`.

---

#### Problema 4 — Multi-time (`.mt-tbl` #1 e #2, aba Multi-time)

**Elementos:**
- `.mt-tbl` #1 — "Métricas da sprint atual", linha 4094 (container `.cc` com tabela como único filho)
- `.mt-tbl` #2 — "DORA comparativo", linha 4144 (container `.cc` com `div.cgh-mt` + tabela)

**Causa:** 4 colunas com conteúdo mínimo ≥332px em viewports 360px e 320px; os containers `.cc` ficam menores que a tabela  
**Medições:** overflow de página de +19px (360px), +59px (320px). Sem overflow a 390px+.

**Observação importante:** a 320px existe overflow residual de ~42px após correção das tabelas, causado pelos canvases Chart.js (`#cMTtp`, `#cMTlt`) dentro do `.g2` na mesma aba — mesma origem do bug canvas da Visão Geral. **Esse overflow residual não pertence ao Lote 4.**

**Confirmado:** ambas as `.mt-tbl` são filhas DIRETAS de seus `.cc` — o seletor `:has()` funciona corretamente e não afeta o `.cc` pai do `#agingList` (onde `.mt-tbl` é neta, não filha direta).

**Solução aprovada — CSS puro com `:has()`, sem alteração em `renderMultiTeam()`:**

```css
#pm .cc:has(> .mt-tbl) {
  overflow-x: auto;
}
```

Suporte: Chrome 105+, Safari 15.4+, Firefox 121+. Compatível com o perfil de uso do produto.  
Não afeta outros `.cc` no painel (chart cards não contêm `.mt-tbl` como filha direta).  
Não altera `renderMultiTeam()`.

---

### 6.4 Bug responsivo separado — Canvas / Chart.js

**Classificação: BUG RESPONSIVO — CANVAS / CHART.JS — FORA DO LOTE 4**

**Elementos encontrados:**

| Canvas | Aba | Largura fixa observada | Page overflow a 390px |
|--------|-----|----------------------|----------------------|
| `#cOv` | Visão geral | ~514px | +167px (com outros) |
| `#cLT` | Visão geral | ~514px | +167px (com outros) |
| `#cCFD` | Visão geral | ~514px | +167px (com outros) |
| `#cMTtp` | Multi-time | ~300px (320px) | contribui para residual |
| `#cMTlt` | Multi-time | ~300px (320px) | contribui para residual |

**Problema:** os canvases não se ajustam ao viewport em mobile, causando overflow horizontal da página. A configuração responsiva do Chart.js (`responsive`, `maintainAspectRatio`, containers com `position:relative; width:100%`) provavelmente requer revisão.

**Decisão:** não corrigir no Lote 4. Requer investigação dedicada antes da implementação. Não registrar solução definitiva neste documento.

---

### 6.5 Riscos atualizados

| Risco | Nível |
|-------|-------|
| `.rpt-sec{overflow:hidden}` removido acidentalmente | Baixo — documentado explicitamente como protegido |
| `:has()` sem suporte no ambiente de visualização | Muito baixo — Chrome/Safari/Firefox modernos suportam |
| Overflow residual Multi-time (canvases) confundido com regressão do Lote 4 | Médio — documentado separadamente; esperado e aceito |
| `#agingList{overflow-x:auto}` afetando `#alertOutput` (sibling) | Baixo — `#alertOutput` não overflow; medido: só `#agingList` transborda no `.cc` pai |

---

## 7. D5 — Tratamento responsivo do botão Exportar HTML

### Problema — revisado após investigação factual

A definição anterior de D5 presumia 3 botões no topbar (Exportar HTML, Exportar MD e Status). A investigação confirmou que:

- O topbar contém **apenas um botão**: `.btn-export` ("Exportar HTML")
- **Exportar Markdown** está dentro dos painéis de conteúdo de cada aba, como `.btn-md` — não pertence ao topbar
- **Status Reports** é uma aba de conteúdo normal (`#ps`) com sub-navegação — não pertence ao topbar
- Não existe `openStatusModal()` no código; a premissa era incorreta

O problema real identificado: com `height:52px` no `.topbar` e `flex-wrap:wrap`, o `.btn-export` renderiza fora da caixa do topbar em mobile — visualmente abaixo do `.main-nav`, portanto fora da região sticky. O botão fica inacessível após qualquer scroll.

### Estado atual (pós-Lote 1)

```html
<!-- linha ~756 — único botão no topbar -->
<button class="btn-export" onclick="exportHTML(this)"
  title="Baixar snapshot HTML para apresentar offline">
  <i class="ti ti-download"></i> Exportar HTML
</button>
```

```css
/* CSS atual do botão — sem aria-label, área de toque ~26px de altura */
.btn-export { white-space:nowrap; padding:5px 12px; font-size:12px; }
```

### Decisão adotada — ABSORVIDA POR D3

D5 é absorvida pelo Lote 2 como complemento a D3. D3 (remoção do `height:52px` + `height:auto` + `flex-direction:column`) já resolve o problema estrutural — o `.btn-export` passará a ficar dentro da região sticky naturalmente.

O que permanece de D5, implementado **dentro do Lote 2**:

- Adicionar `aria-label="Exportar HTML"` ao botão (acessibilidade obrigatória)
- Garantir área de toque confortável (padding vertical → altura efetiva ≥ 40px em mobile)
- **Não converter para icon-only** — com D3, o botão fica em linha própria e o texto cabe sem problema

### Justificativa

D3 já resolve o posicionamento. Icon-only seria uma mudança estética sem necessidade funcional. Texto + ícone preservados mantêm a clareza do botão para o perfil do produto.

### Riscos

| Risco | Nível |
|-------|-------|
| Área de toque insuficiente sem ajuste de padding | Baixo — resolver com `padding` em media query |

**Status: CONCLUÍDA** — absorvida por D3 (commit `6404235`)

---

## 8. D6 — Breakpoints: unificação do sistema

### Problema

CSS atual usa 4 valores distintos de breakpoint (480px, 600px, 640px, 720px) sem sistema consistente.

### Estado atual

| Breakpoint | Ocorrências | Classes afetadas |
|------------|-------------|-----------------|
| 480px | 1 | `.dora-g2x3` → 1 col |
| 600px | 3 | `.g2`, `.mt3`, `.cal-year-grid` |
| 640px | 2 | `.bd-grid`, `.ab-ps-grid` |
| 720px | 1 | `.dora-g2x3` → 2 col |

### Decisão adotada

**Adiar** a padronização geral dos breakpoints para uma refatoração isolada futura.

Motivo: migrar `640px → 600px` e `720px → 768px` introduziria alterações sem defeito funcional comprovado, aumentando o risco desta refatoração sem benefício direto para o objetivo de responsividade mobile.

Regra para esta refatoração: novas regras CSS poderão usar `600px` quando esse breakpoint atender ao comportamento necessário. Os 7 breakpoints existentes **não serão alterados**.

A padronização geral fica registrada como dívida técnica futura a ser tratada em ciclo separado.

**Status: ADIADA**

---

## 9. Matriz consolidada

| ID | Decisão | Recomendação adotada | Risco | Complexidade | Prioridade | Status |
|----|---------|----------------------|-------|--------------|------------|--------|
| D1 | Wrapper sticky único (`.site-header`) | CSS puro — wrapper agrupa topbar + nav | Baixo | Baixa | **OBRIGATÓRIA** — pré-requisito para todas as demais | **CONCLUÍDA** |
| D2 | Nav mobile: icon-only | Icon-only via CSS + `aria-label` + `title` + overflow fallback | Baixo | Baixa | **RECOMENDADA** | **CONCLUÍDA** |
| D3 | Filtros mobile | `flex-direction:column` + `height:auto` + selects encolhíveis | Médio | Baixa | **RECOMENDADA** | **CONCLUÍDA** |
| D4 | Tabelas: `overflow-x:auto` nos containers reais | 3 regras CSS + 1 linha JS em `renderCalendario()` — escopo corrigido após investigação factual | Baixo | Baixa | **RECOMENDADA** | **CONCLUÍDA** |
| D5 | `.btn-export`: `aria-label` + área de toque | Absorvida por D3 — sem icon-only; texto preservado; `aria-label` + padding mobile dentro do Lote 2 | Baixo | Mínima | **ABSORVIDA POR D3** | **CONCLUÍDA** |
| D6 | Breakpoints canônicos | Adiar — sem defeito funcional comprovado | Muito baixo | Baixa | **PODE ESPERAR** | **ADIADA** |

---

## 10. Dependências entre decisões

```
D1 (wrapper sticky)
  └── deve ser implementada PRIMEIRO — pré-requisito estrutural
        ├── D3 (filtros mobile) — D1 garante que topbar pode crescer sem quebrar nav
        │     └── D5 (absorvida por D3) — `aria-label` e área de toque do `.btn-export` implementados dentro do Lote 2
        ├── D2 (nav icon-only) — implementar após D1; independente de D3/D5
        └── D4 (overflow tabelas) — independente; pode ocorrer em qualquer ordem após D1
```

D6 foi removida da cadeia de dependências — era mencionada erroneamente como pré-requisito para D2–D5. As novas regras CSS desta refatoração usarão `600px` diretamente, sem precisar aguardar padronização geral.

**Ordem recomendada de implementação (por lote):**
1. D1 → 2. D3 + D5 → 3. D2 → 4. D4

---

## 11. O que NÃO será feito nesta refatoração

| Item | Motivo |
|------|--------|
| Migração de breakpoints (640→600, 720→768) | D6 adiada — sem defeito funcional |
| Hamburger ou drawer de navegação | D2 resolve sem JavaScript |
| Painel de filtros colapsável | D3 resolve com CSS puro |
| Modais em mobile (`.metric-modal`) | Funcionam; risco não justifica o escopo |
| Fallback CDN offline | Mudança de arquitetura — escopo separado |
| CSS inline (`style=""`) | Limpeza de código — frente separada |
| Dados mockados (Q3/Q4, sprints, datas) | Atualização de dados — frente separada |
| Gráficos Chart.js (responsividade interna) | Bug responsivo confirmado em Visão Geral e Multi-time — requer investigação dedicada separada; não pertence ao Lote 4 |
| Separação do arquivo HTML em múltiplos arquivos | Quebra característica de arquivo único |
| TypeScript, bundler, framework CSS | YAGNI |
| OKRs, forecast, IA preditiva, integrações | Evolução funcional — roadmap Fase 2–4 |

---

*Documento atualizado após investigação factual pré-Lote 3 (2026-09-29): cálculo de largura icon-only corrigido (460px, não 220px); tab() documentada com 8 lazy renders (não 2); estratégia D2 complementada com fallback de cor, área de toque como requisito interno e limites de aria-label/title. Nenhum arquivo de código foi alterado.*

*Documento atualizado após investigação factual e validação da solução mínima do Lote 4 (2026-09-30): D4 revisada com escopo real — premissas antigas de `.dbname`, `.hs-label` e DORA corrigidas; quatro problemas comprovados documentados (Aging, Quarter Report, Calendário, Multi-time); bug responsivo Canvas/Chart.js registrado como frente separada. Nenhum arquivo de código foi alterado.*

*Fechamento formal (2026-09-30): D1–D5 concluídas · D6 adiada · refatoração responsiva encerrada. Ver `REFATORACAO_03_FECHAMENTO.md`.*
