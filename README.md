<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Calculadora Mundial 2026</title>
  <style>
    body {$1padding: 20px;
      overflow-x: hidden;
    }

    h1 {
      text-align: center;
      margin-bottom: 10px;
    }

    .sub {
      text-align: center;
      color: #cbd5e1;
      margin-bottom: 30px;
    }

    .groups {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(420px, 1fr));
      gap: 20px;
    }

    .group {
      background: #1e293b;
      border-radius: 14px;
      padding: 15px;
      box-shadow: 0 0 10px rgba(0,0,0,0.4);
    }

    .group h2 {
      text-align: center;
      margin-top: 0;
      color: #38bdf8;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 20px;
      font-size: 14px;
    }

    th, td {
      border: 1px solid #334155;
      padding: 6px;
      text-align: center;
    }

    th {
      background: #0f172a;
    }

    .matches {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .match {
      background: #0f172a;
      border-radius: 10px;
      padding: 10px;
    }

    .teams {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
      flex-wrap: wrap;
    }

    input {
      width: 50px;
      padding: 5px;
      border-radius: 5px;
      border: none;
      text-align: center;
      font-size: 16px;
    }

    .btn {
      display: block;
      margin: 30px auto;
      padding: 12px 24px;
      background: #38bdf8;
      color: #0f172a;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
      font-size: 16px;
    }

    .btn:hover {
      background: #7dd3fc;
    }

    .qualified {
      background: rgba(34,197,94,0.25);
      font-weight: bold;
    }

    @media print {
      body {
        background: white;
        color: black;
      }

      .group {
        background: #f1f5f9;
        color: black;
      }

      th {
        background: #cbd5e1;
      }

      .match {
        background: #e2e8f0;
      }

      .btn {
        display: none;
      }
    }
  

    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image: url('mundial 2026.jpg');
      background-size: cover;
      background-position: center;
      filter: brightness(0.25);
      z-index: -1;
    }

    .today-box {
      background: rgba(15,23,42,0.92);
      border: 2px solid #38bdf8;
      border-radius: 16px;
      padding: 20px;
      margin-bottom: 30px;
      box-shadow: 0 0 15px rgba(0,0,0,0.4);
    }

    .today-box h2 {
      margin-top: 0;
      text-align: center;
      color: #38bdf8;
    }

    .today-match {
      background: rgba(30,41,59,0.9);
      padding: 12px;
      border-radius: 10px;
      margin-top: 10px;
    }

    .admin-box {
      text-align: center;
      margin-bottom: 20px;
    }

    .admin-box input {
      width: 180px;
    }

    .lock-msg {
      text-align: center;
      color: #93c5fd;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>

  <h1>⚽ Calculadora Mundial 2026 - Fase de Grupos</h1>
  <p class="sub">Ingresá resultados y las posiciones se actualizarán automáticamente.</p>

  <div class="admin-box">
    <input type="password" id="adminPassword" placeholder="Contraseña administrador" />
    <button class="btn" onclick="unlockAdmin()">🔓 Activar edición</button>
  </div>

  <p class="lock-msg">Solo el administrador puede editar resultados. Los visitantes solo pueden ver la tabla actualizada.</p>

  <div class="today-box">
    <h2>📅 Partidos del día</h2>
    <div id="todayMatches"></div>
  </div>

  <button class="btn" onclick="window.print()">📄 Guardar / Imprimir PDF</button>

  <div class="groups" id="groups"></div>

<script>
const ADMIN_PASSWORD = 'mundial2026';
let adminMode = false;

const schedule = [
  { date: '2026-06-11', time: '16:00', match: 'México vs Sudáfrica', stadium: 'Estadio Azteca' },
  { date: '2026-06-11', time: '19:00', match: 'Brasil vs Marruecos', stadium: 'MetLife Stadium' },
  { date: '2026-06-12', time: '15:00', match: 'Argentina vs Argelia', stadium: 'Monumental' },
  { date: '2026-06-12', time: '21:00', match: 'España vs Uruguay', stadium: 'SoFi Stadium' },
  { date: '2026-06-13', time: '18:00', match: 'Francia vs Senegal', stadium: 'AT&T Stadium' }
];

const groupsData = {
  "Grupo A": ["México", "Sudáfrica", "Corea del Sur", "República Checa"],
  "Grupo B": ["Canadá", "Bosnia", "Catar", "Suiza"],
  "Grupo C": ["Brasil", "Marruecos", "Haití", "Escocia"],
  "Grupo D": ["Estados Unidos", "Paraguay", "Australia", "Turquía"],
  "Grupo E": ["Alemania", "Curazao", "Costa de Marfil", "Ecuador"],
  "Grupo F": ["Países Bajos", "Japón", "Suecia", "Túnez"],
  "Grupo G": ["Bélgica", "Egipto", "Irán", "Nueva Zelanda"],
  "Grupo H": ["España", "Cabo Verde", "Arabia Saudita", "Uruguay"],
  "Grupo I": ["Francia", "Senegal", "Irak", "Noruega"],
  "Grupo J": ["Argentina", "Argelia", "Austria", "Jordania"],
  "Grupo K": ["Portugal", "RD Congo", "Uzbekistán", "Colombia"],
  "Grupo L": ["Inglaterra", "Croacia", "Ghana", "Panamá"]
};

function generateMatches(teams) {
  return [
    [teams[0], teams[1]],
    [teams[2], teams[3]],
    [teams[0], teams[2]],
    [teams[1], teams[3]],
    [teams[0], teams[3]],
    [teams[1], teams[2]],
  ];
}

const groupsContainer = document.getElementById('groups');

Object.entries(groupsData).forEach(([groupName, teams]) => {
  const groupDiv = document.createElement('div');
  groupDiv.className = 'group';

  const matches = generateMatches(teams);

  groupDiv.innerHTML = `
    <h2>${groupName}</h2>

    <table>
      <thead>
        <tr>
          <th>Selección</th>
          <th>PTS</th>
          <th>PJ</th>
          <th>PG</th>
          <th>PE</th>
          <th>PP</th>
          <th>GF</th>
          <th>GC</th>
          <th>DG</th>
        </tr>
      </thead>
      <tbody id="table-${groupName.replace(/\s/g, '')}"></tbody>
    </table>

    <div class="matches">
      ${matches.map((m, index) => `
        <div class="match">
          <div class="teams">
            <span>${m[0]}</span>
            <input type="number" min="0" value="" data-group="${groupName}" data-home="${m[0]}" data-away="${m[1]}" data-type="home" />
            <span>vs</span>
            <input type="number" min="0" value="" data-group="${groupName}" data-home="${m[0]}" data-away="${m[1]}" data-type="away" />
            <span>${m[1]}</span>
          </div>
        </div>
      `).join('')}
    </div>
  `;

  groupsContainer.appendChild(groupDiv);

  updateTable(groupName);
});

function updateTable(groupName) {
  const teams = groupsData[groupName];

  const stats = {};

  teams.forEach(team => {
    stats[team] = {
      pts: 0,
      pj: 0,
      pg: 0,
      pe: 0,
      pp: 0,
      gf: 0,
      gc: 0,
      dg: 0
    };
  });

  const inputs = [...document.querySelectorAll(`input[data-group="${groupName}"]`)];

  const processed = new Set();

  inputs.forEach(input => {
    const home = input.dataset.home;
    const away = input.dataset.away;

    const key = home + away;

    if(processed.has(key)) return;

    processed.add(key);

    const homeInput = document.querySelector(`input[data-group="${groupName}"][data-home="${home}"][data-away="${away}"][data-type="home"]`);

    const awayInput = document.querySelector(`input[data-group="${groupName}"][data-home="${home}"][data-away="${away}"][data-type="away"]`);

    const hg = parseInt(homeInput.value);
    const ag = parseInt(awayInput.value);

    if(isNaN(hg) || isNaN(ag)) return;

    stats[home].pj++;
    stats[away].pj++;

    stats[home].gf += hg;
    stats[home].gc += ag;

    stats[away].gf += ag;
    stats[away].gc += hg;

    if(hg > ag) {
      stats[home].pts += 3;
      stats[home].pg++;
      stats[away].pp++;
    }
    else if(hg < ag) {
      stats[away].pts += 3;
      stats[away].pg++;
      stats[home].pp++;
    }
    else {
      stats[home].pts += 1;
      stats[away].pts += 1;
      stats[home].pe++;
      stats[away].pe++;
    }
  });

  teams.forEach(team => {
    stats[team].dg = stats[team].gf - stats[team].gc;
  });

  const sorted = Object.entries(stats).sort((a,b) => {
    if(b[1].pts !== a[1].pts) return b[1].pts - a[1].pts;
    if(b[1].dg !== a[1].dg) return b[1].dg - a[1].dg;
    return b[1].gf - a[1].gf;
  });

  const tbody = document.getElementById(`table-${groupName.replace(/\s/g, '')}`);

  tbody.innerHTML = sorted.map((team, index) => `
    <tr class="${index < 2 ? 'qualified' : ''}">
      <td>${team[0]}</td>
      <td>${team[1].pts}</td>
      <td>${team[1].pj}</td>
      <td>${team[1].pg}</td>
      <td>${team[1].pe}</td>
      <td>${team[1].pp}</td>
      <td>${team[1].gf}</td>
      <td>${team[1].gc}</td>
      <td>${team[1].dg}</td>
    </tr>
  `).join('');
}

function unlockAdmin() {
  const password = document.getElementById('adminPassword').value;

  if(password === ADMIN_PASSWORD) {
    adminMode = true;
    localStorage.setItem('adminMode', 'true');
    enableInputs();
    alert('Modo administrador activado');
  } else {
    alert('Contraseña incorrecta');
  }
}

function enableInputs() {
  const inputs = document.querySelectorAll('input[type="number"]');

  inputs.forEach(input => {
    input.disabled = !adminMode;
  });
}

function loadSavedResults() {
  const saved = JSON.parse(localStorage.getItem('worldCupResults') || '{}');

  document.querySelectorAll('input[type="number"]').forEach(input => {
    const key = `${input.dataset.group}-${input.dataset.home}-${input.dataset.away}-${input.dataset.type}`;

    if(saved[key] !== undefined) {
      input.value = saved[key];
    }
  });

  Object.keys(groupsData).forEach(group => updateTable(group));
}

function saveResults() {
  const saved = {};

  document.querySelectorAll('input[type="number"]').forEach(input => {
    const key = `${input.dataset.group}-${input.dataset.home}-${input.dataset.away}-${input.dataset.type}`;
    saved[key] = input.value;
  });

  localStorage.setItem('worldCupResults', JSON.stringify(saved));
}

function showTodayMatches() {
  const todayContainer = document.getElementById('todayMatches');

  const today = new Date().toISOString().split('T')[0];

  const todayGames = schedule.filter(game => game.date === today);

  if(todayGames.length === 0) {
    todayContainer.innerHTML = '<div class="today-match">No hay partidos programados para hoy.</div>';
    return;
  }

  todayContainer.innerHTML = todayGames.map(game => `
    <div class="today-match">
      <strong>${game.match}</strong><br>
      🕒 ${game.time}<br>
      🏟️ ${game.stadium}
    </div>
  `).join('');
}

window.addEventListener('input', (e) => {
  if(e.target.tagName === 'INPUT' && adminMode) {
    updateTable(e.target.dataset.group);
    saveResults();
  }
});

window.onload = () => {
  if(localStorage.getItem('adminMode') === 'true') {
    adminMode = true;
  }

  enableInputs();
  loadSavedResults();
  showTodayMatches();
};
</script>

</body>
</html>
