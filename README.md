
<!-- HERO -->
<div class="hero">
  <div class="hero-eye">Portfólio de Automação</div>
  <h1>Automações Python<br>para <em>Segurança</em><br>e Monitoramento</h1>
  <p class="hero-sub">
    Três projetos de automação desenvolvidos em Python para monitoramento de câmeras CFTV e sistemas de alarme — do acesso automatizado ao portal, passando pelo processamento de dados, até o envio de dashboards por e-mail.
  </p>
  <div class="pills">
    <span class="pill g">Python</span>
    <span class="pill g">Selenium</span>
    <span class="pill g">Pandas</span>
    <span class="pill b">Dashboard HTML</span>
    <span class="pill b">Chart.js</span>
    <span class="pill b">Excel / XlsxWriter</span>
    <span class="pill r">Win32com / Outlook</span>
    <span class="pill r">Web Scraping</span>
  </div>
  <div class="stats-bar">
    <div class="stat"><div class="stat-val">3</div><div class="stat-lbl">Projetos entregues</div></div>
    <div class="stat"><div class="stat-val">0</div><div class="stat-lbl">Minutos de trabalho manual</div></div>
    <div class="stat"><div class="stat-val">26</div><div class="stat-lbl">UFs monitoradas</div></div>
    <div class="stat"><div class="stat-val">100%</div><div class="stat-lbl">Pipeline automatizado</div></div>
  </div>
</div>

<div class="div-section"><hr class="divider"></div>

<!-- PROJECTS -->
<div class="projects" id="projetos">
  <div class="proj-label">Projetos</div>
  <h2 class="proj-title">O que foi construído</h2>
  <p class="proj-desc">Cada projeto resolve um problema real de operação — acesso a dados, processamento e comunicação — sem nenhuma intervenção manual após a configuração.</p>

  <!-- PROJETO 1 — CFTV -->
  <div class="proj-card">
    <div class="proj-header">
      <div>
        <div class="proj-name">📷 Automação CFTV — Monitor de Câmeras</div>
        <div class="proj-tagline">Acessa o portal de câmeras, exporta dados, processa status por UF e agência, gera dashboard interativo e envia e-mail automático via Outlook.</div>
      </div>
      <span class="proj-badge badge-g">Python · Selenium</span>
    </div>
    <div class="proj-body">
      <div class="proj-grid">
        <div>
          <div class="feat-label">O que o script faz</div>
          <ul class="features">
            <li>Login automatizado no portal CFTV com tratamento de popups e SSL</li>
            <li>Navegação por menus dinâmicos usando clique por texto e CDP</li>
            <li>Download automático do Excel e movimentação para o OneDrive</li>
            <li>Processamento com Pandas — status, disponibilidade por UF e agência</li>
            <li>Comparativo com relatórios anteriores e série histórica</li>
            <li>Dashboard HTML com filtros, KPIs, tabelas e gráficos Chart.js</li>
            <li>Screenshot do dashboard via Chrome headless (1400×920px)</li>
            <li>E-mail automático via Outlook com imagem inline e anexos</li>
          </ul>
          <div class="tech-row">
            <span class="chip">selenium</span><span class="chip">pandas</span>
            <span class="chip">pathlib</span><span class="chip">win32com</span>
            <span class="chip">unicodedata</span><span class="chip">chart.js</span>
          </div>
        </div>
        <div>
          <div class="feat-label">Execução</div>
          <div class="term">
            <div class="term-bar"><div class="d dr"></div><div class="d dy"></div><div class="d dg"></div></div>
            <div class="term-body">
              <div><span class="tp">$ </span><span class="tc">python baixar_cftv_e_envia_email.py</span></div>
              <div><span class="tok">✓ Login realizado</span></div>
              <div><span class="to">Exportando Excel...</span></div>
              <div><span class="tok">✓ 1.847 câmeras processadas</span></div>
              <div><span class="tw">⚠  234 câmeras offline</span></div>
              <div><span class="tok">✓ Dashboard HTML gerado</span></div>
              <div><span class="tok">✓ Screenshot capturado</span></div>
              <div><span class="tok">✓ E-mail enviado via Outlook</span></div>
              <div><span class="tp">$ </span><span class="cur"></span></div>
            </div>
          </div>
          <div style="margin-top:1.2rem">
            <div class="feat-label">Outputs gerados</div>
            <div class="outputs">
              <div class="out-card"><div class="out-icon">📊</div><div class="out-name">Excel com dados</div><div class="out-desc">Status de todas as câmeras salvo no OneDrive</div></div>
              <div class="out-card"><div class="out-icon">🌐</div><div class="out-name">Dashboard HTML</div><div class="out-desc">Painel interativo com filtros e gráficos offline</div></div>
              <div class="out-card"><div class="out-icon">📸</div><div class="out-name">Screenshot PNG</div><div class="out-desc">Imagem embutida no corpo do e-mail</div></div>
              <div class="out-card"><div class="out-icon">📧</div><div class="out-name">E-mail Outlook</div><div class="out-desc">Enviado automaticamente com anexos</div></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- PROJETO 2 — ALARME LOGS -->
  <div class="proj-card">
    <div class="proj-header">
      <div>
        <div class="proj-name">🚨 Automação de Logs de Alarme — BNB</div>
        <div class="proj-tagline">Baixa logs de disparos de alarme do sistema BNB em blocos de período configuráveis, consolida os dados e gera dashboard Excel e HTML com ranking por região, sensor e unidade.</div>
      </div>
      <span class="proj-badge badge-b">Python · Pandas · XlsxWriter</span>
    </div>
    <div class="proj-body">
      <div class="proj-grid">
        <div>
          <div class="feat-label">O que o script faz</div>
          <ul class="features">
            <li>Login e navegação no sistema de gestão BNB com fallback via webdriver_manager</li>
            <li>Download em blocos de período configuráveis para contornar limites do sistema</li>
            <li>Retomada a partir do bloco que falhou — sem precisar recomeçar do zero</li>
            <li>Modo de consolidação de arquivos já baixados, sem acessar o site novamente</li>
            <li>Filtragem e limpeza dos eventos — apenas disparos de alarme reais</li>
            <li>Extração de UF e mapeamento automático para regiões do Brasil</li>
            <li>Ranking por região, sensor e unidade com comparativo histórico</li>
            <li>Dashboard Excel com 7 abas, KPIs, 3 gráficos embutidos e autofiltros</li>
          </ul>
          <div class="tech-row">
            <span class="chip">selenium</span><span class="chip">pandas</span>
            <span class="chip">xlsxwriter</span><span class="chip">webdriver_manager</span>
            <span class="chip">pathlib</span><span class="chip">unicodedata</span>
          </div>
        </div>
        <div>
          <div class="feat-label">Execução</div>
          <div class="term">
            <div class="term-bar"><div class="d dr"></div><div class="d dy"></div><div class="d dg"></div></div>
            <div class="term-body">
              <div><span class="tp">$ </span><span class="tc">python baixar_logs_bnb_alarm_dashboard.py</span></div>
              <div><span class="tok">✓ Login BNB realizado</span></div>
              <div><span class="to">Baixando bloco 01/05...</span></div>
              <div><span class="tok">✓ Bloco 03: 1.240 eventos</span></div>
              <div><span class="to">Consolidando 5 arquivos...</span></div>
              <div><span class="tok">✓ 4.202 disparos filtrados</span></div>
              <div><span class="tw">⚠  Região NORDESTE: 3.963</span></div>
              <div><span class="tok">✓ Dashboard Excel gerado</span></div>
              <div><span class="tp">$ </span><span class="cur"></span></div>
            </div>
          </div>
          <div style="margin-top:1.2rem">
            <div class="feat-label">Outputs gerados</div>
            <div class="outputs">
              <div class="out-card"><div class="out-icon">📋</div><div class="out-name">Base limpa</div><div class="out-desc">Só disparos reais, sem ruído do sistema</div></div>
              <div class="out-card"><div class="out-icon">📈</div><div class="out-name">Rankings</div><div class="out-desc">Por região, sensor e unidade com histórico</div></div>
              <div class="out-card"><div class="out-icon">📊</div><div class="out-name">Dashboard Excel</div><div class="out-desc">7 abas, KPIs e 3 gráficos embutidos</div></div>
              <div class="out-card"><div class="out-icon">🔄</div><div class="out-name">Comparativo</div><div class="out-desc">Melhoria percentual vs. relatório anterior</div></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- PROJETO 3 — DASHBOARD HTML -->
  <div class="proj-card">
    <div class="proj-header">
      <div>
        <div class="proj-name">🌐 Dashboard de Disparos — HTML Interativo</div>
        <div class="proj-tagline">Dashboard visual standalone gerado automaticamente em HTML puro — sem servidor, sem dependências externas — com KPIs, rankings e tabela de top 25 unidades.</div>
      </div>
      <span class="proj-badge badge-r">HTML · CSS · JavaScript</span>
    </div>
    <div class="proj-body">
      <div class="proj-grid">
        <div>
          <div class="feat-label">Funcionalidades do dashboard</div>
          <ul class="features">
            <li>4 KPIs principais — total de disparos, unidade top, sensor top, região top</li>
            <li>Ranking por região com barras de progresso proporcionais</li>
            <li>Ranking por sensor com top 10 e barras visuais</li>
            <li>Ranking por unidade com top 10 e visualização comparativa</li>
            <li>Tabela completa com top 25 unidades e contagem de disparos</li>
            <li>Lista de arquivos brutos utilizados na consolidação</li>
            <li>Design responsivo com tema rose/marrom — identidade visual da empresa</li>
            <li>Arquivo único HTML — sem servidor, abre em qualquer navegador</li>
          </ul>
          <div class="tech-row">
            <span class="chip">HTML5</span><span class="chip">CSS Grid</span>
            <span class="chip">JavaScript</span><span class="chip">Responsivo</span>
            <span class="chip">Standalone</span>
          </div>
        </div>
        <div>
          <div class="feat-label">Dados do último relatório</div>
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:.7rem;margin-bottom:1rem">
            <div class="out-card">
              <div class="out-icon">🔥</div>
              <div class="out-name" style="font-size:1.3rem;color:var(--rose);font-family:var(--mono)">4.202</div>
              <div class="out-desc">Total de disparos no período</div>
            </div>
            <div class="out-card">
              <div class="out-icon">📍</div>
              <div class="out-name">NORDESTE</div>
              <div class="out-desc">Região com mais incidentes (3.963)</div>
            </div>
            <div class="out-card">
              <div class="out-icon">🏦</div>
              <div class="out-name">São Raimundo Nonato - PI</div>
              <div class="out-desc">Unidade com mais disparos (225)</div>
            </div>
            <div class="out-card">
              <div class="out-icon">🔔</div>
              <div class="out-name">Sensor Fumaça</div>
              <div class="out-desc">Sensor que mais disparou (212)</div>
            </div>
          </div>
          <div class="feat-label">Período coberto</div>
          <div class="term" style="font-size:11px">
            <div class="term-body" style="padding:.8rem 1rem;line-height:1.8">
              <div><span class="to">Período: </span><span class="tc">04/07/2026 → 06/07/2026</span></div>
              <div><span class="to">Arquivos: </span><span class="tok">7 blocos consolidados</span></div>
              <div><span class="to">Gerado em: </span><span class="tc">07/07/2026 às 18:25</span></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="div-section"><hr class="divider"></div>

<!-- SKILLS -->
<div class="skills-section" id="skills">
  <div class="proj-label">Stack completa</div>
  <h2 class="proj-title">Tecnologias Dominadas</h2>
  <p class="proj-desc">Cada ferramenta foi escolhida e aplicada para resolver um problema real dentro dos projetos.</p>
  <div class="skills-grid">
    <div class="skill-card">
      <div class="skill-icon">🌐</div>
      <div class="skill-name">Selenium WebDriver</div>
      <div class="skill-desc">Automação de browser — login, navegação, cliques, downloads, tratamento de erros e Chrome DevTools Protocol.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">🐼</div>
      <div class="skill-name">Pandas</div>
      <div class="skill-desc">Leitura de Excel, limpeza, normalização, agrupamento, ranking e geração de comparativos históricos.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">📊</div>
      <div class="skill-name">XlsxWriter</div>
      <div class="skill-desc">Geração de Excel com múltiplas abas, formatação condicional, KPIs e gráficos embutidos programaticamente.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">📧</div>
      <div class="skill-name">Win32com / Outlook</div>
      <div class="skill-desc">Envio de e-mails via Outlook com imagem inline, anexos múltiplos e seleção de conta específica.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">🌐</div>
      <div class="skill-name">HTML / CSS / JS</div>
      <div class="skill-desc">Geração de dashboards HTML standalone com Chart.js, filtros dinâmicos e design responsivo.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">📁</div>
      <div class="skill-name">Pathlib & Shutil</div>
      <div class="skill-desc">Gerenciamento de arquivos — criação de pastas, movimentação, localização e organização no OneDrive.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">🔤</div>
      <div class="skill-name">Normalização de Texto</div>
      <div class="skill-desc">Unicodedata para remoção de acentos e comparação robusta de nomes de agências e sensores.</div>
    </div>
    <div class="skill-card">
      <div class="skill-icon">⚙️</div>
      <div class="skill-name">Arquitetura Resiliente</div>
      <div class="skill-desc">Fallbacks automáticos, retomada de downloads por bloco, modo consolidação e tratamento de erros em cascata.</div>
    </div>
  </div>
</div>

<footer>
  Desenvolvido em Python · Selenium · Pandas · XlsxWriter · Win32com · HTML/CSS/JS
</footer>

</body>
</html>
