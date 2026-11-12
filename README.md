# Samaúma — frontend público

Versão estática (HTML/CSS/JS) do Sistema Samaúma, gerada a partir do repositório privado.
Não contém dados nem segredos: o acesso aos dados exige login por código enviado ao e-mail cadastrado.
Não edite aqui — as alterações são feitas no projeto de origem e publicadas por `node scripts/build_frontend.js`.

---

# Diretrizes e Princípios de Frontend e Design System (Sistema Samaúma)

> **DIRETIVA OBRIGATÓRIA PARA ASSISTENTES DE IA, DESIGNERS E DESENVOLVEDORES:**
> Qualquer nova tela, refatoração ou ajuste de interface DEVE seguir estritamente as regras abaixo antes de propor ou gerar código. A violação desses princípios quebra a integridade visual do sistema e gera retrabalho.

---

## 1. Princípio da Unicidade e Fonte Única da Verdade (Single Source of Truth)

1. **Mesma Função, Mesma Classe:** Elementos de interface que desempenham o mesmo papel funcional e visual em telas distintas (barras de abas, heros, filtros, tabelas, cards, modais e botões) **devem utilizar estritamente as mesmas classes canônicas do Design System (`.ds-*`)**.
2. **Proibição Estrita de Dialetos e CSS Paralelo:** É expressamente proibido criar classes duplicadas com prefixos locais por tela ou módulo (como `.pll-`, `.ext-`, `.moncorp-`, `.exl-`, `.cxa-`) para reproduzir comportamentos já existentes no sistema.
3. **Modificadores em vez de Novas Classes:** Variações legítimas de comportamento ou layout devem ser implementadas exclusivamente através de modificadores semânticos padronizados (ex.: `.ds-tabs-nav--sticky`, `.ds-tabs-nav--acoes`) ou variáveis CSS locais (`--det-acoes-w`), nunca pela recriação de seletores inteiros.
4. **Regra de Manutenção Global:** Qualquer alteração de padrão visual (espessura de borda, cor ativa, altura de sticky, tipografia) deve ser realizada em **um único ponto central** no Design System e propagar-se automaticamente para todas as telas do sistema.

---

## 2. Clonagem Fiel de Casca Estrutural (Layout & Geometria)

1. **Reaproveitamento Rigoroso de Telas Homologadas:** Ao construir uma nova tela cujo formato seja baseado em outra existente (ex.: telas analíticas baseadas no detalhe do plano, ou telas de listagem baseadas na lista de planos), **deve-se clonar integralmente o esqueleto HTML/CSS homologado**, alterando apenas as fontes de dados e manipuladores de eventos (`data-on-*`).
2. **Integridade da Hierarquia do DOM:** Não simplifique nem altere a ordem dos nós-irmãos do DOM de uma casca clonada. Estruturas como compensação de margens negativas (ex.: botões de ação alinhados ao título) e espaçamento vertical entre blocos dependem da hierarquia exata do elemento pai (ex.: `container > * { margin-bottom: 20px; }`).

---

## 3. Disciplina de Rolagem e Comportamento Sticky (Escada Matemática)

1. **Vedação e Cobertura Total:** Barras de navegação e filtros com `position: sticky` devem possuir cobertura de fundo 100% opaca (`var(--ds-color-surface)`) e fechamento de frestas subpixel (`::after` ou bordas sólidas), impedindo que cartões, tabelas ou listas vazem visualmente por trás ao rolar a página.
2. **Compensação de Padding do Contêiner:** A fixação de elementos sticky no contêiner com rolagem deve utilizar as variáveis de compensação estabelecidas (ex.: `--det-sticky-top: -20px`), anulando frestas superiores transparentes.
3. **Escada Matemática de Topo:** Elementos empilhados no topo devem declarar coordenadas escalonadas de forma estrita:
   - **Nível 1 (Abas):** `top: var(--det-sticky-top); z-index: 42;`
   - **Nível 2 (Filtros):** `top: calc(var(--det-sticky-top) + var(--det-tabs-h)); z-index: 41;`
   - **Nível 3 (Cabeçalho de Tabela):** `top: calc(var(--det-sticky-top) + var(--det-tabs-h) + var(--det-filtros-h)); z-index: 40;`
4. **Atenção ao Contexto de Empilhamento (Stacking Context):** Efeitos de elevação visual com `transform` (ex.: `translateY(-2px)` em cards de kanban) criam novos contextos de empilhamento no navegador; as barras de topo devem ter `z-index` e opacidade suficientes para sobrepor esses elementos em qualquer estado interativo.

---

## 4. Alinhamento e Espaçamentos Sem Efeitos Colaterais

1. **Alinhamento Zero à Esquerda:** O primeiro elemento interativo de uma barra de abas ou lista horizontal deve iniciar rigorosamente alinhado à borda esquerda do contêiner (`padding-left: 0`), mantendo alinhamento visual com o título e a área de conteúdo abaixo.
2. **Prevenção de Herança Indesejada:** Nunca aplique `padding` ou `margin` horizontal genérico em classes compartilhadas de contêiner sem que seja essa a regra canônica oficial. Ao estender classes existentes, certifique-se de neutralizar propriedades herdadas que causem deslocamentos acidentais.

---

## 5. Padrão Canônico Oficial de Abas (`.ds-tabs-nav`)

Para qualquer conjunto de abas de navegação de página / escopo principal:

```html
<div class="ds-tabs-nav ds-tabs-nav--sticky [ds-tabs-nav--acoes]">
  <div class="ds-tabs-nav__scroll-wrap">
    <div class="ds-tabs-nav__scroll">
      <div class="ds-tabs-nav__list" role="tablist">
        <button type="button" class="ds-tabs-nav__item active" role="tab" aria-selected="true">
          <span class="material-symbols-outlined icon-sm">checklist</span>
          <span>Aba Ativa</span>
          <span class="ds-tab-count">12</span>
        </button>
      </div>
    </div>
  </div>
</div>
```

---

## 6. Catálogo dos Componentes Canônicos Unificados (`.ds-*`)

Para eliminar duplicatas e manter a fonte única da verdade, utilize exclusivamente estes padrões:

### A. Busca Expansível estilo Drive (`.ds-search-box`)
```html
<div class="ds-search-box">
  <div class="ds-search-box__caixa">
    <span class="material-symbols-outlined ds-search-box__icon">search</span>
    <input type="text" class="ds-search-box__input" placeholder="Buscar…">
    <button type="button" class="ds-search-box__clear hidden" aria-label="Limpar busca">
      <span class="material-symbols-outlined">close</span>
    </button>
  </div>
</div>
```

### B. Barra de Filtros e Pastilhas com Dropdown (`.ds-filter-bar` e `.ds-chip-filter`)
```html
<div class="ds-filter-bar ds-filter-bar--sticky">
  <div class="ds-filter-bar__group">
    <!-- Caixa de busca e pastilhas -->
    <div class="ds-chip-filter">
      <button type="button" class="ds-chip-filter__btn">
        <span>Filtro</span>
        <span class="material-symbols-outlined">expand_more</span>
      </button>
      <button type="button" class="ds-chip-filter__clear hidden" aria-label="Remover filtro">
        <span class="material-symbols-outlined">close</span>
      </button>
    </div>
  </div>
</div>
```

### C. Cabeçalho de Tela / Hero (`.ds-page-hero`)
```html
<section class="ds-page-hero">
  <div class="ds-page-hero__titles">
    <h1 class="ds-page-hero__title">Título da Seção</h1>
    <p class="ds-page-hero__desc">Subtítulo explicativo</p>
  </div>
  <div class="ds-page-hero__actions">
    <button type="button" class="ds-btn ds-btn--primary">Ação Principal</button>
  </div>
</section>
```

### D. Moldura e Tabela com Cabeçalho Sticky (`.ds-data-table-wrap` e `.ds-data-table`)
```html
<div class="ds-data-table-wrap">
  <table class="ds-data-table ds-data-table--sticky-header">
    <thead>
      <tr>
        <th class="ds-col-check"><input type="checkbox"></th>
        <th>Coluna</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td class="ds-col-check"><input type="checkbox"></td>
        <td>Conteúdo</td>
      </tr>
    </tbody>
  </table>
</div>
```

### E. Painel Lateral de Detalhes / Gaveta Flyout (`.ds-drawer`)
```html
<aside class="ds-drawer [aberto]" aria-hidden="true">
  <div class="ds-drawer__header">
    <div class="ds-drawer__tabs">...</div>
    <button type="button" class="ds-icon-action" aria-label="Fechar painel">
      <span class="material-symbols-outlined">close</span>
    </button>
  </div>
  <div class="ds-drawer__body">...</div>
</aside>
```

### F. Barra Suspensa de Ações em Lote (`.ds-bulk-actions-bar`)
```html
<div class="ds-bulk-actions-bar [hidden]">
  <div class="ds-bulk-actions-bar__info">
    <button type="button" class="ds-icon-action" aria-label="Cancelar seleção">
      <span class="material-symbols-outlined">close</span>
    </button>
    <span class="ds-bulk-actions-bar__count">3 selecionados</span>
  </div>
  <div class="ds-bulk-actions-bar__actions">
    <!-- Botões de ação em lote -->
  </div>
</div>
```
