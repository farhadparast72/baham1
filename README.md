<!doctype html>
<html lang="fa" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />
  <title>NiceMind-ish (HTML/SVG) — آنلاین (Room) + استایل درختی/تگ + Drag آسان</title>

  <style>
    :root{
      --bg:#f6f7fb;
      --panel:#ffffff;
      --text:#12212f;
      --muted:#6b7a8b;
      --primary:#0a7b6c;
      --primary-2:#0e9b89;
      --danger:#d14a4a;
      --shadow: 0 10px 30px rgba(16, 24, 40, .10);
      --shadow2: 0 6px 18px rgba(16, 24, 40, .12);
      --radius: 16px;
      --nodeW: 260px;
      --gridA: .12;
    }

    *{ box-sizing:border-box; }
    html, body{ height:100%; margin:0; font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial; color:var(--text); background:var(--bg); }
    button, input, textarea, select{ font:inherit; }
    .app{ height:100%; display:flex; flex-direction:column; }

    .toolbar{
      display:flex; gap:10px; align-items:center;
      padding:10px 12px;
      background:rgba(255,255,255,.88);
      backdrop-filter: blur(8px);
      border-bottom:1px solid rgba(16,24,40,.08);
      position:sticky; top:0; z-index:50;
    }
    .toolbar .group{ display:flex; gap:8px; align-items:center; flex-wrap:wrap; }
    .spacer{ flex:1; }

    .btn{
      border:1px solid rgba(16,24,40,.12);
      background:var(--panel);
      color:var(--text);
      border-radius:12px;
      padding:9px 12px;
      box-shadow: 0 1px 0 rgba(16,24,40,.04);
      cursor:pointer;
      transition:.15s ease;
      display:inline-flex;
      gap:8px;
      align-items:center;
      user-select:none;
      white-space:nowrap;
    }
    .btn:hover{ transform: translateY(-1px); box-shadow: var(--shadow2); }
    .btn:active{ transform: translateY(0); }
    .btn.primary{
      background: linear-gradient(180deg, var(--primary-2), var(--primary));
      color:white;
      border-color: rgba(255,255,255,.2);
    }
    .btn.danger{
      background: linear-gradient(180deg, #f06b6b, var(--danger));
      color:white;
      border-color: rgba(255,255,255,.2);
    }
    .btn:disabled, .iconbtn:disabled{ opacity:.55; cursor:not-allowed; transform:none !important; box-shadow:none !important; }

    .iconbtn{
      width:42px; height:40px;
      padding:0;
      border:1px solid rgba(16,24,40,.12);
      background:var(--panel);
      color:var(--text);
      border-radius:12px;
      cursor:pointer;
      transition:.15s ease;
      display:inline-flex;
      align-items:center;
      justify-content:center;
      user-select:none;
      box-shadow: 0 1px 0 rgba(16,24,40,.04);
      font-size:16px;
    }
    .iconbtn:hover{ transform: translateY(-1px); box-shadow: var(--shadow2); }
    .iconbtn:active{ transform: translateY(0); }
    .iconbtn.primary{
      background: linear-gradient(180deg, var(--primary-2), var(--primary));
      color:white;
      border-color: rgba(255,255,255,.2);
    }
    .iconbtn.danger{
      background: linear-gradient(180deg, #f06b6b, var(--danger));
      color:white;
      border-color: rgba(255,255,255,.2);
    }

    .pill{
      padding:7px 10px;
      border-radius:999px;
      border:1px solid rgba(16,24,40,.12);
      background:var(--panel);
      color:var(--muted);
      font-size:12px;
      user-select:none;
      white-space:nowrap;
      display:inline-flex;
      align-items:center;
      gap:6px;
    }
    .pill.warn{
      background: rgba(209,74,74,.10);
      color: var(--danger);
      border-color: rgba(209,74,74,.18);
    }
    .pill.good{
      background: rgba(10,123,108,.10);
      color: var(--primary);
      border-color: rgba(10,123,108,.18);
    }

    /* Drawer */
    .drawer-backdrop{
      position:fixed; inset:0;
      background: rgba(16,24,40,.22);
      backdrop-filter: blur(3px);
      display:none;
      z-index:125;
    }
    .drawer-backdrop.open{ display:block; }
    .drawer{
      position:fixed;
      top:0; right:0;
      height:100%;
      width:min(460px, calc(100vw - 44px));
      background: rgba(255,255,255,.96);
      border-left:1px solid rgba(16,24,40,.10);
      box-shadow: var(--shadow2);
      transform: translateX(100%);
      transition: transform .18s ease;
      z-index:130;
      display:flex;
      flex-direction:column;
    }
    .drawer.open{ transform: translateX(0); }
    .drawer-hdr{
      padding:14px 14px 10px;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      border-bottom:1px solid rgba(16,24,40,.08);
    }
    .drawer-hdr .title{ font-weight:900; }
    .drawer-body{ padding:12px 14px; overflow:auto; }
    .seg{
      display:flex; gap:8px;
      background: rgba(16,24,40,.05);
      border:1px solid rgba(16,24,40,.10);
      border-radius: 14px;
      padding:6px;
    }
    .seg button{
      flex:1;
      border:0;
      border-radius: 12px;
      padding:10px 10px;
      background: transparent;
      cursor:pointer;
      font-weight:900;
      color: rgba(18,33,47,.75);
    }
    .seg button.active{
      background: white;
      box-shadow: 0 10px 22px rgba(16,24,40,.10);
      color: var(--text);
      border:1px solid rgba(16,24,40,.10);
    }
    .drawer-section{
      margin-top:12px;
      padding:12px;
      border:1px solid rgba(16,24,40,.10);
      border-radius: 16px;
      background: rgba(255,255,255,.72);
    }
    .drawer-section .h{ font-weight:900; margin-bottom:10px; }
    .small{ font-size:12px; color: var(--muted); line-height:1.7; }

    /* Settings */
    .settings-panel{
      position:fixed;
      top:64px;
      right:12px;
      width:min(820px, calc(100vw - 24px));
      background: rgba(255,255,255,.95);
      backdrop-filter: blur(10px);
      border:1px solid rgba(16,24,40,.10);
      border-radius: 16px;
      box-shadow: var(--shadow2);
      padding:12px;
      display:none;
      z-index:120;
    }
    .settings-panel.open{ display:block; }
    .settings-title{
      font-weight:900;
      color:var(--text);
      margin-bottom:10px;
      display:flex;
      align-items:center;
      justify-content:space-between;
    }
    .settings-grid{
      display:flex;
      flex-wrap:wrap;
      gap:10px;
    }

    /* Modals */
    .modal-backdrop{
      position:fixed;
      inset:0;
      background: rgba(16,24,40,.22);
      backdrop-filter: blur(4px);
      display:none;
      z-index:140;
    }
    .modal-backdrop.open{ display:block; }

    .modal{
      position:fixed;
      top:50%;
      left:50%;
      transform: translate(-50%,-50%);
      width:min(720px, calc(100vw - 24px));
      background: rgba(255,255,255,.96);
      border:1px solid rgba(16,24,40,.10);
      border-radius: 18px;
      box-shadow: var(--shadow2);
      padding:14px;
      display:none;
      z-index:150;
      max-height: 88vh;
      overflow: hidden;
    }
    .modal.open{ display:block; }
    .modal h3{
      margin:0 0 10px;
      font-size:16px;
      font-weight:900;
    }
    .row{
      display:flex;
      gap:10px;
      align-items:flex-start;
      margin:10px 0;
      flex-wrap:wrap;
    }
    .field{
      display:flex;
      flex-direction:column;
      gap:6px;
      flex: 1 1 220px;
    }
    .field label{ font-size:12px; color:var(--muted); }
    .input, select, textarea{
      border:1px solid rgba(16,24,40,.12);
      border-radius: 12px;
      padding:10px 10px;
      outline:none;
      background: white;
    }
    textarea{ min-height:120px; resize:vertical; }
    .input:focus, select:focus, textarea:focus{
      border-color: rgba(14,155,137,.45);
      box-shadow: 0 0 0 4px rgba(14,155,137,.10);
    }

    /* Style modal: flex column + scroll + sticky actions (مهم: پالت‌ها دیده شوند) */
    #styleModal.open{ display:flex; flex-direction:column; }
    .modal-scroll{
      flex: 1 1 auto;
      overflow:auto;
      -webkit-overflow-scrolling: touch;
      padding-bottom: 12px;
    }
    .modal-actions{
      position: sticky;
      bottom: 0;
      background: rgba(255,255,255,.96);
      border-top: 1px solid rgba(16,24,40,.10);
      padding: 10px 0 calc(10px + env(safe-area-inset-bottom));
      margin-top: 8px;
    }

    /* Color palette */
    .palette{ display:flex; gap:8px; flex-wrap:wrap; margin-top:6px; }
    .swatch{
      width:28px; height:28px; border-radius:10px;
      border:1px solid rgba(16,24,40,.14);
      cursor:pointer;
      box-shadow: 0 8px 16px rgba(16,24,40,.08);
    }

    /* Viewport */
    .viewport{
      position:relative;
      flex:1;
      overflow:hidden;
      touch-action:none;
      background:
        radial-gradient(circle at 1px 1px, rgba(10,123,108,var(--gridA)) 1px, transparent 0);
      background-size: 22px 22px;
    }
    .viewport.no-grid{ background: var(--bg); }

    /* Camera + World */
    #camera{
      position:absolute;
      left:0; top:0;
      width:100%;
      height:100%;
      transform-origin: 0 0;
      will-change: transform;
      backface-visibility: hidden;
    }
    #world{
      position:absolute;
      left:0; top:0;
      width:10000px;
      height:10000px;
      transform-origin: 0 0;
      will-change: transform;
      backface-visibility: hidden;
    }

    #edges{
      position:absolute;
      inset:0;
      width:100%;
      height:100%;
      pointer-events:none;
    }

    /* Tree edges */
    .edge{
      fill:none;
      stroke: rgba(10, 123, 108, .38);
      stroke-width: 3;
    }
    .edge.done{
      stroke: rgba(107, 122, 139, .32);
      stroke-dasharray: 6 8;
    }

    /* Network links */
    .link{
      fill:none;
      stroke: rgba(46, 96, 160, .45);
      stroke-width: 3;
    }
    .link.work{ stroke: rgba(14,155,137,.55); }
    .link.semantic{
      stroke: rgba(46,96,160,.52);
      stroke-dasharray: 7 8;
    }
    .link-label{
      fill: rgba(18,33,47,.70);
      font-size: 12px;
      paint-order: stroke;
      stroke: rgba(255,255,255,.95);
      stroke-width: 3px;
    }

    #nodes{ position:absolute; inset:0; }

    .node{
      position:absolute;
      width: var(--nodeW);
      min-height: 98px;
      border-radius: var(--radius);
      background: var(--panel);
      box-shadow: var(--shadow);
      border:1px solid rgba(16,24,40,.10);
      overflow:hidden;
      user-select:none;
      transform: translateZ(0);
      opacity:1;
    }
    .node.selected{
      outline: 3px solid rgba(14, 155, 137, .28);
      border-color: rgba(14, 155, 137, .38);
    }
    .node.drop-target{
      outline: 3px solid rgba(14,155,137,.40);
      border-color: rgba(14,155,137,.55);
      box-shadow: 0 0 0 6px rgba(14,155,137,.10), var(--shadow2);
    }
    .node.dragging{
      opacity: .96;
      box-shadow: 0 14px 34px rgba(16,24,40,.18);
    }

    .node .hdr{
      display:flex;
      align-items:center;
      gap:10px;
      padding:10px 12px;
      background: linear-gradient(180deg, rgba(14,155,137,.12), rgba(14,155,137,.06));
      border-bottom:1px solid rgba(16,24,40,.06);
      cursor: grab;
    }
    .node.dragging .hdr{ cursor: grabbing; }

    /* ✅ Easy Drag Handle */
    .drag-handle{
      width: 38px;
      height: 34px;
      border-radius: 12px;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      background: rgba(255,255,255,.85);
      border: 1px solid rgba(16,24,40,.12);
      box-shadow: 0 8px 16px rgba(16,24,40,.08);
      user-select:none;
      cursor: grab;
      touch-action:none;
      flex: 0 0 auto;
    }
    .node.dragging .drag-handle{ cursor: grabbing; }

    .node .checkbox{
      width:18px; height:18px;
      accent-color: var(--primary);
      cursor:pointer;
      flex:0 0 auto;
    }

    .node .title{
      flex:1;
      font-weight:900;
      font-size:15px;
      line-height:1.2;
      padding:6px 8px;
      border-radius:10px;
      outline:none;
      cursor:text;
      user-select:text;
      background: rgba(255,255,255,.65);
      border:1px solid transparent;
      text-rendering: geometricPrecision;
      min-width: 0;
    }
    .node .title:focus{
      border-color: rgba(14,155,137,.45);
      background: white;
    }
    .node.done .title{
      text-decoration: line-through;
      color: rgba(18,33,47,.55);
    }

    .node .meta{
      display:flex;
      gap:8px;
      align-items:center;
      padding:10px 12px 12px;
      color: var(--muted);
      font-size:12px;
      justify-content:space-between;
    }

    .mini{
      border:1px solid rgba(16,24,40,.12);
      background: rgba(255,255,255,.88);
      border-radius: 10px;
      padding:6px 8px;
      cursor:pointer;
      color: var(--text);
      user-select:none;
      display:inline-flex;
      align-items:center;
      gap:6px;
    }
    .mini:hover{ box-shadow: 0 6px 14px rgba(16,24,40,.10); }
    .mini.icon{
      width:34px; height:32px;
      justify-content:center;
      padding:0;
    }

    .badge{
      padding:5px 8px;
      border-radius:999px;
      background: rgba(10,123,108,.10);
      color: var(--primary);
      border:1px solid rgba(10,123,108,.18);
      white-space:nowrap;
    }
    .badge.note{
      background: rgba(46,96,160,.10);
      color: rgba(46,96,160,.95);
      border-color: rgba(46,96,160,.18);
    }
    .badge.tag{
      background: rgba(124,58,237,.10);
      color: rgba(124,58,237,.95);
      border-color: rgba(124,58,237,.18);
    }

    /* Templates */
    .node.tpl-card{}
    .node.tpl-cluster{ border-radius: 999px; }
    .node.tpl-cluster .hdr{ border-bottom:0; background: rgba(16,24,40,.03); cursor: grab; }
    .node.tpl-cluster .title{ text-align:center; border-radius: 999px; }

    .node.tpl-sticky{
      border-radius: 18px;
      border:1px solid rgba(234,179,8,.28);
      box-shadow: 0 12px 28px rgba(16,24,40,.12);
      background: #fff7cc;
    }
    .node.tpl-sticky .hdr{
      background: rgba(234,179,8,.16);
      border-bottom:1px dashed rgba(16,24,40,.12);
      cursor: grab;
    }

    .node.tpl-outline{
      background: rgba(255,255,255,.30);
      backdrop-filter: blur(2px);
      border:2px dashed rgba(16,24,40,.18);
      box-shadow: 0 10px 22px rgba(16,24,40,.08);
    }
    .node.tpl-outline .hdr{ background: rgba(255,255,255,.55); cursor: grab; }

    .node.tpl-minimal{
      box-shadow: 0 8px 18px rgba(16,24,40,.08);
      border-color: rgba(16,24,40,.08);
    }
    .node.tpl-minimal .hdr{ padding:8px 10px; }
    .node.tpl-minimal .meta{ padding:8px 10px 10px; }

    /* Context menu */
    .ctx{
      position:absolute;
      min-width: 290px;
      background: white;
      border:1px solid rgba(16,24,40,.10);
      border-radius: 14px;
      box-shadow: var(--shadow2);
      overflow:hidden;
      z-index:100;
      display:none;
    }
    .ctx.open{ display:block; }
    .ctx .item{
      padding:10px 12px;
      display:flex;
      justify-content:space-between;
      align-items:center;
      cursor:pointer;
      font-size:14px;
      user-select:none;
    }
    .ctx .item:hover{ background: rgba(14,155,137,.10); }
    .ctx .sep{ height:1px; background: rgba(16,24,40,.08); }

    .kbd{
      font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", monospace;
      font-size:11px;
      background: rgba(16,24,40,.06);
      border:1px solid rgba(16,24,40,.10);
      padding:2px 6px;
      border-radius:8px;
      color: var(--text);
      white-space:nowrap;
    }

    @media (max-width: 900px){
      .toolbar{ flex-wrap:wrap; }
      .spacer{ display:none; }
    }
  </style>

  <!-- ✅ برای آنلاین (اختیاری): Firebase compat (اگر تنظیمات را پر کنی فعال می‌شود) -->
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"></script>
</head>

<body>
<div class="app">
  <div class="toolbar">
    <div class="group">
      <button class="iconbtn" id="btnDrawer" title="منوی کشویی">☰</button>
      <button class="iconbtn primary" id="btnAddRoot" title="نود ریشه">➕</button>
      <button class="iconbtn" id="btnAddChild" title="زیرمجموعه">↳</button>
      <button class="iconbtn danger" id="btnDelete" title="حذف (به سطل زباله)">🗑</button>
    </div>

    <div class="group">
      <button class="iconbtn" id="btnZoomOut" title="Zoom out">➖</button>
      <span class="pill" id="zoomLabel">100%</span>
      <button class="iconbtn" id="btnZoomIn" title="Zoom in">➕</button>
      <button class="iconbtn" id="btnZoomReset" title="Reset">⟲</button>
    </div>

    <div class="spacer"></div>

    <div class="group">
      <span class="pill" id="onlinePill" title="وضعیت آنلاین">● آفلاین</span>

      <span class="pill warn" id="viewOnlyPill" style="display:none;"></span>
      <button class="btn" id="btnTakeCopy" style="display:none;">✍️ ساخت نسخه خودم</button>

      <span class="pill warn" id="linkModePill" style="display:none;"></span>
      <button class="iconbtn" id="btnSettings" title="تنظیمات">⚙️</button>
    </div>
  </div>

  <!-- Drawer -->
  <div class="drawer-backdrop" id="drawerBackdrop"></div>
  <div class="drawer" id="drawer">
    <div class="drawer-hdr">
      <div class="title">بخش‌ها / همکاری</div>
      <button class="iconbtn" id="btnCloseDrawer" title="بستن">✕</button>
    </div>
    <div class="drawer-body">
      <div class="seg" id="boardSeg">
        <button data-board="main" class="active">بخش اصلی</button>
        <button data-board="team">کار تیمی</button>
      </div>

      <div class="drawer-section">
        <div class="h">✅ همکاری آنلاین (Room)</div>
        <div class="small">
          اگر Firebase را تنظیم کنی، هر تغییری که تو انجام بدی برای همه کسانی که همین Room را باز کرده‌اند، آنلاین اعمال می‌شود و آن‌ها هم می‌توانند تغییر بدهند.
        </div>
        <div class="row" style="margin-top:10px;">
          <div class="field" style="flex:1 1 260px;">
            <label>Room ID (مثلاً: team-2026)</label>
            <input class="input" id="roomInput" placeholder="مثلاً: my-team-room" />
          </div>
          <div class="field" style="flex:0 0 auto;">
            <label>&nbsp;</label>
            <button class="btn primary" id="btnJoinRoom">ورود / ساخت Room</button>
          </div>
        </div>
        <div style="display:flex; gap:10px; flex-wrap:wrap;">
          <button class="btn" id="btnCopyRoomLink">🔗 کپی لینک Room</button>
          <button class="btn" id="btnLeaveRoom">⛔ خروج از Room</button>
        </div>
        <div class="small" id="roomHint" style="margin-top:8px;"></div>
      </div>

      <div class="drawer-section">
        <div class="h">اشتراک با دیگران (لینک داخل URL)</div>
        <div class="small">
          این گزینه دیتا را داخل URL می‌گذارد. برای ارسال سریع و «نمایش فقط» عالی است.
        </div>
        <div style="display:flex; gap:10px; flex-wrap:wrap; margin-top:10px;">
          <button class="btn primary" id="btnShareActive">🔗 لینک اشتراک (همین بخش)</button>
          <button class="btn" id="btnShareAll">🔗 لینک اشتراک (هر دو بخش)</button>
        </div>
      </div>

      <div class="drawer-section">
        <div class="h">Import/Export</div>
        <div style="display:flex; gap:10px; flex-wrap:wrap;">
          <button class="btn" id="btnExportAll">⬇️ خروجی همه بخش‌ها</button>
          <button class="btn" id="btnImportAll">⬆️ ورود همه بخش‌ها</button>
          <input type="file" id="fileImportAll" accept="application/json" hidden />
        </div>
      </div>

      <div class="drawer-section">
        <div class="h">نکته</div>
        <div class="small">
          ✅ جابجایی باکس‌ها خیلی راحت‌تر شده: روی دسته‌ی ⠿ بگیر و بکش. (موبایل هم عالی)
        </div>
      </div>
    </div>
  </div>

  <!-- Settings -->
  <div class="settings-panel" id="settingsPanel" aria-hidden="true">
    <div class="settings-title">
      <span>تنظیمات</span>
      <button class="iconbtn" id="btnCloseSettings" title="بستن">✕</button>
    </div>
    <div class="settings-grid">
      <button class="btn" id="btnUndo">↶ Undo</button>
      <button class="btn" id="btnRedo">↷ Redo</button>
      <button class="btn" id="btnArrange">✨ مرتب‌سازی</button>
      <button class="btn" id="btnCenter">🎯 مرکز</button>
      <button class="btn" id="btnGrid">🟦 گرید</button>

      <button class="btn" id="btnToggleLinks">🔗 لینک‌ها: روشن</button>
      <button class="btn" id="btnToggleNetwork">🧠 مد شبکه: خاموش</button>
      <button class="btn primary" id="btnCreateLink">➕ ایجاد ارتباط</button>
      <button class="btn" id="btnManageLinks">📎 مدیریت ارتباطات</button>

      <button class="btn" id="btnTagStyles">🏷 مدیریت استایل تگ‌ها</button>

      <button class="btn" id="btnTrash">🧺 سطل زباله</button>

      <button class="btn" id="btnSave">💾 ذخیره</button>
      <button class="btn" id="btnExport">⬇️ خروجی JSON (همین بخش)</button>
      <button class="btn" id="btnImport">⬆️ ورود JSON (همین بخش)</button>
      <input type="file" id="fileImport" accept="application/json" hidden />
    </div>
  </div>

  <!-- Modals -->
  <div class="modal-backdrop" id="modalBackdrop"></div>

  <!-- Create Link -->
  <div class="modal" id="linkCreateModal" role="dialog" aria-modal="true">
    <h3>ایجاد ارتباط بین نودها</h3>
    <div class="row">
      <div class="field">
        <label>نوع ارتباط</label>
        <select id="linkTypeSelect">
          <option value="work">کاری (Work)</option>
          <option value="semantic">معنایی (Semantic)</option>
        </select>
      </div>
      <div class="field">
        <label>برچسب (اختیاری) — مثلاً اسم فرد/تیم</label>
        <input class="input" id="linkLabelInput" placeholder="مثلاً: علی ↔ سارا / تیم A" />
      </div>
    </div>
    <div class="row" style="justify-content:flex-end;">
      <button class="btn" id="btnCancelCreateLink">لغو</button>
      <button class="btn primary" id="btnStartLinking">شروع (بعدش مقصد رو لمس کن)</button>
    </div>
    <div class="small">میانبر: <span class="kbd">L</span> (فعال/غیرفعال)</div>
  </div>

  <!-- Manage Links -->
  <div class="modal" id="linkManageModal" role="dialog" aria-modal="true">
    <h3>مدیریت ارتباطات</h3>
    <div id="linkManageBody" style="max-height:55vh; overflow:auto; padding:6px 2px;"></div>
    <div class="row" style="justify-content:flex-end;">
      <button class="btn" id="btnCloseManageLinks">بستن</button>
    </div>
  </div>

  <!-- Tag styles modal -->
  <div class="modal" id="tagStylesModal" role="dialog" aria-modal="true">
    <h3>مدیریت استایل تگ‌ها</h3>
    <div class="small" style="margin-bottom:6px;">
      هر تگ می‌تواند یک استایل داشته باشد و هر نودی که آن تگ را داشته باشد همان استایل را می‌گیرد.
    </div>
    <div id="tagStylesBody" style="max-height:55vh; overflow:auto; padding:6px 2px;"></div>
    <div class="row" style="justify-content:flex-end;">
      <button class="btn" id="btnCloseTagStyles">بستن</button>
    </div>
  </div>

  <!-- Style Modal -->
  <div class="modal" id="styleModal" role="dialog" aria-modal="true">
    <div class="modal-scroll">
      <h3>استایل نود</h3>
      <div class="small" id="styleModalTitle" style="margin-bottom:6px;"></div>

      <div class="row">
        <div class="field">
          <label>Template / نوع نود</label>
          <select id="templateSelect">
            <option value="card">کارت (پیش‌فرض)</option>
            <option value="cluster">خوشه‌ای (Cluster)</option>
            <option value="sticky">استیکی (Sticky)</option>
            <option value="outline">Outline</option>
            <option value="minimal">مینیمال</option>
          </select>
        </div>

        <div class="field">
          <label>فونت</label>
          <select id="fontSelect">
            <option value="">پیش‌فرض سیستم</option>
            <option value="Vazirmatn, ui-sans-serif, system-ui, Segoe UI, Roboto, Arial">Vazirmatn (اگر نصب باشد)</option>
            <option value="IRANSans, ui-sans-serif, system-ui, Segoe UI, Roboto, Arial">IRANSans (اگر نصب باشد)</option>
            <option value="Tahoma, ui-sans-serif, system-ui, Segoe UI, Roboto, Arial">Tahoma</option>
            <option value="Georgia, serif">Georgia</option>
            <option value="ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, Liberation Mono, monospace">Monospace</option>
          </select>
          <input class="input" id="customFontInput" placeholder="یا فونت دلخواه (اختیاری)" />
        </div>

        <div class="field">
          <label>سایز فونت عنوان</label>
          <input class="input" id="fontSizeInput" type="number" min="10" max="32" step="1" />
        </div>
      </div>

      <div class="row">
        <div class="field">
          <label>رنگ بدنه (کل نود)</label>
          <input class="input" id="bodyColorInput" type="color" value="#ffffff" />
          <div class="palette" id="bodyPalette"></div>
        </div>
        <div class="field">
          <label>رنگ کاور/هدر</label>
          <input class="input" id="coverColorInput" type="color" value="#0e9b89" />
          <div class="palette" id="coverPalette"></div>
        </div>
      </div>

      <div class="row">
        <div class="field">
          <label>تگ‌ها (با کاما جدا کن) — برای استایل تارگتی</label>
          <input class="input" id="tagsInput" placeholder="مثلاً: مهم, تیمA, فوری" />
          <div class="small">نودهایی که همین تگ را داشته باشند می‌توانند استایل مخصوص تگ بگیرند.</div>
        </div>
      </div>

      <div class="row">
        <div class="field" style="flex:1 1 100%;">
          <label>یادداشت طولانی (Note)</label>
          <textarea id="noteInput" placeholder="هرچقدر خواستی طولانی بنویس…"></textarea>
        </div>
      </div>

      <div class="row">
        <div class="field" style="flex:1 1 100%;">
          <label>قوانین استایل (خیلی مهم)</label>
          <div style="display:flex; gap:10px; flex-wrap:wrap; align-items:center;">
            <label class="pill" style="cursor:pointer;">
              <input type="checkbox" id="chkPropagate" style="margin-left:8px;"> استایلِ «درختی» (به زیرمجموعه‌ها منتقل شود)
            </label>
            <label class="pill" style="cursor:pointer;">
              <input type="checkbox" id="chkApplyToTag" style="margin-left:8px;"> این استایل برای تگ(های) بالا هم ذخیره شود (Target Style)
            </label>
          </div>
          <div class="small">
            • «درختی»: هر فرزندی که استایل خودش را تنظیم نکرده باشد، این استایل را ارث می‌برد.<br>
            • «Target»: هر نودی که آن تگ را داشته باشد این استایل را می‌گیرد.
          </div>
        </div>
      </div>

    </div>

    <div class="modal-actions">
      <div class="row" style="justify-content:space-between; align-items:center; margin:0;">
        <button class="btn" id="btnResetStyle">ریست استایل</button>
        <div style="display:flex; gap:10px;">
          <button class="btn" id="btnCancelStyle">لغو</button>
          <button class="btn primary" id="btnApplyStyle">اعمال</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Trash Modal -->
  <div class="modal" id="trashModal" role="dialog" aria-modal="true">
    <h3>سطل زباله (قابل بازیابی)</h3>
    <div id="trashBody" style="max-height:55vh; overflow:auto; padding:6px 2px;"></div>
    <div class="row" style="justify-content:flex-end;">
      <button class="btn" id="btnCloseTrash">بستن</button>
    </div>
  </div>

  <!-- Viewport -->
  <div class="viewport" id="viewport">
    <div id="camera">
      <div id="world">
        <svg id="edges" viewBox="0 0 10000 10000" preserveAspectRatio="none"></svg>
        <div id="nodes"></div>
      </div>
    </div>

    <!-- Context Menu -->
    <div class="ctx" id="ctxMenu">
      <div class="item" data-act="addChild">افزودن زیرمجموعه <span class="kbd">Enter</span></div>
      <div class="item" data-act="addSibling">افزودن هم‌سطح <span class="kbd">Tab</span></div>
      <div class="item" data-act="toggleDone">تیک/برداشتن <span class="kbd">Space</span></div>
      <div class="item" data-act="toggleCollapse">جمع/باز کردن <span class="kbd">C</span></div>
      <div class="sep"></div>
      <div class="item" data-act="style">🎨 استایل (درختی/تگ/رنگ/فونت/یادداشت)</div>
      <div class="sep"></div>
      <div class="item" data-act="startLink">ایجاد ارتباط از این نود <span class="kbd">L</span></div>
      <div class="item" data-act="manageLinks">مدیریت ارتباطات <span class="kbd">M</span></div>
      <div class="sep"></div>
      <div class="item" data-act="delete" style="color:var(--danger)">حذف (به سطل زباله) <span class="kbd">Del</span></div>
    </div>
  </div>
</div>

<script>
(() => {
  const $ = (sel, root=document) => root.querySelector(sel);

  const viewport = $("#viewport");
  const camera = $("#camera");
  const world = $("#world");
  const edgesSvg = $("#edges");
  const nodesLayer = $("#nodes");
  const zoomLabel = $("#zoomLabel");
  const ctxMenu = $("#ctxMenu");

  const btnSettings = $("#btnSettings");
  const settingsPanel = $("#settingsPanel");
  const btnCloseSettings = $("#btnCloseSettings");

  const btnToggleLinks = $("#btnToggleLinks");
  const btnToggleNetwork = $("#btnToggleNetwork");
  const btnCreateLink = $("#btnCreateLink");
  const btnManageLinks = $("#btnManageLinks");
  const linkModePill = $("#linkModePill");

  const onlinePill = $("#onlinePill");

  // Share / view-only UI
  const viewOnlyPill = $("#viewOnlyPill");
  const btnTakeCopy = $("#btnTakeCopy");

  // Drawer
  const btnDrawer = $("#btnDrawer");
  const drawer = $("#drawer");
  const drawerBackdrop = $("#drawerBackdrop");
  const btnCloseDrawer = $("#btnCloseDrawer");
  const boardSeg = $("#boardSeg");
  const btnExportAll = $("#btnExportAll");
  const btnImportAll = $("#btnImportAll");
  const fileImportAll = $("#fileImportAll");
  const btnShareActive = $("#btnShareActive");
  const btnShareAll = $("#btnShareAll");

  // Online room controls
  const roomInput = $("#roomInput");
  const btnJoinRoom = $("#btnJoinRoom");
  const btnCopyRoomLink = $("#btnCopyRoomLink");
  const btnLeaveRoom = $("#btnLeaveRoom");
  const roomHint = $("#roomHint");

  // Modals
  const modalBackdrop = $("#modalBackdrop");
  const linkCreateModal = $("#linkCreateModal");
  const linkTypeSelect = $("#linkTypeSelect");
  const linkLabelInput = $("#linkLabelInput");
  const btnCancelCreateLink = $("#btnCancelCreateLink");
  const btnStartLinking = $("#btnStartLinking");
  const linkManageModal = $("#linkManageModal");
  const linkManageBody = $("#linkManageBody");
  const btnCloseManageLinks = $("#btnCloseManageLinks");

  const tagStylesModal = $("#tagStylesModal");
  const tagStylesBody = $("#tagStylesBody");
  const btnCloseTagStyles = $("#btnCloseTagStyles");

  // Style modal
  const styleModal = $("#styleModal");
  const styleModalTitle = $("#styleModalTitle");
  const templateSelect = $("#templateSelect");
  const fontSelect = $("#fontSelect");
  const customFontInput = $("#customFontInput");
  const fontSizeInput = $("#fontSizeInput");
  const bodyColorInput = $("#bodyColorInput");
  const coverColorInput = $("#coverColorInput");
  const tagsInput = $("#tagsInput");
  const noteInput = $("#noteInput");
  const chkPropagate = $("#chkPropagate");
  const chkApplyToTag = $("#chkApplyToTag");
  const bodyPalette = $("#bodyPalette");
  const coverPalette = $("#coverPalette");
  const btnResetStyle = $("#btnResetStyle");
  const btnCancelStyle = $("#btnCancelStyle");
  const btnApplyStyle = $("#btnApplyStyle");

  // Trash
  const trashModal = $("#trashModal");
  const trashBody = $("#trashBody");
  const btnCloseTrash = $("#btnCloseTrash");

  const fileImport = $("#fileImport");

  // --------------------
  // ✅ Firebase config (برای آنلاین واقعی اینا رو پر کن)
  // --------------------
  const FIREBASE_CONFIG = {
    apiKey: "",
    authDomain: "",
    projectId: "",
    // (اگر خواستی) storageBucket, messagingSenderId, appId
  };

  // --------------------
  const MIN_ZOOM = 0.18;
  const MAX_ZOOM = 2.8;

  const SUPPORTS_CSS_ZOOM = (() => {
    try {
      const d = document.createElement("div");
      d.style.zoom = "1.23";
      return d.style.zoom === "1.23";
    } catch { return false; }
  })();

  function deepClone(obj){ return JSON.parse(JSON.stringify(obj)); }
  function clamp(v,a,b){ return Math.max(a, Math.min(b, v)); }
  function cssEsc(str){ return (str||"").replace(/["\\]/g, "\\$&"); }
  function newId(){
    if (window.crypto && crypto.randomUUID) return crypto.randomUUID();
    return "id_" + Math.random().toString(16).slice(2) + "_" + Date.now();
  }
  function now(){ return Date.now(); }

  function escHtml(s){
    return (s||"").replace(/[&<>"']/g, c => ({
      "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
    }[c]));
  }

  // UTF-8 safe base64url
  function utf8ToB64Url(str){
    const bytes = new TextEncoder().encode(str);
    let bin = "";
    for(const b of bytes) bin += String.fromCharCode(b);
    return btoa(bin).replace(/\+/g,"-").replace(/\//g,"_").replace(/=+$/,"");
  }
  function b64UrlToUtf8(b64url){
    const b64 = b64url.replace(/-/g,"+").replace(/_/g,"/");
    const pad = b64.length % 4 ? "=".repeat(4 - (b64.length % 4)) : "";
    const bin = atob(b64 + pad);
    const bytes = Uint8Array.from(bin, c => c.charCodeAt(0));
    return new TextDecoder().decode(bytes);
  }

  // ---------- Boards ----------
  function makeBoard(){
    return {
      nodes: {},
      roots: [],
      links: [],
      trash: { nodes: {} },
      selectedId: null,
      pan: { x: 0, y: 0 },
      zoom: 1,
      showGrid: true,
      showLinks: true,
      networkMode: false,
      tagStyles: {} // ✅ Target styles by tag
    };
  }

  const app = {
    data: { version: 2, activeBoard: "main", boards: { main: makeBoard(), team: makeBoard() } },
    hist: { main: { undo: [], redo: [] }, team: { undo: [], redo: [] } },
    share: { active:false, readOnly:false },
    drag: {
      mode: null,
      nodeId: null,
      pointerId: null,
      startClient: {x:0,y:0},
      startPan: {x:0,y:0},
      didMutate: false,
      dropTargetId: null,
      draggingEl: null,
      grabOffset: { x: 0, y: 0 },
    },
    linker: { active: false, fromId: null, type: "work", label: "" },
    pointers: new Map(),
    pinch: { active:false, startDist:0, startZoom:1 },
    longpress: { timer: null, start: {x:0,y:0}, fired: false },
    styleEdit: { nodeId: null, snapshot: null },
    cloud: {
      enabled: false,
      roomId: null,
      db: null,
      roomRef: null,
      clientId: "c_" + newId(),
      rev: 0,
      lastAppliedRev: 0,
      applyingRemote: false,
      saveTimer: null,
      lastWriteAt: 0
    }
  };

  const B = () => app.data.boards[app.data.activeBoard];
  const H = () => app.hist[app.data.activeBoard];

  // ---------- Readonly ----------
  function isReadOnly(){ return app.share.active && app.share.readOnly; }
  function guardEdit(){
    if(isReadOnly()){
      alert("این لینک در حالت «نمایش فقط» است.\nبرای ویرایش: روی «ساخت نسخه خودم» بزن.");
      return true;
    }
    return false;
  }

  // ---------- Normalize ----------
  function normalizeNode(n){
    n.children = n.children || [];
    n.done = !!n.done;
    n.collapsed = !!n.collapsed;
    n.createdAt = n.createdAt || now();
    n.note = typeof n.note === "string" ? n.note : "";
    n.tags = typeof n.tags === "string" ? n.tags : "";
    n.style = n.style || {};
    n.style.body = n.style.body ?? null;
    n.style.cover = n.style.cover ?? null;
    n.style.template = n.style.template || "card";
    n.style.fontFamily = n.style.fontFamily || "";
    n.style.fontSize = (typeof n.style.fontSize === "number") ? n.style.fontSize : 15;
    n.style.propagate = !!n.style.propagate; // ✅ tree style
    return n;
  }
  function normalizeBoard(bd){
    bd.nodes = bd.nodes || {};
    for(const id in bd.nodes) bd.nodes[id] = normalizeNode(bd.nodes[id]);
    bd.roots = bd.roots || [];
    bd.links = Array.isArray(bd.links) ? bd.links : [];
    bd.trash = bd.trash || { nodes:{} };
    bd.trash.nodes = bd.trash.nodes || {};
    for(const id in bd.trash.nodes) bd.trash.nodes[id] = normalizeNode(bd.trash.nodes[id]);
    bd.pan = bd.pan || {x:0,y:0};
    bd.zoom = (typeof bd.zoom === "number") ? bd.zoom : 1;
    bd.showGrid = (typeof bd.showGrid === "boolean") ? bd.showGrid : true;
    bd.showLinks = (typeof bd.showLinks === "boolean") ? bd.showLinks : true;
    bd.networkMode = (typeof bd.networkMode === "boolean") ? bd.networkMode : false;
    bd.tagStyles = bd.tagStyles || {};
    return bd;
  }
  function migrateIfNeeded(obj){
    // v1 -> v2
    if(obj && obj.nodes && obj.roots){
      return { version:2, activeBoard:"main", boards:{ main: normalizeBoard(obj), team: makeBoard() } };
    }
    if(obj && obj.boards){
      if(!obj.boards.main) obj.boards.main = makeBoard();
      if(!obj.boards.team) obj.boards.team = makeBoard();
      obj.boards.main = normalizeBoard(obj.boards.main);
      obj.boards.team = normalizeBoard(obj.boards.team);
      obj.activeBoard = obj.activeBoard || "main";
      obj.version = 2;
      return obj;
    }
    return obj;
  }

  // ---------- Storage keys (per room) ----------
  const BASE_KEY = "atomic_mindmap_v9_online_tree_tag_drag";
  function storageKey(){ return BASE_KEY + "_room_" + (app.cloud.roomId || "local"); }
  function backupKey(){ return storageKey() + "_backup"; }

  // ---------- Persistence ----------
  function saveLocal(){
    if(isReadOnly()) return;
    const k = storageKey();
    localStorage.setItem(k, JSON.stringify(app.data));
    localStorage.setItem(backupKey(), JSON.stringify({ t: now(), data: app.data }));
  }
  function loadLocal(){
    const raw = localStorage.getItem(storageKey());
    if(!raw) return false;
    try{
      let obj = JSON.parse(raw);
      obj = migrateIfNeeded(obj);
      if(!obj || !obj.boards) return false;
      app.data = obj;
      return true;
    }catch(e){ return false; }
  }

  // ---------- Cloud (Firebase Firestore) ----------
  function hasFirebaseConfig(){
    return !!(FIREBASE_CONFIG && FIREBASE_CONFIG.apiKey && FIREBASE_CONFIG.projectId);
  }

  function setOnlinePill(state, text){
    onlinePill.className = "pill " + (state === "good" ? "good" : state === "warn" ? "warn" : "");
    onlinePill.textContent = text;
  }

  function connectRoom(roomId){
    roomId = (roomId || "").trim();
    if(!roomId){
      alert("Room ID را وارد کن.");
      return;
    }
    if(!hasFirebaseConfig()){
      setOnlinePill("warn", "● آنلاین خاموش (Firebase تنظیم نشده)");
      roomHint.textContent = "برای آنلاین شدن باید FIREBASE_CONFIG را در فایل پر کنی (apiKey, projectId, ...).";
      alert("برای آنلاین واقعی: FIREBASE_CONFIG را در بالای فایل پر کن. (Firebase project لازم است)");
      return;
    }

    try{
      if(!firebase.apps.length) firebase.initializeApp(FIREBASE_CONFIG);
      const db = firebase.firestore();

      app.cloud.enabled = true;
      app.cloud.db = db;
      app.cloud.roomId = roomId;
      app.cloud.roomRef = db.collection("mindrooms").doc(roomId);

      // reflect room in URL
      const url = new URL(location.href);
      url.searchParams.set("room", roomId);
      history.replaceState({}, "", url.toString());

      // show status
      setOnlinePill("good", "● آنلاین: " + roomId);
      roomHint.textContent = "این Room مشترک است. هرکس همین لینک/Room را باز کند تغییرات را می‌بیند.";

      // Listen remote
      app.cloud.roomRef.onSnapshot((snap)=>{
        if(!snap.exists) return;
        const data = snap.data() || {};
        const remote = data.state;
        const rev = data.rev || 0;
        const fromClient = data.clientId || "";

        if(!remote || typeof remote !== "object") return;

        // اگر همین کلاینت نوشته و همان rev است: ignore
        if(fromClient === app.cloud.clientId && rev === app.cloud.rev) return;

        if(rev <= app.cloud.lastAppliedRev) return;

        app.cloud.applyingRemote = true;
        try{
          const obj = migrateIfNeeded(deepClone(remote));
          if(obj && obj.boards){
            app.data = obj;
            // پاک کردن تاریخچه برای جلوگیری از قاطی شدن
            app.hist.main.undo = []; app.hist.main.redo = [];
            app.hist.team.undo = []; app.hist.team.redo = [];
            cancelLinkMode();
            syncUI(true); // skip local save inside
            saveLocal();  // ذخیره محلی برای آفلاین
            app.cloud.lastAppliedRev = rev;
          }
        } finally{
          app.cloud.applyingRemote = false;
        }
      });

      // Ensure room doc exists (first write if empty)
      app.cloud.roomRef.get().then((snap)=>{
        if(!snap.exists){
          scheduleCloudSave(true);
        }else{
          // optionally load remote first
          const data = snap.data() || {};
          if(data.state){
            app.cloud.applyingRemote = true;
            try{
              const obj = migrateIfNeeded(deepClone(data.state));
              if(obj && obj.boards){
                app.data = obj;
                app.cloud.lastAppliedRev = data.rev || 0;
                syncUI(true);
                saveLocal();
              }
            } finally{
              app.cloud.applyingRemote = false;
            }
          }
        }
      });

      // load local for this room if exists
      // (اگر دوست داری اول local بیاد، این خط رو قبل از connectRoom صدا بزن)
    }catch(err){
      console.error(err);
      setOnlinePill("warn", "● آنلاین خطا");
      alert("اتصال آنلاین ناموفق بود. کنسول را ببین.");
    }
  }

  function disconnectRoom(){
    // ما نمی‌تونیم listener رو راحت detach کنیم چون onSnapshot unsubscribe ذخیره نشده،
    // پس ساده‌ترین: reload بدون پارامتر room
    app.cloud.enabled = false;
    app.cloud.roomId = null;
    app.cloud.db = null;
    app.cloud.roomRef = null;
    app.cloud.rev = 0;
    app.cloud.lastAppliedRev = 0;

    const url = new URL(location.href);
    url.searchParams.delete("room");
    history.replaceState({}, "", url.toString());

    setOnlinePill("", "● آفلاین");
    roomHint.textContent = "از Room خارج شدی. اکنون تغییرات فقط محلی ذخیره می‌شوند.";
  }

  function scheduleCloudSave(force=false){
    if(!app.cloud.enabled || !app.cloud.roomRef) return;
    if(app.cloud.applyingRemote) return;
    if(isReadOnly()) return;
    if(!force){
      if(app.cloud.saveTimer) clearTimeout(app.cloud.saveTimer);
      app.cloud.saveTimer = setTimeout(()=> cloudSaveNow(), 450);
      return;
    }
    cloudSaveNow();
  }

  function cloudSaveNow(){
    if(!app.cloud.enabled || !app.cloud.roomRef) return;
    if(app.cloud.applyingRemote) return;
    if(isReadOnly()) return;

    app.cloud.rev = (app.cloud.rev || 0) + 1;
    const payload = {
      state: app.data,
      rev: app.cloud.rev,
      clientId: app.cloud.clientId,
      updatedAtMs: Date.now()
    };

    app.cloud.roomRef.set(payload, { merge: true }).catch((e)=>{
      console.warn("cloud save failed", e);
      setOnlinePill("warn", "● آنلاین مشکل دارد");
    });
  }

  // ---------- Share link (view-only) ----------
  function getSharePayload(kind){
    if(kind === "all") return app.data;
    return { version:2, activeBoard: app.data.activeBoard, boards: { [app.data.activeBoard]: B() } };
  }
  function makeShareLink(kind){
    const payload = getSharePayload(kind);
    const json = JSON.stringify(payload);
    const token = utf8ToB64Url(json);
    const url = new URL(location.href);
    url.searchParams.set("share", token);
    url.searchParams.set("ro", "1");
    url.searchParams.set("k", kind);
    return url.toString();
  }
  async function copyToClipboard(text){
    try{ await navigator.clipboard.writeText(text); alert("کپی شد ✅"); }
    catch{ prompt("کپی کن:", text); }
  }
  function tryLoadFromShareLink(){
    const url = new URL(location.href);
    const token = url.searchParams.get("share");
    if(!token) return false;
    try{
      const json = b64UrlToUtf8(token);
      const obj = migrateIfNeeded(JSON.parse(json));
      if(!obj || !obj.boards) return false;
      app.data = obj;
      app.share.active = true;
      app.share.readOnly = url.searchParams.get("ro") === "1";
      if(app.share.readOnly){
        viewOnlyPill.style.display = "inline-flex";
        viewOnlyPill.textContent = "نمایش فقط (لینک اشتراکی)";
        btnTakeCopy.style.display = "inline-flex";
      }
      return true;
    }catch(e){
      console.warn(e);
      return false;
    }
  }
  function takeCopyFromShare(){
    app.share.readOnly = false;
    app.share.active = false;

    const url = new URL(location.href);
    url.searchParams.delete("share");
    url.searchParams.delete("ro");
    url.searchParams.delete("k");
    history.replaceState({}, "", url.toString());

    viewOnlyPill.style.display = "none";
    btnTakeCopy.style.display = "none";

    saveLocal();
    syncUI(true);
    alert("نسخه شما ساخته شد ✅ حالا می‌تونی ویرایش کنی.");
  }

  // ---------- History ----------
  function pushHistory(){
    if(isReadOnly()) return;
    H().undo.push(deepClone(B()));
    if(H().undo.length > 200) H().undo.shift();
    H().redo.length = 0;
  }
  function undo(){
    if(isReadOnly()) return guardEdit();
    if(H().undo.length === 0) return;
    H().redo.push(deepClone(B()));
    app.data.boards[app.data.activeBoard] = H().undo.pop();
    cancelLinkMode();
    syncUI();
  }
  function redo(){
    if(isReadOnly()) return guardEdit();
    if(H().redo.length === 0) return;
    H().undo.push(deepClone(B()));
    app.data.boards[app.data.activeBoard] = H().redo.pop();
    cancelLinkMode();
    syncUI();
  }

  // ---------- UI helpers ----------
  function openSettings(){
    settingsPanel.classList.add("open");
    settingsPanel.setAttribute("aria-hidden","false");
    closeDrawer();
    closeContextMenu();
  }
  function closeSettings(){
    settingsPanel.classList.remove("open");
    settingsPanel.setAttribute("aria-hidden","true");
  }
  function toggleSettings(){
    if(settingsPanel.classList.contains("open")) closeSettings();
    else openSettings();
  }
  function openDrawer(){
    drawerBackdrop.classList.add("open");
    drawer.classList.add("open");
    closeSettings();
    closeContextMenu();
  }
  function closeDrawer(){
    drawerBackdrop.classList.remove("open");
    drawer.classList.remove("open");
  }
  function openModal(modalEl){
    modalBackdrop.classList.add("open");
    modalEl.classList.add("open");
  }
  function closeModal(modalEl){
    modalEl.classList.remove("open");
    const anyOpen =
      linkCreateModal.classList.contains("open") ||
      linkManageModal.classList.contains("open") ||
      styleModal.classList.contains("open") ||
      trashModal.classList.contains("open") ||
      tagStylesModal.classList.contains("open");
    if(!anyOpen) modalBackdrop.classList.remove("open");
  }

  settingsPanel.addEventListener("pointerdown", (e)=> e.stopPropagation());
  drawer.addEventListener("pointerdown", (e)=> e.stopPropagation());
  [linkCreateModal, linkManageModal, styleModal, trashModal, tagStylesModal].forEach(m=>{
    m.addEventListener("pointerdown", (e)=> e.stopPropagation());
  });

  // ---------- Zoom / Camera ----------
  function formatZoomLabel(){
    const pct = B().zoom * 100;
    if(pct >= 10) return Math.round(pct) + "%";
    if(pct >= 1) return pct.toFixed(1) + "%";
    return pct.toFixed(2) + "%";
  }
  function updateGridAlpha(){
    if(!B().showGrid) return;
    const z = B().zoom;
    const a = clamp(0.12 * Math.min(1, z * 1.3), 0.015, 0.12);
    viewport.style.setProperty("--gridA", a.toFixed(3));
  }
  function applyTransforms(){
    const z = B().zoom;
    const snap = (z >= 0.55 && z <= 1.8);
    const tx = snap ? Math.round(B().pan.x) : B().pan.x;
    const ty = snap ? Math.round(B().pan.y) : B().pan.y;

    camera.style.transform = `translate3d(${tx}px, ${ty}px, 0)`;

    if(SUPPORTS_CSS_ZOOM){
      world.style.zoom = String(z);
      world.style.transform = "scale(1)";
    }else{
      world.style.zoom = "1";
      world.style.transform = `scale(${z})`;
    }

    zoomLabel.textContent = formatZoomLabel();
    updateGridAlpha();
  }
  function zoomAt(clientX, clientY, nextZoom){
    nextZoom = clamp(nextZoom, MIN_ZOOM, MAX_ZOOM);
    const rect = viewport.getBoundingClientRect();
    const sx = clientX - rect.left;
    const sy = clientY - rect.top;

    const oldZ = B().zoom;
    const wx = (sx - B().pan.x) / oldZ;
    const wy = (sy - B().pan.y) / oldZ;

    B().zoom = nextZoom;
    B().pan.x = sx - wx * B().zoom;
    B().pan.y = sy - wy * B().zoom;

    applyTransforms();
    renderEdges();
  }
  function clientToWorld(clientX, clientY){
    const rect = viewport.getBoundingClientRect();
    const sx = clientX - rect.left;
    const sy = clientY - rect.top;
    return { x: (sx - B().pan.x) / B().zoom, y: (sy - B().pan.y) / B().zoom };
  }
  function worldToClient(wx, wy){
    const rect = viewport.getBoundingClientRect();
    return { x: rect.left + B().pan.x + wx * B().zoom, y: rect.top + B().pan.y + wy * B().zoom };
  }
  function nodeCenter(id){
    const n = B().nodes[id];
    if(!n) return {x:0,y:0};
    return { x: n.x + 130, y: n.y + 36 };
  }
  function centerOnNode(id){
    const rect = viewport.getBoundingClientRect();
    const c = nodeCenter(id);
    B().pan.x = rect.width/2  - c.x * B().zoom;
    B().pan.y = rect.height/2 - c.y * B().zoom;
    applyTransforms();
  }

  // ---------- Tree helpers ----------
  function isHiddenByAncestor(id){
    let cur = B().nodes[id];
    while(cur && cur.parentId){
      const p = B().nodes[cur.parentId];
      if(!p) break;
      if(p.collapsed) return true;
      cur = p;
    }
    return false;
  }
  function isInSubtree(rootId, searchId){
    const root = B().nodes[rootId];
    if(!root) return false;
    const stack = [...(root.children || [])];
    while(stack.length){
      const id = stack.pop();
      if(id === searchId) return true;
      const n = B().nodes[id];
      if(n) stack.push(...(n.children || []));
    }
    return false;
  }

  // ---------- Progress ----------
  function subtreeStats(id, memo){
    memo ||= new Map();
    if(memo.has(id)) return memo.get(id);
    const n = B().nodes[id];
    if(!n){ const z = {done:0,total:0}; memo.set(id,z); return z; }
    let total = 1;
    let done = n.done ? 1 : 0;
    for(const cid of (n.children||[])){
      const st = subtreeStats(cid, memo);
      total += st.total; done += st.done;
    }
    const res = {done,total};
    memo.set(id,res);
    return res;
  }

  // ---------- CRUD ----------
  function createNode({text="کار جدید", parentId=null, x=5000, y=5000}){
    const id = newId();
    B().nodes[id] = normalizeNode({
      id, parentId, text,
      done:false, collapsed:false, children:[],
      x,y, createdAt: now(),
      note:"",
      tags:"",
      style:{ body:null, cover:null, template:"card", fontFamily:"", fontSize:15, propagate:false }
    });
    if(parentId){
      B().nodes[parentId].children.push(id);
    }else{
      B().roots.push(id);
    }
    return id;
  }

  function trashNode(id){
    const n = B().nodes[id];
    if(!n) return;

    const toTrash = new Set();
    const stack = [id];
    while(stack.length){
      const cur = stack.pop();
      const cn = B().nodes[cur];
      if(!cn) continue;
      toTrash.add(cur);
      stack.push(...(cn.children||[]));
    }

    if(n.parentId){
      const p = B().nodes[n.parentId];
      if(p) p.children = (p.children||[]).filter(cid => cid !== id);
    }else{
      B().roots = B().roots.filter(rid => rid !== id);
    }

    B().links = (B().links||[]).filter(l => !toTrash.has(l.fromId) && !toTrash.has(l.toId));

    const ts = now();
    for(const nid of toTrash){
      B().trash.nodes[nid] = { ...B().nodes[nid], trashedAt: ts };
      delete B().nodes[nid];
    }

    if(B().selectedId && toTrash.has(B().selectedId)) B().selectedId = null;
    if(app.linker.active && app.linker.fromId && toTrash.has(app.linker.fromId)) cancelLinkMode();
  }

  function restoreFromTrash(nodeId){
    const t = B().trash.nodes[nodeId];
    if(!t) return;

    const allTrash = B().trash.nodes;
    const stack = [nodeId];
    const restoredIds = new Set();
    while(stack.length){
      const cur = stack.pop();
      const item = allTrash[cur];
      if(!item) continue;
      restoredIds.add(cur);
      for(const cid of (item.children||[])){
        if(allTrash[cid]) stack.push(cid);
      }
    }

    pushHistory();
    for(const rid of restoredIds){
      const item = deepClone(allTrash[rid]);
      delete item.trashedAt;
      B().nodes[rid] = normalizeNode(item);
    }

    B().nodes[nodeId].parentId = null;
    B().roots.push(nodeId);

    for(const rid of restoredIds) delete B().trash.nodes[rid];

    B().selectedId = nodeId;
    syncUI();
    centerOnNode(nodeId);
  }

  function addRoot(){
    if(guardEdit()) return;
    pushHistory();
    const rect = viewport.getBoundingClientRect();
    const centerWorld = clientToWorld(rect.left + rect.width/2, rect.top + rect.height/2);
    const id = createNode({
      text:"نود ریشه",
      x: clamp(centerWorld.x, 80, 9800),
      y: clamp(centerWorld.y, 80, 9800)
    });
    B().selectedId = id;
    syncUI();
    focusTitle(id);
  }
  function addChild(parentId){
    if(guardEdit()) return;
    const p = B().nodes[parentId];
    if(!p) return;
    pushHistory();
    p.collapsed = false;
    const idx = (p.children||[]).length;

    const id = createNode({
      text:"زیرمجموعه",
      parentId,
      x: clamp(p.x + 320, 80, 9800),
      y: clamp(p.y + idx*120, 80, 9800)
    });
    B().selectedId = id;
    syncUI();
    focusTitle(id);
  }
  function addSibling(id){
    if(guardEdit()) return;
    const n = B().nodes[id];
    if(!n) return;
    if(!n.parentId) return addRoot();
    addChild(n.parentId);
  }
  function toggleDone(id){
    if(guardEdit()) return;
    const n = B().nodes[id];
    if(!n) return;
    pushHistory();
    n.done = !n.done;
    syncUI();
  }
  function toggleCollapse(id){
    if(guardEdit()) return;
    const n = B().nodes[id];
    if(!n) return;
    pushHistory();
    n.collapsed = !n.collapsed;
    syncUI();
  }
  function setText(id, text){
    if(guardEdit()) return;
    const n = B().nodes[id];
    if(!n) return;
    pushHistory();
    n.text = (text||"").trim() || "بدون عنوان";
    syncUI();
  }

  // ---------- Reparent helpers ----------
  function detachFromParent(id){
    const n = B().nodes[id];
    if(!n) return;
    if(n.parentId){
      const p = B().nodes[n.parentId];
      if(p) p.children = (p.children||[]).filter(cid => cid !== id);
    }else{
      B().roots = B().roots.filter(rid => rid !== id);
    }
    n.parentId = null;
  }
  function attachToParent(id, newParentId){
    const n = B().nodes[id];
    const p = B().nodes[newParentId];
    if(!n || !p) return;
    p.collapsed = false;
    n.parentId = newParentId;
    p.children = p.children || [];
    p.children.push(id);
  }
  function reparentNode(id, newParentId){
    const n = B().nodes[id];
    if(!n) return;
    if(newParentId === id) return;
    if(isInSubtree(id, newParentId)) return;

    detachFromParent(id);
    attachToParent(id, newParentId);

    const p = B().nodes[newParentId];
    n.x = clamp(p.x + 320, 40, 9800);
    n.y = clamp(n.y, 40, 9800);
  }

  // ---------- Links ----------
  function startLinkMode(fromId, type, label){
    if(!fromId || !B().nodes[fromId]) return;
    app.linker.active = true;
    app.linker.fromId = fromId;
    app.linker.type = type || "work";
    app.linker.label = (label||"").trim();

    linkModePill.style.display = "inline-flex";
    linkModePill.className = "pill warn";
    linkModePill.textContent = "حالت لینک فعال: مقصد را انتخاب کن… (Esc لغو)";

    closeContextMenu(); closeSettings(); closeDrawer();
  }
  function cancelLinkMode(){
    app.linker.active = false;
    app.linker.fromId = null;
    app.linker.type = "work";
    app.linker.label = "";
    linkModePill.style.display = "none";
    linkModePill.textContent = "";
  }
  function createLink(fromId, toId, type, label){
    if(guardEdit()) return;
    if(!fromId || !toId) return;
    if(fromId === toId) return;
    if(!B().nodes[fromId] || !B().nodes[toId]) return;

    const cleanLabel = (label||"").trim();
    const cleanType = type || "work";

    const exists = (B().links||[]).some(l =>
      l.fromId === fromId &&
      l.toId === toId &&
      (l.type||"work") === cleanType &&
      (l.label||"") === cleanLabel
    );
    if(exists) return;

    pushHistory();
    B().links.push({
      id: newId(),
      fromId, toId,
      type: cleanType,
      label: cleanLabel,
      createdAt: now()
    });
    syncUI();

    linkModePill.className = "pill good";
    linkModePill.textContent = "لینک ساخته شد ✅";
    setTimeout(()=>{
      if(app.linker.active){
        linkModePill.className = "pill warn";
        linkModePill.textContent = "حالت لینک فعال: مقصد را انتخاب کن… (Esc لغو)";
      }else{
        linkModePill.style.display = "none";
      }
    }, 700);
  }
  function deleteLink(linkId){
    if(guardEdit()) return;
    pushHistory();
    B().links = (B().links||[]).filter(l => l.id !== linkId);
    syncUI();
  }
  function linksForNode(nodeId){
    const all = B().links || [];
    return { out: all.filter(l => l.fromId === nodeId), inc: all.filter(l => l.toId === nodeId) };
  }
  function openManageLinksModal(){
    const id = B().selectedId;
    if(!id){ alert("اول یک نود را انتخاب کن."); return; }
    renderManageLinks(id);
    openModal(linkManageModal);
  }
  function renderManageLinks(nodeId){
    const n = B().nodes[nodeId];
    const name = n?.text || "بدون عنوان";
    const {out, inc} = linksForNode(nodeId);

    const renderItem = (l, dir) => {
      const otherId = (dir==="out") ? l.toId : l.fromId;
      const other = B().nodes[otherId];
      const otherName = other?.text || "(حذف شده)";
      const type = l.type || "work";
      const label = (l.label||"").trim();

      return `
        <div style="border:1px solid rgba(16,24,40,.08); border-radius:14px; padding:10px; margin:8px 0; background:white;">
          <div style="display:flex; justify-content:space-between; gap:10px; flex-wrap:wrap;">
            <div>
              <b>${type === "work" ? "Work" : "Semantic"}</b>
              ${label ? `<span class="badge tag" style="margin-inline-start:8px;">${escHtml(label)}</span>` : `<span class="small" style="margin-inline-start:8px;">بدون برچسب</span>`}
            </div>
            <div class="small">${dir==="out" ? "→" : "←"} ${escHtml(otherName)}</div>
          </div>
          <div style="display:flex; gap:10px; justify-content:flex-end; margin-top:10px;">
            <button class="btn" data-act="jump" data-id="${escHtml(otherId)}">رفتن</button>
            <button class="btn danger" data-act="del" data-id="${escHtml(l.id)}" ${isReadOnly() ? "disabled" : ""}>حذف</button>
          </div>
        </div>
      `;
    };

    linkManageBody.innerHTML = `
      <div class="small">نود: <b>${escHtml(name)}</b></div>
      <div class="small" style="margin:8px 0;">${out.length} خروجی · ${inc.length} ورودی</div>

      <div style="margin-top:10px; font-weight:900;">خروجی‌ها</div>
      ${out.length ? out.map(l => renderItem(l,"out")).join("") : `<div class="small">هیچ خروجی‌ای ندارد.</div>`}

      <div style="margin-top:14px; font-weight:900;">ورودی‌ها</div>
      ${inc.length ? inc.map(l => renderItem(l,"inc")).join("") : `<div class="small">هیچ ورودی‌ای ندارد.</div>`}
    `;

    linkManageBody.querySelectorAll('button[data-act="jump"]').forEach(btn=>{
      btn.addEventListener("click", ()=>{
        const tid = btn.getAttribute("data-id");
        if(B().nodes[tid]){
          selectNode(tid);
          centerOnNode(tid);
          closeModal(linkManageModal);
        }
      });
    });

    linkManageBody.querySelectorAll('button[data-act="del"]').forEach(btn=>{
      btn.addEventListener("click", ()=>{
        if(isReadOnly()) return guardEdit();
        const lid = btn.getAttribute("data-id");
        if(confirm("این ارتباط حذف شود؟")){
          deleteLink(lid);
          renderManageLinks(nodeId);
        }
      });
    });
  }

  // ---------- Tag styles modal ----------
  function openTagStylesModal(){
    renderTagStyles();
    openModal(tagStylesModal);
  }
  function renderTagStyles(){
    const ts = B().tagStyles || {};
    const tags = Object.keys(ts).sort((a,b)=>a.localeCompare(b,"fa"));
    if(!tags.length){
      tagStylesBody.innerHTML = `<div class="small">هیچ استایلی برای تگ‌ها ذخیره نشده.</div>`;
      return;
    }
    tagStylesBody.innerHTML = tags.map(tag=>{
      const st = ts[tag] || {};
      const tpl = st.template || "card";
      const body = st.body || "";
      const cover = st.cover || "";
      const ff = st.fontFamily || "";
      const fs = st.fontSize || 15;

      return `
        <div style="border:1px solid rgba(16,24,40,.10); border-radius:16px; padding:12px; background:white; margin:10px 0;">
          <div style="display:flex; align-items:center; justify-content:space-between; gap:10px; flex-wrap:wrap;">
            <div><span class="badge tag">#${escHtml(tag)}</span></div>
            <button class="btn danger" data-act="delTag" data-tag="${escHtml(tag)}" ${isReadOnly() ? "disabled" : ""}>حذف استایل تگ</button>
          </div>
          <div class="small" style="margin-top:8px;">
            Template: <b>${escHtml(tpl)}</b> · FontSize: <b>${fs}</b>
            ${ff ? ` · Font: <b>${escHtml(ff)}</b>` : ""}
            ${body ? ` · Body: <b>${escHtml(body)}</b>` : ""}
            ${cover ? ` · Cover: <b>${escHtml(cover)}</b>` : ""}
          </div>
        </div>
      `;
    }).join("");

    tagStylesBody.querySelectorAll('button[data-act="delTag"]').forEach(btn=>{
      btn.addEventListener("click", ()=>{
        if(isReadOnly()) return guardEdit();
        const tag = btn.getAttribute("data-tag");
        if(!tag) return;
        if(confirm(`استایل تگ «${tag}» حذف شود؟`)){
          pushHistory();
          delete B().tagStyles[tag];
          syncUI();
          renderTagStyles();
        }
      });
    });
  }

  // ---------- Style / Templates / Fonts / Notes ----------
  const PALETTE = [
    "#ffffff","#f3f4f6","#fde2e2","#ffe8cc","#fff3bf","#d3f9d8","#d0ebff","#e5dbff","#ffe3f1",
    "#0e9b89","#2e60a0","#7c3aed","#c026d3","#ea580c","#16a34a","#0f172a"
  ];
  function renderPalette(container, onPick){
    container.innerHTML = "";
    for(const c of PALETTE){
      const s = document.createElement("div");
      s.className = "swatch";
      s.style.background = c;
      s.title = c;
      s.addEventListener("click", ()=> onPick(c));
      container.appendChild(s);
    }
  }
  renderPalette(bodyPalette, c => { bodyColorInput.value = c; const id = app.styleEdit.nodeId; if(id) previewStyle(id); });
  renderPalette(coverPalette, c => { coverColorInput.value = c; const id = app.styleEdit.nodeId; if(id) previewStyle(id); });

  function parseTags(s){
    return (s||"")
      .split(",")
      .map(x => x.trim())
      .filter(Boolean)
      .join(", ");
  }

  function getStylePatchFromInputs(){
    const chosenFont = (customFontInput.value || "").trim() || (fontSelect.value || "");
    const fs = clamp(parseInt(fontSizeInput.value || "15", 10) || 15, 10, 32);
    return {
      template: templateSelect.value,
      body: bodyColorInput.value,
      cover: coverColorInput.value,
      fontFamily: chosenFont,
      fontSize: fs,
      note: noteInput.value || "",
      tags: parseTags(tagsInput.value || ""),
      propagate: !!chkPropagate.checked,
      applyToTag: !!chkApplyToTag.checked
    };
  }

  function previewStyle(nodeId){
    const n = B().nodes[nodeId];
    if(!n) return;

    const patch = getStylePatchFromInputs();

    n.style = n.style || {};
    n.style.template = patch.template || "card";
    n.style.body = patch.body || null;
    n.style.cover = patch.cover || null;
    n.style.fontFamily = patch.fontFamily || "";
    n.style.fontSize = patch.fontSize || 15;
    n.style.propagate = !!patch.propagate;

    n.note = patch.note || "";
    n.tags = patch.tags || "";

    render(); // فقط رندر - بدون history/save
  }

  function restoreStyleSnapshot(){
    const id = app.styleEdit.nodeId;
    const snap = app.styleEdit.snapshot;
    if(!id || !snap || !B().nodes[id]) return;

    B().nodes[id].style = deepClone(snap.style || {});
    B().nodes[id].note  = snap.note || "";
    B().nodes[id].tags  = snap.tags || "";
    render();
  }

  function openStyleModal(nodeId){
    const n = B().nodes[nodeId];
    if(!n) return;

    app.styleEdit.nodeId = nodeId;
    app.styleEdit.snapshot = {
      style: deepClone(n.style || {}),
      note: n.note || "",
      tags: n.tags || ""
    };

    templateSelect.value = (n.style?.template || "card");
    bodyColorInput.value = n.style?.body || "#ffffff";
    coverColorInput.value = n.style?.cover || "#0e9b89";
    fontSelect.value = n.style?.fontFamily || "";
    customFontInput.value = "";
    fontSizeInput.value = String(n.style?.fontSize ?? 15);
    noteInput.value = n.note || "";
    tagsInput.value = n.tags || "";

    chkPropagate.checked = !!(n.style?.propagate);
    chkApplyToTag.checked = false;

    styleModalTitle.textContent = `نود: ${n.text || "بدون عنوان"}`;
    openModal(styleModal);
  }

  // Live preview
  const liveInputs = [templateSelect, fontSelect, customFontInput, fontSizeInput, bodyColorInput, coverColorInput, noteInput, tagsInput, chkPropagate];
  liveInputs.forEach(el=>{
    el.addEventListener("input", ()=>{ const id = app.styleEdit.nodeId; if(id) previewStyle(id); });
    el.addEventListener("change", ()=>{ const id = app.styleEdit.nodeId; if(id) previewStyle(id); });
  });

  // ---------- Effective style (tree + tag) ----------
  function getTagStyleForNode(n){
    const tags = parseTags(n.tags).split(",").map(t=>t.trim()).filter(Boolean);
    if(!tags.length) return null;
    for(const t of tags){
      if(B().tagStyles && B().tagStyles[t]) return B().tagStyles[t];
    }
    return null;
  }

  function getInheritedStyle(n){
    // walk ancestors, collect propagate styles
    const merged = {};
    let cur = n;
    while(cur && cur.parentId){
      const p = B().nodes[cur.parentId];
      if(!p) break;
      if(p.style && p.style.propagate){
        // parent propagate provides base
        if(p.style.template) merged.template = p.style.template;
        if(p.style.body) merged.body = p.style.body;
        if(p.style.cover) merged.cover = p.style.cover;
        if(p.style.fontFamily) merged.fontFamily = p.style.fontFamily;
        if(p.style.fontSize) merged.fontSize = p.style.fontSize;
      }
      cur = p;
    }
    return merged;
  }

  function getEffectiveStyle(n){
    const inherited = getInheritedStyle(n);
    const tagStyle = getTagStyleForNode(n) || {};
    const local = n.style || {};

    // priority: inherited -> tagStyle -> local
    const eff = {
      template: inherited.template || tagStyle.template || local.template || "card",
      body: local.body || tagStyle.body || inherited.body || null,
      cover: local.cover || tagStyle.cover || inherited.cover || null,
      fontFamily: local.fontFamily || tagStyle.fontFamily || inherited.fontFamily || "",
      fontSize: local.fontSize || tagStyle.fontSize || inherited.fontSize || 15,
      propagate: !!local.propagate
    };
    return eff;
  }

  // ---------- Layout ----------
  function autoArrange(){
    if(guardEdit()) return;
    pushHistory();
    let cursorY = 400;
    for(const rid of B().roots){
      cursorY = layoutSubtree(rid, 500, cursorY) + 160;
    }
    syncUI();
  }
  function layoutSubtree(id, x, y){
    const n = B().nodes[id];
    if(!n) return y;
    const children = n.children || [];
    n.x = clamp(x, 80, 9800);

    if(children.length === 0 || n.collapsed){
      n.y = clamp(y, 80, 9800);
      return y;
    }

    let childY = y;
    const childCenters = [];
    for(const cid of children){
      childY = layoutSubtree(cid, x + 320, childY);
      childCenters.push(B().nodes[cid].y);
      childY += 120;
    }

    const minY = Math.min(...childCenters);
    const maxY = Math.max(...childCenters);
    n.y = clamp((minY + maxY) / 2, 80, 9800);

    return Math.max(y, childY - 120);
  }

  // ---------- Render ----------
  function clear(el){ while(el.firstChild) el.removeChild(el.firstChild); }

  function renderEdges(){
    clear(edgesSvg);

    // defs arrow
    const defs = document.createElementNS("http://www.w3.org/2000/svg", "defs");
    const marker = document.createElementNS("http://www.w3.org/2000/svg", "marker");
    marker.setAttribute("id", "arrowHead");
    marker.setAttribute("markerWidth", "10");
    marker.setAttribute("markerHeight", "10");
    marker.setAttribute("refX", "9");
    marker.setAttribute("refY", "5");
    marker.setAttribute("orient", "auto");
    marker.setAttribute("markerUnits", "strokeWidth");
    const arrowPath = document.createElementNS("http://www.w3.org/2000/svg", "path");
    arrowPath.setAttribute("d", "M 0 0 L 10 5 L 0 10 z");
    arrowPath.setAttribute("fill", "rgba(18,33,47,.55)");
    marker.appendChild(arrowPath);
    defs.appendChild(marker);
    edgesSvg.appendChild(defs);

    // tree edges
    if(!B().networkMode){
      for(const id in B().nodes){
        const n = B().nodes[id];
        if(!n.parentId) continue;
        if(isHiddenByAncestor(n.id)) continue;

        const p = B().nodes[n.parentId];
        if(!p) continue;
        if(p.collapsed) continue;
        if(isHiddenByAncestor(p.id)) continue;

        const start = { x: p.x + 260, y: p.y + 36 };
        const end   = { x: n.x,       y: n.y + 36 };

        const dx = Math.max(90, (end.x - start.x) * 0.5);
        const c1 = { x: start.x + dx, y: start.y };
        const c2 = { x: end.x   - dx, y: end.y   };

        const path = document.createElementNS("http://www.w3.org/2000/svg", "path");
        path.setAttribute("d", `M ${start.x} ${start.y} C ${c1.x} ${c1.y}, ${c2.x} ${c2.y}, ${end.x} ${end.y}`);
        path.setAttribute("class", "edge" + ((p.done || n.done) ? " done" : ""));
        edgesSvg.appendChild(path);
      }
    }

    // custom links
    if(B().showLinks){
      for(const l of (B().links||[])){
        const from = B().nodes[l.fromId];
        const to   = B().nodes[l.toId];
        if(!from || !to) continue;
        if(isHiddenByAncestor(from.id) || isHiddenByAncestor(to.id)) continue;

        const a = { x: from.x + 130, y: from.y + 36 };
        const b = { x: to.x   + 130, y: to.y   + 36 };

        const dx = b.x - a.x;
        const dy = b.y - a.y;
        const bend = clamp(Math.hypot(dx, dy) * 0.25, 60, 240);

        const c1 = { x: a.x + (dx >= 0 ? bend : -bend), y: a.y };
        const c2 = { x: b.x - (dx >= 0 ? bend : -bend), y: b.y };

        const path = document.createElementNS("http://www.w3.org/2000/svg", "path");
        path.setAttribute("d", `M ${a.x} ${a.y} C ${c1.x} ${c1.y}, ${c2.x} ${c2.y}, ${b.x} ${b.y}`);
        path.setAttribute("class", `link ${(l.type||"work")}`);
        path.setAttribute("marker-end", "url(#arrowHead)");
        edgesSvg.appendChild(path);

        const label = (l.label||"").trim();
        if(label){
          const tx = (a.x + b.x) / 2;
          const ty = (a.y + b.y) / 2 - 8;
          const text = document.createElementNS("http://www.w3.org/2000/svg", "text");
          text.setAttribute("x", tx);
          text.setAttribute("y", ty);
          text.setAttribute("text-anchor", "middle");
          text.setAttribute("class", "link-label");
          text.textContent = label;
          edgesSvg.appendChild(text);
        }
      }
    }
  }

  function render(){
    clear(nodesLayer);
    renderEdges();

    const memo = new Map();
    for(const id in B().nodes){
      renderNode(B().nodes[id], memo);
    }

    applyTransforms();
    updateGrid();
    updateButtonsText();
  }

  function renderNode(n, memo){
    if(isHiddenByAncestor(n.id)) return;

    const eff = getEffectiveStyle(n);
    const tpl = eff.template || "card";

    const el = document.createElement("div");
    el.className =
      "node" +
      ` tpl-${tpl}` +
      (n.id === B().selectedId ? " selected" : "") +
      (n.done ? " done" : "") +
      (n.id === app.drag.dropTargetId ? " drop-target" : "");
    el.style.left = n.x + "px";
    el.style.top  = n.y + "px";
    el.dataset.id = n.id;

    if(eff.body) el.style.background = eff.body;
    if(eff.fontFamily) el.style.fontFamily = eff.fontFamily;

    const st = subtreeStats(n.id, memo);
    const percent = st.total ? Math.round((st.done / st.total) * 100) : 0;
    const hasNote = (n.note || "").trim().length > 0;
    const tags = parseTags(n.tags);
    const hasTag = !!tags;

    el.innerHTML = `
      <div class="hdr" data-role="hdr">
        <div class="drag-handle" data-role="drag" title="برای جابجایی بگیر و بکش">⠿</div>
        <input class="checkbox" type="checkbox" ${n.done ? "checked" : ""} data-role="done" />
        <div class="title" contenteditable="${isReadOnly() ? "false" : "true"}" spellcheck="false" data-role="title"></div>
        <button class="mini icon" data-role="collapse" title="جمع/باز" ${isReadOnly() ? "disabled" : ""}>${n.collapsed ? "▸" : "▾"}</button>
        <button class="mini icon" data-role="style" title="استایل" ${isReadOnly() ? "disabled" : ""}>🎨</button>
      </div>

      <div class="meta">
        <div style="display:flex; gap:8px; flex-wrap:wrap; align-items:center;">
          <span class="badge">${st.done}/${st.total} · ${percent}%</span>
          ${hasNote ? `<span class="badge note">📝 Note</span>` : ``}
          ${hasTag ? `<span class="badge tag">🏷 ${escHtml(tags)}</span>` : ``}
          ${eff.propagate ? `<span class="badge" title="این نود استایل را به زیرمجموعه‌ها می‌دهد">🌿 درختی</span>` : ``}
        </div>
        <div style="display:flex; gap:8px;">
          <button class="mini" data-role="addChild" ${isReadOnly() ? "disabled" : ""}>➕ زیرکار</button>
          <button class="mini" data-role="delete" style="color:var(--danger)" ${isReadOnly() ? "disabled" : ""}>حذف</button>
        </div>
      </div>
    `;

    const hdr = el.querySelector('[data-role="hdr"]');
    if(eff.cover){
      hdr.style.background = `linear-gradient(180deg, ${eff.cover}22, ${eff.cover}11)`;
    }

    const title = el.querySelector('[data-role="title"]');
    title.textContent = n.text;
    title.style.fontSize = (eff.fontSize ? eff.fontSize : 15) + "px";

    // select + handle drag + linking
    el.addEventListener("pointerdown", (e) => {
      if(e.button === 2) return;
      selectNode(n.id);

      if(app.linker.active){
        e.preventDefault();
        e.stopPropagation();
        const fromId = app.linker.fromId;
        const toId = n.id;
        if(fromId && fromId !== toId){
          createLink(fromId, toId, app.linker.type, app.linker.label);
        }
        return;
      }

      const isTitle = e.target.closest?.('[data-role="title"]');
      const isDone  = e.target.closest?.('[data-role="done"]');
      const isBtnOrInput = e.target.closest?.('button, input, textarea, select');
      const isDragHandle = e.target.closest?.('[data-role="drag"]');
      const inHdr = e.target.closest?.('[data-role="hdr"]');

      // عنوان/چک‌باکس: فقط ادیت/تیک
      if(isTitle || isDone) return;

      // دکمه‌ها: نه (جز drag-handle که div است)
      if(isBtnOrInput && !isDragHandle) return;

      // فقط از handle یا از فضای هدر (به جز title) اجازه‌ی drag بده
      if(!(isDragHandle || inHdr)) return;

      viewport.setPointerCapture(e.pointerId);
      app.drag.mode = (e.pointerType === "mouse") ? "node" : "node_pending";
      app.drag.nodeId = n.id;
      app.drag.pointerId = e.pointerId;
      app.drag.startClient = { x: e.clientX, y: e.clientY };
      app.drag.didMutate = false;
      app.drag.dropTargetId = null;
      app.drag.draggingEl = el;

      const w = clientToWorld(e.clientX, e.clientY);
      app.drag.grabOffset = { x: w.x - n.x, y: w.y - n.y };

      if(app.drag.mode === "node"){
        el.classList.add("dragging");
      }
    });

    el.querySelector('[data-role="done"]').addEventListener("change", ()=> toggleDone(n.id));
    el.querySelector('[data-role="collapse"]').addEventListener("click", (e)=>{ e.stopPropagation(); toggleCollapse(n.id); });
    el.querySelector('[data-role="addChild"]').addEventListener("click", (e)=>{ e.stopPropagation(); addChild(n.id); });
    el.querySelector('[data-role="style"]').addEventListener("click", (e)=>{ e.stopPropagation(); openStyleModal(n.id); });
    el.querySelector('[data-role="delete"]').addEventListener("click", (e)=> {
      e.stopPropagation();
      if(isReadOnly()) return guardEdit();
      if(confirm("این نود و زیرمجموعه‌ها به سطل زباله منتقل شوند؟")){
        pushHistory();
        trashNode(n.id);
        syncUI();
      }
    });

    title.addEventListener("focus", ()=> selectNode(n.id));
    title.addEventListener("keydown", (e)=>{
      if(e.key === "Enter"){ e.preventDefault(); title.blur(); addChild(n.id); }
      if(e.key === "Escape"){ e.preventDefault(); title.textContent = B().nodes[n.id]?.text ?? ""; title.blur(); }
    });
    title.addEventListener("blur", ()=>{
      const text = title.textContent;
      if((text||"").trim() !== (B().nodes[n.id]?.text||"").trim()){
        setText(n.id, text);
      }
    });

    nodesLayer.appendChild(el);
  }

  function syncUI(skipSave=false){
    render();
    if(!skipSave){
      saveLocal();
      scheduleCloudSave(false); // ✅ هر تغییر برای همه آنلاین می‌رود
    }
  }

  function selectNode(id){
    if(B().selectedId === id) return;
    B().selectedId = id;
    render();
  }

  function focusTitle(id){
    requestAnimationFrame(()=>{
      const el = nodesLayer.querySelector(`.node[data-id="${cssEsc(id)}"]`);
      if(!el) return;
      const t = el.querySelector('[data-role="title"]');
      if(isReadOnly()) return;
      t?.focus();
      if(t){
        const range = document.createRange();
        range.selectNodeContents(t);
        range.collapse(false);
        const sel = window.getSelection();
        sel.removeAllRanges();
        sel.addRange(range);
      }
    });
  }

  // ---------- Drop target for reparent ----------
  function findDropTarget(clientX, clientY, draggedId){
    let best = null;
    let bestDist = Infinity;

    for(const id in B().nodes){
      if(id === draggedId) continue;
      if(isHiddenByAncestor(id)) continue;

      const n = B().nodes[id];
      if(!n) continue;
      if(isInSubtree(draggedId, id)) continue;

      const topLeft = worldToClient(n.x, n.y);
      const w = 260 * B().zoom;
      const h = 120 * B().zoom;

      const inside =
        clientX >= topLeft.x && clientX <= topLeft.x + w &&
        clientY >= topLeft.y && clientY <= topLeft.y + h;
      if(!inside) continue;

      const cx = topLeft.x + w/2;
      const cy = topLeft.y + h/2;
      const d = Math.hypot(clientX - cx, clientY - cy);
      if(d < bestDist){
        bestDist = d;
        best = id;
      }
    }
    return best;
  }

  // ---------- Context menu ----------
  function openContextMenu(clientX, clientY){
    const under = document.elementFromPoint(clientX, clientY);
    const hitNode = under?.closest?.(".node");
    if(hitNode?.dataset?.id) selectNode(hitNode.dataset.id);

    const rect = viewport.getBoundingClientRect();
    const x = clientX - rect.left;
    const y = clientY - rect.top;
    ctxMenu.style.left = x + "px";
    ctxMenu.style.top  = y + "px";
    ctxMenu.classList.add("open");
  }
  function closeContextMenu(){ ctxMenu.classList.remove("open"); }

  ctxMenu.addEventListener("pointerup", (e)=>{
    e.preventDefault();
    e.stopPropagation();
    const item = e.target.closest?.(".item[data-act]");
    if(!item) return;
    const act = item.dataset.act;
    const id = B().selectedId;

    if(act === "addChild"){ if(id) addChild(id); else addRoot(); }
    if(act === "addSibling"){ if(id) addSibling(id); }
    if(act === "toggleDone"){ if(id) toggleDone(id); }
    if(act === "toggleCollapse"){ if(id) toggleCollapse(id); }
    if(act === "style"){ if(id) openStyleModal(id); }
    if(act === "startLink"){ if(id) startLinkMode(id, "work", ""); }
    if(act === "manageLinks"){ openManageLinksModal(); }
    if(act === "delete"){
      if(!id) return;
      if(isReadOnly()) return guardEdit();
      if(confirm("این نود و زیرمجموعه‌ها به سطل زباله منتقل شوند؟")){
        pushHistory();
        trashNode(id);
        syncUI();
      }
    }
    closeContextMenu();
  });
  ctxMenu.addEventListener("pointerdown", (e)=> e.stopPropagation());

  // ---------- Grid / Buttons ----------
  function updateGrid(){
    if(B().showGrid) viewport.classList.remove("no-grid");
    else viewport.classList.add("no-grid");
    updateGridAlpha();
  }
  function updateButtonsText(){
    btnToggleLinks.textContent = `🔗 لینک‌ها: ${B().showLinks ? "روشن" : "خاموش"}`;
    btnToggleNetwork.textContent = `🧠 مد شبکه: ${B().networkMode ? "روشن" : "خاموش"}`;
  }

  // ---------- Export / Import ----------
  function exportJSON(obj, filename){
    const blob = new Blob([JSON.stringify(obj, null, 2)], {type:"application/json"});
    const a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = filename;
    document.body.appendChild(a);
    a.click();
    a.remove();
    setTimeout(()=> URL.revokeObjectURL(a.href), 1500);
  }
  function importJSONFromFile(file, onLoad){
    const reader = new FileReader();
    reader.onload = ()=>{
      try{ onLoad(JSON.parse(reader.result)); }
      catch(e){ alert("فایل JSON معتبر نیست."); }
    };
    reader.readAsText(file);
  }

  // ---------- Trash modal ----------
  function openTrashModal(){
    renderTrash();
    openModal(trashModal);
  }
  function renderTrash(){
    const items = Object.values(B().trash.nodes || {});
    items.sort((a,b)=>(b.trashedAt||0)-(a.trashedAt||0));

    if(items.length === 0){
      trashBody.innerHTML = `<div class="small">سطل زباله خالی است.</div>`;
      return;
    }

    trashBody.innerHTML = items.map(n => `
      <div style="border:1px solid rgba(16,24,40,.10); border-radius:16px; padding:12px; background:white; margin:10px 0;">
        <div style="display:flex; justify-content:space-between; gap:10px; flex-wrap:wrap;">
          <div><b>${escHtml(n.text || "بدون عنوان")}</b></div>
          <button class="btn primary" data-act="restore" data-id="${escHtml(n.id)}" ${isReadOnly() ? "disabled" : ""}>بازیابی</button>
        </div>
        <div class="small" style="margin-top:8px;">
          ID: ${escHtml(n.id)} · ${new Date(n.trashedAt||now()).toLocaleString("fa-IR")}
        </div>
      </div>
    `).join("");

    trashBody.querySelectorAll('button[data-act="restore"]').forEach(btn=>{
      btn.addEventListener("click", ()=>{
        if(isReadOnly()) return guardEdit();
        const id = btn.getAttribute("data-id");
        restoreFromTrash(id);
        renderTrash();
      });
    });
  }

  // ---------- Buttons wiring ----------
  btnSettings.addEventListener("click", (e)=>{ e.stopPropagation(); toggleSettings(); });
  btnCloseSettings.addEventListener("click", (e)=>{ e.stopPropagation(); closeSettings(); });

  btnDrawer.addEventListener("click", (e)=>{ e.stopPropagation(); openDrawer(); });
  btnCloseDrawer.addEventListener("click", (e)=>{ e.stopPropagation(); closeDrawer(); });
  drawerBackdrop.addEventListener("click", closeDrawer);

  btnTakeCopy.addEventListener("click", (e)=>{ e.stopPropagation(); takeCopyFromShare(); });

  boardSeg.addEventListener("click", (e)=>{
    const btn = e.target.closest?.("button[data-board]");
    if(!btn) return;
    const boardId = btn.dataset.board;
    if(!app.data.boards[boardId]) return;
    app.data.activeBoard = boardId;
    cancelLinkMode();
    boardSeg.querySelectorAll("button").forEach(b=> b.classList.toggle("active", b.dataset.board === boardId));
    syncUI(true);
    if(!isReadOnly()){
      saveLocal();
      scheduleCloudSave(false);
    }
    closeDrawer();
    const focusId = B().selectedId ?? B().roots[0];
    if(focusId) centerOnNode(focusId);
  });

  $("#btnAddRoot").addEventListener("click", ()=>{ closeDrawer(); closeSettings(); addRoot(); });
  $("#btnAddChild").addEventListener("click", ()=>{
    closeDrawer(); closeSettings();
    const id = B().selectedId;
    if(!id) return addRoot();
    addChild(id);
  });
  $("#btnDelete").addEventListener("click", ()=>{
    closeDrawer(); closeSettings();
    const id = B().selectedId;
    if(!id) return;
    if(isReadOnly()) return guardEdit();
    if(confirm("این نود و زیرمجموعه‌ها به سطل زباله منتقل شوند؟")){
      pushHistory();
      trashNode(id);
      syncUI();
    }
  });

  $("#btnZoomIn").addEventListener("click", ()=>{
    const r = viewport.getBoundingClientRect();
    zoomAt(r.left + r.width/2, r.top + r.height/2, B().zoom * 1.12);
  });
  $("#btnZoomOut").addEventListener("click", ()=>{
    const r = viewport.getBoundingClientRect();
    zoomAt(r.left + r.width/2, r.top + r.height/2, B().zoom / 1.12);
  });
  $("#btnZoomReset").addEventListener("click", ()=>{
    if(isReadOnly()){
      B().zoom = 1;
      const id = B().selectedId ?? B().roots[0];
      if(id) centerOnNode(id);
      applyTransforms();
      return;
    }
    pushHistory();
    B().zoom = 1;
    const id = B().selectedId ?? B().roots[0];
    if(id) centerOnNode(id);
    applyTransforms();
    saveLocal();
    scheduleCloudSave(false);
  });

  $("#btnUndo").addEventListener("click", undo);
  $("#btnRedo").addEventListener("click", redo);
  $("#btnArrange").addEventListener("click", autoArrange);
  $("#btnCenter").addEventListener("click", ()=>{
    const id = B().selectedId ?? B().roots[0];
    if(!id) return;
    centerOnNode(id);
  });
  $("#btnGrid").addEventListener("click", ()=>{
    if(guardEdit()) return;
    pushHistory();
    B().showGrid = !B().showGrid;
    syncUI();
  });

  btnToggleLinks.addEventListener("click", ()=>{
    if(guardEdit()) return;
    pushHistory();
    B().showLinks = !B().showLinks;
    syncUI();
  });

  btnToggleNetwork.addEventListener("click", ()=>{
    if(guardEdit()) return;
    pushHistory();
    B().networkMode = !B().networkMode;
    syncUI();
  });

  btnCreateLink.addEventListener("click", ()=>{
    const id = B().selectedId;
    if(!id){ alert("اول یک نود را انتخاب کن."); return; }
    linkLabelInput.value = app.linker.label || "";
    linkTypeSelect.value = app.linker.type || "work";
    openModal(linkCreateModal);
  });

  btnManageLinks.addEventListener("click", openManageLinksModal);
  btnCloseManageLinks.addEventListener("click", ()=> closeModal(linkManageModal));

  $("#btnTagStyles").addEventListener("click", openTagStylesModal);
  btnCloseTagStyles.addEventListener("click", ()=> closeModal(tagStylesModal));

  btnCancelCreateLink.addEventListener("click", ()=> closeModal(linkCreateModal));
  btnStartLinking.addEventListener("click", ()=>{
    const id = B().selectedId;
    if(!id){ alert("اول یک نود را انتخاب کن."); return; }
    const type = linkTypeSelect.value;
    const label = linkLabelInput.value;
    closeModal(linkCreateModal);
    startLinkMode(id, type, label);
  });

  // Style modal buttons
  btnResetStyle.addEventListener("click", ()=>{
    templateSelect.value = "card";
    bodyColorInput.value = "#ffffff";
    coverColorInput.value = "#0e9b89";
    fontSelect.value = "";
    customFontInput.value = "";
    fontSizeInput.value = "15";
    noteInput.value = "";
    tagsInput.value = "";
    chkPropagate.checked = false;
    chkApplyToTag.checked = false;
    const id = app.styleEdit.nodeId;
    if(id) previewStyle(id);
  });

  btnCancelStyle.addEventListener("click", ()=>{
    restoreStyleSnapshot();
    closeModal(styleModal);
  });

  btnApplyStyle.addEventListener("click", ()=>{
    const id = app.styleEdit.nodeId;
    if(!id) return closeModal(styleModal);
    if(guardEdit()) return;

    const patch = getStylePatchFromInputs();
    pushHistory();

    const n = B().nodes[id];
    n.style = n.style || {};
    n.style.template = patch.template || "card";
    n.style.body = patch.body || null;
    n.style.cover = patch.cover || null;
    n.style.fontFamily = patch.fontFamily || "";
    n.style.fontSize = patch.fontSize || 15;
    n.style.propagate = !!patch.propagate;

    n.note = patch.note || "";
    n.tags = patch.tags || "";

    // ✅ Target style by tag: ذخیره‌ی استایل برای تگ‌ها
    if(patch.applyToTag){
      const tags = parseTags(n.tags).split(",").map(t=>t.trim()).filter(Boolean);
      if(tags.length){
        for(const t of tags){
          B().tagStyles[t] = {
            template: n.style.template,
            body: n.style.body,
            cover: n.style.cover,
            fontFamily: n.style.fontFamily,
            fontSize: n.style.fontSize
          };
        }
      }
    }

    syncUI();
    closeModal(styleModal);
  });

  $("#btnTrash").addEventListener("click", openTrashModal);
  btnCloseTrash.addEventListener("click", ()=> closeModal(trashModal));

  $("#btnSave").addEventListener("click", ()=>{
    if(isReadOnly()) return guardEdit();
    saveLocal();
    scheduleCloudSave(false);
    alert("ذخیره شد ✅");
  });

  $("#btnExport").addEventListener("click", ()=> exportJSON(B(), `${app.data.activeBoard}_mindmap.json`));
  $("#btnImport").addEventListener("click", ()=>{
    if(isReadOnly()) return guardEdit();
    fileImport.click();
  });
  fileImport.addEventListener("change", ()=>{
    if(!fileImport.files?.[0]) return;
    importJSONFromFile(fileImport.files[0], (st)=>{
      if(!st || !st.nodes || !st.roots) throw new Error("invalid");
      normalizeBoard(st);
      pushHistory();
      app.data.boards[app.data.activeBoard] = st;
      cancelLinkMode();
      syncUI();
    });
    fileImport.value = "";
  });

  btnExportAll.addEventListener("click", ()=> exportJSON(app.data, "all_boards_mindmap.json"));
  btnImportAll.addEventListener("click", ()=>{
    if(isReadOnly()) return guardEdit();
    fileImportAll.click();
  });
  fileImportAll.addEventListener("change", ()=>{
    if(!fileImportAll.files?.[0]) return;
    importJSONFromFile(fileImportAll.files[0], (obj)=>{
      obj = migrateIfNeeded(obj);
      if(!obj || !obj.boards) throw new Error("invalid");
      app.data = obj;
      cancelLinkMode();
      syncUI();
      closeDrawer();
      alert("ورود انجام شد ✅");
    });
    fileImportAll.value = "";
  });

  // Share buttons
  btnShareActive.addEventListener("click", async ()=>{
    const link = makeShareLink("active");
    await copyToClipboard(link);
  });
  btnShareAll.addEventListener("click", async ()=>{
    const link = makeShareLink("all");
    await copyToClipboard(link);
  });

  // Online controls
  btnJoinRoom.addEventListener("click", ()=>{
    const rid = (roomInput.value||"").trim();
    if(!rid) return alert("Room ID را وارد کن.");
    // load local of that room first (اگر موجود است)
    app.cloud.roomId = rid; // set for storageKey
    const ok = loadLocal();
    if(ok){ syncUI(true); }
    connectRoom(rid);
    closeDrawer();
  });

  btnCopyRoomLink.addEventListener("click", async ()=>{
    const rid = (app.cloud.roomId || (roomInput.value||"").trim());
    if(!rid) return alert("اول یک Room بساز/وارد شو.");
    const url = new URL(location.href);
    url.searchParams.set("room", rid);
    url.searchParams.delete("share");
    url.searchParams.delete("ro");
    url.searchParams.delete("k");
    await copyToClipboard(url.toString());
  });

  btnLeaveRoom.addEventListener("click", ()=>{
    if(confirm("از Room خارج شوی؟ (اتصال آنلاین قطع می‌شود)")){
      disconnectRoom();
      closeDrawer();
    }
  });

  // ---------- Modal backdrop behavior ----------
  modalBackdrop.addEventListener("click", ()=>{
    closeModal(linkCreateModal);
    closeModal(linkManageModal);
    closeModal(tagStylesModal);

    if(styleModal.classList.contains("open")){
      restoreStyleSnapshot();
      closeModal(styleModal);
    }
    closeModal(trashModal);
  });

  // ---------- Viewport wheel zoom ----------
  viewport.addEventListener("wheel", (e)=>{
    e.preventDefault();
    closeContextMenu();
    const factor = e.deltaY < 0 ? 1.08 : 1/1.08;
    zoomAt(e.clientX, e.clientY, B().zoom * factor);
  }, {passive:false});

  // ---------- Long press (mobile) ----------
  function startLongPress(e){
    if(e.pointerType === "mouse") return;
    app.longpress.fired = false;
    app.longpress.start = { x:e.clientX, y:e.clientY };
    if(app.longpress.timer) clearTimeout(app.longpress.timer);

    app.longpress.timer = setTimeout(()=>{
      if(app.pinch.active) return;
      if(app.drag.mode === "node") return;
      app.longpress.fired = true;
      openContextMenu(e.clientX, e.clientY);
    }, 520);
  }
  function cancelLongPress(){
    if(app.longpress.timer){
      clearTimeout(app.longpress.timer);
      app.longpress.timer = null;
    }
  }

  // ---------- Pointer interactions ----------
  viewport.addEventListener("pointerdown", (e)=>{
    if(e.button === 2) return;

    if(settingsPanel.classList.contains("open") && !settingsPanel.contains(e.target)) closeSettings();
    if(drawer.classList.contains("open")) closeDrawer();
    closeContextMenu();

    app.pointers.set(e.pointerId, {x:e.clientX, y:e.clientY});

    if(app.pointers.size === 2){
      cancelLongPress();
      const pts = [...app.pointers.values()];
      const d = Math.hypot(pts[0].x - pts[1].x, pts[0].y - pts[1].y);
      app.pinch.active = true;
      app.pinch.startDist = d;
      app.pinch.startZoom = B().zoom;
      return;
    }

    startLongPress(e);

    const hitNode = e.target.closest?.(".node");
    if(!hitNode){
      viewport.setPointerCapture(e.pointerId);
      app.drag.mode = "pan";
      app.drag.pointerId = e.pointerId;
      app.drag.startClient = {x:e.clientX, y:e.clientY};
      app.drag.startPan = {x:B().pan.x, y:B().pan.y};
    }
  });

  viewport.addEventListener("pointermove", (e)=>{
    if(app.pointers.has(e.pointerId)){
      app.pointers.set(e.pointerId, {x:e.clientX, y:e.clientY});
    }

    const mdx = Math.abs(e.clientX - app.longpress.start.x);
    const mdy = Math.abs(e.clientY - app.longpress.start.y);
    if(mdx + mdy > 6) cancelLongPress();

    // pinch zoom
    if(app.pinch.active && app.pointers.size >= 2){
      cancelLongPress();
      const pts = [...app.pointers.values()].slice(0,2);
      const d = Math.hypot(pts[0].x - pts[1].x, pts[0].y - pts[1].y);
      const mid = { x:(pts[0].x+pts[1].x)/2, y:(pts[0].y+pts[1].y)/2 };
      const ratio = d / Math.max(1, app.pinch.startDist);
      zoomAt(mid.x, mid.y, app.pinch.startZoom * ratio);
      return;
    }

    // ✅ Node drag (easy + smooth, world-based)
    if((app.drag.mode === "node_pending" || app.drag.mode === "node") && app.drag.pointerId === e.pointerId){
      const dx = e.clientX - app.drag.startClient.x;
      const dy = e.clientY - app.drag.startClient.y;
      const dist = Math.abs(dx) + Math.abs(dy);

      if(app.drag.mode === "node_pending"){
        if(dist < 5) return;
        app.drag.mode = "node";
        cancelLongPress();
        app.drag.draggingEl?.classList.add("dragging");
      }

      cancelLongPress();

      const id = app.drag.nodeId;
      const n = B().nodes[id];
      if(!n) return;

      if(!app.drag.didMutate){
        if(isReadOnly()) return;
        pushHistory();
        app.drag.didMutate = true;
      }
      if(isReadOnly()) return;

      const w = clientToWorld(e.clientX, e.clientY);
      n.x = clamp(w.x - app.drag.grabOffset.x, 40, 9800);
      n.y = clamp(w.y - app.drag.grabOffset.y, 40, 9800);

      // update element only
      const el = app.drag.draggingEl || nodesLayer.querySelector(`.node[data-id="${cssEsc(id)}"]`);
      if(el){
        el.style.left = n.x + "px";
        el.style.top  = n.y + "px";
      }
      renderEdges();

      // drop target
      const target = findDropTarget(e.clientX, e.clientY, id);
      if(target !== app.drag.dropTargetId){
        app.drag.dropTargetId = target;
        render();
      }
      return;
    }

    // pan
    if(app.drag.mode === "pan" && app.drag.pointerId === e.pointerId){
      cancelLongPress();
      const dx = e.clientX - app.drag.startClient.x;
      const dy = e.clientY - app.drag.startClient.y;
      B().pan.x = app.drag.startPan.x + dx;
      B().pan.y = app.drag.startPan.y + dy;
      applyTransforms();
      return;
    }
  });

  viewport.addEventListener("pointerup", (e)=>{
    cancelLongPress();

    if(app.pointers.has(e.pointerId)) app.pointers.delete(e.pointerId);
    if(app.pointers.size < 2) app.pinch.active = false;

    if(app.drag.pointerId === e.pointerId){
      if(app.drag.mode === "node" || app.drag.mode === "node_pending"){
        const draggedId = app.drag.nodeId;
        const targetId = app.drag.dropTargetId;

        if(!isReadOnly() && targetId){
          reparentNode(draggedId, targetId);
        }

        // پایان
        app.drag.draggingEl?.classList.remove("dragging");
        app.drag.draggingEl = null;

        if(!isReadOnly() && app.drag.didMutate){
          syncUI();
        }else{
          render();
          if(!isReadOnly()){
            saveLocal();
            scheduleCloudSave(false);
          }
        }
      }

      app.drag.mode = null;
      app.drag.nodeId = null;
      app.drag.pointerId = null;
      app.drag.dropTargetId = null;
      app.drag.didMutate = false;
    }
  });

  viewport.addEventListener("pointercancel", (e)=>{
    cancelLongPress();
    if(app.pointers.has(e.pointerId)) app.pointers.delete(e.pointerId);
    app.pinch.active = false;

    app.drag.draggingEl?.classList.remove("dragging");
    app.drag.draggingEl = null;

    app.drag.mode = null;
    app.drag.nodeId = null;
    app.drag.pointerId = null;
    app.drag.dropTargetId = null;
    app.drag.didMutate = false;
  });

  viewport.addEventListener("contextmenu", (e)=>{
    e.preventDefault();
    openContextMenu(e.clientX, e.clientY);
  });

  document.addEventListener("pointerdown", (e)=>{
    if(!ctxMenu.contains(e.target)) closeContextMenu();
    if(settingsPanel.classList.contains("open") && !settingsPanel.contains(e.target) && e.target !== btnSettings) closeSettings();
  });

  // ---------- Keyboard shortcuts ----------
  function visibleChildren(id){
    const n = B().nodes[id];
    if(!n || n.collapsed) return [];
    return (n.children||[]).filter(cid => !isHiddenByAncestor(cid));
  }
  function siblingList(id){
    const n = B().nodes[id];
    if(!n) return [];
    if(!n.parentId) return B().roots.filter(rid => !isHiddenByAncestor(rid));
    const p = B().nodes[n.parentId];
    if(!p) return [];
    return (p.children||[]).filter(cid => !isHiddenByAncestor(cid));
  }

  document.addEventListener("keydown", (e)=>{
    const id = B().selectedId;
    const activeEl = document.activeElement;
    const inEditable = activeEl && activeEl.closest?.(".node") && activeEl.getAttribute("contenteditable") === "true";

    if(e.key === "Escape"){
      closeContextMenu();
      if(settingsPanel.classList.contains("open")) closeSettings();
      if(drawer.classList.contains("open")) closeDrawer();
      if(linkCreateModal.classList.contains("open")) closeModal(linkCreateModal);
      if(linkManageModal.classList.contains("open")) closeModal(linkManageModal);
      if(tagStylesModal.classList.contains("open")) closeModal(tagStylesModal);

      if(styleModal.classList.contains("open")){
        restoreStyleSnapshot();
        closeModal(styleModal);
      }
      if(trashModal.classList.contains("open")) closeModal(trashModal);
      if(app.linker.active) cancelLinkMode();
      return;
    }

    if(e.ctrlKey && (e.key === "z" || e.key === "Z")){ e.preventDefault(); undo(); return; }
    if(e.ctrlKey && (e.key === "y" || e.key === "Y")){ e.preventDefault(); redo(); return; }
    if(e.ctrlKey && (e.key === "s" || e.key === "S")){
      e.preventDefault();
      if(isReadOnly()) return guardEdit();
      saveLocal();
      scheduleCloudSave(false);
      alert("ذخیره شد ✅");
      return;
    }

    if(inEditable) return;

    if(e.key === "Delete"){
      if(!id) return;
      e.preventDefault();
      if(isReadOnly()) return guardEdit();
      if(confirm("این نود و زیرمجموعه‌ها به سطل زباله منتقل شوند؟")){
        pushHistory();
        trashNode(id);
        syncUI();
      }
      return;
    }

    if(e.key === "Enter"){ e.preventDefault(); if(!id) addRoot(); else addChild(id); return; }
    if(e.key === "Tab"){ e.preventDefault(); if(id) addSibling(id); return; }
    if(e.key === " "){ e.preventDefault(); if(id) toggleDone(id); return; }
    if(e.key === "c" || e.key === "C"){ e.preventDefault(); if(id) toggleCollapse(id); return; }

    if(e.key === "l" || e.key === "L"){
      e.preventDefault();
      if(!id) return;
      if(app.linker.active) cancelLinkMode();
      else startLinkMode(id, app.linker.type || "work", app.linker.label || "");
      return;
    }
    if(e.key === "m" || e.key === "M"){ e.preventDefault(); openManageLinksModal(); return; }

    if(!id) return;

    if(e.key === "ArrowLeft"){
      const n = B().nodes[id];
      if(n?.parentId){
        e.preventDefault();
        selectNode(n.parentId);
        centerOnNode(n.parentId);
      }
      return;
    }
    if(e.key === "ArrowRight"){
      const kids = visibleChildren(id);
      if(kids.length){
        e.preventDefault();
        selectNode(kids[0]);
        centerOnNode(kids[0]);
      }
      return;
    }
    if(e.key === "ArrowUp" || e.key === "ArrowDown"){
      const sibs = siblingList(id);
      const idx = sibs.indexOf(id);
      if(idx >= 0 && sibs.length > 1){
        e.preventDefault();
        const next = (e.key === "ArrowUp")
          ? sibs[(idx - 1 + sibs.length) % sibs.length]
          : sibs[(idx + 1) % sibs.length];
        selectNode(next);
        centerOnNode(next);
      }
      return;
    }
  });

  // ---------- Exit confirmation ----------
  let exitGuardEnabled = true;
  window.addEventListener("beforeunload", (e)=>{
    if(!exitGuardEnabled) return;
    e.preventDefault();
    e.returnValue = "";
    return "";
  });

  // Android back button guard
  history.pushState({ mm_guard: true }, "", location.href);
  window.addEventListener("popstate", ()=>{
    if(!exitGuardEnabled) return;
    const ok = confirm("آیا مطمئن هستید می‌خواهید خارج شوید؟");
    if(ok){
      exitGuardEnabled = false;
      history.back();
    }else{
      history.pushState({ mm_guard: true }, "", location.href);
    }
  });

  // ---------- Boot ----------
  function boot(){
    // Room param (online)
    const url = new URL(location.href);
    const roomFromUrl = (url.searchParams.get("room") || "").trim();
    if(roomFromUrl){
      app.cloud.roomId = roomFromUrl;
      roomInput.value = roomFromUrl;
      // load local for this room
      const okLocal = loadLocal();
      if(okLocal) syncUI(true);
      connectRoom(roomFromUrl);
    }else{
      setOnlinePill("", "● آفلاین");
    }

    // Share link
    const loadedShare = tryLoadFromShareLink();
    if(!loadedShare){
      // if no room and local not loaded -> seed
      const ok = loadLocal();
      if(!ok){
        app.data.activeBoard = "main";
        const r = createNode({ text:"بخش اصلی", x:1600, y:1200 });
        const a = createNode({ text:"کارها", parentId:r, x:1920, y:1200 });
        const t1 = createNode({ text:"تسک ۱", parentId:a, x:2240, y:1120 });
        const t2 = createNode({ text:"تسک ۲", parentId:a, x:2240, y:1240 });
        B().links.push({ id:newId(), fromId:t1, toId:t2, type:"work", label:"Team A", createdAt:now() });
        B().selectedId = r;

        app.data.activeBoard = "team";
        const tr = createNode({ text:"کار تیمی", x:1600, y:1200 });
        B().selectedId = tr;

        app.data.activeBoard = "main";
        saveLocal();
      }
    }

    boardSeg.querySelectorAll("button").forEach(b=>{
      b.classList.toggle("active", b.dataset.board === app.data.activeBoard);
    });

    applyTransforms();
    render();

    const focusId = B().selectedId ?? B().roots[0];
    if(focusId) centerOnNode(focusId);

    // periodic save local + cloud (light)
    setInterval(()=>{
      if(!isReadOnly()){
        saveLocal();
        scheduleCloudSave(false);
      }
    }, 12000);
  }

  boot();
})();
</script>

<!--
✅ برای آنلاین واقعی (که هر تغییر همه ببینن و همه هم ویرایش کنن):
1) یک Firebase project بساز.
2) Firestore را فعال کن.
3) Firestore Rules (ساده‌ترین حالت تستی):
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /mindrooms/{roomId} {
         allow read, write: if true;
       }
     }
   }
   (بعداً برای امنیت، محدودیت بذار.)
4) FIREBASE_CONFIG را در بالای فایل پر کن (apiKey, projectId, ...).
5) سایت را روی یک هاست اجرا کن (Netlify / Vercel / GitHub Pages).
6) یک Room بساز و لینک Room را برای بقیه بفرست.
-->
</body>
</html>
