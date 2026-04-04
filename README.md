// ── NAV CONFIG ────────────────────────────────────────────────────────────────
const NAV = [
  {
    section: 'UMortgage',
    items: [
      { id: 'home',      icon: '🏠', label: 'UMortgage',     type: 'iframe',    url: 'https://www.umortgage.com/' },
      { id: 'community', icon: '👥', label: 'Community',      type: 'linkcard',  url: 'https://www.facebook.com/groups/umortgage', desc: 'Join the UMortgage Facebook community — connect with teammates, share wins, and stay in the loop.', btnLabel: 'Open Facebook Group' },
      { id: 'arive',     icon: '🏦', label: 'ARIVE',          type: 'iframe',    url: 'https://umortgage.myarive.com/' },
      { id: 'tempo',     icon: '🎯', label: 'Tempo',          type: 'iframe',    url: 'https://tempo.umortgage.com/' },
    ]
  },
  {
    section: 'AI Tools',
    items: [
      { id: 'pipeline',  icon: '📊', label: 'Processing Report', type: 'tool', toolFile: 'tools/pipeline.html' },
      { id: 'turntimes', icon: '⏱', label: 'Turn Times',      type: 'beta',    betaIcon: '⏱', betaDesc: 'Real-time lender turn time tracking is coming soon. You\'ll be able to see current UW turn times by lender, updated daily.' },
      { id: 'closing',   icon: '📅', label: 'Closing Engine',  type: 'beta',    betaIcon: '📅', betaDesc: 'AI-powered closing timeline generator. Input your loan details and get a full milestone schedule built around your target close date.' },
    ]
  }
];

// ── SIDEBAR HTML ──────────────────────────────────────────────────────────────
function buildSidebar(activeId) {
  const navHtml = NAV.map(section => {
    const items = section.items.map(item => `
      <div class="nav-item ${item.id === activeId ? 'active' : ''}"
           onclick="navigate('${item.id}')"
           data-id="${item.id}">
        <span class="nav-icon">${item.icon}</span>
        <span class="nav-label-text">${item.label}</span>
        ${item.type === 'beta' ? '<span class="beta-pill">BETA</span>' : ''}
      </div>
    `).join('');
    return `
      <div class="nav-section-label">${section.section}</div>
      ${items}
      <div class="nav-divider"></div>
    `;
  }).join('');

  return `
    <div class="sidebar">
      <div class="sidebar-logo">
        <div class="logo-badge">
          <div class="logo-pulse"></div>
          <span>AI Powered</span>
        </div>
        <div class="sidebar-title">UMortgage <span class="ai">AI</span> Tech</div>
        <div class="sidebar-sub">Internal Tools Platform</div>
      </div>
      <nav class="sidebar-nav">
        ${navHtml}
      </nav>
      <div class="sidebar-footer">
        <a class="footer-credit" href="https://en.wikipedia.org/wiki/Michael_Jordan" target="_blank">
          Employee #23 Built This 🐐
        </a>
      </div>
    </div>
  `;
}

// ── PAGE CONTENT ──────────────────────────────────────────────────────────────
function buildPage(item) {
  const topbar = `
    <div class="topbar">
      <div class="topbar-left">
        <span style="font-size:16px;">${item.icon}</span>
        <span class="topbar-page-title">${item.label}</span>
        ${item.type === 'beta' ? '<span class="topbar-badge">BETA</span>' : ''}
      </div>
      <div class="topbar-right">
        <span class="topbar-badge">UMortgage AI Tech</span>
      </div>
    </div>
  `;

  let content = '';

  if (item.type === 'iframe') {
    content = `
      <div class="iframe-container">
        <iframe src="${item.url}" sandbox="allow-same-origin allow-scripts allow-forms allow-popups allow-popups-to-escape-sandbox" allow="fullscreen"></iframe>
      </div>
    `;
  } else if (item.type === 'linkcard') {
    content = `
      <div class="link-card-page">
        <div class="link-card">
          <div class="link-card-icon">${item.icon}</div>
          <div class="link-card-title">${item.label}</div>
          <div class="link-card-desc">${item.desc}</div>
          <a class="link-card-btn" href="${item.url}" target="_blank">
            ${item.btnLabel} ↗
          </a>
        </div>
      </div>
    `;
  } else if (item.type === 'beta') {
    content = `
      <div class="placeholder-page">
        <div class="placeholder-icon">${item.betaIcon}</div>
        <div class="beta-badge-big">⚡ Coming Soon</div>
        <div class="placeholder-title">${item.label}</div>
        <div class="placeholder-sub">${item.betaDesc}</div>
      </div>
    `;
  } else if (item.type === 'tool') {
    content = `
      <div class="content-page">
        <iframe src="${item.toolFile}?embedded=1"></iframe>
      </div>
    `;
  }

  return topbar + content;
}

// ── NAVIGATE ──────────────────────────────────────────────────────────────────
function navigate(id) {
  const allItems = NAV.flatMap(s => s.items);
  const item = allItems.find(i => i.id === id);
  if (!item) return;

  // Update sidebar active state
  document.querySelectorAll('.nav-item').forEach(el => {
    el.classList.toggle('active', el.dataset.id === id);
    // update active bar
    const before = el.querySelector('::before');
  });

  // Re-render active states
  document.querySelectorAll('.nav-item').forEach(el => {
    el.classList.remove('active');
    if (el.dataset.id === id) el.classList.add('active');
  });

  // Render page
  document.getElementById('main').innerHTML = buildPage(item);

  // Update URL hash
  history.pushState({id}, '', `#${id}`);
  document.title = `${item.label} · UMortgage AI Tech`;
}

// ── INIT ──────────────────────────────────────────────────────────────────────
function initApp() {
  const hash = window.location.hash.replace('#', '') || 'home';
  const allItems = NAV.flatMap(s => s.items);
  const item = allItems.find(i => i.id === hash) || allItems[0];

  document.getElementById('sidebar-container').innerHTML = buildSidebar(item.id);
  document.getElementById('main').innerHTML = buildPage(item);
  document.title = `${item.label} · UMortgage AI Tech`;
}

window.addEventListener('popstate', (e) => {
  if (e.state?.id) navigate(e.state.id);
});

document.addEventListener('DOMContentLoaded', initApp);
