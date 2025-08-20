[index.html](https://github.com/user-attachments/files/21891561/index.html)
<!doctype html>
<html lang="ja">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>MNP管理（181日・回線数集計・乗り換え可リスト・オプション管理・プロフィール対応）</title>
<meta name="description" content="契約日から最短MNP可能日を算出。キャリア別の総回線数、乗り換え可能回線、オプションの最短解約日を管理。各ユーザーごとに編集・保存・復元できます。" />
<style>
  :root{
    --bg:#0f1216; --card:#151a20; --ink:#e8eef6; --muted:#a8b3c2;
    --accent:#4da3ff; --accent-2:#7bd389; --danger:#ff6b6b; --border:#232b35;
    --radius:14px; --shadow:0 10px 30px rgba(0,0,0,.35);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{margin:0; font-family:ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;
    color:var(--ink); background:radial-gradient(1200px 600px at 10% -20%, #1a2230 0%, var(--bg) 50%) fixed var(--bg);}
  .wrap{max-width:1200px; margin:40px auto; padding:0 16px}
  header{display:flex; align-items:center; justify-content:space-between; gap:12px; margin-bottom:18px; flex-wrap:wrap}
  header h1{font-size:clamp(20px,3vw,28px); margin:0}
  header .pill{font-size:12px; color:#0b1726; background:linear-gradient(90deg, var(--accent), #9ec9ff);
    padding:6px 10px; border-radius:99px; font-weight:700}
  .card{background:linear-gradient(180deg, rgba(255,255,255,.02), rgba(255,255,255,0)) border-box;
    border:1px solid var(--border); border-radius:var(--radius); box-shadow:var(--shadow);}
  .grid{display:grid; gap:16px}
  @media (min-width:1100px){ .grid{grid-template-columns:1.25fr .75fr} }
  .section{padding:18px}
  .section + .section{border-top:1px solid var(--border)}
  .section h2{margin:0 0 10px 0; font-size:16px; color:var(--muted); font-weight:600}
  label{display:block; font-size:13px; color:var(--muted); margin:0 0 6px 2px}
  input[type="date"], select, input[type="number"], input[type="text"]{
    width:100%; padding:12px; border-radius:12px; border:1px solid var(--border);
    background:#0d131a; color:var(--ink); outline:none;
  }
  .row{display:grid; gap:12px}
  @media (min-width:560px){ .row.two{grid-template-columns:1fr 1fr} .row.three{grid-template-columns:1fr 1fr 1fr} }
  .radio{display:flex; gap:10px; flex-wrap:wrap}
  .radio label{display:inline-flex; align-items:center; gap:8px; padding:10px 12px; border:1px solid var(--border);
    border-radius:12px; background:#0d131a; cursor:pointer; user-select:none}
  .radio input{accent-color:var(--accent)}
  .btns{display:flex; gap:10px; flex-wrap:wrap}
  button{cursor:pointer; padding:10px 12px; border-radius:10px; border:1px solid var(--border);
    background:linear-gradient(180deg, #122033, #0b1624); color:var(--ink); font-weight:600}
  button.primary{background:linear-gradient(180deg, #2b75ff, #1a4fcc); border-color:#1f49a8}
  button.warn{background:linear-gradient(180deg, #3a1212, #220b0b); border-color:#4b1b1b}
  .muted{color:var(--muted); font-size:12px}
  .result{padding:12px; border-radius:12px; border:1px dashed var(--border); background:#0b131a}
  .result strong{font-size:18px}
  .good{color:var(--accent-2)} .bad{color:var(--danger)}
  table{width:100%; border-collapse:collapse}
  th,td{padding:8px 6px; border-bottom:1px solid var(--border); text-align:left; font-size:13.5px; vertical-align:top}
  th{color:var(--muted); font-weight:600}
  .tag{display:inline-block; padding:4px 8px; border:1px solid var(--border); border-radius:999px; font-size:12px; margin:2px}
  .bar{display:flex; flex-wrap:wrap; gap:8px}
  .kbd{font-family:ui-monospace,SFMono-Regular,Menlo,monospace; font-size:12px; padding:2px 6px; border:1px solid var(--border); border-radius:6px}
  .foot{font-size:12px; color:var(--muted); margin-top:12px}
  .split{display:grid; gap:12px}
  @media (min-width:900px){ .split{grid-template-columns:1fr 1fr} }
  .optline{font-size:12px; color:var(--muted)}
  a{color:var(--accent)}
  .profile{display:flex; gap:8px; align-items:center; flex-wrap:wrap}
  .profile input{max-width:220px}
</style>
</head>
<body>
<div class="wrap">
  <header>
    <div style="display:flex; align-items:center; gap:12px; flex-wrap:wrap">
      <div class="pill">v4 / 181日＋回線集計＋乗り換え可リスト＋オプション管理＋プロフィール</div>
      <h1 style="margin-left:4px">MNP 管理ツール</h1>
    </div>
    <div class="profile">
      <span class="muted">プロフィール</span>
      <input id="profileName" type="text" placeholder="例：さとう家_父" />
      <button id="profileSwitchBtn">切替/新規</button>
      <button id="profileExportBtn">このプロフィールをJSON書き出し</button>
      <button id="profileImportBtn">JSONから読み込み</button>
      <button class="warn" id="profileClearBtn">このプロフィールの全データ削除</button>
    </div>
  </header>

  <div class="card grid" id="app">
    <!-- 入力パネル -->
    <div class="section">
      <h2>回線を追加</h2>
      <div class="row two">
        <div>
          <label for="contract">契約日</label>
          <input id="contract" type="date" />
        </div>
        <div>
          <label for="carrier">キャリア</label>
          <select id="carrier">
            <option value="ymobile">Y!mobile</option>
            <option value="uq">UQ mobile</option>
            <option value="linemo">LINEMO</option>
            <option value="softbank">SoftBank</option>
            <option value="au">au</option>
            <option value="docomo">docomo</option>
            <option value="rakuten">楽天モバイル</option>
            <option value="povo">povo</option>
            <option value="other">その他 / カスタム</option>
          </select>
        </div>
      </div>

      <div class="row two" style="margin-top:10px">
        <div>
          <label for="days">最低維持期間（日） <span class="muted">(キャリア毎の既定値は下で編集可)</span></label>
          <input id="days" type="number" min="1" value="181" />
        </div>
        <div>
          <label for="note">メモ（任意）</label>
          <input id="note" type="text" placeholder="案件名/名義/番号など" />
        </div>
      </div>

      <div class="radio" id="counting" style="margin-top:10px">
        <label><input type="radio" name="count" value="nextday" checked> 契約日の翌日を1日目（一般的）</label>
        <label><input type="radio" name="count" value="sameday"> 契約日を1日目</label>
      </div>

      <div class="btns" style="margin-top:12px">
        <button class="primary" id="addLineBtn">回線を追加</button>
        <button id="todayBtn">契約日を今日に</button>
        <button id="shareBtn">共有リンク（単票）</button>
        <button id="resetBtn">入力リセット</button>
      </div>

      <div class="foot">
        ※「回線を追加」で、下の一覧・集計・乗り換え可リストに反映されます。<br>
        ※ **プロフィール** を使えば、家族やメンバーごとに別データとして管理できます（ブラウザに自動保存）。
      </div>
    </div>

    <!-- 単票プレビュー -->
    <div class="section">
      <h2>単票プレビュー</h2>
      <div class="result" id="r1">
        <div class="muted">（翌日を1日目）最短MNP可能日</div>
        <strong id="d1">—</strong>
        <div class="muted" id="d1sub">—</div>
      </div>
      <div class="result" id="r2" style="margin-top:10px">
        <div class="muted">（契約日を1日目）最短MNP可能日</div>
        <strong id="d2">—</strong>
        <div class="muted" id="d2sub">—</div>
      </div>
    </div>

    <!-- 集計 -->
    <div class="section">
      <h2>キャリア別の総回線数</h2>
      <div id="summary" class="bar"></div>
      <div class="foot">※下の「回線一覧」の件数をリアルタイム集計。</div>
    </div>

    <!-- 乗り換え可リスト -->
    <div class="section">
      <h2>次の乗り換えができる回線</h2>
      <div class="split">
        <div class="result">
          <div><strong>本日時点で乗り換え可能</strong></div>
          <div id="eligibleNowList">—</div>
        </div>
        <div class="result">
          <div style="display:flex; align-items:center; gap:8px">
            <strong id="upcomingTitle">直近30日以内に乗り換え可能になる回線</strong>
            <span class="muted">（変更可）</span>
            <input id="upcomingDays" type="number" min="1" value="30" style="width:90px" />
          </div>
          <div id="upcomingList">—</div>
        </div>
      </div>
      <div class="foot">計算は「翌日を1日目＝契約日＋最低維持日数」で判定しています。</div>
    </div>

    <!-- 既定日数 -->
    <div class="section">
      <h2>キャリア別デフォルト日数</h2>
      <div class="row three" id="defaults"></div>
    </div>

    <!-- 回線一覧 -->
    <div class="section" style="grid-column:1/-1">
      <h2>回線一覧</h2>
      <div class="muted" style="margin-bottom:6px">
        列：# / 契約日 / キャリア / メモ / 日数 / 最短（翌日1） / 残り日数 / オプション / 操作
      </div>
      <div class="btns" style="margin-bottom:10px">
        <button id="exportBtn">このプロフィールの回線だけJSON書き出し</button>
        <button id="importBtn">回線JSONを読み込み</button>
        <button class="warn" id="clearBtn">回線だけ全削除</button>
      </div>
      <div style="overflow:auto">
        <table id="linesTable">
          <thead>
            <tr>
              <th>#</th><th>契約日</th><th>キャリア</th><th>メモ</th><th>日数</th>
              <th>最短（翌日1）</th><th>残り</th><th>オプション</th><th>操作</th>
            </tr>
          </thead>
          <tbody id="linesTbody"></tbody>
        </table>
      </div>
      <div class="foot">
        ● オプションは「開始日（既定は契約日）＋最短日数」で最短解約日を計算。<br>
        ● 例：オプション名「端末補償」開始=契約日、最短=93日 → <span class="kbd">開始日＋93日</span> が最短解約日。<br>
        ● 乗り換え可否とオプションの可否は別判定（両方OKの日を見て動くのがおすすめ）。
      </div>
    </div>
  </div>

  <div class="foot">© 2025 MNP工具 / ブラウザ保存・プロフィール切替対応</div>
</div>

<script>
  // ===== Utilities =====
  const $ = sel => document.querySelector(sel);
  const $$ = sel => Array.from(document.querySelectorAll(sel));
  const WEEKDAYS = ["日","月","火","水","木","金","土"];

  const LABELS = {
    ymobile:"Y!mobile", uq:"UQ mobile", linemo:"LINEMO", softbank:"SoftBank",
    au:"au", docomo:"docomo", rakuten:"楽天モバイル", povo:"povo", other:"その他"
  };
  const DEFAULTS_GLOBAL = {
    ymobile:181, uq:181, linemo:181, softbank:181, au:181, docomo:181, rakuten:181, povo:181, other:181
  };

  const todayLocal = ()=> new Date(new Date().getFullYear(), new Date().getMonth(), new Date().getDate());
  const toDateYMD = (str)=> {
    if(!str) return null;
    const [y,m,d] = str.split("-").map(n=>parseInt(n,10));
    if(!y||!m||!d) return null;
    return new Date(y, m-1, d);
  };
  const fmt = (d)=>{
    if(!(d instanceof Date) || isNaN(d)) return "—";
    const y = d.getFullYear();
    const m = String(d.getMonth()+1).padStart(2,"0");
    const day = String(d.getDate()).padStart(2,"0");
    const wd = WEEKDAYS[d.getDay()];
    return `${y}/${m}/${day}（${wd}）`;
  };
  const fmtISO = (d)=>{
    if(!(d instanceof Date) || isNaN(d)) return "—";
    const y = d.getFullYear();
    const m = String(d.getMonth()+1).padStart(2,"0");
    const day = String(d.getDate()).padStart(2,"0");
    return `${y}-${m}-${day}`;
  };
  const addDaysLocal = (d, days)=> new Date(d.getFullYear(), d.getMonth(), d.getDate() + days);
  const diffDays = (a,b)=>{
    const A = new Date(a.getFullYear(), a.getMonth(), a.getDate());
    const B = new Date(b.getFullYear(), b.getMonth(), b.getDate());
    return Math.round((B - A) / 86400000);
  };
  const escapeHtml = (s)=> String(s||"").replace(/[&<>"']/g, m=>({ "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;" }[m]));

  // ===== Profiled storage =====
  const PROFILE_PREFIX = "mnp_profile_v4_"; // localStorage key prefix
  let profileName = ""; // user-provided
  let DEFAULTS = JSON.parse(JSON.stringify(DEFAULTS_GLOBAL));
  let LINES = []; // {id,date,carrier,days,note, options:[{id,name,startDate,minDays}]}

  function keyFor(name){ return PROFILE_PREFIX + (name||"default"); }

  function loadProfile(name){
    profileName = name || "default";
    const raw = localStorage.getItem(keyFor(profileName));
    if(raw){
      try {
        const obj = JSON.parse(raw);
        DEFAULTS = obj.defaults || JSON.parse(JSON.stringify(DEFAULTS_GLOBAL));
        LINES = Array.isArray(obj.lines) ? obj.lines : [];
      } catch { DEFAULTS = JSON.parse(JSON.stringify(DEFAULTS_GLOBAL)); LINES = []; }
    }else{
      DEFAULTS = JSON.parse(JSON.stringify(DEFAULTS_GLOBAL));
      LINES = [];
    }
    $("#profileName").value = profileName;
    // reflect UI
    renderDefaults();
    renderAll();
  }

  function saveProfile(){
    const payload = { defaults: DEFAULTS, lines: LINES };
    localStorage.setItem(keyFor(profileName), JSON.stringify(payload));
  }

  // ===== Single preview compute =====
  function computePreview(){
    const contractStr = $("#contract").value;
    const days = parseInt($("#days").value || "0", 10);
    const contract = toDateYMD(contractStr);
    if (!Number.isFinite(days) || days<=0 || !(contract instanceof Date) || isNaN(contract)){
      $("#d1").textContent = "—"; $("#d2").textContent = "—";
      $("#d1sub").textContent = contract ? "日数を入力してください。" : "契約日を入力してください。";
      $("#d2sub").textContent = "";
      return;
    }
    const a = addDaysLocal(contract, days);          // 翌日1日目（判定用）
    const b = addDaysLocal(contract, Math.max(0, days-1)); // 契約日1日目
    const t = todayLocal();
    const aLeft = diffDays(t, a);
    const bLeft = diffDays(t, b);
    $("#d1").textContent = fmt(a);
    $("#d2").textContent = fmt(b);
    $("#d1sub").innerHTML = `ISO: ${fmtISO(a)}　/　本日から<span class="${aLeft<0?'bad':''}">${aLeft}</span>日`;
    $("#d2sub").innerHTML = `ISO: ${fmtISO(b)}　/　本日から<span class="${bLeft<0?'bad':''}">${bLeft}</span>日`;
  }

  // ===== Summary (carrier count) =====
  function computeCarrierCounts(){
    const counts = {}; let total=0;
    Object.keys(LABELS).forEach(k=> counts[k]=0);
    LINES.forEach(l=>{ counts[l.carrier]=(counts[l.carrier]||0)+1; total++; });
    return {counts,total};
  }
  function renderSummary(){
    const {counts,total} = computeCarrierCounts();
    const box = $("#summary");
    box.innerHTML = "";
    Object.keys(LABELS).forEach(key=>{
      const n = counts[key]||0;
      const tag = document.createElement("div");
      tag.className = "tag";
      tag.textContent = `${LABELS[key]}：${n}本`;
      box.appendChild(tag);
    });
    const all = document.createElement("div");
    all.className = "tag"; all.style.borderColor = "#365";
    all.textContent = `合計：${total}本`;
    box.appendChild(all);
  }

  // ===== Eligibility (MNP) =====
  function earliestMnpDate(line){ // use "翌日1日目" ＝ 契約日 + days
    const d = toDateYMD(line.date);
    if(!d) return null;
    return addDaysLocal(d, Number(line.days||0));
  }
  function renderEligibility(){
    const t = todayLocal();
    const eligibleNow = [];
    const upcoming = [];
    const horizon = Math.max(1, parseInt($("#upcomingDays").value||"30",10));
    LINES.forEach(l=>{
      const mnp = earliestMnpDate(l);
      if(!mnp) return;
      const left = diffDays(t, mnp);
      if(left<=0){ eligibleNow.push({l, mnp, left}); }
      else if(left<=horizon){ upcoming.push({l, mnp, left}); }
    });

    const makeItem = ({l,mnp,left})=>{
      const spanOpt = renderOptionSummary(l);
      return `<div class="optline">
        ・${fmt(mnp)}（残り${left}日） / ${LABELS[l.carrier]} / ${escapeHtml(l.note||"")} / 契約日:${l.date} / オプション:${spanOpt}
      </div>`;
    };

    $("#eligibleNowList").innerHTML = eligibleNow.length
      ? eligibleNow.sort((a,b)=>a.left-b.left).map(makeItem).join("")
      : "—";

    $("#upcomingList").innerHTML = upcoming.length
      ? upcoming.sort((a,b)=>a.left-b.left).map(makeItem).join("")
      : "—";

    $("#upcomingTitle").textContent = `直近${horizon}日以内に乗り換え可能になる回線`;
  }

  // ===== Options (per line) =====
  // option: {id,name,startDate(YYYY-MM-DD),minDays(number)}
  function earliestOptionCancelDate(opt, fallbackStart){
    const start = toDateYMD(opt.startDate || fallbackStart);
    if(!start) return null;
    return addDaysLocal(start, Number(opt.minDays||0));
  }
  function renderOptionSummary(line){
    const opts = Array.isArray(line.options)? line.options : [];
    if(!opts.length) return "なし";
    const parts = opts.map(o=>{
      const d = earliestOptionCancelDate(o, line.date);
      return `${escapeHtml(o.name||"オプション")}→${d?fmt(d):"—"}`;
    });
    return parts.join(" / ");
  }

  // Simple prompt-based option editor for each line
  function editOptions(line){
    const current = (line.options||[]).map(o=>{
      const d = earliestOptionCancelDate(o, line.date);
      return `・${o.name||"(名称未設定)"} / 開始:${o.startDate||line.date||"—"} / 最短日数:${o.minDays||0} / 最短解約:${d?fmtISO(d):"—"}`;
    }).join("\n") || "（未登録）";
    const howto =
`現在の登録:
${current}

操作:
1) 追加 → 「+ オプション名, 開始日YYYY-MM-DD(空なら契約日), 最短日数」
   例）+ 端末補償, 2025-08-10, 93
2) 削除 → 「- インデックス」 例）- 1
3) 終了 → そのままOK`;

    let cmd = prompt(howto, "");
    while(cmd){
      cmd = cmd.trim();
      if(cmd.startsWith("+")){
        const body = cmd.slice(1).trim();
        const parts = body.split(",").map(s=>s.trim());
        const name = parts[0]||"オプション";
        const start = parts[1]||"";
        const days = parseInt(parts[2]||"0",10)||0;
        line.options = line.options||[];
        line.options.push({ id: Date.now()+Math.random().toString(16).slice(2), name, startDate:start, minDays:days });
      }else if(cmd.startsWith("-")){
        const idx = parseInt(cmd.slice(1).trim(),10);
        if(Number.isFinite(idx) && idx>0 && idx<= (line.options?line.options.length:0)){
          line.options.splice(idx-1,1);
        }
      }else{
        alert("コマンドが読み取れませんでした。もう一度お願いします。");
      }
      // 次の操作 or 終了
      const cur = (line.options||[]).map((o,i)=>{
        const d = earliestOptionCancelDate(o, line.date);
        return `${i+1}) ${o.name||"(名称未設定)"} / 開始:${o.startDate||line.date||"—"} / 最短日数:${o.minDays||0} / 最短解約:${d?fmtISO(d):"—"}`;
      }).join("\n") || "（未登録）";
      cmd = prompt("現在:\n"+cur+"\n\n続けますか？（+ / - / 空で終了）","");
    }
  }

  // ===== Lines table =====
  function renderLines(){
    const tbody = $("#linesTbody"); tbody.innerHTML = "";
    const t = todayLocal();
    LINES
      .slice()
      .sort((a,b)=> (a.date>b.date?1:-1)) // 契約日昇順
      .forEach((l,idx)=>{
        const contract = toDateYMD(l.date);
        const mnpA = contract ? addDaysLocal(contract, l.days) : null; // 翌日1
        const leftA = (mnpA && diffDays(t,mnpA));
        const opts = l.options||[];
        const optHtml = opts.length
          ? opts.map((o,i)=>{
              const d = earliestOptionCancelDate(o, l.date);
              const left = d? diffDays(t,d) : null;
              return `<div class="optline">${i+1}) ${escapeHtml(o.name||"オプション")} / 開始:${o.startDate||l.date||"—"} / 最短:${o.minDays||0}日 / 解約最短:${d?fmt(d):"—"}（残り${left!=null?(left<0?`<span class="bad">${left}</span>`:left):"—"}日）</div>`;
            }).join("")
          : `<span class="muted">なし</span>`;

        const tr = document.createElement("tr");
        tr.innerHTML = `
          <td>${idx+1}</td>
          <td>${l.date || "—"}</td>
          <td>${LABELS[l.carrier]||l.carrier}</td>
          <td>${escapeHtml(l.note||"")}</td>
          <td>${l.days}</td>
          <td>${mnpA?fmt(mnpA):"—"}</td>
          <td>${Number.isFinite(leftA)?(leftA<0?`<span class="bad">${leftA}</span>`:leftA):"—"}</td>
          <td>${optHtml}</td>
          <td>
            <button data-act="edit" data-id="${l.id}">編集</button>
            <button data-act="opts" data-id="${l.id}">オプション</button>
            <button data-act="del" data-id="${l.id}">削除</button>
          </td>
        `;
        tbody.appendChild(tr);
      });

    // 操作
    tbody.querySelectorAll("button[data-act='del']").forEach(btn=>{
      btn.addEventListener("click", ()=>{
        const id = btn.getAttribute("data-id");
        LINES = LINES.filter(x=> String(x.id)!==String(id));
        saveProfile(); renderAll();
      });
    });
    tbody.querySelectorAll("button[data-act='edit']").forEach(btn=>{
      btn.addEventListener("click", ()=>{
        const id = btn.getAttribute("data-id");
        const item = LINES.find(x=> String(x.id)===String(id));
        if(!item) return;
        const nd = prompt("契約日（YYYY-MM-DD）", item.date);
        if(!nd) return;
        const dd = prompt("最低維持期間（日）", String(item.days));
        if(dd===null) return;
        const nn = prompt("メモ（任意）", item.note || "");
        item.date = nd;
        item.days = Math.max(1, parseInt(dd,10)||item.days);
        item.note = nn || "";
        saveProfile(); renderAll();
      });
    });
    tbody.querySelectorAll("button[data-act='opts']").forEach(btn=>{
      btn.addEventListener("click", ()=>{
        const id = btn.getAttribute("data-id");
        const item = LINES.find(x=> String(x.id)===String(id));
        if(!item) return;
        editOptions(item);
        saveProfile(); renderAll();
      });
    });
  }

  // ===== Defaults editor =====
  function renderDefaults(){
    const wrap = $("#defaults");
    wrap.innerHTML = "";
    Object.entries(DEFAULTS).forEach(([key, val])=>{
      const label = LABELS[key] || key;
      const id = `def_${key}`;
      const box = document.createElement("div");
      box.innerHTML = `
        <label for="${id}">${label}（日）</label>
        <input id="${id}" type="number" min="1" value="${val}">
      `;
      wrap.appendChild(box);
      box.querySelector("input").addEventListener("input", e=>{
        const v = parseInt(e.target.value,10);
        if(v>0) DEFAULTS[key] = v;
        if($("#carrier").value === key){
          $("#days").value = DEFAULTS[key];
          computePreview();
        }
        saveProfile();
      });
      box.classList.add("carrier-default");
    });
  }

  // ===== render all =====
  function renderAll(){
    computePreview();
    renderSummary();
    renderLines();
    renderEligibility();
  }

  // ===== Share URL for single preview =====
  function makeShareURL(){
    const params = new URLSearchParams();
    params.set("d", $("#contract").value || "");
    params.set("c", $("#carrier").value);
    params.set("n", $("#note").value || "");
    params.set("days", $("#days").value || "");
    Object.entries(DEFAULTS).forEach(([k,v])=> params.set(`def_${k}`, String(v)));
    return `${location.origin}${location.pathname}?${params.toString()}`;
  }
  function restoreFromQuery(){
    const q = new URLSearchParams(location.search);
    const set = (id,key=id) => { if(q.get(key)) $(id).value = q.get(key) };
    set("#contract","d"); set("#note","n");
    if(q.get("c")) $("#carrier").value = q.get("c");
    if(q.get("days")) $("#days").value = q.get("days");
    Object.keys(DEFAULTS).forEach(k=>{
      const v = parseInt(q.get(`def_${k}`) || "", 10);
      if(Number.isFinite(v) && v>0) DEFAULTS[k]=v;
    });
  }

  // ===== Init & wiring =====
  function initTodayIfEmpty(){
    if(!$("#contract").value){
      const t = todayLocal();
      $("#contract").value = fmtISO(t);
    }
  }

  // Profile UI
  $("#profileSwitchBtn").addEventListener("click", ()=>{
    const name = ($("#profileName").value||"").trim() || "default";
    loadProfile(name);
    saveProfile();
    alert("プロフィールを切り替えました（新規名なら新規作成）。");
  });
  $("#profileExportBtn").addEventListener("click", ()=>{
    const data = localStorage.getItem(keyFor(profileName)) || JSON.stringify({defaults:DEFAULTS, lines:LINES}, null, 2);
    const blob = new Blob([data], {type:"application/json"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url; a.download = `mnp_profile_${profileName}.json`;
    document.body.appendChild(a); a.click(); a.remove();
    URL.revokeObjectURL(url);
  });
  $("#profileImportBtn").addEventListener("click", ()=>{
    const inp = document.createElement("input");
    inp.type = "file"; inp.accept = "application/json";
    inp.onchange = e=>{
      const file = e.target.files[0]; if(!file) return;
      const fr = new FileReader();
      fr.onload = ()=>{
        try{
          const obj = JSON.parse(fr.result);
          if(obj && typeof obj==="object"){
            DEFAULTS = obj.defaults || DEFAULTS;
            LINES = Array.isArray(obj.lines)? obj.lines : LINES;
            saveProfile(); renderAll(); alert("読み込みました。");
          }else alert("JSONの形式が不正です。");
        }catch(_){ alert("JSONの読み込みに失敗しました。"); }
      };
      fr.readAsText(file);
    };
    inp.click();
  });
  $("#profileClearBtn").addEventListener("click", ()=>{
    if(confirm(`プロフィール「${profileName}」のデータ（既定日数・回線）を全削除します。よろしいですか？`)){
      DEFAULTS = JSON.parse(JSON.stringify(DEFAULTS_GLOBAL));
      LINES = [];
      saveProfile(); renderAll();
    }
  });

  // Single form
  $("#carrier").addEventListener("change", e=>{
    const key = e.target.value;
    $("#days").value = DEFAULTS[key] ?? 181;
    computePreview();
  });
  ["#contract","#days","#note","#upcomingDays"].forEach(sel=> $(sel).addEventListener("input", ()=>{ computePreview(); renderEligibility(); }));
  $$("#counting input").forEach(r => r.addEventListener("change", computePreview));

  $("#todayBtn").addEventListener("click", ()=>{ initTodayIfEmpty(); computePreview(); });

  $("#resetBtn").addEventListener("click", ()=>{
    $("#contract").value = "";
    $("#carrier").value = "ymobile";
    $("#days").value = DEFAULTS["ymobile"];
    $("#note").value = "";
    initTodayIfEmpty(); computePreview();
  });

  $("#shareBtn").addEventListener("click", async ()=>{
    const url = makeShareURL();
    try{ await navigator.clipboard.writeText(url); alert("共有リンクをコピーしました！\n" + url); }
    catch(e){ prompt("共有リンク（手動でコピー）", url); }
  });

  // Add line
  $("#addLineBtn").addEventListener("click", ()=>{
    const date = $("#contract").value;
    const carrier = $("#carrier").value;
    const days = parseInt($("#days").value,10);
    const note = $("#note").value || "";
    if(!date){ alert("契約日を入力してください"); return; }
    if(!Number.isFinite(days) || days<=0){ alert("日数が正しくありません"); return; }
    LINES.push({ id: Date.now()+""+Math.random().toString(16).slice(2), date, carrier, note, days, options: [] });
    saveProfile(); renderAll();
  });

  // Export / Import / Clear lines only
  $("#exportBtn").addEventListener("click", ()=>{
    const data = JSON.stringify(LINES, null, 2);
    const blob = new Blob([data], {type:"application/json"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url; a.download = `mnp_lines_${profileName}.json`;
    document.body.appendChild(a); a.click(); a.remove();
    URL.revokeObjectURL(url);
  });

  $("#importBtn").addEventListener("click", ()=>{
    const inp = document.createElement("input");
    inp.type = "file"; inp.accept = "application/json";
    inp.onchange = e=>{
      const file = e.target.files[0];
      if(!file) return;
      const fr = new FileReader();
      fr.onload = ()=>{
        try{
          const arr = JSON.parse(fr.result);
          if(Array.isArray(arr)){
            LINES = arr.filter(x=> x && x.date && x.carrier && x.days);
            saveProfile(); renderAll();
            alert("回線を読み込みました。");
          }else{
            alert("JSON形式が不正です。");
          }
        }catch(_){ alert("JSONの読み込みに失敗しました。"); }
      };
      fr.readAsText(file);
    };
    inp.click();
  });

  $("#clearBtn").addEventListener("click", ()=>{
    if(confirm("回線データのみ全削除します。よろしいですか？")){
      LINES = []; saveProfile(); renderAll();
    }
  });

  // Defaults editor
  function renderDefaults(){
    const wrap = $("#defaults");
    wrap.innerHTML = "";
    Object.entries(DEFAULTS).forEach(([key, val])=>{
      const label = LABELS[key] || key;
      const id = `def_${key}`;
      const box = document.createElement("div");
      box.innerHTML = `
        <label for="${id}">${label}（日）</label>
        <input id="${id}" type="number" min="1" value="${val}">
      `;
      wrap.appendChild(box);
      box.querySelector("input").addEventListener("input", e=>{
        const v = parseInt(e.target.value,10);
        if(v>0) DEFAULTS[key] = v;
        if($("#carrier").value === key){ $("#days").value = DEFAULTS[key]; computePreview(); }
        saveProfile();
      });
    });
  }

  // Boot
  restoreFromQuery();
  initTodayIfEmpty();
  // 最初のプロフィール名：URLの ?profile=xxx があればそれを使用
  const qp = new URLSearchParams(location.search);
  const p0 = qp.get("profile") || "default";
  loadProfile(p0);
  saveProfile();
  renderAll();
</script>
</body>
</html>
