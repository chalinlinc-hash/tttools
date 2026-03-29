<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>截圖筆記 → Notion</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700&family=Space+Mono:wght@400;700&display=swap');

  :root {
    --bg: #0f0f11;
    --surface: #18181c;
    --surface2: #22222a;
    --border: #2e2e3a;
    --accent: #7c6af7;
    --accent2: #b8f0a0;
    --text: #eeeef5;
    --muted: #7a7a92;
    --danger: #f06080;
    --success: #6deba0;
    --orange: #f59e0b;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans TC', sans-serif;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 32px 16px 64px;
  }

  header { text-align: center; margin-bottom: 36px; }

  .logo {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    letter-spacing: 4px;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 10px;
  }

  h1 {
    font-size: 26px;
    font-weight: 700;
    background: linear-gradient(135deg, #fff 0%, var(--accent) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .subtitle { color: var(--muted); font-size: 13px; margin-top: 8px; }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
    width: 100%;
    max-width: 620px;
    margin-bottom: 16px;
  }

  .card-title {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 16px;
  }

  .mode-switcher {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
    margin-bottom: 20px;
  }

  .mode-btn {
    padding: 14px 10px;
    border-radius: 12px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-family: 'Noto Sans TC', sans-serif;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 5px;
  }

  .mode-btn .mode-icon { font-size: 24px; }
  .mode-btn .mode-desc { font-size: 11px; color: var(--muted); font-weight: 400; }

  .mode-btn.active {
    border-color: var(--accent);
    background: rgba(124,106,247,0.1);
    color: var(--text);
  }

  .mode-btn.active .mode-desc { color: rgba(124,106,247,0.8); }

  .drop-zone {
    border: 2px dashed var(--border);
    border-radius: 12px;
    padding: 48px 24px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
  }

  .drop-zone:hover, .drop-zone.drag-over {
    border-color: var(--accent);
    background: rgba(124,106,247,0.06);
  }

  .drop-zone input[type="file"] {
    position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%;
  }

  .drop-icon { font-size: 40px; margin-bottom: 12px; display: block; }
  .drop-text { color: var(--text); font-size: 15px; font-weight: 500; margin-bottom: 6px; }
  .drop-sub { color: var(--muted); font-size: 12px; }

  .preview-container { position: relative; border-radius: 10px; overflow: hidden; background: var(--surface2); }
  .preview-container img { width: 100%; max-height: 280px; object-fit: contain; display: block; }
  .preview-remove {
    position: absolute; top: 8px; right: 8px;
    background: rgba(0,0,0,0.7); color: #fff; border: none;
    border-radius: 50%; width: 28px; height: 28px; cursor: pointer;
    font-size: 14px; display: flex; align-items: center; justify-content: center;
  }

  .url-input-wrap { display: flex; gap: 8px; }

  .url-input {
    flex: 1;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 12px 16px;
    color: var(--text);
    font-size: 14px;
    font-family: 'Noto Sans TC', sans-serif;
    outline: none;
    transition: border-color 0.2s;
  }

  .url-input::placeholder { color: var(--muted); }
  .url-input:focus { border-color: var(--accent); }

  .article-preview {
    background: var(--surface2);
    border-radius: 10px;
    padding: 12px 16px;
    display: none;
    border-left: 3px solid var(--orange);
    margin-top: 10px;
  }

  .article-preview.show { display: block; }
  .article-url { font-size: 11px; color: var(--muted); word-break: break-all; margin-top: 4px; }
  .article-domain { font-size: 13px; color: var(--orange); font-weight: 600; }

  .source-pills { display: flex; gap: 8px; flex-wrap: wrap; }

  .pill {
    padding: 6px 14px; border-radius: 100px; border: 1px solid var(--border);
    font-size: 12px; cursor: pointer; transition: all 0.15s;
    background: transparent; color: var(--muted);
  }
  .pill:hover { border-color: var(--accent); color: var(--text); }
  .pill.active { background: var(--accent); border-color: var(--accent); color: #fff; font-weight: 500; }

  .tag-grid { display: flex; flex-wrap: wrap; gap: 6px; }

  .tag {
    padding: 4px 12px; border-radius: 100px; font-size: 11px; cursor: pointer;
    border: 1px solid var(--border); background: transparent; color: var(--muted); transition: all 0.15s;
  }
  .tag:hover { opacity: 0.8; }
  .tag.active { color: #fff; font-weight: 500; }
  .tag[data-tag="行銷"].active { background: #e8568a; border-color: #e8568a; }
  .tag[data-tag="設計"].active { background: #8b5cf6; border-color: #8b5cf6; }
  .tag[data-tag="科技"].active { background: #3b82f6; border-color: #3b82f6; }
  .tag[data-tag="商業"].active { background: #f97316; border-color: #f97316; }
  .tag[data-tag="生活"].active { background: #22c55e; border-color: #22c55e; }
  .tag[data-tag="學習"].active { background: #eab308; border-color: #eab308; }
  .tag[data-tag="其他"].active { background: #6b7280; border-color: #6b7280; }

  .field { margin-bottom: 16px; }

  .field label {
    display: block; font-size: 11px; letter-spacing: 2px; text-transform: uppercase;
    color: var(--muted); margin-bottom: 8px; font-family: 'Space Mono', monospace;
  }

  .field input, .field textarea {
    width: 100%; background: var(--surface2); border: 1px solid var(--border);
    border-radius: 8px; padding: 10px 14px; color: var(--text); font-size: 14px;
    font-family: 'Noto Sans TC', sans-serif; outline: none; transition: border-color 0.2s; resize: vertical;
  }
  .field input:focus, .field textarea:focus { border-color: var(--accent); }
  .field textarea { min-height: 80px; }

  .btn-analyze {
    width: 100%; padding: 14px;
    background: linear-gradient(135deg, var(--accent), #a78bfa);
    border: none; border-radius: 10px; color: #fff; font-size: 15px; font-weight: 700;
    cursor: pointer; transition: all 0.2s; font-family: 'Noto Sans TC', sans-serif;
    display: flex; align-items: center; justify-content: center; gap: 8px;
  }
  .btn-analyze:hover { opacity: 0.9; transform: translateY(-1px); }
  .btn-analyze:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

  .btn-save {
    width: 100%; padding: 14px;
    background: transparent; border: 1px solid var(--accent2);
    border-radius: 10px; color: var(--accent2); font-size: 15px; font-weight: 700;
    cursor: pointer; transition: all 0.2s; font-family: 'Noto Sans TC', sans-serif;
    display: flex; align-items: center; justify-content: center; gap: 8px; margin-top: 12px;
  }
  .btn-save:hover { background: rgba(184,240,160,0.08); transform: translateY(-1px); }
  .btn-save:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

  .status {
    display: none; padding: 12px 16px; border-radius: 10px;
    font-size: 13px; margin-top: 12px; align-items: center; gap: 10px;
  }
  .status.show { display: flex; }
  .status.loading { background: rgba(124,106,247,0.15); color: #a78bfa; border: 1px solid rgba(124,106,247,0.3); }
  .status.success { background: rgba(109,235,160,0.12); color: var(--success); border: 1px solid rgba(109,235,160,0.3); }
  .status.error { background: rgba(240,96,128,0.12); color: var(--danger); border: 1px solid rgba(240,96,128,0.3); }

  .spinner {
    width: 16px; height: 16px;
    border: 2px solid rgba(167,139,250,0.3); border-top-color: #a78bfa;
    border-radius: 50%; animation: spin 0.8s linear infinite; flex-shrink: 0;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  .divider { height: 1px; background: var(--border); margin: 4px 0 20px; }

  .history-item {
    padding: 12px; border: 1px solid var(--border); border-radius: 10px;
    margin-bottom: 8px; font-size: 13px; display: flex; gap: 10px; align-items: flex-start;
  }
  .history-icon { font-size: 20px; flex-shrink: 0; }
  .history-title { font-weight: 500; margin-bottom: 4px; }
  .history-meta { color: var(--muted); font-size: 11px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
  .history-tags { display: flex; gap: 4px; margin-top: 6px; flex-wrap: wrap; }
  .history-tag { padding: 2px 8px; border-radius: 100px; font-size: 10px; background: var(--surface2); color: var(--muted); }
  .empty-history { text-align: center; color: var(--muted); font-size: 13px; padding: 24px; }

  .hidden { display: none !important; }
</style>
</head>
<body>

<header>
  <div class="logo">✦ Note Clipper</div>
  <h1>截圖 & 連結 → Notion</h1>
  <p class="subtitle">上傳截圖或貼上文章連結，AI 自動萃取重點存入 Notion</p>
</header>

<!-- Step 1: Mode -->
<div class="card">
  <div class="card-title">① 選擇模式</div>
  <div class="mode-switcher">
    <button class="mode-btn active" id="modeScreenshot" onclick="switchMode('screenshot')">
      <span class="mode-icon">📸</span>
      <span style="font-size:13px;font-weight:600">截圖模式</span>
      <span class="mode-desc">上傳 Threads 或任意截圖</span>
    </button>
    <button class="mode-btn" id="modeUrl" onclick="switchMode('url')">
      <span class="mode-icon">🔗</span>
      <span style="font-size:13px;font-weight:600">連結模式</span>
      <span class="mode-desc">貼上文章連結自動摘要</span>
    </button>
  </div>

  <!-- Screenshot -->
  <div id="screenshotSection">
    <div class="drop-zone" id="dropZone">
      <input type="file" id="fileInput" accept="image/*">
      <span class="drop-icon">📸</span>
      <div class="drop-text">點擊或拖曳截圖至此</div>
      <div class="drop-sub">支援 JPG、PNG、WEBP</div>
    </div>
    <div id="previewContainer" style="display:none; margin-top: 14px;">
      <div class="preview-container">
        <img id="preview" src="" alt="">
        <button class="preview-remove" onclick="removeImage()">✕</button>
      </div>
    </div>
  </div>

  <!-- URL -->
  <div id="urlSection" class="hidden">
    <div class="url-input-wrap">
      <input type="url" class="url-input" id="urlInput" placeholder="https://..." oninput="onUrlInput()">
    </div>
    <div class="article-preview" id="articlePreview">
      <div class="article-domain" id="articleDomain"></div>
      <div class="article-url" id="articleUrl"></div>
    </div>
  </div>
</div>

<!-- Step 2: Source & Tags -->
<div class="card">
  <div class="card-title">② 來源 & 分類</div>
  <div style="margin-bottom: 16px;">
    <div style="font-size:12px;color:var(--muted);margin-bottom:8px">來源</div>
    <div class="source-pills" id="sourcePills">
      <button class="pill active" data-source="Threads" onclick="selectSource(this)">💬 Threads</button>
      <button class="pill" data-source="手機截圖" onclick="selectSource(this)">📱 手機截圖</button>
      <button class="pill" data-source="網頁截圖" onclick="selectSource(this)">🌐 網頁截圖</button>
      <button class="pill" data-source="其他" onclick="selectSource(this)">📄 其他</button>
    </div>
  </div>
  <div>
    <div style="font-size:12px;color:var(--muted);margin-bottom:8px">分類標籤（可多選）</div>
    <div class="tag-grid">
      <button class="tag" data-tag="行銷" onclick="toggleTag(this)">行銷</button>
      <button class="tag" data-tag="設計" onclick="toggleTag(this)">設計</button>
      <button class="tag" data-tag="科技" onclick="toggleTag(this)">科技</button>
      <button class="tag" data-tag="商業" onclick="toggleTag(this)">商業</button>
      <button class="tag" data-tag="生活" onclick="toggleTag(this)">生活</button>
      <button class="tag" data-tag="學習" onclick="toggleTag(this)">學習</button>
      <button class="tag" data-tag="其他" onclick="toggleTag(this)">其他</button>
    </div>
  </div>
</div>

<!-- Step 3: Analyze & Save -->
<div class="card">
  <div class="card-title">③ AI 分析 & 儲存</div>
  <button class="btn-analyze" id="analyzeBtn" onclick="analyze()" disabled>
    <span id="analyzeBtnIcon">🔍</span>
    <span id="analyzeBtnText">分析截圖內容</span>
  </button>
  <div class="status" id="analyzeStatus"></div>

  <div id="editSection" style="display:none; margin-top: 20px;">
    <div class="divider"></div>
    <div class="field">
      <label>筆記標題</label>
      <input type="text" id="noteTitle" placeholder="輸入標題...">
    </div>
    <div class="field">
      <label>筆記摘要</label>
      <textarea id="noteSummary" placeholder="AI 生成的摘要，可自行修改..."></textarea>
    </div>
    <div class="field">
      <label>原文重點</label>
      <textarea id="notePoints" placeholder="重點條列..."></textarea>
    </div>
    <button class="btn-save" id="saveBtn" onclick="saveToNotion()">
      <span>✦</span> 儲存到 Notion
    </button>
    <div class="status" id="saveStatus"></div>
  </div>
</div>

<!-- History -->
<div class="card">
  <div class="card-title">✦ 今日已儲存</div>
  <div id="historyList"><div class="empty-history">尚未儲存任何筆記</div></div>
</div>

<script>
let currentMode = 'screenshot';
let imageBase64 = null;
let currentUrl = '';
let selectedSource = 'Threads';
let selectedTags = [];
let savedItems = [];

function switchMode(mode) {
  currentMode = mode;
  document.getElementById('modeScreenshot').classList.toggle('active', mode === 'screenshot');
  document.getElementById('modeUrl').classList.toggle('active', mode === 'url');
  document.getElementById('screenshotSection').classList.toggle('hidden', mode !== 'screenshot');
  document.getElementById('urlSection').classList.toggle('hidden', mode !== 'url');

  const pills = document.getElementById('sourcePills');
  if (mode === 'url') {
    pills.innerHTML = `
      <button class="pill active" data-source="個人部落格" onclick="selectSource(this)">✍️ 部落格</button>
      <button class="pill" data-source="新聞媒體" onclick="selectSource(this)">📰 新聞媒體</button>
      <button class="pill" data-source="技術文章" onclick="selectSource(this)">💻 技術文章</button>
      <button class="pill" data-source="其他" onclick="selectSource(this)">📄 其他</button>
    `;
    selectedSource = '個人部落格';
    document.getElementById('analyzeBtnIcon').textContent = '🔗';
    document.getElementById('analyzeBtnText').textContent = '讀取並分析文章';
  } else {
    pills.innerHTML = `
      <button class="pill active" data-source="Threads" onclick="selectSource(this)">💬 Threads</button>
      <button class="pill" data-source="手機截圖" onclick="selectSource(this)">📱 手機截圖</button>
      <button class="pill" data-source="網頁截圖" onclick="selectSource(this)">🌐 網頁截圖</button>
      <button class="pill" data-source="其他" onclick="selectSource(this)">📄 其他</button>
    `;
    selectedSource = 'Threads';
    document.getElementById('analyzeBtnIcon').textContent = '🔍';
    document.getElementById('analyzeBtnText').textContent = '分析截圖內容';
  }
  resetEdit();
  checkReady();
}

function checkReady() {
  document.getElementById('analyzeBtn').disabled =
    currentMode === 'screenshot' ? !imageBase64 : !currentUrl;
}

// Screenshot
const dropZone = document.getElementById('dropZone');
const fileInput = document.getElementById('fileInput');

dropZone.addEventListener('dragover', e => { e.preventDefault(); dropZone.classList.add('drag-over'); });
dropZone.addEventListener('dragleave', () => dropZone.classList.remove('drag-over'));
dropZone.addEventListener('drop', e => {
  e.preventDefault(); dropZone.classList.remove('drag-over');
  if (e.dataTransfer.files[0]) handleFile(e.dataTransfer.files[0]);
});
fileInput.addEventListener('change', e => { if (e.target.files[0]) handleFile(e.target.files[0]); });

function handleFile(file) {
  const reader = new FileReader();
  reader.onload = e => {
    imageBase64 = e.target.result.split(',')[1];
    document.getElementById('preview').src = e.target.result;
    document.getElementById('previewContainer').style.display = 'block';
    document.getElementById('dropZone').style.display = 'none';
    checkReady(); resetEdit();
  };
  reader.readAsDataURL(file);
}

function removeImage() {
  imageBase64 = null;
  document.getElementById('previewContainer').style.display = 'none';
  document.getElementById('dropZone').style.display = 'block';
  fileInput.value = '';
  checkReady(); resetEdit();
}

// URL
function onUrlInput() {
  const val = document.getElementById('urlInput').value.trim();
  currentUrl = (val.startsWith('http')) ? val : '';
  const preview = document.getElementById('articlePreview');
  if (currentUrl) {
    try {
      const u = new URL(currentUrl);
      document.getElementById('articleDomain').textContent = u.hostname;
      document.getElementById('articleUrl').textContent = currentUrl;
      preview.classList.add('show');
    } catch { preview.classList.remove('show'); }
  } else {
    preview.classList.remove('show');
  }
  checkReady();
}

// Source & Tags
function selectSource(btn) {
  document.querySelectorAll('.pill').forEach(p => p.classList.remove('active'));
  btn.classList.add('active');
  selectedSource = btn.dataset.source;
}

function toggleTag(btn) {
  btn.classList.toggle('active');
  const tag = btn.dataset.tag;
  selectedTags = selectedTags.includes(tag) ? selectedTags.filter(t => t !== tag) : [...selectedTags, tag];
}

// Status helpers
function setStatus(id, type, msg) {
  const el = document.getElementById(id);
  el.className = 'status';
  if (!type) return;
  el.classList.add('show', type);
  el.innerHTML = type === 'loading'
    ? `<div class="spinner"></div><span>${msg}</span>`
    : `<span>${type === 'success' ? '✓' : '✕'}</span><span>${msg}</span>`;
}

function resetEdit() {
  document.getElementById('editSection').style.display = 'none';
  setStatus('analyzeStatus', '', '');
  setStatus('saveStatus', '', '');
}

function fillEdit(title, summary, points) {
  document.getElementById('noteTitle').value = title || '';
  document.getElementById('noteSummary').value = summary || '';
  document.getElementById('notePoints').value = points || '';
  document.getElementById('editSection').style.display = 'block';
}

// Analyze
async function analyze() {
  document.getElementById('analyzeBtn').disabled = true;
  document.getElementById('editSection').style.display = 'none';

  if (currentMode === 'screenshot') {
    setStatus('analyzeStatus', 'loading', '正在分析截圖內容...');
    try {
      const res = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514', max_tokens: 1000,
          messages: [{ role: 'user', content: [
            { type: 'image', source: { type: 'base64', media_type: 'image/jpeg', data: imageBase64 } },
            { type: 'text', text: '請分析這張截圖，提取有用的筆記內容。只回傳 JSON，不要有其他文字：\n{"title":"簡短有力的標題（15字以內）","summary":"2-3句話的核心摘要","points":"3-5個重點，每點用「• 」開頭，用換行分隔"}\n若無有意義內容，title填「（無法辨識）」。' }
          ]}]
        })
      });
      const data = await res.json();
      const text = data.content.map(i => i.text || '').join('');
      const parsed = JSON.parse(text.replace(/```json|```/g, '').trim());
      fillEdit(parsed.title, parsed.summary, parsed.points);
      setStatus('analyzeStatus', 'success', '分析完成！請確認內容後儲存');
    } catch (e) {
      setStatus('analyzeStatus', 'error', '分析失敗，請重試');
    }
  } else {
    setStatus('analyzeStatus', 'loading', '正在讀取並分析文章...');
    try {
      const res = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514', max_tokens: 1500,
          tools: [{ type: 'web_search_20250305', name: 'web_search' }],
          messages: [{ role: 'user', content:
            `請用 web_search 工具讀取這個文章網址：${currentUrl}\n\n分析後只回傳 JSON，不要其他文字：\n{"title":"文章標題的中文摘要（20字以內）","summary":"3-4句話的核心摘要，說明這篇文章的核心觀點","points":"4-6個重點，每點用「• 」開頭，用換行分隔，包含關鍵數據或論點"}`
          }]
        })
      });
      const data = await res.json();
      const allText = data.content.filter(b => b.type === 'text').map(b => b.text).join('');
      const match = allText.match(/\{[\s\S]*\}/);
      if (!match) throw new Error('no json');
      const parsed = JSON.parse(match[0]);
      fillEdit(parsed.title, parsed.summary, parsed.points);
      setStatus('analyzeStatus', 'success', '文章分析完成！請確認內容後儲存');
    } catch (e) {
      setStatus('analyzeStatus', 'error', '分析失敗，請確認連結是否可公開讀取');
    }
  }

  checkReady();
}

// Save to Notion
async function saveToNotion() {
  const title = document.getElementById('noteTitle').value.trim();
  const summary = document.getElementById('noteSummary').value.trim();
  const points = document.getElementById('notePoints').value.trim();
  if (!title) { setStatus('saveStatus', 'error', '請填寫標題'); return; }

  document.getElementById('saveBtn').disabled = true;
  setStatus('saveStatus', 'loading', '儲存到 Notion 中...');

  const today = new Date().toISOString().split('T')[0];
  const itemType = currentMode === 'url' ? '文章連結' : '截圖';
  const urlField = currentMode === 'url' ? currentUrl : '';

  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514', max_tokens: 1000,
        mcp_servers: [{ type: 'url', url: 'https://mcp.notion.com/mcp', name: 'notion-mcp' }],
        messages: [{ role: 'user', content:
          `在 Notion data_source_id 為 10da3e2f-45ce-4dd0-8bec-28d7ffe93446 的資料庫，使用 notion-create-pages 建立一筆記錄：
標題: ${title}
筆記摘要: ${summary}
原文重點: ${points}
來源: ${selectedSource}
類型: ${itemType}
分類標籤: ${JSON.stringify(selectedTags)}
原始連結: ${urlField}
儲存日期: ${today}
已複習: false`
        }]
      })
    });
    const data = await res.json();
    if (data.error) throw new Error(data.error.message);

    savedItems.unshift({ title, source: selectedSource, tags: [...selectedTags], type: itemType, url: urlField, time: new Date().toLocaleTimeString('zh-TW', { hour: '2-digit', minute: '2-digit' }) });
    renderHistory();
    setStatus('saveStatus', 'success', '✓ 已成功儲存到 Notion！');

    setTimeout(() => {
      if (currentMode === 'screenshot') removeImage();
      else {
        document.getElementById('urlInput').value = '';
        document.getElementById('articlePreview').classList.remove('show');
        currentUrl = '';
      }
      resetEdit();
      checkReady();
    }, 2000);
  } catch (e) {
    setStatus('saveStatus', 'error', '儲存失敗，請確認 Notion 已連線');
  }

  document.getElementById('saveBtn').disabled = false;
}

function renderHistory() {
  const el = document.getElementById('historyList');
  if (!savedItems.length) { el.innerHTML = '<div class="empty-history">尚未儲存任何筆記</div>'; return; }
  const icons = { 'Threads': '💬', '手機截圖': '📱', '網頁截圖': '🌐', '其他': '📄', '個人部落格': '✍️', '新聞媒體': '📰', '技術文章': '💻' };
  el.innerHTML = savedItems.map(item => `
    <div class="history-item">
      <div class="history-icon">${item.type === '文章連結' ? '🔗' : (icons[item.source] || '📄')}</div>
      <div style="min-width:0;flex:1">
        <div class="history-title">${item.title}</div>
        <div class="history-meta">${item.type} · ${item.source} · ${item.time}</div>
        ${item.url ? `<div class="history-meta">${item.url}</div>` : ''}
        ${item.tags.length ? `<div class="history-tags">${item.tags.map(t => `<span class="history-tag">${t}</span>`).join('')}</div>` : ''}
      </div>
    </div>
  `).join('');
}
</script>
</body>
</html>
