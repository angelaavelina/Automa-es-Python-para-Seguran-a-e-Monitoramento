!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Portfólio — Automações Python</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;600&family=Inter:wght@300;400;500;600;700;800&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

:root{
  --bg:#0b0d12;
  --bg2:#111318;
  --bg3:#181c25;
  --border:#1f2332;
  --green:#00e57a;
  --green2:rgba(0,229,122,.08);
  --blue:#4d9eff;
  --blue2:rgba(77,158,255,.08);
  --rose:#e5507a;
  --rose2:rgba(229,80,122,.08);
  --text:#dde3f0;
  --muted:#606880;
  --sans:'Inter',sans-serif;
  --mono:'IBM Plex Mono',monospace;
}

html{scroll-behavior:smooth}
body{background:var(--bg);color:var(--text);font-family:var(--sans);font-size:15px;line-height:1.7}

/* NAV */
nav{
  position:fixed;top:0;left:0;right:0;z-index:100;
  display:flex;align-items:center;justify-content:space-between;
  padding:0 2.5rem;height:54px;
  background:rgba(11,13,18,.94);
  backdrop-filter:blur(14px);
  border-bottom:1px solid var(--border);
}
.nav-logo{font-family:var(--mono);font-size:12px;color:var(--green);letter-spacing:.08em}
.nav-links{display:flex;gap:2rem;list-style:none}
.nav-links a{font-size:12px;color:var(--muted);text-decoration:none;transition:color .2s}
.nav-links a:hover{color:var(--text)}

/* HERO */
.hero{
  min-height:100vh;
  display:flex;flex-direction:column;justify-content:center;
  padding:8rem 2.5rem 5rem;
  max-width:960px;margin:0 auto;
}
.hero-eye{
  font-family:var(--mono);font-size:11px;color:var(--green);
  letter-spacing:.14em;text-transform:uppercase;
  display:flex;align-items:center;gap:.75rem;margin-bottom:1.5rem;
}
.hero-eye::before{content:'';width:28px;height:1px;background:var(--green)}
.hero h1{
  font-size:clamp(2.4rem,5.5vw,4.2rem);
  font-weight:800;line-height:1.08;letter-spacing:-.03em;
  margin-bottom:1.4rem;
}
.hero h1 em{font-style:normal;color:var(--green)}
.hero-sub{font-size:1.05rem;color:var(--muted);max-width:580px;line-height:1.9;margin-bottom:2.5rem}
.pills{display:flex;flex-wrap:wrap;gap:.5rem;margin-bottom:3.5rem}
.pill{
  font-family:var(--mono);font-size:11px;
  padding:3px 10px;border-radius:4px;
  border:1px solid var(--border);color:var(--muted);background:var(--bg2);
}
.pill.g{border-color:var(--green);color:var(--green);background:var(--green2)}
.pill.b{border-color:var(--blue);color:var(--blue);background:var(--blue2)}
.pill.r{border-color:var(--rose);color:var(--rose);background:var(--rose2)}

/* STATS BAR */
.stats-bar{
  display:grid;grid-template-columns:repeat(4,1fr);
  border:1px solid var(--border);border-radius:10px;overflow:hidden;
  background:var(--border);gap:1px;
}
.stat{
  background:var(--bg2);padding:1.4rem 1.2rem;
}
.stat-val{font-family:var(--mono);font-size:1.9rem;font-weight:600;color:var(--green);line-height:1}
.stat-lbl{font-size:.78rem;color:var(--muted);margin-top:.3rem}

/* PROJECTS */
.projects{max-width:960px;margin:0 auto;padding:5rem 2.5rem}
.proj-label{font-family:var(--mono);font-size:11px;color:var(--muted);letter-spacing:.12em;text-transform:uppercase;margin-bottom:.8rem}
.proj-title{font-size:1.8rem;font-weight:700;letter-spacing:-.02em;margin-bottom:.8rem}
.proj-desc{color:var(--muted);max-width:640px;line-height:1.9;margin-bottom:3rem}

/* PROJECT CARD */
.proj-card{
  border:1px solid var(--border);border-radius:12px;overflow:hidden;
  margin-bottom:2rem;background:var(--bg2);
  transition:border-color .2s;
}
.proj-card:hover{border-color:#2a3045}
.proj-header{
  padding:1.6rem 1.8rem 1.2rem;
  display:flex;align-items:flex-start;justify-content:space-between;gap:1rem;
  border-bottom:1px solid var(--border);
}
.proj-badge{
  font-family:var(--mono);font-size:10px;
  padding:3px 9px;border-radius:3px;
  white-space:nowrap;flex-shrink:0;margin-top:4px;
}
.badge-g{background:var(--green2);color:var(--green);border:1px solid rgba(0,229,122,.25)}
.badge-b{background:var(--blue2);color:var(--blue);border:1px solid rgba(77,158,255,.25)}
.badge-r{background:var(--rose2);color:var(--rose);border:1px solid rgba(229,80,122,.25)}
.proj-name{font-size:1.15rem;font-weight:700;margin-bottom:.4rem}
.proj-tagline{font-size:.88rem;color:var(--muted);line-height:1.7}
.proj-body{padding:1.6rem 1.8rem}
.proj-grid{display:grid;grid-template-columns:1fr 1fr;gap:1.8rem}

/* FEATURES */
.features{list-style:none;display:flex;flex-direction:column;gap:.7rem}
.features li{
  display:flex;gap:.7rem;font-size:.88rem;color:var(--muted);line-height:1.6
}
.features li::before{
  content:'→';color:var(--green);flex-shrink:0;font-family:var(--mono);font-size:.8rem;margin-top:2px
}
.feat-label{font-size:.75rem;font-family:var(--mono);color:var(--muted);letter-spacing:.06em;text-transform:uppercase;margin-bottom:.8rem}

/* MINI TERMINAL */
.term{
  background:var(--bg3);border:1px solid var(--border);border-radius:8px;overflow:hidden;
  font-family:var(--mono);font-size:12px;
}
.term-bar{
  background:var(--bg);padding:.5rem .9rem;
  display:flex;align-items:center;gap:.4rem;
  border-bottom:1px solid var(--border);
}
.d{width:9px;height:9px;border-radius:50%}
.dr{background:#ff5f57}.dy{background:#febc2e}.dg{background:#28c840}
.term-body{padding:1rem 1.1rem;line-height:2}
.tp{color:var(--green)}.tc{color:var(--text)}
.to{color:var(--muted)}.tok{color:var(--green)}.tw{color:#f5c518}.te{color:var(--rose)}
.cur{display:inline-block;width:7px;height:13px;background:var(--green);vertical-align:middle;animation:bl 1s step-end infinite}
@keyframes bl{50%{opacity:0}}

/* TECH CHIPS */
.tech-row{display:flex;flex-wrap:wrap;gap:.4rem;margin-top:1.2rem}
.chip{
  font-family:var(--mono);font-size:10px;
  padding:2px 8px;border-radius:3px;
  border:1px solid var(--border);color:var(--muted);background:var(--bg);
}

/* OUTPUTS SECTION */
.outputs{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:.8rem;margin-top:1.2rem}
.out-card{
  background:var(--bg3);border:1px solid var(--border);border-radius:7px;
  padding:1rem 1rem;
}
.out-icon{font-size:1.3rem;margin-bottom:.4rem}
.out-name{font-size:.82rem;font-weight:600;margin-bottom:.2rem}
.out-desc{font-size:.76rem;color:var(--muted);line-height:1.6}

/* DIVIDER */
.div-section{max-width:960px;margin:0 auto;padding:0 2.5rem}
.divider{border:none;border-top:1px solid var(--border);margin:0}

/* SKILLS */
.skills-section{max-width:960px;margin:0 auto;padding:5rem 2.5rem}
.skills-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:1px;background:var(--border);border:1px solid var(--border);border-radius:10px;overflow:hidden}
.skill-card{background:var(--bg2);padding:1.2rem 1.1rem}
.skill-icon{font-size:1.4rem;margin-bottom:.5rem}
.skill-name{font-weight:600;font-size:.9rem;margin-bottom:.25rem}
.skill-desc{font-size:.78rem;color:var(--muted);line-height:1.6}

/* FOOTER */
footer{
  border-top:1px solid var(--border);
  padding:2rem;text-align:center;
  font-family:var(--mono);font-size:11px;color:var(--muted);
}

@media(max-width:640px){
  nav{padding:0 1rem}
  .nav-links{display:none}
  .hero,.projects,.skills-section,.div-section{padding-left:1.2rem;padding-right:1.2rem}
  .proj-grid{grid-template-columns:1fr}
  .stats-bar{grid-template-columns:1fr 1fr}
}
</style>
</head>
<body>

<nav>
  <div class="nav-logo">$ python ./projetos</div>
  <ul class="nav-links">
    <li><a href="#projetos">Projetos</a></li>
    <li><a href="#skills">Skills</a></li>
  </ul>
</nav>

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
