<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>GitHub Commit Dashboard — NithishwaranSenthilkumar</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@2.44.0/tabler-icons.min.css" />
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #ffffff;
    --bg-secondary: #f5f4f8;
    --text: #18181b;
    --text-secondary: #71717a;
    --border: rgba(0,0,0,0.1);
    --border-secondary: rgba(0,0,0,0.2);
    --purple-50: #EEEDFE;
    --purple-200: #AFA9EC;
    --purple-400: #7F77DD;
    --purple-600: #534AB7;
    --purple-800: #3C3489;
    --purple-900: #26215C;
    --radius-md: 8px;
    --radius-lg: 12px;
  }

  @media (prefers-color-scheme: dark) {
    :root {
      --bg: #18181b;
      --bg-secondary: #27272a;
      --text: #fafafa;
      --text-secondary: #a1a1aa;
      --border: rgba(255,255,255,0.1);
      --border-secondary: rgba(255,255,255,0.2);
    }
  }

  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    padding: 2rem 1rem;
  }

  .container { max-width: 860px; margin: 0 auto; }

  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 1.5rem;
  }
  .header-left { display: flex; align-items: center; gap: 12px; }
  .avatar {
    width: 44px; height: 44px; border-radius: 50%;
    background: var(--purple-50); display: flex; align-items: center;
    justify-content: center; font-weight: 500; font-size: 15px;
    color: var(--purple-800); border: 0.5px solid var(--border);
  }
  .name { font-size: 16px; font-weight: 500; }
  .handle { font-size: 12px; color: var(--text-secondary); margin-top: 2px; }

  .refresh-btn {
    cursor: pointer; font-size: 12px; color: var(--text-secondary);
    display: flex; align-items: center; gap: 4px; padding: 6px 12px;
    border: 0.5px solid var(--border-secondary); border-radius: var(--radius-md);
    background: none; transition: background 0.15s;
  }
  .refresh-btn:hover { background: var(--bg-secondary); }

  .metric-grid {
    display: grid; grid-template-columns: repeat(4, minmax(0, 1fr));
    gap: 10px; margin-bottom: 1.5rem;
  }
  @media (max-width: 600px) { .metric-grid { grid-template-columns: repeat(2, 1fr); } }
  .metric {
    background: var(--bg-secondary); border-radius: var(--radius-md);
    padding: 0.85rem 1rem;
  }
  .metric-val { font-size: 22px; font-weight: 500; }
  .metric-lbl { font-size: 12px; color: var(--text-secondary); margin-top: 4px; }

  .card {
    background: var(--bg); border: 0.5px solid var(--border);
    border-radius: var(--radius-lg); padding: 1rem 1.25rem; margin-bottom: 1.25rem;
  }
  .section-label {
    font-size: 11px; text-transform: uppercase; letter-spacing: 0.06em;
    color: var(--text-secondary); font-weight: 500; margin-bottom: 12px;
  }

  /* Heatmap */
  .heatmap-grid {
    display: grid; grid-template-columns: repeat(52, 1fr); gap: 2px;
  }
  .heatmap-week { display: flex; flex-direction: column; gap: 2px; }
  .heatmap-cell { width: 100%; aspect-ratio: 1; border-radius: 2px; cursor: default; }
  .heatmap-legend {
    display: flex; align-items: center; gap: 5px; margin-top: 8px;
    font-size: 11px; color: var(--text-secondary);
  }
  .legend-cell { width: 10px; height: 10px; border-radius: 2px; display: inline-block; }

  /* Two-col layout */
  .two-col { display: grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap: 1.25rem; }
  @media (max-width: 600px) { .two-col { grid-template-columns: 1fr; } }

  /* Language bars */
  .lang-row { display: flex; align-items: center; gap: 10px; margin-bottom: 8px; }
  .lang-name { font-size: 13px; min-width: 80px; }
  .lang-bar-bg { flex: 1; height: 6px; background: var(--border); border-radius: 3px; overflow: hidden; }
  .lang-bar-fill { height: 100%; border-radius: 3px; transition: width 0.5s ease; }
  .lang-pct { font-size: 12px; color: var(--text-secondary); min-width: 32px; text-align: right; }

  /* Repos */
  .repo-row {
    display: flex; justify-content: space-between; align-items: flex-start;
    padding: 10px 0; border-bottom: 0.5px solid var(--border);
  }
  .repo-row:last-child { border-bottom: none; padding-bottom: 0; }
  .repo-name { font-size: 13px; font-weight: 500; color: #534AB7; text-decoration: none; }
  .repo-name:hover { text-decoration: underline; }
  .repo-desc { font-size: 11px; color: var(--text-secondary); margin-top: 3px; max-width: 400px; }
  .repo-meta { display: flex; gap: 12px; font-size: 12px; color: var(--text-secondary); align-items: center; white-space: nowrap; }

  /* Loading / error */
  .loading { display: flex; align-items: center; gap: 8px; padding: 1.5rem 0; color: var(--text-secondary); font-size: 13px; }
  .spinner {
    width: 16px; height: 16px; border: 2px solid var(--border);
    border-top-color: #7c3aed; border-radius: 50%;
    animation: spin 0.7s linear infinite; flex-shrink: 0;
  }
  @keyframes spin { to { transform: rotate(360deg); } }
  .error-msg { font-size: 12px; color: var(--text-secondary); padding: 1rem 0; }
</style>
</head>
<body>
<div class="container">

  <!-- Header -->
  <div class="header">
    <div class="header-left">
      <div class="avatar">NS</div>
      <div>
        <div class="name">Nithishwaran S</div>
        <div class="handle">@NithishwaranSenthilkumar · CSE @ MIT Chennai</div>
      </div>
    </div>
    <button class="refresh-btn" onclick="loadAll()">
      <i class="ti ti-refresh" style="font-size:14px;" aria-hidden="true"></i> Refresh
    </button>
  </div>

  <!-- Metrics -->
  <div class="metric-grid">
    <div class="metric"><div class="metric-val" id="m-repos">—</div><div class="metric-lbl">Public repos</div></div>
    <div class="metric"><div class="metric-val" id="m-commits">—</div><div class="metric-lbl">Commits tracked</div></div>
    <div class="metric"><div class="metric-val" id="m-stars">—</div><div class="metric-lbl">Stars earned</div></div>
    <div class="metric"><div class="metric-val" id="m-followers">—</div><div class="metric-lbl">Followers</div></div>
  </div>

  <!-- Heatmap -->
  <div class="card">
    <div class="section-label">Contribution activity — last 52 weeks</div>
    <div id="heatmap-wrap"><div class="loading"><span class="spinner"></span> Loading contributions…</div></div>
    <div class="heatmap-legend">
      <span>Less</span>
      <span class="legend-cell" style="background:var(--bg-secondary);border:0.5px solid var(--border);"></span>
      <span class="legend-cell" style="background:#c4b5fd;"></span>
      <span class="legend-cell" style="background:#8b5cf6;"></span>
      <span class="legend-cell" style="background:#6d28d9;"></span>
      <span class="legend-cell" style="background:#4c1d95;"></span>
      <span>More</span>
    </div>
  </div>

  <!-- Lang + DOW -->
  <div class="two-col">
    <div class="card" style="margin-bottom:0;">
      <div class="section-label">Top languages</div>
      <div id="langs-wrap"><div class="loading"><span class="spinner"></span></div></div>
    </div>
    <div class="card" style="margin-bottom:0;">
      <div class="section-label">Commits by day of week</div>
      <div style="position:relative;width:100%;height:150px;">
        <canvas id="dowChart" role="img" aria-label="Commits grouped by day of week"></canvas>
      </div>
    </div>
  </div>

  <!-- Repos -->
  <div class="card" style="margin-top:1.25rem;">
    <div class="section-label">Recent repositories</div>
    <div id="repos-wrap"><div class="loading"><span class="spinner"></span></div></div>
  </div>

</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<script>
const USERNAME = 'NithishwaranSenthilkumar';
const GH = 'https://api.github.com';
let dowChartInstance = null;

async function ghFetch(path) {
  const r = await fetch(GH + path, { headers: { Accept: 'application/vnd.github+json' } });
  if (!r.ok) throw new Error(r.status);
  return r.json();
}

async function loadUser() {
  try {
    const u = await ghFetch('/users/' + USERNAME);
    document.getElementById('m-repos').textContent = u.public_repos;
    document.getElementById('m-followers').textContent = u.followers;
  } catch(e) {}
}

async function loadRepos() {
  try {
    const repos = await ghFetch('/users/' + USERNAME + '/repos?per_page=100&sort=pushed');
    let stars = 0;
    repos.forEach(r => { stars += r.stargazers_count; });
    document.getElementById('m-stars').textContent = stars;

    const top = repos.slice(0, 6);
    document.getElementById('repos-wrap').innerHTML = top.map(r => `
      <div class="repo-row">
        <div>
          <a class="repo-name" href="${r.html_url}" target="_blank" rel="noopener">${r.name}</a>
          <div class="repo-desc">${r.description || 'No description'}</div>
        </div>
        <div class="repo-meta">
          <span><i class="ti ti-star" style="font-size:12px;vertical-align:-1px;" aria-hidden="true"></i> ${r.stargazers_count}</span>
          <span><i class="ti ti-git-fork" style="font-size:12px;vertical-align:-1px;" aria-hidden="true"></i> ${r.forks_count}</span>
          <span style="font-size:11px;">${r.language || '—'}</span>
        </div>
      </div>`).join('');

    const langMap = {};
    repos.forEach(r => { if (r.language) langMap[r.language] = (langMap[r.language] || 0) + 1; });
    const sorted = Object.entries(langMap).sort((a,b) => b[1]-a[1]).slice(0, 6);
    const total = sorted.reduce((s,[,v]) => s+v, 0);
    const colors = ['#7c3aed','#6d28d9','#5b21b6','#4c1d95','#8b5cf6','#a78bfa'];
    document.getElementById('langs-wrap').innerHTML = sorted.map(([lang, cnt], i) => {
      const pct = Math.round((cnt / total) * 100);
      return `<div class="lang-row">
        <span class="lang-name">${lang}</span>
        <div class="lang-bar-bg"><div class="lang-bar-fill" style="width:${pct}%;background:${colors[i]||'#7c3aed'};"></div></div>
        <span class="lang-pct">${pct}%</span>
      </div>`;
    }).join('');

    return repos;
  } catch(e) {
    document.getElementById('repos-wrap').innerHTML = '<p class="error-msg">Could not load repos. GitHub rate limit may apply.</p>';
    return [];
  }
}

async function loadCommits(repos) {
  try {
    const picks = repos.slice(0, 8);
    let total = 0;
    const dow = [0,0,0,0,0,0,0];
    await Promise.all(picks.map(async repo => {
      try {
        const commits = await ghFetch('/repos/' + USERNAME + '/' + repo.name + '/commits?per_page=100&author=' + USERNAME);
        total += commits.length;
        commits.forEach(c => { dow[new Date(c.commit.author.date).getDay()]++; });
      } catch(e) {}
    }));
    document.getElementById('m-commits').textContent = total + (repos.length > 8 ? '+' : '');
    renderDOW(dow);
  } catch(e) {
    document.getElementById('m-commits').textContent = '—';
  }
}

function renderDOW(counts) {
  if (dowChartInstance) dowChartInstance.destroy();
  const max = Math.max(...counts);
  const ctx = document.getElementById('dowChart').getContext('2d');
  dowChartInstance = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'],
      datasets: [{
        data: counts,
        backgroundColor: counts.map(v => v === max ? '#7c3aed' : '#c4b5fd'),
        borderRadius: 4
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false } },
      scales: {
        x: { grid: { display: false }, ticks: { font: { size: 11 }, color: '#888' } },
        y: {
          grid: { color: 'rgba(130,130,130,0.15)' },
          ticks: { font: { size: 11 }, color: '#888', stepSize: 1 },
          beginAtZero: true
        }
      }
    }
  });
}

async function loadHeatmap() {
  const wrap = document.getElementById('heatmap-wrap');
  try {
    const res = await fetch(`https://github-contributions-api.jogruber.de/v4/${USERNAME}?y=last`);
    if (!res.ok) throw new Error();
    const data = await res.json();
    const contribs = data.contributions || [];
    const weeks = [];
    let week = [];
    contribs.forEach(day => {
      week.push(day);
      if (week.length === 7) { weeks.push(week); week = []; }
    });
    if (week.length) weeks.push(week);

    const maxVal = Math.max(...contribs.map(d => d.count || 0), 1);
    const colors = ['var(--bg-secondary)', '#c4b5fd', '#8b5cf6', '#6d28d9', '#4c1d95'];

    wrap.innerHTML = `<div class="heatmap-grid">${weeks.map(wk =>
      `<div class="heatmap-week">${wk.map(d => {
        const v = d.count || 0;
        const ci = v === 0 ? 0 : Math.min(4, Math.ceil((v / maxVal) * 4));
        return `<div class="heatmap-cell" style="background:${colors[ci]};" title="${d.date}: ${v} contribution${v !== 1 ? 's' : ''}"></div>`;
      }).join('')}</div>`
    ).join('')}</div>`;
  } catch(e) {
    wrap.innerHTML = `<p class="error-msg">
      Contribution data unavailable (CORS or rate limit).<br>
      <a href="https://github.com/${USERNAME}" target="_blank" rel="noopener" style="color:#534AB7;">View on GitHub →</a>
    </p>`;
  }
}

async function loadAll() {
  document.getElementById('heatmap-wrap').innerHTML = '<div class="loading"><span class="spinner"></span> Loading contributions…</div>';
  document.getElementById('repos-wrap').innerHTML = '<div class="loading"><span class="spinner"></span></div>';
  document.getElementById('langs-wrap').innerHTML = '<div class="loading"><span class="spinner"></span></div>';
  document.getElementById('m-commits').textContent = '—';
  document.getElementById('m-stars').textContent = '—';

  await Promise.all([loadUser(), loadHeatmap()]);
  const repos = await loadRepos();
  await loadCommits(repos);
}

loadAll();
</script>
</body>
</html>
