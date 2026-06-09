# Brief — Patterns à répliquer

## 1. Infrastructure HTML

### 1.1 Reset CSS
```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
```

### 1.2 Body
```css
body {
  font-family: var(--font-body);
  min-height: 100vh;
  color: var(--text);
  transition: background var(--transition), color var(--transition);
}
```
Le fond peut être une couleur unie `var(--bg)` ou un dégradé `linear-gradient(135deg, var(--primary), var(--secondary), var(--accent))`.

### 1.3 Google Fonts
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=…&family=DM+Sans:…&display=swap" rel="stylesheet">
```
```css
:root {
  --font-display: '...', serif;
  --font-body: 'DM Sans', sans-serif;
  --transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}
```
Toujours 2 polices : une serif d'affichage + DM Sans pour le corps.

### 1.4 Favicon par emoji
```html
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🚗</text></svg>">
```
Changer l'emoji selon le projet.

---

## 2. Header — Titre + contrôles à droite

```html
<header>
  <div class="header-left">
    <h1>…</h1>
    <p class="subtitle">…</p>
  </div>
  <div class="header-right">
    <!-- contrôles : theme toggle, etc. -->
  </div>
</header>
```
```css
header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  animation: fadeInDown 0.8s ease-out;
}
.header-left { text-align: left; }
.header-right {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  flex-shrink: 0;
  padding-top: 0.25rem;
}
```

---

## 3. Système de couleurs (3 thèmes)

### Principe
Un fichier HTML unique embarque 3 jeux de variables CSS. Le thème est choisi par attribut sur `<html>`.

### Les 3 thèmes
| Thème | Déclencheur | Palette |
|---|---|---|
| **Chrome** | `data-force-chrome` (forcé si navigateur = Chrome) | Foncé bordeaux/brun, accent rouge vif |
| **Light** | `data-theme="light"` (défaut non-Chrome) | Selon projet (clair froid/teal, ou beige/rouge) |
| **Dark** | `data-theme="dark"` | Foncé froid (GitHub Dark), accent bleu |

### Noms de variables (adaptables)
Les noms de variables peuvent varier selon le projet. Voici les équivalences :

| Rôle | Variante A (labolycee) | Variante B (index) |
|---|---|---|
| Texte principal | `--text` | `--text` |
| Texte atténué | `--text-muted` | `--text-muted` |
| Couleur d'accent | `--highlight` | `--accent` |
| Accent foncé | `--highlight-dim` | `--accent-dim` |
| Accent clair | `--highlight-light` | — |
| Fond page | `--primary` (gradient) | `--bg` (uni) |
| Fond carte | `--card-bg` | `--bg-card` |
| Bordure carte | `--card-border` | `--border` |
| Fond survol | `--glass-bg-hover` | `--bg-hover` |
| Fond input | `--input-bg` | — |
| Ombre | `--shadow` | `--shadow` |
| Lueur | `--glow` | `--glow` |

### Structure CSS
```css
:root { /* Polices + transition globale */ }

/* CHROME — forcé, pas de toggle */
html[data-force-chrome] {
  --text: #f8f9fa;
  --highlight: #e94560;
  --card-bg: rgba(255,255,255,0.05);
  /* … */
}

/* LIGHT */
html[data-theme="light"]:not([data-force-chrome]) {
  --text: #0f172a;
  --highlight: #0d9488;
  --card-bg: rgba(255,255,255,0.95);
  /* … */
}

/* DARK */
html[data-theme="dark"]:not([data-force-chrome]) {
  --text: #e2e8f0;
  --highlight: #4facde;
  --card-bg: rgba(255,255,255,0.03);
  /* … */
}
```

### Variables clés à prévoir dans chaque thème
- `--text` / `--text-muted`
- `--highlight` / `--highlight-dim` / `--highlight-light` (accent)
- `--card-bg` / `--card-border` (fond carte)
- `--shadow` / `--glow`
- `--bg-hover` (survol des éléments interactifs)
- `--border` (bordures génériques)

### Toggle UI (non-Chrome seulement)
```html
<label class="theme-toggle" id="themeToggle">
  <span class="theme-toggle-label" id="themeLabel">Nuit</span>
  <div class="toggle-track" id="toggleTrack">
    <div class="toggle-thumb"></div>
  </div>
</label>
```
```css
.theme-toggle {
  display: inline-flex; align-items: center; gap: 0.5rem;
  cursor: pointer; user-select: none;
}
.theme-toggle-label { font-size: 0.75rem; color: var(--text-muted); font-weight: 500; }
.toggle-track {
  width: 42px; height: 22px;
  background: var(--bg-hover);
  border-radius: 99px; position: relative;
  transition: background var(--transition);
}
.toggle-track.active { background: var(--highlight); }
.toggle-thumb {
  width: 16px; height: 16px;
  background: var(--text); border-radius: 50%;
  position: absolute; top: 2px; left: 3px;
  transition: transform var(--transition);
  box-shadow: 0 1px 4px rgba(0,0,0,0.3);
}
.toggle-track.active .toggle-thumb { transform: translateX(20px); }
html[data-force-chrome] .theme-toggle { display: none; }
```

### JavaScript — Détection + Toggle
```js
const isChrome = /Chrome/.test(navigator.userAgent) && /Google Inc/.test(navigator.vendor);
if (isChrome) {
  document.documentElement.setAttribute('data-force-chrome', '');
} else {
  const saved = localStorage.getItem('project-theme') || 'light';
  document.documentElement.setAttribute('data-theme', saved);
  updateToggleUI(saved);
}

function updateToggleUI(theme) {
  const track = document.getElementById('toggleTrack');
  const label = document.getElementById('themeLabel');
  if (!track || !label) return;
  if (theme === 'dark') {
    track.classList.add('active');
    label.textContent = 'Jour';
  } else {
    track.classList.remove('active');
    label.textContent = 'Nuit';
  }
}

document.getElementById('themeToggle').addEventListener('click', () => {
  if (isChrome) return;
  const current = document.documentElement.getAttribute('data-theme');
  const next = current === 'dark' ? 'light' : 'dark';
  document.documentElement.setAttribute('data-theme', next);
  localStorage.setItem('project-theme', next);
  updateToggleUI(next);
});
```

---

## 4. Animations

```css
@keyframes fadeInDown {
  from { opacity: 0; transform: translateY(-30px); }
  to   { opacity: 1; transform: translateY(0); }
}
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to   { opacity: 1; transform: translateY(0); }
}
@keyframes fadeSlideIn {
  from { opacity: 0; transform: translateY(12px); }
  to   { opacity: 1; transform: translateY(0); }
}
```
Appliquer l'animation sur le header, les sections, les cartes. Utiliser `animation-fill-mode: backwards` pour un décalage staggered : `animation: fadeInUp 0.8s ease-out 0.2s backwards`.

---

## 5. Carte / Effet verre

```css
.card {
  background: var(--card-bg);
  backdrop-filter: blur(10px);
  border: 1px solid var(--card-border);
  border-radius: 16px;
  padding: 25px;
  animation: fadeSlideIn 0.4s ease;
  transition: all 0.3s ease;
}
.card:hover {
  transform: translateY(-5px);
  border-color: var(--highlight);
  box-shadow: var(--shadow), var(--glow);
}
```

---

## 6. Barre de statistiques

```html
<div class="stats">
  <div class="stat-item">
    <span class="stat-value">42</span>
    <span class="stat-label">Éléments</span>
  </div>
</div>
```
```css
.stats {
  display: flex;
  justify-content: center;
  gap: 40px;
  flex-wrap: wrap;
  padding-top: 30px;
  border-top: 1px solid var(--border);
}
.stat-value {
  font-family: var(--font-display);
  font-size: 2.5rem;
  font-weight: 700;
  color: var(--highlight);
  display: block;
}
.stat-label {
  font-size: 0.85rem;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 1px;
}
```

---

## 7. Chargement CSV — Auto avec fallback manuel

### Principe
`fetch()` tente de charger un fichier CSV distant au chargement de la page. Si la requête échoue (fichier manquant, CORS, hors-ligne), un bouton "📁 Charger le fichier CSV" apparaît.

### HTML
```html
<div class="csv-fallback" id="csvFallback" style="display: none;">
  <label for="csvFile" class="upload-btn">📁 Charger le fichier CSV</label>
  <input type="file" id="csvFile" accept=".csv" style="display: none;" />
</div>
```

### CSS
```css
.csv-fallback {
  grid-column: 1 / -1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}
.upload-btn {
  display: inline-block;
  background: var(--highlight);
  color: white;
  padding: 15px 40px;
  border-radius: 12px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 1.1rem;
}
.upload-btn:hover {
  background: var(--highlight-dim);
  transform: translateY(-2px);
  box-shadow: 0 5px 20px var(--highlight-shadow);
}
```

### JavaScript
```js
// Upload manuel
document.getElementById('csvFile').addEventListener('change', function(e) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = (event) => {
    parseCSV(event.target.result);
    document.getElementById('csvFallback').style.display = 'none';
    onDataReady();
  };
  reader.readAsText(file);
});

// Tentative auto
fetch('data.csv')
  .then(response => {
    if (!response.ok) throw new Error();
    return response.text();
  })
  .then(csvData => {
    parseCSV(csvData);
    onDataReady();
  })
  .catch(() => {
    document.getElementById('csvFallback').style.display = 'flex';
  });
```

### Parse CSV — Deux variantes

**Variante A** (séparateur `;`, simple) :
```js
function parseCSV(text) {
  const lines = text.split('\n');
  const headers = lines[0].split(';');
  const data = [];
  for (let i = 1; i < lines.length; i++) {
    if (!lines[i].trim()) continue;
    const values = lines[i].split(';');
    data.push({
      field1: values[0],
      field2: values[1],
      // …
    });
  }
  return data;
}
```

**Variante B** (séparateur `,`, avec guillemets) :
```js
function parseCSV(text) {
  const lines = text.split('\n').filter(l => l.trim());
  const rows = [];
  for (let i = 1; i < lines.length; i++) {
    const cols = [];
    let inQuote = false, cur = '';
    for (let j = 0; j < lines[i].length; j++) {
      const c = lines[i][j];
      if (c === '"') {
        if (inQuote && lines[i][j+1] === '"') { cur += '"'; j++; }
        else inQuote = !inQuote;
      } else if (c === ',' && !inQuote) {
        cols.push(cur); cur = '';
      } else cur += c;
    }
    cols.push(cur);
    if (cols.length >= 4) {
      rows.push({ col1: cols[0], col2: cols[1] /* … */ });
    }
  }
  return rows;
}
```
La même fonction `parseCSV()` est utilisée pour l'auto-chargement ET l'upload manuel.

---

## 8. Loading spinner

```css
.loading {
  text-align: center;
  padding: 4rem 2rem;
  color: var(--text-muted);
}
.loading-spinner {
  width: 36px; height: 36px;
  border: 3px solid var(--border);
  border-top-color: var(--highlight);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin: 0 auto 1rem;
}
@keyframes spin { to { transform: rotate(360deg); } }
```
```html
<div class="loading">
  <div class="loading-spinner"></div>
  <p>Chargement…</p>
</div>
```

---

## 9. Boutons — Hover lift

```css
.btn {
  padding: 0.75rem 1.5rem;
  background: var(--highlight);
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}
.btn:hover {
  background: var(--highlight-dim);
  transform: translateY(-2px);
  box-shadow: 0 5px 20px var(--highlight-shadow);
}
.btn:active { transform: translateY(0); }
.btn:disabled {
  opacity: 0.3;
  cursor: not-allowed;
  pointer-events: none;
}
```

---

## 10. Responsive — Breakpoint mobile

```css
@media (max-width: 560px) {
  h1 { font-size: 2.5rem; }
  .filters-grid,
  .results-grid { grid-template-columns: 1fr; }
  .stats { gap: 20px; }
  /* Réduire les paddings de 25-50% sur tous les conteneurs */
}
```

---

## 11. Emoji comme icônes
Utiliser directement des emojis dans le HTML pour les icônes, pas de bibliothèque externe :
```html
<button>🔍 Rechercher</button>
<span>⏱️ 30min</span>
<label>📁 Charger un fichier</label>
```

---

## 12. Règles générales
- **Pas de commentaires** dans le code produit (sauf si demandé)
- **Tout dans un seul `.html`** — pas de dépendances externes autres que Google Fonts
- **Vanilla JS** uniquement, aucun framework
- **Emojis comme icônes** plutôt que SVG ou font-icons
- **Classes partagées** entre projets : `.csv-fallback`, `.upload-btn`, `.theme-toggle`, `.toggle-track`, `.toggle-thumb`, `.header-left`, `.filter-label`
- Répondre en français
