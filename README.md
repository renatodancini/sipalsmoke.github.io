<html lang="pt-BR"><head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Controle de Execução — Smoke Test (Visão Geral + Ondas)</title>
<style>
  :root {
    --bg: #0b1220;
    --panel: #10182b;
    --muted: #7f8ea3;
    --text: #e8eefb;
    --accent: #4da3ff;
    --ok: #22c55e;
    --warn: #f59e0b;
    --bad: #ef4444;
    --chip: #1a2540;
    --border: #223153;
    /* Cores para o Quadro de Avisos (Red/Danger) */
    --nb-bg: #451a1a;
    --nb-border: #993f3f;
    --nb-text: #fecaca;
    --nb-title: #f87171;
  }
  html, body { margin:0; padding:0; background:var(--bg); color:var(--text);
    font: 14px/1.5 system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, "Helvetica Neue", Arial, "Noto Sans", "Liberation Sans", sans-serif; }
  .wrap { max-width: 1300px; margin: 24px auto; padding: 0 16px; }
  header { display:flex; gap:16px; align-items:center; justify-content:space-between; margin-bottom:12px; flex-wrap:wrap; }
  h1 { font-size:22px; margin:0; }
  .toolbar { display:flex; gap:8px; flex-wrap:wrap; }
  button, .btn { background:var(--panel); border:1px solid var(--border); color:var(--text); padding:8px 12px; border-radius:10px; cursor:pointer; }
  button:hover { border-color: var(--accent); }
  button:disabled, .btn:disabled { opacity: 0.5; cursor: not-allowed; }
  input[type="file"] { display:none; }
  .card { background:var(--panel); border:1px solid var(--border); border-radius:16px; padding:14px; }
  .tabs { display:flex; gap:8px; flex-wrap:wrap; margin:8px 0 16px; }
  .tab { padding:8px 12px; border-radius:999px; background:var(--chip); border:1px solid var(--border); cursor:pointer; }
  .tab.active { background: var(--accent); color:#001029; font-weight:600; }
  .subtabs { display:flex; gap:6px; flex-wrap:wrap; margin-top:8px; }
  .subtab { padding:6px 10px; border-radius:999px; background:transparent; border:1px dashed var(--border); cursor:pointer; color:var(--muted); }
  .subtab.active { background:#0f1a32; color:var(--text); border-style:solid; }
  .grid-scroll { overflow:auto; border-radius:12px; border:1px solid var(--border); }
  table { width:100%; border-collapse:separate; border-spacing:0; min-width:900px; }
  thead th { position:sticky; top:0; background:#0f1a32; z-index:2; }
  th, td { padding:10px 12px; border-bottom:1px solid var(--border); vertical-align:top; }
  th { text-align:left; color:#b7c6dc; font-weight:600; }
  td[contenteditable="true"] { outline:1px solid transparent; border-radius:4px; padding:10px 11px; transition: outline-color 0.2s ease; } /* Estilo para edição */
  td[contenteditable="true"]:focus { outline:1px solid var(--accent); }
  .row-actions { display:flex; gap:8px; }
  .status { border:1px solid var(--border); background:#0f1a32; color:var(--text); border-radius:8px; padding:6px; }
  .kpis { display:flex; gap:12px; flex-wrap:wrap; margin:12px 0; }
  .kpi { background:var(--panel); border:1px solid var(--border); border-radius:12px; padding:10px 14px; min-width:160px; }
  .bar { height:10px; background:#0f1a32; border-radius:999px; overflow:hidden; border:1px solid var(--border); }
  .bar > span { display:block; height:100%; width:0; background: linear-gradient(90deg, var(--accent), #7dd3fc); }
  .chips { display:flex; gap:8px; flex-wrap:wrap; }
  .chip { background:var(--chip); border:1px solid var(--border); padding:6px 10px; border-radius:999px; cursor:pointer; }
  .chip.active { outline:2px solid var(--accent); }
  .muted { color:var(--muted); }
  .footer-note { color:var(--muted); margin-top:12px; font-size:12px; }
  /* Ondas */
  .ondas { display:grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap:12px; margin-top:8px; }
  .onda-card { background:#0f1a32; border:1px solid var(--border); border-radius:12px; padding:10px 12px; }
  .onda-title { font-weight:700; margin-bottom:6px; }
  .onda-metrics { display:flex; gap:12px; flex-wrap:wrap; margin-bottom:6px; }
  .pill { background:var(--chip); border:1px solid var(--border); padding:4px 8px; border-radius:999px; font-size:12px; }
  /* Visão geral */
  .section-title { font-weight:700; margin-top:10px; margin-bottom:4px; }

  /* QUADRO DE AVISOS (NOVO) */
  .notice-board {
    background: var(--nb-bg); border:1px solid var(--nb-border); border-left:6px solid var(--bad);
    color:var(--nb-text); border-radius:12px; padding:12px; margin: 16px 0;
  }
  .nb-head { display:flex; gap:10px; align-items:center; justify-content:space-between; flex-wrap:wrap; margin-bottom:8px }
  .nb-head .title { font-weight:800; color:var(--nb-title); }
  .nb-actions { display:flex; gap:8px; flex-wrap:wrap; align-items:center }
  .nb-actions input {
    min-width:320px; max-width:560px; width:100%; padding:8px 10px;
    border:1px solid var(--nb-border); border-radius:10px;
    background:var(--bg); color:var(--nb-text);
  }
  .nb-list { list-style:none; margin:0; padding:0; display:grid; gap:8px }
  .nb-item {
    display:flex; align-items:flex-start; gap:8px;
    background: var(--chip); border:1px solid var(--nb-border);
    border-radius:10px; padding:8px 10px;
    color: var(--nb-text);
  }
  .nb-text[contenteditable="true"] { outline:1px solid var(--nb-border); padding:2px 4px; border-radius:4px; }
  .nb-text[contenteditable="true"]:focus { outline-color: var(--accent); }
  .nb-del {
    margin-left:auto; border:1px solid var(--nb-border);
    background:var(--nb-bg); color:var(--nb-text);
    border-radius:10px; padding:6px 10px; cursor:pointer;
  }
  .nb-del:hover { border-color:var(--bad); }
  .nb-muted { font-size:12px; color:var(--muted); }
</style>
</head>
<body>
<div class="wrap">
  <header>
    <h1>Controle de Execução — Smoke Test</h1>
    <div class="toolbar">
      <button id="toggleEdit" title="Habilitar/Desabilitar edição">🔓 Habilitar edição</button>
      <button id="saveHtml" title="Salvar alterações no arquivo HTML">💾 Salvar</button>
      <label class="btn"><input id="importJson" type="file" accept="application/json">Importar JSON</label>
      <button id="exportJson">Exportar JSON</button>
      <button id="exportCsv">Exportar CSV (aba atual)</button>
      <button id="addSheet">+ Nova Planilha</button>
      <button id="printView">Imprimir/Salvar PDF</button>
    </div>
  </header>

  <div class="card">
    <div class="section-title">Visão Geral — Todos os livros e abas</div>
    <div class="kpis" id="globalKpis">
      <div class="kpi">
        <div>Total de Cenários</div>
        <div id="gTotal" style="font-size:22px; font-weight:700;">53</div>
      </div>
      <div class="kpi">
        <div>Concluídos</div>
        <div id="gDone" style="font-size:22px; font-weight:700; color: var(--ok);">2</div>
      </div>
      <div class="kpi">
        <div>Em Andamento</div>
        <div id="gDoing" style="font-size:22px; font-weight:700; color: var(--warn);">4</div>
      </div>
      <div class="kpi">
        <div>Bloqueados/Com Erro</div>
        <div id="gBlocked" style="font-size:22px; font-weight:700; color: var(--bad);">1</div>
      </div>
      <div class="kpi" style="flex:1">
        <div>Progresso Geral</div>
        <div class="bar"><span id="gProgressBar" style="width: 4%;"></span></div>
        <div class="muted" id="gProgressLabel" style="margin-top:6px;">4%</div>
      </div>
    </div>
  
    <div class="notice-board" id="noticeBoard">
      <div class="nb-head">
        <div class="title">Quadro de Avisos — Status do Smoke Test</div>
        <div class="nb-actions">
          <input id="nbInput" type="text" placeholder="Digite um aviso (problema, bloqueio, observação) e Adicionar...">
          <button class="btn" id="nbAdd">Adicionar</button>
          <button class="btn" id="nbClear">Limpar avisos</button>
        </div>
      </div>
      <ul class="nb-list" id="nbList"><li class="nb-item"><div class="nb-text" contenteditable="false">1 - Erro: NF emitida na expedição estava com Peso Líquido e Peso Bruto zerado (00,00), Jackson (consultor ACM -SPRO) está realizando a correção. Status: Defeito corrigido, consultor SPRO realizando as preparações da request para o Ambiente 320 para validação do key user.</div><button class="nb-del">Remover</button></li><li class="nb-item"><div class="nb-text" contenteditable="false">2 - Erro: Produto MALTE com estoque zerado, não é possivel realziar expedição do produto. Ação: Paulo Meazza e Alessandro realizando a conferência dos contratos e priorizando a carga. (Expectativa: Problema será resolvido em 30 minutos).</div><button class="nb-del">Remover</button></li></ul>
      <div class="nb-muted" id="nbEmpty" style="display: none;">Sem avisos ou problemas registrados.</div>
    </div>
    <div class="tabs" id="wbTabs"><div class="tab active">Smoke Test - Fertilizantes</div><div class="tab">Smoke Test - Porto</div><div class="tab">Smoke Test - Primaver-Sinop</div></div>
    <div class="subtabs" id="sheetTabs"><div class="subtab active">Caderno Smoke Test</div><div class="subtab">Planilha1</div></div>

    <div class="section-title">KPIs da Aba Atual</div>
    <div class="kpis">
      <div class="kpi">
        <div>Total de Cenários</div>
        <div id="kpiTotal" style="font-size:22px; font-weight:700;">11</div>
      </div>
      <div class="kpi">
        <div>Concluídos</div>
        <div id="kpiDone" style="font-size:22px; font-weight:700; color: var(--ok);">0</div>
      </div>
      <div class="kpi">
        <div>Em Andamento</div>
        <div id="kpiDoing" style="font-size:22px; font-weight:700; color: var(--warn);">0</div>
      </div>
      <div class="kpi">
        <div>Bloqueados/Com Erro</div>
        <div id="kpiBlocked" style="font-size:22px; font-weight:700; color: var(--bad);">0</div>
      </div>
      <div class="kpi" style="flex:1">
        <div>Progresso</div>
        <div class="bar"><span id="progressBar" style="width: 0%;"></span></div>
        <div class="muted" id="progressLabel" style="margin-top:6px;">0%</div>
      </div>
    </div>

    <div class="muted" style="margin:6px 0 4px;">Indicadores por Onda (aba atual):</div>
    <div id="ondasWrap" class="ondas"><div class="onda-card">
      <div class="onda-title">Onda: 2</div>
      <div class="onda-metrics">
        <span class="pill">Total: <b>5</b></span>
        <span class="pill">Concluídos: <b style="color:var(--ok);">0</b></span>
        <span class="pill">Em andamento: <b style="color:var(--warn);">0</b></span>
        <span class="pill">Bloqueados/Erro: <b style="color:var(--bad);">0</b></span>
      </div>
      <div class="bar"><span style="width:0%; background: linear-gradient(90deg, var(--ok), #7dd3fc);"></span></div>
      <div class="muted" style="margin-top:6px;">Progresso: 0%</div>
    </div><div class="onda-card">
      <div class="onda-title">Onda: 3</div>
      <div class="onda-metrics">
        <span class="pill">Total: <b>6</b></span>
        <span class="pill">Concluídos: <b style="color:var(--ok);">0</b></span>
        <span class="pill">Em andamento: <b style="color:var(--warn);">0</b></span>
        <span class="pill">Bloqueados/Erro: <b style="color:var(--bad);">0</b></span>
      </div>
      <div class="bar"><span style="width:0%; background: linear-gradient(90deg, var(--ok), #7dd3fc);"></span></div>
      <div class="muted" style="margin-top:6px;">Progresso: 0%</div>
    </div></div>
    <div id="ondasInfo" class="muted" style="margin-top: 4px; display: block;">Os indicadores por Onda são extraídos da coluna 'Macroprocesso (MM/LE/SD/ACM)' (ex: [ST - Onda 1]).</div>

    <div class="chips" id="statusChips" style="margin-top:12px;">
      <div class="chip active" data-status="todos">Todos</div>
      <div class="chip" data-status="Planejado">Planejado</div>
      <div class="chip" data-status="Em andamento">Em andamento</div>
      <div class="chip" data-status="Concluído">Concluído</div>
      <div class="chip" data-status="Bloqueado">Bloqueado</div>
      <div class="chip" data-status="Erro">Erro</div>
    </div>

    <div class="grid-scroll" style="margin-top:12px;">
      <table id="dataTable">
        <thead><tr><th>ID</th><th>Macroprocesso (MM/LE/SD/ACM)</th><th>Descrição do Cenário</th><th>Centros</th><th>Pré-requisitos</th><th>Passos de Teste (alto nível)</th><th>Passos de Teste (detalhados)</th><th>Massa de Dados (por cenário)</th><th>Resultado Esperado</th><th>Evidências (links / NF / print)</th><th>Status</th><th>Ações</th></tr></thead>
        <tbody><tr><td contenteditable="false">3-133816</td><td contenteditable="false">[ST - Onda 2] BPH004031 - Centro 1012 - Realizar expedição de produtos Processo (SD) Fertilizantes</td><td contenteditable="false">Realizar expedição de produtos Processo (SD) Fertilizantes</td><td contenteditable="false">1012</td><td contenteditable="false">Contrato de Venda/Ordem de venda - Integração com GP (apoio de Bruno Damião ou Marafon se necessario) - Pesagem na balança - Ordem de carregamento/Ordem de programação</td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                                      2 - Lançamento da ordem de venda.               3 - Integração com GP.                                           4 - Pesagem.                                                              5 - Emissão da nota fiscal</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133819</td><td contenteditable="false">[ST - Onda 2] BPH004032 - Centro 1038 - Realizar Troca de NF (SD) Fertilizantes</td><td contenteditable="false">Realizar Troca de NF (SD) Fertilizantes</td><td contenteditable="false">1038</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133817</td><td contenteditable="false">[ST - Onda 2] BPH004074 - Centro 1038 - Emitir Nota Fiscal (SD) Fertilizantes</td><td contenteditable="false">Emitir Nota Fiscal (SD) Fertilizantes</td><td contenteditable="false">1038</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133820</td><td contenteditable="false">[ST - Onda 2] BPH004032 - Centro 1045 - Realizar Troca de NF (SD) Fertilizantes</td><td contenteditable="false">Realizar Troca de NF (SD) Fertilizantes</td><td contenteditable="false">1045</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133818</td><td contenteditable="false">[ST - Onda 2] BPH004074 - Centro 1045 - Emitir Nota Fiscal (SD) Fertilizantes</td><td contenteditable="false">Emitir Nota Fiscal (SD) Fertilizantes</td><td contenteditable="false">1045</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133857</td><td contenteditable="false">[ST - Onda 3] BPH004032 - Centro 1013 - Realizar Troca de NF ( SD) Fertilizantes</td><td contenteditable="false">Realizar Troca de NF ( SD) Fertilizantes</td><td contenteditable="false">1013</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133855</td><td contenteditable="false">[ST - Onda 3] BPH004074 - Centro 1013 - Emitir Nota Fiscal ( SD) Fertilizantes</td><td contenteditable="false">Emitir Nota Fiscal ( SD) Fertilizantes</td><td contenteditable="false">1013</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133858</td><td contenteditable="false">[ST - Onda 3] BPH004032 - Centro 1019 - Realizar Troca de NF ( SD) Fertilizantes</td><td contenteditable="false">Realizar Troca de NF ( SD) Fertilizantes</td><td contenteditable="false">1019</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133856</td><td contenteditable="false">[ST - Onda 3] BPH004074 - Centro 1019 - Emitir Nota Fiscal ( SD) Fertilizantes</td><td contenteditable="false">Emitir Nota Fiscal ( SD) Fertilizantes</td><td contenteditable="false">1019</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133859</td><td contenteditable="false">[ST - Onda 3] BPH004084 - Centro 1019 - Realizar gestão de armazenagem</td><td contenteditable="false">Realizar gestão de armazenagem</td><td contenteditable="false">1019</td><td contenteditable="false">Contrato de compra/Pedido de compra MIGO/MIRO - Devolução via GNF (apoio do fiscal se necessario) - Contrato de Venda/Ordem de venda</td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                       2 - Criação de ordem e pedido.          3 - Devolução de armazenagem.          4 - Emissão de nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr><tr><td contenteditable="false">3-133852</td><td contenteditable="false">[ST - Onda 3] BPH004032 - Centro 1019 - Realizar Troca de NF</td><td contenteditable="false">Realizar Troca de NF</td><td contenteditable="false">1019</td><td contenteditable="false">Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) </td><td contenteditable="false">Criação dos documentos e emissão da nota fiscal</td><td contenteditable="false">1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.</td><td contenteditable="false"></td><td contenteditable="false">Emissão de nota fiscal e homologação.</td><td contenteditable="false"></td><td><select class="status"><option value="Planejado">Planejado</option><option value="Em andamento">Em andamento</option><option value="Concluído">Concluído</option><option value="Bloqueado">Bloqueado</option><option value="Erro">Erro</option></select></td><td class="row-actions"><button>Excluir</button></td></tr></tbody>
      </table>
    </div>
    <div style="margin-top:10px; display:flex; gap:8px; flex-wrap:wrap;">
      <button id="addRow">+ Adicionar Linha</button>
      <button id="addColumn">+ Adicionar Coluna</button>
    </div>
    <div class="footer-note">Dica: todos os campos são editáveis. A coluna "Status" aceita: Planejado, Em andamento, Concluído, Bloqueado, Erro.</div>
  </div>
</div>

<script>
// Dados fornecidos no arquivo original
const appData = {
  "Smoke Test - Fertilizantes": {
    "Caderno Smoke Test": {
      "columns": [
        "ID",
        "Macroprocesso (MM/LE/SD/ACM)",
        "Descrição do Cenário",
        "Centros",
        "Pré-requisitos",
        "Passos de Teste (alto nível)",
        "Passos de Teste (detalhados)",
        "Massa de Dados (por cenário)",
        "Resultado Esperado",
        "Evidências (links / NF / print)",
        "Status"
      ],
      "rows": [
        {
          "ID": "3-133816",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 2] BPH004031 - Centro 1012 - Realizar expedição de produtos Processo (SD) Fertilizantes",
          "Descrição do Cenário": "Realizar expedição de produtos Processo (SD) Fertilizantes",
          "Centros": "1012",
          "Pré-requisitos": "Contrato de Venda/Ordem de venda - Integração com GP (apoio de Bruno Damião ou Marafon se necessario) - Pesagem na balança - Ordem de carregamento/Ordem de programação",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                                      2 - Lançamento da ordem de venda.               3 - Integração com GP.                                           4 - Pesagem.                                                              5 - Emissão da nota fiscal",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133819",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 2] BPH004032 - Centro 1038 - Realizar Troca de NF (SD) Fertilizantes",
          "Descrição do Cenário": "Realizar Troca de NF (SD) Fertilizantes",
          "Centros": "1038",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133817",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 2] BPH004074 - Centro 1038 - Emitir Nota Fiscal (SD) Fertilizantes",
          "Descrição do Cenário": "Emitir Nota Fiscal (SD) Fertilizantes",
          "Centros": "1038",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133820",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 2] BPH004032 - Centro 1045 - Realizar Troca de NF (SD) Fertilizantes",
          "Descrição do Cenário": "Realizar Troca de NF (SD) Fertilizantes",
          "Centros": "1045",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133818",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 2] BPH004074 - Centro 1045 - Emitir Nota Fiscal (SD) Fertilizantes",
          "Descrição do Cenário": "Emitir Nota Fiscal (SD) Fertilizantes",
          "Centros": "1045",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133857",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004032 - Centro 1013 - Realizar Troca de NF ( SD) Fertilizantes",
          "Descrição do Cenário": "Realizar Troca de NF ( SD) Fertilizantes",
          "Centros": "1013",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133855",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004074 - Centro 1013 - Emitir Nota Fiscal ( SD) Fertilizantes",
          "Descrição do Cenário": "Emitir Nota Fiscal ( SD) Fertilizantes",
          "Centros": "1013",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133858",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004032 - Centro 1019 - Realizar Troca de NF ( SD) Fertilizantes",
          "Descrição do Cenário": "Realizar Troca de NF ( SD) Fertilizantes",
          "Centros": "1019",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133856",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004074 - Centro 1019 - Emitir Nota Fiscal ( SD) Fertilizantes",
          "Descrição do Cenário": "Emitir Nota Fiscal ( SD) Fertilizantes",
          "Centros": "1019",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133859",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004084 - Centro 1019 - Realizar gestão de armazenagem",
          "Descrição do Cenário": "Realizar gestão de armazenagem",
          "Centros": "1019",
          "Pré-requisitos": "Contrato de compra/Pedido de compra MIGO/MIRO - Devolução via GNF (apoio do fiscal se necessario) - Contrato de Venda/Ordem de venda",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                       2 - Criação de ordem e pedido.          3 - Devolução de armazenagem.          4 - Emissão de nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133852",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH004032 - Centro 1019 - Realizar Troca de NF",
          "Descrição do Cenário": "Realizar Troca de NF",
          "Centros": "1019",
          "Pré-requisitos": "Contrato de compra/Pedido de compra - Migo/Miro (Apoio do setor fiscal caso necessario) - Contrato de Compra/Ordem de Venda - Ordem de Programação/Ordem de carregamento (Apoio do setor logistico) ",
          "Passos de Teste (alto nível)": "Criação dos documentos e emissão da nota fiscal",
          "Passos de Teste (detalhados)": "1 - Criação dos contratos.                     2 - Lançamento Pedido/Ordem.           3 - Documento de troca.                       4 - Ordem de programação e carregamento.                                           5 - Emissão da nota fiscal.",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "Emissão de nota fiscal e homologação.",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        }
      ]
    },
    "Planilha1": {
      "columns": [
        "Unnamed: 0",
        "ONDA 2",
        "Unnamed: 2",
        "Status"
      ],
      "rows": [
        {
          "Unnamed: 0": "",
          "ONDA 2": "",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "SIPAL - SORRISO (1012): 07/10/2025",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4031 - Realizar expedição de produtos Processo (SD) Fertilizantes",
          "Unnamed: 2": " - 7 Tons Sorriso"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4074 - Emitir Nota Fiscal (SD) Fertilizantes",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4032 - Realizar Troca de NF (SD) Fertilizantes ",
          "Unnamed: 2": " - Alterar Feliz Natal / Arnaldo Hartmann"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "SIPAL - SIPAL-RONDONOPOLIS (1045): 07/10/2025",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4074 - Emitir Nota Fiscal (SD) Fertilizantes",
          "Unnamed: 2": " - KCL Mosaic"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4032 - Realizar Troca de NF (SD) Fertilizantes",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "ONDA 3",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "SIPAL - LUCAS DO RIO VERDE (1005): 09/10/2025",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4074 - Emitir Nota Fiscal ( SD) Fertilizantes",
          "Unnamed: 2": " - Alterar para Boa Esperança"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4032 - Realizar Troca de NF ( SD) Fertilizantes",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "SIPAL - CAMPOS DE JULHO - CIDADE (1019): 09/10/2025",
          "Unnamed: 2": ""
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4074 - Emitir Nota Fiscal ( SD) Fertilizantes",
          "Unnamed: 2": " - Ok Campo Rico x Valério"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH4032 - Realizar Troca de NF ( SD) Fertilizantes",
          "Unnamed: 2": " - Sidnei Zanella - KCL 0,52k"
        },
        {
          "Unnamed: 0": "",
          "ONDA 2": "BPH004084 - Realizar gestão de armazenagem",
          "Unnamed: 2": " - Campo Rico"
        }
      ]
    }
  },
  "Smoke Test - Porto": {
    "Caderno Smoke Test": {
      "columns": [
        "ID",
        "Macroprocesso (MM/LE/SD/ACM)",
        "Descrição do Cenário",
        "Centros",
        "Pré-requisitos",
        "Passos de Teste (alto nível)",
        "Passos de Teste (detalhados)",
        "Massa de Dados (por cenário)",
        "Resultado Esperado",
        "Evidências (links / NF / print)",
        "Status"
      ],
      "rows": [
        {
          "ID": "INCLUIR TESTE",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000693 - Centro 1052 - Realizar Recebimento para Armazenagem Porto",
          "Descrição do Cenário": "Realizar Recebimento para Armazenagem Porto",
          "Centros": "1052",
          "Pré-requisitos": "NF Armazenagem Cliente / Lote",
          "Passos de Teste (alto nível)": "Criar EDC manual / Escriturar NF ",
          "Passos de Teste (detalhados)": "Criar EDC / Informar Lote / Baixar NF Inbound",
          "Massa de Dados (por cenário)": "BP 1020979 / Lote 77608JUPO1 / Trigo Tipo II - 10019",
          "Resultado Esperado": "Gravar EDC e gerar estoque no Depósito TEMP",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133800",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000738 - Centro 1002 - Realizar Pesagem Porto",
          "Descrição do Cenário": "Realizar Pesagem Porto",
          "Centros": "1002",
          "Pré-requisitos": "Lote no Gestão de Pátio",
          "Passos de Teste (alto nível)": "Recepção via balança de armazanagem Navio Importação ",
          "Passos de Teste (detalhados)": "Informar ID Viagem / Realizar 1ª Pesagem / Realizar 2ª Pesagem ",
          "Massa de Dados (por cenário)": "BP 1025893 / Lote 77350PGON3 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Em andamento"
        },
        {
          "ID": "3-133801",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000738 - Centro 1035 - Realizar Pesagem Porto",
          "Descrição do Cenário": "Realizar Pesagem Porto",
          "Centros": "1035",
          "Pré-requisitos": "Lote no Gestão de Pátio",
          "Passos de Teste (alto nível)": "Recepção via balança de armazanagem Navio Importação ",
          "Passos de Teste (detalhados)": "Informar ID Viagem / Realizar 1ª Pesagem / Realizar 2ª Pesagem ",
          "Massa de Dados (por cenário)": "BP 1025893 / Lote 77350PGON3 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Erro"
        },
        {
          "ID": "3-133798",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000738 - Centro 1028 - Realizar Pesagem Porto",
          "Descrição do Cenário": "Realizar Pesagem Porto",
          "Centros": "1028",
          "Pré-requisitos": "Lote no Gestão de Pátio",
          "Passos de Teste (alto nível)": "Recepção via balança de armazanagem Navio Importação ",
          "Passos de Teste (detalhados)": "Informar ID Viagem / Realizar 1ª Pesagem / Realizar 2ª Pesagem ",
          "Massa de Dados (por cenário)": "BP 1025893 / Lote 77350PGON3 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Em andamento"
        },
        {
          "ID": "3-133799",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000738 - Centro 1052 - Realizar Pesagem Porto",
          "Descrição do Cenário": "Realizar Pesagem Porto",
          "Centros": "1052",
          "Pré-requisitos": "Lote no Gestão de Pátio",
          "Passos de Teste (alto nível)": "Recepção via balança de armazanagem Navio Importação ",
          "Passos de Teste (detalhados)": "Informar ID Viagem / Realizar 1ª Pesagem / Realizar 2ª Pesagem ",
          "Massa de Dados (por cenário)": "BP 1025893 / Lote 77350PGON3 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133804",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000692 - Centro 1002 - Realizar Expedição via Balança",
          "Descrição do Cenário": "Realizar Expedição via Balança",
          "Centros": "1002",
          "Pré-requisitos": "Lote no Gestão de Pátio / Lote no SAP / Saldo em estoque disponível",
          "Passos de Teste (alto nível)": "Expedição armazenagem via balança",
          "Passos de Teste (detalhados)": "Realizar pré-pesagem / Informar lote / Realizar 1ª Pesagem / Realizar 2ª Pesagem",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133802",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000692 - Centro 1028 - Realizar Expedição via Balança",
          "Descrição do Cenário": "Realizar Expedição via Balança",
          "Centros": "1028",
          "Pré-requisitos": "Lote no Gestão de Pátio / Lote no SAP / Saldo em estoque disponível",
          "Passos de Teste (alto nível)": "Expedição armazenagem via balança",
          "Passos de Teste (detalhados)": "Realizar pré-pesagem / Informar lote / Realizar 1ª Pesagem / Realizar 2ª Pesagem",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Em andamento"
        },
        {
          "ID": "3-133805",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000692 - Centro 1035 - Realizar Expedição via Balança",
          "Descrição do Cenário": "Realizar Expedição via Balança",
          "Centros": "1035",
          "Pré-requisitos": "Lote no Gestão de Pátio / Lote no SAP / Saldo em estoque disponível",
          "Passos de Teste (alto nível)": "Expedição armazenagem via balança",
          "Passos de Teste (detalhados)": "Realizar pré-pesagem / Informar lote / Realizar 1ª Pesagem / Realizar 2ª Pesagem",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Concluído"
        },
        {
          "ID": "3-133803",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000692 - Centro 1052 - Realizar Expedição via Balança",
          "Descrição do Cenário": "Realizar Expedição via Balança",
          "Centros": "1052",
          "Pré-requisitos": "Lote no Gestão de Pátio / Lote no SAP / Saldo em estoque disponível",
          "Passos de Teste (alto nível)": "Expedição armazenagem via balança",
          "Passos de Teste (detalhados)": "Realizar pré-pesagem / Informar lote / Realizar 1ª Pesagem / Realizar 2ª Pesagem",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Concluir pesagem com laudo finalizado",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133794",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000684|BPH000690 - Centro 1002 - Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Descrição do Cenário": "Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Centros": "1002",
          "Pré-requisitos": "NF armazenagem escriturada no SAP / Pesagem de expedição finalizada pelo GP e integrada no SAP",
          "Passos de Teste (alto nível)": "Verificar pesagem no MEB",
          "Passos de Teste (detalhados)": "Aprovar pesagem no MEB / Conferir NF monitor",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Emitir NF de retorno de armazenagem",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133792",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000684|BPH000690 - Centro 1028 - Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Descrição do Cenário": "no Fiscal de Recebimento para Armazenagem",
          "Centros": "1028",
          "Pré-requisitos": "NF armazenagem escriturada no SAP / Pesagem de expedição finalizada pelo GP e integrada no SAP",
          "Passos de Teste (alto nível)": "Verificar pesagem no MEB",
          "Passos de Teste (detalhados)": "Aprovar pesagem no MEB / Conferir NF monitor",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Emitir NF de retorno de armazenagem",
          "Evidências (links / NF / print)": "",
          "Status": "Em andamento"
        },
        {
          "ID": "3-133795",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000684|BPH000690 - Centro 1035 - Centro 1035 - Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Descrição do Cenário": "Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Centros": "1035",
          "Pré-requisitos": "NF armazenagem escriturada no SAP / Pesagem de expedição finalizada pelo GP e integrada no SAP",
          "Passos de Teste (alto nível)": "Verificar pesagem no MEB",
          "Passos de Teste (detalhados)": "Aprovar pesagem no MEB / Conferir NF monitor",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Emitir NF de retorno de armazenagem",
          "Evidências (links / NF / print)": "",
          "Status": "Concluído"
        },
        {
          "ID": "3-133793",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000684|BPH000690 - Centro 1052 - Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Descrição do Cenário": "Realizar Retorno Fiscal de Recebimento para Armazenagem",
          "Centros": "1052",
          "Pré-requisitos": "NF armazenagem escriturada no SAP / Pesagem de expedição finalizada pelo GP e integrada no SAP",
          "Passos de Teste (alto nível)": "Verificar pesagem no MEB",
          "Passos de Teste (detalhados)": "Aprovar pesagem no MEB / Conferir NF monitor",
          "Massa de Dados (por cenário)": "BP 1025896 / Lote 76824FOIS1 / Cevada 10008",
          "Resultado Esperado": "Emitir NF de retorno de armazenagem",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133806",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH002790 - Centro 1052 - Realizar Troca de notas",
          "Descrição do Cenário": "Realizar Troca de notas",
          "Centros": "1052",
          "Pré-requisitos": "Criar troca / Contrato armazenagem / NF retorno de armazenagem",
          "Passos de Teste (alto nível)": "Carregamento em terminal de terceiros",
          "Passos de Teste (detalhados)": "Lançar NF de entrada por devolução de armazenagem emitida por um terceiro",
          "Massa de Dados (por cenário)": "Troca nota / BP 1032013 / Contrato / Lote /  Cevada 10008",
          "Resultado Esperado": "Escriturar NF retorno de terceiros / emitir NF retorno armazenagem para Cliente / Vefiricar vínculos com lote",
          "Evidências (links / NF / print)": "Visto a dependência de um carregamento em armazem de terceiros e uma NF de terceiros, este processo será executado no dia 06/10/25 no carregamento efetivo de um caminhão ",
          "Status": "Planejado"
        },
        {
          "ID": "3-133796",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000732 - Centro 1002 - Realizar a compra com descarga no recinto alfandegado - Centro Sul",
          "Descrição do Cenário": "Realizar a compra com descarga no recinto alfandegado - Centro Sul",
          "Centros": "1002",
          "Pré-requisitos": "Teste será realizado assim que ocorrer uma descarga efetiva no recinto alfandegado (Visto que o lançamento da NF ocorre após o caminhão ter realizado a descarga no recinto alfandegado terceiros)",
          "Passos de Teste (alto nível)": "",
          "Passos de Teste (detalhados)": "",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133797",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 1] BPH000732 - Centro 1302 - Realizar a compra com descarga no recinto alfandegado - Centro Sul",
          "Descrição do Cenário": "Realizar a compra com descarga no recinto alfandegado - Centro Sul",
          "Centros": "1302",
          "Pré-requisitos": "Teste será realizado assim que ocorrer uma descarga efetiva no recinto alfandegado (Visto que o lançamento da NF ocorre após o caminhão ter realizado a descarga no recinto alfandegado terceiros)",
          "Passos de Teste (alto nível)": "",
          "Passos de Teste (detalhados)": "",
          "Massa de Dados (por cenário)": "",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        }
      ]
    }
  },
  "Smoke Test - Primaver-Sinop": {
    "Caderno Smoke Test": {
      "columns": [
        "ID",
        "Macroprocesso (MM/LE/SD/ACM)",
        "Descrição do Cenário",
        "Centros",
        "Pré-requisitos",
        "Passos de Teste (alto nível)",
        "Passos de Teste (detalhados)",
        "Massa de Dados (por cenário)",
        "Resultado Esperado",
        "Evidências (links / NF / print)",
        "Status"
      ],
      "rows": [
        {
          "ID": "3-133849",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH003933 - Centro 1061 - Faturamento de Entrada",
          "Descrição do Cenário": "Faturamento de Entrada",
          "Centros": "1061",
          "Pré-requisitos": "Compra realizada - Pedido / Contrato devidamente lançado - Recebimento da mercadoria",
          "Passos de Teste (alto nível)": "Conferencia contrato / pedido - entrada MIGO e MIRO",
          "Passos de Teste (detalhados)": "Entrada MIGO - validação da entrada em estoque - lançamento da fatura - validação da partida a pagar no fluxo financeiro",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133848",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH003933 - Centro 1022 - Faturamento de Entrada",
          "Descrição do Cenário": "Faturamento de Entrada",
          "Centros": "1022",
          "Pré-requisitos": "Compra realizada - Pedido / Contrato devidamente lançado - Recebimento da mercadoria",
          "Passos de Teste (alto nível)": "Conferencia contrato / pedido - entrada MIGO e MIRO",
          "Passos de Teste (detalhados)": "Entrada MIGO - validação da entrada em estoque - lançamento da fatura - validação da partida a pagar no fluxo financeiro",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133851",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH003934 - Centro 1061 - Faturamento de Saída",
          "Descrição do Cenário": "Faturamento de Saída",
          "Centros": "1061",
          "Pré-requisitos": "Venda realizada - Contrato devidamente lançado - Material em estoque",
          "Passos de Teste (alto nível)": "Conferir solicitação de faturamento - conferir material no estoque - verificar se contrato aprovado - gerar demandas de faturamento",
          "Passos de Teste (detalhados)": "Conferencia da solicitação de faturamento - verificar disponibilidade em estoque - realizar VA01 - Criar pedido do cliente, VL01N Criar entrega com referencia a ordem, VF01 criar documento de faturamento, J1N3N - Monitor de NF brasil (Temos a etapa do Agritis geração de demanda de receita, quando o produto for quimico e exigir receituario agronomico)",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133850",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH003934 - Centro 1022 - Faturamento de Saída",
          "Descrição do Cenário": "Faturamento de Saída",
          "Centros": "1022",
          "Pré-requisitos": "Venda realizada - Contrato devidamente lançado - Material em estoque",
          "Passos de Teste (alto nível)": "Conferir solicitação de faturamento - conferir material no estoque - verificar se contrato aprovado - gerar demandas de faturamento",
          "Passos de Teste (detalhados)": "Conferencia da solicitação de faturamento - verificar disponibilidade em estoque - realizar VA01 - Criar pedido do cliente, VL01N Criar entrega com referencia a ordem, VF01 criar documento de faturamento, J1N3N - Monitor de NF brasil (Temos a etapa do Agritis geração de demanda de receita, quando o produto for quimico e exigir receituario agronomico)",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133854",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH002447 - Centro 1061 - Programar embarques  - distribuição insumos (Sementes/Químicos)",
          "Descrição do Cenário": "Programar embarques  - distribuição insumos (Sementes/Químicos)",
          "Centros": "1061",
          "Pré-requisitos": "Solcitação de faturamento validada, contratação do transporte, ou veiculo proprio informado, faturamento realizado",
          "Passos de Teste (alto nível)": "contratação do transporte, informações do veiculo, motorista, transportadora, ou nos casos de entrega propria informações do condutor, faturamento realizado, mercadoria separada",
          "Passos de Teste (detalhados)": "contratação do transporte, informações do veiculo, motorista, transportadora, ou nos casos de entrega propria informações do condutor, faturamento realizado, mercadoria separada, conferida. Realizado embarque coleta assinatura dos responsaveis, entrega documentos e libera para entrega ",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        },
        {
          "ID": "3-133853",
          "Macroprocesso (MM/LE/SD/ACM)": "[ST - Onda 3] BPH002447 - Centro 1022 - Programar embarques  - distribuição insumos (Sementes/Químicos)",
          "Descrição do Cenário": "Programar embarques  - distribuição insumos (Sementes/Químicos)",
          "Centros": "1022",
          "Pré-requisitos": "Solcitação de faturamento validada, contratação do transporte, ou veiculo proprio informado, faturamento realizado",
          "Passos de Teste (alto nível)": "contratação do transporte, informações do veiculo, motorista, transportadora, ou nos casos de entrega propria informações do condutor, faturamento realizado, mercadoria separada",
          "Passos de Teste (detalhados)": "contratação do transporte, informações do veiculo, motorista, transportadora, ou nos casos de entrega propria informações do condutor, faturamento realizado, mercadoria separada, conferida. Realizado embarque coleta assinatura dos responsaveis, entrega documentos e libera para entrega ",
          "Massa de Dados (por cenário)": "se tivermos entradas vamos usar cenario real, caso contrario usaremos um pedido do sistema e posterior estonor",
          "Resultado Esperado": "",
          "Evidências (links / NF / print)": "",
          "Status": "Planejado"
        }
      ]
    }
  },
  "__noticeBoard": [
    "1 - Erro: NF emitida na expedição estava com Peso Líquido e Peso Bruto zerado (00,00), Jackson (consultor ACM -SPRO) está realizando a correção. Status: Defeito corrigido, consultor SPRO realizando as preparações da request para o Ambiente 320 para validação do key user.",
    "2 - Erro: Produto MALTE com estoque zerado, não é possivel realziar expedição do produto. Ação: Paulo Meazza e Alessandro realizando a conferência dos contratos e priorizando a carga. (Expectativa: Problema será resolvido em 30 minutos)."
  ]
};

function deepClone(o) { return JSON.parse(JSON.stringify(o)); }

// State
let editingEnabled = false;
let currentWb = Object.keys(appData)[0] || "Smoke Test";
let currentSheet = currentWb && Object.keys(appData[currentWb]||{})[0];

// Inicialização/Normalização do Estado (Ajustado para o seu caso)
if(!currentWb) {
  appData["Smoke Test"] = {"Planilha 1": { "columns": ["ID", "Macroprocesso (MM/LE/SD/ACM)", "Descrição do Cenário", "Status", "Evidências (links / NF / print)"], "rows": [] } };
  currentWb = "Smoke Test";
  currentSheet = "Planilha 1";
} else if(!currentSheet) {
  currentSheet = Object.keys(appData[currentWb])[0];
}

// Garante que todas as sheets tenham a coluna 'Status' e inicializa valores vazios
for (const wb of Object.keys(appData)) {
  for (const sh of Object.keys(appData[wb])) {
    const m = appData[wb][sh];
    if (!m.columns) m.columns = Object.keys((m.rows && m.rows[0]) || {});
    if (!m.rows) m.rows = [];
    if (!m.columns.includes("Status")) m.columns.push("Status");
    m.rows.forEach(r => {
      // Garante que todos os campos do JSON estejam no formato String para a edição de texto
      for (const key of Object.keys(r)) { r[key] = String(r[key] || ""); }
      // Garante que o status seja normalizado
      if (r["Status"] === "") r["Status"] = "Planejado";
    });
  }
}

// Chave única para o armazenamento local dos avisos
function storeKeyForSheet(wb, sheet) { return `smokePanelNotices::${wb}::${sheet}`; }

// --- Funções do Quadro de Avisos ---
function loadNotices() {
  const key = storeKeyForSheet(currentWb, currentSheet);
  try {
    const raw = localStorage.getItem(key);
    return raw ? JSON.parse(raw) : [];
  } catch (e) { return []; }
}

function saveNotices(arr) {
  const key = storeKeyForSheet(currentWb, currentSheet);
  try {
    localStorage.setItem(key, JSON.stringify(arr));
  } catch (e) { console.error("Erro ao salvar avisos:", e); }
}

function renderNoticeBoard() {
  const notices = loadNotices();
  const nbList = document.getElementById('nbList');
  const nbEmpty = document.getElementById('nbEmpty');
  nbList.innerHTML = '';

  if (!notices.length) {
    nbEmpty.style.display = 'block';
  } else {
    nbEmpty.style.display = 'none';
    notices.forEach((txt, idx) => {
      const li = document.createElement('li'); li.className = 'nb-item';
      const span = document.createElement('div'); span.className = 'nb-text'; span.textContent = txt;
      span.contentEditable = String(editingEnabled); // Sempre editável
      span.onblur = () => {
        const now = loadNotices();
        now[idx] = span.textContent;
        saveNotices(now);
      };

      const del = document.createElement('button'); del.className = 'nb-del'; del.textContent = 'Remover';
      del.onclick = () => {
        const now = loadNotices();
        now.splice(idx, 1);
        saveNotices(now);
        renderNoticeBoard();
      };

      li.appendChild(span); li.appendChild(del);
      nbList.appendChild(li);
    });
  }
}

document.getElementById('nbAdd').onclick = () => {
  const nbInput = document.getElementById('nbInput');
  const v = (nbInput.value || '').trim();
  if (!v) return;
  const now = loadNotices(); now.push(v);
  saveNotices(now);
  nbInput.value = '';
  renderNoticeBoard();
};

document.getElementById('nbInput').onkeypress = (e) => {
  if (e.key === 'Enter') { e.preventDefault(); document.getElementById('nbAdd').click(); }
};

document.getElementById('nbClear').onclick = () => {
  if (confirm('Tem certeza que deseja limpar TODOS os avisos desta aba?')) {
    saveNotices([]);
    renderNoticeBoard();
  }
};
// --- Fim Funções do Quadro de Avisos ---


// Função para extrair a Onda da coluna Macroprocesso
function extractOnda(macroprocesso) {
  if (!macroprocesso) return "(sem onda)";
  const match = String(macroprocesso).match(/\[ST\s*-\s*Onda\s*([^\]]+)\]/i);
  return match ? match[1].trim() : "(sem onda)";
}

// ---- Global KPIs (all books, all sheets) ----
function computeGlobalKPIs() {
  let all=0, done=0, doing=0, blocked=0, err=0;
  for (const wb of Object.keys(appData)) {
    for (const sh of Object.keys(appData[wb])) {
      const m = appData[wb][sh];
      const rows = m.rows || [];
      all += rows.length;
      rows.forEach(r => {
        const st = (r["Status"]||"").trim();
        if (st==="Concluído") done++;
        else if (st==="Em andamento") doing++;
        else if (st==="Bloqueado") blocked++;
        else if (st==="Erro") err++;
      });
    }
  }
  const blockedErr = blocked + err;
  const pct = all ? Math.round((done / all) * 100) : 0;
  document.getElementById('gTotal').textContent = String(all);
  document.getElementById('gDone').textContent = String(done);
  document.getElementById('gDoing').textContent = String(doing);
  document.getElementById('gBlocked').textContent = String(blockedErr);
  document.getElementById('gProgressBar').style.width = pct + '%';
  document.getElementById('gProgressLabel').textContent = pct + '%';
}

// ---- Per-sheet KPIs ----
let currentFilter = "todos";

function renderOndaKPIs(model) {
  const wrap = document.getElementById('ondasWrap');
  const info = document.getElementById('ondasInfo');
  wrap.innerHTML = '';

  const macroCol = model.columns.find(c => c.toLowerCase().includes('macroprocesso'));
  if (!macroCol) {
    info.style.display = 'block';
    info.textContent = "Os indicadores por Onda requerem a coluna 'Macroprocesso (MM/LE/SD/ACM)'.";
    return;
  }
  info.style.display = 'block';
  info.textContent = "Os indicadores por Onda são extraídos da coluna 'Macroprocesso (MM/LE/SD/ACM)' (ex: [ST - Onda 1]).";


  const groups = {};
  model.rows.forEach(r => {
    const onda = extractOnda(r[macroCol]);
    const st = String(r["Status"]||"").trim();
    if (!groups[onda]) groups[onda] = {total:0, done:0, doing:0, blocked:0, err:0};
    groups[onda].total++;
    if (st==="Concluído") groups[onda].done++;
    else if (st==="Em andamento") groups[onda].doing++;
    else if (st==="Bloqueado") groups[onda].blocked++;
    else if (st==="Erro") groups[onda].err++;
  });

  Object.keys(groups).sort().forEach(onda => {
    const g = groups[onda];
    const pct = g.total ? Math.round((g.done/g.total)*100) : 0;
    const card = document.createElement('div');
    card.className = 'onda-card';
    card.innerHTML = `
      <div class="onda-title">Onda: ${onda}</div>
      <div class="onda-metrics">
        <span class="pill">Total: <b>${g.total}</b></span>
        <span class="pill">Concluídos: <b style="color:var(--ok);">${g.done}</b></span>
        <span class="pill">Em andamento: <b style="color:var(--warn);">${g.doing}</b></span>
        <span class="pill">Bloqueados/Erro: <b style="color:var(--bad);">${g.blocked + g.err}</b></span>
      </div>
      <div class="bar"><span style="width:${pct}%; background: linear-gradient(90deg, var(--ok), #7dd3fc);"></span></div>
      <div class="muted" style="margin-top:6px;">Progresso: ${pct}%</div>
    `;
    wrap.appendChild(card);
  });
}

function renderTable() {
  const model = appData[currentWb][currentSheet];
  const columns = model.columns;
  const tbody = document.querySelector('#dataTable tbody');
  const thead = document.querySelector('#dataTable thead');
  tbody.innerHTML = '';
  thead.innerHTML = '';

  // Header
  const thr = document.createElement('tr');
  columns.forEach(col => {
    const h = document.createElement('th'); h.textContent = col; thr.appendChild(h);
  });
  const hAct = document.createElement('th'); hAct.textContent = 'Ações'; thr.appendChild(hAct);
  thead.appendChild(thr);

  // Rows (respect filter for display)
  const rows = model.rows;
  rows.forEach((row, idx) => {
    const st = (row["Status"]||"").trim();
    const pass = (currentFilter==="todos" || st===currentFilter);
    if (!pass) return;

    const tr = document.createElement('tr');
    columns.forEach(col => {
      const td = document.createElement('td');
      if (col==="Status") {
        const sel = statusSelect(st);
        sel.onchange = (e)=>{ row["Status"]=e.target.value; computeKPIs(); };
        td.appendChild(sel);
      } else {
        td.contentEditable = editingEnabled ? "true" : "false";
        td.textContent = String(row[col] ?? "");
        td.onblur = (e)=>{
          row[col] = e.currentTarget.textContent;
        };
      }
      tr.appendChild(td);
    });

    const act = document.createElement('td');
    act.className = 'row-actions';
    const btnDel = document.createElement('button'); btnDel.textContent = 'Excluir';
    btnDel.onclick = ()=>{
        const indexToDelete = model.rows.findIndex(r => r === row);
        if (indexToDelete > -1) {
            model.rows.splice(indexToDelete, 1);
            computeKPIs(); renderTable();
        }
    };
    act.appendChild(btnDel);
    tr.appendChild(act);

    tbody.appendChild(tr);
  });

  // KPIs (per-sheet; based on all rows)
  const all = model.rows.length || 0;
  const done = model.rows.filter(r=> (r["Status"]||"") === "Concluído").length;
  const doing = model.rows.filter(r=> (r["Status"]||"") === "Em andamento").length;
  const blocked = model.rows.filter(r=> (r["Status"]||"") === "Bloqueado").length;
  const err = model.rows.filter(r=> (r["Status"]||"") === "Erro").length;
  const blockedErr = blocked + err;

  document.getElementById('kpiTotal').textContent = String(all);
  document.getElementById('kpiDone').textContent = String(done);
  document.getElementById('kpiDoing').textContent = String(doing);
  document.getElementById('kpiBlocked').textContent = String(blockedErr);
  const pct = all ? Math.round((done / all) * 100) : 0;
  document.getElementById('progressBar').style.width = pct + '%';
  document.getElementById('progressLabel').textContent = pct + '%';

  // Ondas (per-sheet)
  renderOndaKPIs(model);

  // Quadro de Avisos
  renderNoticeBoard();

  // After any update, recompute global KPIs
  computeGlobalKPIs();
}

function computeKPIs() { renderTable(); }

function statusSelect(value) {
  const opts = ["Planejado","Em andamento","Concluído","Bloqueado","Erro"];
  const sel = document.createElement('select');
  sel.className = 'status';
  opts.forEach(o => {
    const op = document.createElement('option');
    op.value = o; op.textContent = o;
    if (o===value) op.selected = true;
    sel.appendChild(op);
  });
  return sel;
}

// Tabs
function buildTabs() {
  const wbTabs = document.getElementById('wbTabs');
  wbTabs.innerHTML = '';
  Object.keys(appData).forEach(name => {
    const el = document.createElement('div');
    el.className = 'tab' + (name===currentWb?' active':'');
    el.textContent = name;
    el.onclick = () => { currentWb = name; currentSheet = Object.keys(appData[currentWb])[0]; buildTabs(); buildSheetTabs(); renderTable(); };
    wbTabs.appendChild(el);
  });
}

function buildSheetTabs() {
  const sheetTabs = document.getElementById('sheetTabs');
  sheetTabs.innerHTML = '';
  Object.keys(appData[currentWb]).forEach(name => {
    const el = document.createElement('div');
    el.className = 'subtab' + (name===currentSheet?' active':'');
    el.textContent = name;
    el.onclick = () => { currentSheet = name; renderTable(); };
    sheetTabs.appendChild(el);
  });
}

// Buttons & actions
document.querySelectorAll('#statusChips .chip').forEach(ch => {
  ch.onclick = ()=>{
    document.querySelectorAll('#statusChips .chip').forEach(c=>c.classList.remove('active'));
    ch.classList.add('active');
    currentFilter = ch.dataset.status;
    renderTable();
  }
});

document.getElementById('addRow').onclick = ()=> {
  const model = appData[currentWb][currentSheet];
  const row = {};
  model.columns.forEach(c => row[c] = '');
  if (model.columns.includes("Status")) row["Status"] = "Planejado";
  model.rows.push(row);
  computeKPIs();
};

document.getElementById('addColumn').onclick = ()=> {
  const name = prompt('Nome da nova coluna:');
  if (!name || name.trim() === '') return;
  const model = appData[currentWb][currentSheet];
  const trimmedName = name.trim();
  if (!model.columns.includes(trimmedName)) model.columns.push(trimmedName);
  model.rows.forEach(r => { if(!(trimmedName in r)) r[trimmedName] = ''; });
  computeKPIs();
};

document.getElementById('addSheet').onclick = ()=> {
  const name = prompt('Nome da nova planilha:');
  if (!name || name.trim() === '') return;
  if (!appData[currentWb]) appData[currentWb] = {};
  appData[currentWb][name.trim()] = {
    columns: ["ID", "Macroprocesso (MM/LE/SD/ACM)", "Descrição do Cenário", "Centros", "Pré-requisitos", "Passos de Teste (alto nível)", "Passos de Teste (detalhados)", "Massa de Dados (por cenário)", "Resultado Esperado", "Evidências (links / NF / print)", "Status"],
    rows: []
  };
  currentSheet = name.trim();
  buildSheetTabs();
  computeKPIs();
};

document.getElementById('exportJson').onclick = ()=> {
  const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(appData, null, 2));
  const a = document.createElement('a');
  a.href = dataStr;
  a.download = "smoke_test_controle.json";
  a.click();
};

document.getElementById('exportCsv').onclick = ()=> {
  const model = appData[currentWb][currentSheet];
  const cols = model.columns;
  const csvRows = [];
  csvRows.push(cols.map(c => '"' + String(c).replace(/"/g,'""') + '"').join(','));
  model.rows.forEach(r => {
    const row = cols.map(c => '"' + String(r[c]||'').replace(/"/g,'""') + '"').join(',');
    csvRows.push(row);
  });
  const blob = new Blob([csvRows.join('\n')], {type: 'text/csv;charset=utf-8;'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = (currentSheet.replace(/\s+/g,'_')) + ".csv";
  a.click();
  setTimeout(()=>URL.revokeObjectURL(url), 1000);
};

document.getElementById('printView').onclick = ()=> window.print();

document.getElementById('importJson').addEventListener('change', (ev)=> {
  const file = ev.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = (e)=> {
    try {
      const incoming = JSON.parse(e.target.result);
      // Mescla o novo JSON nos dados existentes, se necessário.
      for (const k of Object.keys(incoming)) appData[k] = incoming[k];

      // Re-normaliza os dados após a importação
      for (const wb of Object.keys(appData)) {
          for (const sh of Object.keys(appData[wb])) {
              const m = appData[wb][sh];
              if (!m.columns) m.columns = Object.keys((m.rows && m.rows[0]) || {});
              if (!m.rows) m.rows = [];
              if (!m.columns.includes("Status")) m.columns.push("Status");
              m.rows.forEach(r => {
                  for (const key of Object.keys(r)) { r[key] = String(r[key] || ""); }
                  if (r["Status"] === "") r["Status"] = "Planejado";
              });
          }
      }

      currentWb = Object.keys(appData)[0];
      currentSheet = Object.keys(appData[currentWb])[0];
      buildTabs(); buildSheetTabs(); computeKPIs();
    } catch(err) { alert('JSON inválido'); }
  };
  reader.readAsText(file);
});

// Init
buildTabs();
buildSheetTabs();
computeKPIs();
renderTable();

// ====== Edição e Salvamento (Adição) ======
(function(){
  const btnToggle = document.getElementById('toggleEdit');
  const btnSave   = document.getElementById('saveHtml');

  function updateToggleLabel(){
    if (!btnToggle) return;
    btnToggle.textContent = (editingEnabled ? '🔒 Desabilitar edição' : '🔓 Habilitar edição');
  }

  if (btnToggle){
    btnToggle.addEventListener('click', function(){
      editingEnabled = !editingEnabled;
      updateToggleLabel();
      // Re-render para aplicar contentEditable conforme o modo
      try { renderTable(); } catch(e){}
      try { renderNoticeBoard(); } catch(e){}
    });
    updateToggleLabel();
  }

  // Serializa o HTML atual com o appData modificado embutido
  function downloadCurrentHTML(){
    try {
      // Atualiza localStorage com appData atual
      localStorage.setItem('smokePanelAppData', JSON.stringify(appData));

      // Serializa DOM
      let html = document.documentElement.outerHTML;
      // Substitui o bloco "const appData = ..." pelo estado atual
      const jsonStr = JSON.stringify(appData, null, 2);
      html = html.replace(/const\s+appData\s*=\s*{[\s\S]*?};/, 'const appData = ' + jsonStr + ';');

      const blob = new Blob([html], {type: 'text/html;charset=utf-8'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      const today = new Date();
      const ts = today.toISOString().slice(0,19).replace(/[:T]/g,'-');
      a.download = 'gestao_patio_uso_editavel.SAVED-' + ts + '.html';
      document.body.appendChild(a);
      a.click();
      setTimeout(function(){ URL.revokeObjectURL(url); document.body.removeChild(a); }, 1000);
    } catch (e){
      alert('Falha ao salvar HTML: ' + e.message);
      console.error(e);
    }
  }

  if (btnSave){
    btnSave.addEventListener('click', downloadCurrentHTML);
  }

  // Opcional: se existir um appData salvo no navegador, permitir mesclar ao carregar
  try {
    const saved = localStorage.getItem('smokePanelAppData');
    if (saved){
      const parsed = JSON.parse(saved);
      // Mescla superficial por workbook/sheet
      Object.keys(parsed || {}).forEach(function(wb){
        if (!appData[wb]) appData[wb] = parsed[wb];
        else {
          Object.keys(parsed[wb] || {}).forEach(function(sh){
            appData[wb][sh] = parsed[wb][sh];
          });
        }
      });
      // Re-render para refletir o appData salvo
      try { computeKPIs(); } catch(e){}
      try { renderTable(); } catch(e){}
    }
  } catch(e){ console.warn('Não foi possível carregar appData salvo:', e); }
})();
// ====== Fim Edição e Salvamento ======
</script>


<script>
// ====== Persistência do Quadro de Avisos + Salvamento ======
(function(){
  const NB_STORE_KEY = 'smokePanelNoticeBoard';
  const APP_STORE_KEY = 'smokePanelAppData';
  const nbListEl = document.getElementById('nbList');
  const nbInputEl = document.getElementById('nbInput');
  const nbAddBtn = document.getElementById('nbAdd');
  const nbClearBtn = document.getElementById('nbClear');
  const btnToggle = document.getElementById('toggleEdit');
  const btnSave   = document.getElementById('saveHtml');
  const nbEmptyEl = document.getElementById('nbEmpty');

  // Estado em memória
  let noticeItems = [];

  function loadNoticeBoard(){
    try {
      const raw = localStorage.getItem(NB_STORE_KEY);
      if (raw) noticeItems = JSON.parse(raw);
    } catch(e){ noticeItems = []; }
    // Também tenta carregar do appData.__noticeBoard (se vier de arquivo salvo)
    try {
      if (typeof appData === 'object' && appData && Array.isArray(appData.__noticeBoard) && !noticeItems.length){
        noticeItems = appData.__noticeBoard.slice();
      }
    } catch(e){}
    renderNoticeBoard();
  }

  function saveNoticeBoard(){
    localStorage.setItem(NB_STORE_KEY, JSON.stringify(noticeItems));
    // espelha no appData para que o botão "Salvar" incorpore no arquivo .html
    try {
      if (typeof appData === 'object' && appData) appData.__noticeBoard = noticeItems.slice();
    } catch(e){}
  }

  function renderNoticeBoard(){
    if (!nbListEl) return;
    nbListEl.innerHTML = '';
    if (!noticeItems.length){
      if (nbEmptyEl) nbEmptyEl.style.display = 'block';
      return;
    } else {
      if (nbEmptyEl) nbEmptyEl.style.display = 'none';
    }
    noticeItems.forEach((text, idx) => {
      const li = document.createElement('li');
      li.className = 'nb-item';
      const div = document.createElement('div');
      div.className = 'nb-text';
      div.setAttribute('contenteditable', String(editingEnabled));
      div.textContent = text;
      div.addEventListener('blur', () => {
        noticeItems[idx] = div.textContent.trim();
        saveNoticeBoard();
      });
      const del = document.createElement('button');
      del.className = 'nb-del';
      del.textContent = 'Remover';
      del.addEventListener('click', () => {
        noticeItems.splice(idx,1);
        saveNoticeBoard();
        renderNoticeBoard();
      });
      li.appendChild(div);
      li.appendChild(del);
      nbListEl.appendChild(li);
    });
  }

  function addNotice(text){
    const t = (text||'').trim();
    if (!t) return;
    noticeItems.push(t);
    saveNoticeBoard();
    renderNoticeBoard();
  }

  // Bind botões
  if (nbAddBtn && nbInputEl){
    nbAddBtn.onclick = function(){
      addNotice(nbInputEl.value);
      nbInputEl.value='';
      nbInputEl.focus();
    };
  }
  if (nbClearBtn){
    nbClearBtn.onclick = function(){
      if (confirm('Limpar todos os avisos?')){
        noticeItems = [];
        saveNoticeBoard();
        renderNoticeBoard();
      }
    };
  }

  // Toggle edição
  function updateToggleLabel(){
    if (!btnToggle) return;
    btnToggle.textContent = (editingEnabled ? '🔒 Desabilitar edição' : '🔓 Habilitar edição');
  }
  if (btnToggle){
    btnToggle.addEventListener('click', function(){
      editingEnabled = !editingEnabled;
      updateToggleLabel();
      // Reaplica contentEditable nos avisos
      Array.from(document.querySelectorAll('.nb-text')).forEach(el=>{
        el.setAttribute('contenteditable', String(editingEnabled));
      });
      // Se existir render de tabela em outro script, tente disparar
      try { if (typeof renderTable==='function') renderTable(); } catch(e){}
    });
    updateToggleLabel();
  }

  // Botão Salvar — incorpora appData + __noticeBoard no arquivo HTML
  function downloadCurrentHTML(){
    try {
      // garante refletir avisos no appData
      saveNoticeBoard();

      // Serializa DOM atual
      let html = document.documentElement.outerHTML;

      // Substitui bloco "const appData = {...};" por estado atualizado com avisos
      const jsonStr = JSON.stringify(appData || {}, null, 2);
      html = html.replace(/const\s+appData\s*=\s*{[\s\S]*?};/, 'const appData = ' + jsonStr + ';');

      const blob = new Blob([html], {type: 'text/html;charset=utf-8'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      const ts = new Date().toISOString().slice(0,19).replace(/[:T]/g,'-');
      a.download = 'gestao_patio_uso_editavel.NOTICES-SAVED-' + ts + '.html';
      document.body.appendChild(a);
      a.click();
      setTimeout(function(){ URL.revokeObjectURL(url); document.body.removeChild(a); }, 1000);
    } catch (e){
      alert('Falha ao salvar HTML: ' + e.message);
      console.error(e);
    }
  }
  if (btnSave){
    btnSave.addEventListener('click', downloadCurrentHTML);
  }

  // Carrega ao iniciar
  if (document.readyState === 'loading'){
    document.addEventListener('DOMContentLoaded', loadNoticeBoard);
  } else {
    loadNoticeBoard();
  }
})();
// ====== Fim persistência do Quadro de Avisos ======
</script>
<a href="blob:null/3b5bbf57-8e93-4c45-8ed4-35d8723b1047" download="gestao_patio_uso_editavel.SAVED-2025-10-05-18-05-16.html"></a>
<!-- ===== Fix Porto Filter / Tab Delegation ===== -->
<script>
(function(){
  // Defensive: make tab switching work even after saving a new HTML
  function firstSheetName(wb){
    try {
      return Object.keys(appData[wb] || {})[0] || null;
    } catch(e){ return null; }
  }
  function setWorkbook(wbName){
    if (!wbName || !appData[wbName]) return;
    // Try to set globals used by the original script
    try { window.currentWorkbook = wbName; } catch(e){}
    try { window.currentWorkbookName = wbName; } catch(e){}
    try { window.activeWorkbook = wbName; } catch(e){}
    var sh = firstSheetName(wbName);
    if (sh){
      try { window.currentSheet = sh; } catch(e){}
      try { window.currentSheetName = sh; } catch(e){}
      try { window.activeSheet = sh; } catch(e){}
    }
    // Update active tab visual
    var tabs = document.querySelectorAll('#wbTabs .tab');
    tabs.forEach(function(t){
      var label = (t.textContent||'').trim();
      t.classList.toggle('active', label === wbName);
    });
    // Re-render using original functions if available
    var ok = false;
    try { if (typeof computeKPIs === 'function'){ computeKPIs(); ok = true; } } catch(e){}
    try { if (typeof renderTable === 'function'){ renderTable(); ok = true; } } catch(e){}
    // Fallback minimal render if original functions are not present
    if (!ok){
      try {
        var table = document.getElementById('dataTable');
        if (!table) return;
        var tbody = table.querySelector('tbody');
        if (!tbody) return;
        // get first sheet rows
        var sheet = appData[wbName][sh];
        var cols = (sheet && sheet.columns) ? sheet.columns.slice() : [];
        var rows = (sheet && sheet.rows) ? sheet.rows.slice() : [];
        // build rows
        var htmlRows = rows.map(function(r){
          var tds = cols.map(function(c){
            var v = (r[c] != null ? r[c] : '');
            // keep cells non-editable by default (original script controls edit mode elsewhere)
            return '<td contenteditable="false">' + String(v).replace(/&/g,'&amp;').replace(/</g,'&lt;') + '</td>';
          }).join('');
          // Add Status select + actions if missing (best effort)
          if (cols.indexOf('Status') === -1) {
            tds += '<td><select class="status"><option>Planejado</option><option>Em andamento</option><option>Concluído</option><option>Bloqueado</option><option>Erro</option></select></td>';
          }
          tds += '<td class="row-actions"><button>Excluir</button></td>';
          return '<tr>' + tds + '</tr>';
        }).join('');
        tbody.innerHTML = htmlRows;
      } catch(e){ console.warn('Fallback render failed:', e); }
    }
  }

  // Delegation on #wbTabs to survive HTML saves
  function bindTabDelegation(){
    var wbTabs = document.getElementById('wbTabs');
    if (!wbTabs || wbTabs.__delegated) return;
    wbTabs.addEventListener('click', function(ev){
      var t = ev.target;
      if (!t) return;
      // if clicking inside a tab child, bubble up to .tab
      if (!t.classList.contains('tab')){
        t = t.closest('.tab');
      }
      if (!t) return;
      var label = (t.textContent||'').trim();
      if (!label) return;
      setWorkbook(label);
    });
    wbTabs.__delegated = true;
  }

  // Optional quick fix button: if an element with id="filterPorto" exists, wire it up
  function bindQuickPorto(){
    var b = document.getElementById('filterPorto');
    if (!b || b.__bound) return;
    b.addEventListener('click', function(){
      setWorkbook('Smoke Test - Porto');
    });
    b.__bound = true;
  }

  // Initialize once DOM is ready
  if (document.readyState === 'loading'){
    document.addEventListener('DOMContentLoaded', function(){
      bindTabDelegation();
      bindQuickPorto();
    });
  } else {
    bindTabDelegation();
    bindQuickPorto();
  }

  // Expose for debugging
  window.__fixSetWorkbook = setWorkbook;
})();
</script>
<!-- ===== End Fix Porto Filter ===== -->

</body></html>
