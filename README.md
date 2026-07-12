[log-analyzer.html](https://github.com/user-attachments/files/29935762/log-analyzer.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>EVIDENCE TERMINAL // Log Analyzer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=IBM+Plex+Mono:wght@400;500;600&family=IBM+Plex+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
:root{
  --bg:#0B0A08; --bg2:#0F0D09; --panel:#14120D; --panel2:#1A170F;
  --line:#2B2620; --line-soft:#1F1C15;
  --amber:#FFB000; --amber-dim:#8C6A1F; --amber-soft:rgba(255,176,0,0.08);
  --text:#E8E1D3; --text-dim:#8A8272; --text-faint:#54503F;
  --red:#FF4433; --red-soft:rgba(255,68,51,0.1);
  --orange:#FF8C3D; --orange-soft:rgba(255,140,61,0.1);
  --yellow:#E8C547; --yellow-soft:rgba(232,197,71,0.1);
  --green:#6FCF6F; --green-soft:rgba(111,207,111,0.1);
  --blue:#5FA8E8;
  --f-display:'Space Mono',monospace; --f-body:'IBM Plex Sans',sans-serif; --f-mono:'IBM Plex Mono',monospace;
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html,body{height:100%}
body{
  background:
    repeating-linear-gradient(180deg, rgba(255,255,255,0.008) 0px, rgba(255,255,255,0.008) 1px, transparent 1px, transparent 3px),
    radial-gradient(ellipse 70% 40% at 50% 0%, rgba(255,176,0,0.05), transparent 60%),
    var(--bg);
  color:var(--text); font-family:var(--f-body); font-size:13px;
  -webkit-font-smoothing:antialiased; min-height:100vh;
}
@media (prefers-reduced-motion: reduce){ *{animation-duration:0.001ms !important; transition-duration:0.001ms !important;} }
::-webkit-scrollbar{width:6px;height:6px}
::-webkit-scrollbar-thumb{background:var(--line);border-radius:0}
::selection{background:var(--amber);color:#0B0A08}

.boot{border-bottom:1px solid var(--line);padding:14px 20px;display:flex;align-items:center;justify-content:space-between;gap:16px;background:linear-gradient(180deg, rgba(255,176,0,0.03), transparent);flex-wrap:wrap}
.boot-id{display:flex;align-items:center;gap:12px}
.boot-mark{width:32px;height:32px;border:1px solid var(--amber-dim);display:flex;align-items:center;justify-content:center;font-family:var(--f-display);font-weight:700;font-size:14px;color:var(--amber);position:relative;flex-shrink:0}
.boot-mark::after{content:'';position:absolute;inset:-4px;border:1px solid var(--line);pointer-events:none}
.boot-title{font-family:var(--f-display);font-weight:700;font-size:15px;letter-spacing:0.06em;color:var(--text)}
.boot-sub{font-family:var(--f-mono);font-size:10px;color:var(--text-dim);letter-spacing:0.05em;margin-top:2px}
.boot-clock{font-family:var(--f-mono);font-size:11px;color:var(--amber-dim);text-align:right;line-height:1.6}
.boot-clock b{color:var(--amber);font-weight:500}

.ticker{display:flex;border-bottom:1px solid var(--line);overflow-x:auto}
.tick{flex:1;min-width:110px;padding:12px 16px;border-right:1px solid var(--line);position:relative}
.tick:last-child{border-right:none}
.tick-lbl{font-family:var(--f-mono);font-size:9px;text-transform:uppercase;letter-spacing:0.1em;color:var(--text-faint);margin-bottom:5px}
.tick-val{font-family:var(--f-display);font-weight:700;font-size:21px;color:var(--text);font-variant-numeric:tabular-nums}
.tick.crit .tick-val{color:var(--red)} .tick.high .tick-val{color:var(--orange)}
.tick.med .tick-val{color:var(--yellow)} .tick.low .tick-val{color:var(--green)}
.tick.total .tick-val{color:var(--amber)} .tick.mitre .tick-val{color:var(--blue)}

.body-grid{display:grid;grid-template-columns:200px 1fr 320px;min-height:calc(100vh - 130px)}
@media (max-width:1000px){.body-grid{grid-template-columns:1fr}}

.rail{border-right:1px solid var(--line);padding:16px 0}
.rail-title{font-family:var(--f-mono);font-size:9px;text-transform:uppercase;letter-spacing:0.12em;color:var(--text-faint);padding:0 16px 10px}
.tape-item{display:flex;align-items:center;gap:10px;padding:9px 16px;cursor:pointer;border-left:2px solid transparent;color:var(--text-dim);font-family:var(--f-mono);font-size:11.5px;transition:background .1s,color .1s,border-color .1s}
.tape-item:hover{background:var(--panel);color:var(--text)}
.tape-item.active{border-left-color:var(--amber);background:var(--amber-soft);color:var(--amber)}
.tape-dot{width:6px;height:6px;border-radius:50%;flex-shrink:0}
.tape-item[data-sev="all"] .tape-dot{background:var(--text-dim)}
.tape-item[data-sev="critical"] .tape-dot{background:var(--red)}
.tape-item[data-sev="high"] .tape-dot{background:var(--orange)}
.tape-item[data-sev="medium"] .tape-dot{background:var(--yellow)}
.tape-item[data-sev="low"] .tape-dot{background:var(--green)}
.tape-count{margin-left:auto;font-size:10.5px;color:var(--text-faint)}
.rail-div{height:1px;background:var(--line);margin:14px 16px}
.rail-btn{margin:0 16px 8px;display:block;width:calc(100% - 32px);padding:8px 10px;background:transparent;border:1px solid var(--line);color:var(--text-dim);font-family:var(--f-mono);font-size:10.5px;text-transform:uppercase;letter-spacing:0.06em;cursor:pointer;transition:all .12s}
.rail-btn:hover{border-color:var(--amber-dim);color:var(--amber)}
.rail-btn.primary{border-color:var(--amber-dim);color:var(--amber);background:var(--amber-soft)}
.rail-btn.primary:hover{background:var(--amber);color:#0B0A08}

.center{display:flex;flex-direction:column;min-width:0}
.tab-bar{display:flex;border-bottom:1px solid var(--line)}
.tab{padding:11px 18px;font-family:var(--f-mono);font-size:11px;text-transform:uppercase;letter-spacing:0.06em;color:var(--text-faint);cursor:pointer;border-bottom:2px solid transparent}
.tab:hover{color:var(--text-dim)}
.tab.active{color:var(--amber);border-bottom-color:var(--amber)}

.intake{border-bottom:1px solid var(--line);padding:14px 18px;display:flex;gap:10px;align-items:center;flex-wrap:wrap}
.search-wrap{flex:1;min-width:180px;display:flex;align-items:center;gap:8px;border:1px solid var(--line);background:var(--panel);padding:8px 10px}
.search-wrap svg{width:13px;height:13px;stroke:var(--text-faint);flex-shrink:0}
.search-wrap input{flex:1;background:none;border:none;outline:none;color:var(--text);font-family:var(--f-mono);font-size:12px}
.search-wrap input::placeholder{color:var(--text-faint)}
.intake-btn{padding:8px 14px;background:transparent;border:1px solid var(--line);color:var(--text-dim);font-family:var(--f-mono);font-size:10.5px;text-transform:uppercase;letter-spacing:0.05em;cursor:pointer;transition:all .12s;white-space:nowrap}
.intake-btn:hover{border-color:var(--amber-dim);color:var(--amber)}

.view{display:none;flex:1;min-height:0;flex-direction:column}
.view.active{display:flex}

.table-wrap{flex:1;overflow:auto;position:relative}
table{width:100%;border-collapse:collapse;font-family:var(--f-mono);font-size:11.5px}
thead th{position:sticky;top:0;background:var(--bg2);text-align:left;padding:9px 12px;font-size:9.5px;text-transform:uppercase;letter-spacing:0.08em;color:var(--text-faint);border-bottom:1px solid var(--line);white-space:nowrap}
tbody tr{border-bottom:1px solid var(--line-soft);cursor:pointer;transition:background .1s}
tbody tr:hover{background:var(--panel)}
tbody tr.sel{background:var(--amber-soft)}
tbody td{padding:8px 12px;color:var(--text-dim);vertical-align:top}
td.msg{color:var(--text);max-width:0;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
td.idx{color:var(--text-faint);width:40px}
td.ts{white-space:nowrap;color:var(--text-faint)}
.sev-chip{display:inline-flex;align-items:center;gap:5px;padding:2px 7px;font-size:9.5px;font-weight:600;text-transform:uppercase;letter-spacing:0.05em;border:1px solid;white-space:nowrap}
.sev-critical{color:var(--red);border-color:rgba(255,68,51,.4);background:var(--red-soft)}
.sev-high{color:var(--orange);border-color:rgba(255,140,61,.4);background:var(--orange-soft)}
.sev-medium{color:var(--yellow);border-color:rgba(232,197,71,.4);background:var(--yellow-soft)}
.sev-low{color:var(--green);border-color:rgba(111,207,111,.4);background:var(--green-soft)}
.tactic-chip{display:inline-flex;align-items:center;padding:2px 7px;font-size:9.5px;border:1px solid var(--line);color:var(--blue);background:rgba(95,168,232,0.08);white-space:nowrap;font-family:var(--f-mono)}

.empty-state{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:70px 20px;color:var(--text-faint);text-align:center;gap:10px}
.empty-state svg{width:34px;height:34px;stroke:var(--text-faint);opacity:.5}
.empty-title{font-family:var(--f-display);font-weight:700;font-size:13px;color:var(--text-dim)}
.empty-sub{font-family:var(--f-mono);font-size:11px;max-width:280px;line-height:1.7}

/* Analytics view */
.analytics{padding:18px;overflow-y:auto;display:grid;grid-template-columns:1fr 1fr;gap:16px}
@media (max-width:700px){.analytics{grid-template-columns:1fr}}
.a-card{border:1px solid var(--line);background:var(--panel);padding:16px}
.a-card.full{grid-column:1/-1}
.a-title{font-family:var(--f-mono);font-size:10px;text-transform:uppercase;letter-spacing:0.1em;color:var(--text-faint);margin-bottom:14px}
.a-canvas-wrap{position:relative;height:220px}
.bar-list{display:flex;flex-direction:column;gap:8px}
.bar-row{display:grid;grid-template-columns:140px 1fr 40px;align-items:center;gap:10px;font-family:var(--f-mono);font-size:11px}
.bar-name{color:var(--text-dim);overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.bar-track{height:8px;background:var(--panel2);border:1px solid var(--line-soft);position:relative;overflow:hidden}
.bar-fill{height:100%;background:var(--amber)}
.bar-num{color:var(--text-faint);text-align:right}

.evidence{border-left:1px solid var(--line);padding:18px;display:flex;flex-direction:column;gap:14px;overflow-y:auto}
.ev-empty{color:var(--text-faint);font-family:var(--f-mono);font-size:11px;line-height:1.8;margin-top:20px}
.stamp{align-self:flex-start;font-family:var(--f-display);font-weight:700;font-size:13px;letter-spacing:0.1em;text-transform:uppercase;padding:6px 14px;border:2px solid;transform:rotate(-3deg)}
.stamp.sev-critical{color:var(--red);border-color:var(--red)}
.stamp.sev-high{color:var(--orange);border-color:var(--orange)}
.stamp.sev-medium{color:var(--yellow);border-color:var(--yellow)}
.stamp.sev-low{color:var(--green);border-color:var(--green)}
.ev-field{border-top:1px solid var(--line-soft);padding-top:10px}
.ev-lbl{font-family:var(--f-mono);font-size:9px;text-transform:uppercase;letter-spacing:0.1em;color:var(--text-faint);margin-bottom:5px}
.ev-val{font-family:var(--f-mono);font-size:12px;color:var(--text);word-break:break-word;line-height:1.6}
.ev-raw{background:var(--panel);border:1px solid var(--line);padding:10px;font-family:var(--f-mono);font-size:11px;color:var(--text-dim);white-space:pre-wrap;word-break:break-word;line-height:1.6;max-height:200px;overflow-y:auto}

.modal-bg{position:fixed;inset:0;background:rgba(6,5,3,0.75);display:none;align-items:center;justify-content:center;z-index:200;padding:20px}
.modal-bg.show{display:flex}
.modal{background:var(--panel2);border:1px solid var(--line);width:100%;max-width:560px;padding:20px}
.modal-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
.modal-title{font-family:var(--f-display);font-weight:700;font-size:14px;letter-spacing:0.04em}
.modal-x{background:none;border:none;color:var(--text-faint);font-size:18px;cursor:pointer;line-height:1}
.modal-x:hover{color:var(--text)}
.modal textarea{width:100%;min-height:180px;background:var(--bg2);border:1px solid var(--line);color:var(--text);font-family:var(--f-mono);font-size:11.5px;padding:10px;resize:vertical;outline:none}
.modal textarea:focus{border-color:var(--amber-dim)}
.modal-hint{font-family:var(--f-mono);font-size:10px;color:var(--text-faint);margin-top:8px;line-height:1.6}
.modal-actions{display:flex;gap:8px;margin-top:14px;justify-content:flex-end}
button:focus-visible, input:focus-visible, .tape-item:focus-visible, tr:focus-visible, .tab:focus-visible{outline:1px solid var(--amber);outline-offset:1px}
</style>
</head>
<body>

<div class="boot">
  <div class="boot-id">
    <div class="boot-mark">ET</div>
    <div>
      <div class="boot-title">EVIDENCE TERMINAL</div>
      <div class="boot-sub">LOG ANALYZER — DETECTION + ANALYTICS, LOCAL SESSION ONLY</div>
    </div>
  </div>
  <div class="boot-clock" id="clock">— : — : —</div>
</div>

<div class="ticker">
  <div class="tick total"><div class="tick-lbl">Total Entries</div><div class="tick-val" id="k-total">0</div></div>
  <div class="tick crit"><div class="tick-lbl">Critical</div><div class="tick-val" id="k-crit">0</div></div>
  <div class="tick high"><div class="tick-lbl">High</div><div class="tick-val" id="k-high">0</div></div>
  <div class="tick med"><div class="tick-lbl">Medium</div><div class="tick-val" id="k-med">0</div></div>
  <div class="tick low"><div class="tick-lbl">Low / Info</div><div class="tick-val" id="k-low">0</div></div>
  <div class="tick mitre"><div class="tick-lbl">Tactics Seen</div><div class="tick-val" id="k-mitre">0</div></div>
</div>

<div class="body-grid">

  <div class="rail">
    <div class="rail-title">Filter Tape</div>
    <div class="tape-item active" data-sev="all" tabindex="0"><span class="tape-dot"></span>All entries<span class="tape-count" id="c-all">0</span></div>
    <div class="tape-item" data-sev="critical" tabindex="0"><span class="tape-dot"></span>Critical<span class="tape-count" id="c-critical">0</span></div>
    <div class="tape-item" data-sev="high" tabindex="0"><span class="tape-dot"></span>High<span class="tape-count" id="c-high">0</span></div>
    <div class="tape-item" data-sev="medium" tabindex="0"><span class="tape-dot"></span>Medium<span class="tape-count" id="c-medium">0</span></div>
    <div class="tape-item" data-sev="low" tabindex="0"><span class="tape-dot"></span>Low / Info<span class="tape-count" id="c-low">0</span></div>
    <div class="rail-div"></div>
    <button class="rail-btn primary" id="btn-load">+ Load Logs</button>
    <button class="rail-btn" id="btn-sample">Load Sample Tape</button>
    <button class="rail-btn" id="btn-clear">Clear Session</button>
  </div>

  <div class="center">
    <div class="tab-bar">
      <div class="tab active" data-view="tape">Log Tape</div>
      <div class="tab" data-view="analytics">Analytics</div>
    </div>

    <div class="view active" id="view-tape">
      <div class="intake">
        <div class="search-wrap">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round"><circle cx="11" cy="11" r="7"/><path d="m21 21-4.3-4.3"/></svg>
          <input type="text" id="search" placeholder="search entries — user, ip, keyword…">
        </div>
        <input type="file" id="file-input" accept=".json,.csv,.log,.txt" style="display:none" multiple>
        <button class="intake-btn" id="btn-file">Upload File</button>
      </div>
      <div class="table-wrap" id="table-wrap">
        <div class="empty-state" id="empty-state">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="1.4" stroke-linecap="round" stroke-linejoin="round"><path d="M9 2H4a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h5m0-20h11a2 2 0 0 1 2 2v16a2 2 0 0 1-2 2H9m0-20v20"/><path d="M13 8h5M13 12h5M13 16h3"/></svg>
          <div class="empty-title">No tape loaded</div>
          <div class="empty-sub">Paste or upload logs (JSON array, CSV, or plain text) to begin triage. Everything stays in this browser tab.</div>
        </div>
        <table id="log-table" style="display:none">
          <thead><tr><th class="idx">#</th><th>Severity</th><th>Tactic</th><th class="ts">Timestamp</th><th>Message</th></tr></thead>
          <tbody id="log-body"></tbody>
        </table>
      </div>
    </div>

    <div class="view" id="view-analytics">
      <div class="analytics" id="analytics-wrap">
        <div class="a-card"><div class="a-title">Severity Breakdown</div><div class="a-canvas-wrap"><canvas id="chart-sev"></canvas></div></div>
        <div class="a-card"><div class="a-title">Detected Tactics</div><div class="a-canvas-wrap"><canvas id="chart-tactic"></canvas></div></div>
        <div class="a-card full"><div class="a-title">Event Timeline</div><div class="a-canvas-wrap"><canvas id="chart-timeline"></canvas></div></div>
        <div class="a-card"><div class="a-title">Top Actors</div><div class="bar-list" id="top-users"></div></div>
        <div class="a-card"><div class="a-title">Top Hosts / IPs</div><div class="bar-list" id="top-hosts"></div></div>
      </div>
    </div>
  </div>

  <div class="evidence" id="evidence">
    <div class="ev-empty">Select an entry from the tape to inspect it here.</div>
  </div>

</div>

<div class="modal-bg" id="modal-bg">
  <div class="modal">
    <div class="modal-head"><div class="modal-title">PASTE LOG TAPE</div><button class="modal-x" id="modal-x">&times;</button></div>
    <textarea id="paste-area" placeholder='[{"timestamp":"2026-07-12T03:14:00Z","user":"j.reyes","message":"Failed login attempt — invalid credentials"}]

or plain lines:
2026-07-12 03:14:00 auth: failed login for user j.reyes from 10.4.2.19'></textarea>
    <div class="modal-hint">Accepts a JSON array of objects, CSV with headers, or raw text (one entry per line). Severity and tactic are inferred from keywords in each entry.</div>
    <div class="modal-actions">
      <button class="rail-btn" id="modal-cancel">Cancel</button>
      <button class="rail-btn primary" id="modal-ingest">Ingest Tape</button>
    </div>
  </div>
</div>

<script>
(function(){
  let entries = [];
  let activeSev = 'all';
  let selectedId = null;
  let charts = {};

  // Ordered ruleset: first match wins. Each rule = severity + tactic (MITRE-style) + technique label.
  const RULES = [
    { sev:'critical', tactic:'Impact',               tech:'T1486', words:['ransomware','mass file encryption','wiper','encrypted your files','service stopped unexpectedly'] },
    { sev:'critical', tactic:'Command and Control',   tech:'T1071', words:['c2','backdoor','beacon','known malicious ip','command and control'] },
    { sev:'critical', tactic:'Exfiltration',          tech:'T1041', words:['exfiltrat','large outbound transfer','data leak','upload to unknown host'] },
    { sev:'high',     tactic:'Credential Access',     tech:'T1110', words:['brute force','failed login','invalid credentials','password spray','account locked'] },
    { sev:'high',     tactic:'Privilege Escalation',  tech:'T1078', words:['privilege escalation','admin group membership','sudo added','elevated to administrator'] },
    { sev:'high',     tactic:'Defense Evasion',       tech:'T1027', words:['encoded command','obfuscated','disabled logging','cleared event log','antivirus disabled'] },
    { sev:'high',     tactic:'Lateral Movement',      tech:'T1021', words:['psexec','pass-the-hash','lateral movement','remote desktop from unusual host','smb admin share'] },
    { sev:'high',     tactic:'Initial Access',        tech:'T1566', words:['phishing','malicious attachment','exploit public-facing','malware signature detected'] },
    { sev:'medium',   tactic:'Persistence',           tech:'T1136', words:['new user account created','scheduled task created','registry run key','startup entry added'] },
    { sev:'medium',   tactic:'Discovery',             tech:'T1046', words:['port scan','network scan','whoami','systeminfo','subnet scan'] },
    { sev:'medium',   tactic:'Execution',             tech:'T1059', words:['powershell','cmd.exe spawned','script execution','suspicious process'] },
    { sev:'medium',   tactic:'Collection',            tech:'T1005', words:['mass file access','screen capture','unusual anomaly','response time anomaly','timeout'] },
    { sev:'medium',   tactic:null,                    tech:null,    words:['warn','warning','suspicious','unusual','retry'] },
  ];
  function classify(text){
    const t = (text||'').toLowerCase();
    for(const rule of RULES){
      if(rule.words.some(w=>t.includes(w))) return { severity:rule.sev, tactic:rule.tactic, tech:rule.tech };
    }
    return { severity:'low', tactic:null, tech:null };
  }

  function clockTick(){
    const d = new Date();
    document.getElementById('clock').innerHTML = d.toISOString().slice(0,10) + '  <b>' + d.toTimeString().slice(0,8) + '</b>';
  }
  clockTick(); setInterval(clockTick, 1000);

  function parseInput(raw){
    raw = raw.trim();
    if(!raw) return [];
    try{
      const j = JSON.parse(raw);
      if(Array.isArray(j)) return j.map(normalizeObj);
    }catch(e){}
    const lines = raw.split(/\r?\n/).filter(l=>l.trim().length);
    if(lines.length > 1 && lines[0].includes(',') && !lines[0].includes('{')){
      const headers = lines[0].split(',').map(h=>h.trim().toLowerCase());
      if(headers.length > 1){
        return lines.slice(1).map(line=>{
          const cells = line.split(',');
          const obj = {};
          headers.forEach((h,i)=> obj[h] = (cells[i]||'').trim());
          return normalizeObj(obj);
        });
      }
    }
    return lines.map(line=>{
      const m = line.match(/^(\d{4}-\d{2}-\d{2}[T ]\d{2}:\d{2}:\d{2})/);
      return normalizeObj({ timestamp: m ? m[1] : '', message: m ? line.slice(m[1].length).trim() : line });
    });
  }
  function normalizeObj(o){
    const message = o.message || o.msg || o.event || o.description || JSON.stringify(o);
    const timestamp = o.timestamp || o.time || o.ts || o.date || '';
    const cls = classify(message + ' ' + JSON.stringify(o));
    return {
      raw:o, timestamp, message,
      user: o.user || o.username || o.actor || '',
      host: o.host || o.hostname || o.ip || o.source_ip || '',
      severity: (o.severity||o.level||'').toLowerCase() || cls.severity,
      tactic: cls.tactic, tech: cls.tech
    };
  }

  function render(){
    const tbody = document.getElementById('log-body');
    const table = document.getElementById('log-table');
    const empty = document.getElementById('empty-state');
    const q = document.getElementById('search').value.trim().toLowerCase();

    const counts = {all:entries.length, critical:0, high:0, medium:0, low:0};
    const tacticSet = new Set();
    entries.forEach(e=>{ if(counts[e.severity]!==undefined) counts[e.severity]++; else counts.low++; if(e.tactic) tacticSet.add(e.tactic); });
    document.getElementById('k-total').textContent = counts.all;
    document.getElementById('k-crit').textContent = counts.critical;
    document.getElementById('k-high').textContent = counts.high;
    document.getElementById('k-med').textContent = counts.medium;
    document.getElementById('k-low').textContent = counts.low;
    document.getElementById('k-mitre').textContent = tacticSet.size;
    document.getElementById('c-all').textContent = counts.all;
    document.getElementById('c-critical').textContent = counts.critical;
    document.getElementById('c-high').textContent = counts.high;
    document.getElementById('c-medium').textContent = counts.medium;
    document.getElementById('c-low').textContent = counts.low;

    if(entries.length === 0){
      empty.style.display = 'flex'; table.style.display = 'none';
      renderAnalytics();
      return;
    }

    let filtered = entries.map((e,i)=>({...e,_id:i}));
    if(activeSev !== 'all') filtered = filtered.filter(e=>e.severity===activeSev);
    if(q) filtered = filtered.filter(e=>(e.message+e.user+e.host+e.timestamp+(e.tactic||'')).toLowerCase().includes(q));

    if(filtered.length === 0){
      empty.style.display = 'flex'; table.style.display = 'none';
      document.querySelector('.empty-title').textContent = 'No matches';
      document.querySelector('.empty-sub').textContent = 'Nothing in this tape matches the current filter and search.';
      renderAnalytics();
      return;
    }
    empty.style.display = 'none'; table.style.display = 'table';

    tbody.innerHTML = filtered.map(e=>`
      <tr data-id="${e._id}" class="${e._id===selectedId?'sel':''}" tabindex="0">
        <td class="idx">${e._id+1}</td>
        <td><span class="sev-chip sev-${e.severity}">${e.severity}</span></td>
        <td>${e.tactic ? `<span class="tactic-chip">${escapeHtml(e.tactic)}${e.tech ? ' · '+e.tech : ''}</span>` : '<span style="color:var(--text-faint)">—</span>'}</td>
        <td class="ts">${escapeHtml(e.timestamp||'—')}</td>
        <td class="msg">${escapeHtml(e.message)}</td>
      </tr>
    `).join('');

    tbody.querySelectorAll('tr').forEach(tr=>{
      tr.addEventListener('click', ()=>{ selectedId = parseInt(tr.dataset.id); render(); showEvidence(); });
      tr.addEventListener('keydown', ev=>{ if(ev.key==='Enter'){ selectedId = parseInt(tr.dataset.id); render(); showEvidence(); }});
    });

    renderAnalytics();
  }

  function showEvidence(){
    const panel = document.getElementById('evidence');
    if(selectedId===null || !entries[selectedId]){
      panel.innerHTML = '<div class="ev-empty">Select an entry from the tape to inspect it here.</div>';
      return;
    }
    const e = entries[selectedId];
    const verdict = e.severity==='critical'||e.severity==='high' ? 'FLAGGED' : e.severity==='medium' ? 'REVIEW' : 'CLEAR';
    panel.innerHTML = `
      <div class="stamp sev-${e.severity}">${verdict}</div>
      <div class="ev-field"><div class="ev-lbl">Entry</div><div class="ev-val">#${selectedId+1} — <span class="sev-chip sev-${e.severity}">${e.severity}</span></div></div>
      ${e.tactic ? `<div class="ev-field"><div class="ev-lbl">MITRE-style Tactic</div><div class="ev-val"><span class="tactic-chip">${escapeHtml(e.tactic)} · ${e.tech}</span></div></div>` : ''}
      <div class="ev-field"><div class="ev-lbl">Timestamp</div><div class="ev-val">${escapeHtml(e.timestamp || 'not provided')}</div></div>
      ${e.user ? `<div class="ev-field"><div class="ev-lbl">User / Actor</div><div class="ev-val">${escapeHtml(e.user)}</div></div>` : ''}
      ${e.host ? `<div class="ev-field"><div class="ev-lbl">Host / IP</div><div class="ev-val">${escapeHtml(e.host)}</div></div>` : ''}
      <div class="ev-field"><div class="ev-lbl">Message</div><div class="ev-val">${escapeHtml(e.message)}</div></div>
      <div class="ev-field"><div class="ev-lbl">Raw Entry</div><div class="ev-raw">${escapeHtml(JSON.stringify(e.raw, null, 2))}</div></div>
    `;
  }

  function escapeHtml(s){ return String(s).replace(/[&<>"']/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c])); }

  // ---------- Analytics ----------
  function destroyCharts(){ Object.values(charts).forEach(c=>c && c.destroy()); charts = {}; }

  function renderAnalytics(){
    destroyCharts();
    const wrap = document.getElementById('analytics-wrap');
    if(entries.length === 0){
      wrap.style.opacity = 0.35; wrap.style.pointerEvents = 'none';
    } else {
      wrap.style.opacity = 1; wrap.style.pointerEvents = 'auto';
    }

    const sevColors = {critical:'#FF4433', high:'#FF8C3D', medium:'#E8C547', low:'#6FCF6F'};
    const sevCounts = {critical:0, high:0, medium:0, low:0};
    entries.forEach(e=> sevCounts[e.severity] = (sevCounts[e.severity]||0)+1);

    charts.sev = new Chart(document.getElementById('chart-sev'), {
      type:'doughnut',
      data:{ labels:Object.keys(sevCounts).map(k=>k[0].toUpperCase()+k.slice(1)),
        datasets:[{ data:Object.values(sevCounts), backgroundColor:Object.keys(sevCounts).map(k=>sevColors[k]), borderColor:'#0B0A08', borderWidth:2 }] },
      options:{ plugins:{legend:{position:'bottom',labels:{color:'#8A8272',font:{family:'IBM Plex Mono',size:10},boxWidth:10,padding:12}}}, cutout:'62%', maintainAspectRatio:false }
    });

    const tacticCounts = {};
    entries.forEach(e=>{ if(e.tactic) tacticCounts[e.tactic] = (tacticCounts[e.tactic]||0)+1; });
    const tacticLabels = Object.keys(tacticCounts);
    charts.tactic = new Chart(document.getElementById('chart-tactic'), {
      type:'bar',
      data:{ labels:tacticLabels, datasets:[{ data:tacticLabels.map(k=>tacticCounts[k]), backgroundColor:'#5FA8E8' }] },
      options:{ indexAxis:'y', plugins:{legend:{display:false}},
        scales:{ x:{ticks:{color:'#54503F',font:{family:'IBM Plex Mono',size:9}},grid:{color:'#1F1C15'}},
                 y:{ticks:{color:'#8A8272',font:{family:'IBM Plex Mono',size:10}},grid:{display:false}} },
        maintainAspectRatio:false }
    });

    // Timeline: bucket by hour if timestamps parse, else by index groups of 5
    const buckets = {};
    let hasTime = false;
    entries.forEach(e=>{
      const d = new Date(e.timestamp);
      let key;
      if(!isNaN(d.getTime())){ hasTime = true; key = d.toISOString().slice(0,13)+':00'; }
      else key = 'unspecified';
      buckets[key] = buckets[key] || {critical:0,high:0,medium:0,low:0};
      buckets[key][e.severity] = (buckets[key][e.severity]||0) + 1;
    });
    const bucketKeys = Object.keys(buckets).sort();
    charts.timeline = new Chart(document.getElementById('chart-timeline'), {
      type:'bar',
      data:{ labels:bucketKeys.map(k=> hasTime ? k.slice(11,16) : k),
        datasets:['critical','high','medium','low'].map(sev=>({
          label:sev[0].toUpperCase()+sev.slice(1), data:bucketKeys.map(k=>buckets[k][sev]||0),
          backgroundColor:sevColors[sev], stack:'s'
        })) },
      options:{ plugins:{legend:{position:'bottom',labels:{color:'#8A8272',font:{family:'IBM Plex Mono',size:10},boxWidth:10,padding:12}}},
        scales:{ x:{stacked:true,ticks:{color:'#54503F',font:{family:'IBM Plex Mono',size:9}},grid:{display:false}},
                 y:{stacked:true,ticks:{color:'#54503F',font:{family:'IBM Plex Mono',size:9},precision:0},grid:{color:'#1F1C15'}} },
        maintainAspectRatio:false }
    });

    renderBarList('top-users', tally(entries,'user'));
    renderBarList('top-hosts', tally(entries,'host'));
  }

  function tally(list, field){
    const m = {};
    list.forEach(e=>{ const v = e[field]; if(v) m[v] = (m[v]||0)+1; });
    return Object.entries(m).sort((a,b)=>b[1]-a[1]).slice(0,8);
  }
  function renderBarList(id, pairs){
    const el = document.getElementById(id);
    if(pairs.length === 0){ el.innerHTML = '<div style="font-family:var(--f-mono);font-size:11px;color:var(--text-faint)">No data yet</div>'; return; }
    const max = pairs[0][1];
    el.innerHTML = pairs.map(([name,count])=>`
      <div class="bar-row">
        <div class="bar-name">${escapeHtml(name)}</div>
        <div class="bar-track"><div class="bar-fill" style="width:${(count/max*100).toFixed(0)}%"></div></div>
        <div class="bar-num">${count}</div>
      </div>
    `).join('');
  }

  // ---------- Ingest ----------
  function ingest(raw){
    const parsed = parseInput(raw);
    if(parsed.length === 0) return;
    entries = entries.concat(parsed);
    selectedId = null; activeSev = 'all';
    document.querySelectorAll('.tape-item').forEach(t=>t.classList.remove('active'));
    document.querySelector('.tape-item[data-sev="all"]').classList.add('active');
    render(); showEvidence();
  }

  const SAMPLE = [
    {timestamp:"2026-07-12T02:58:11Z", user:"svc-backup", host:"10.1.4.22", message:"Scheduled backup job completed successfully"},
    {timestamp:"2026-07-12T03:01:44Z", user:"j.reyes", host:"10.4.2.19", message:"Failed login attempt — invalid credentials (3rd try)"},
    {timestamp:"2026-07-12T03:02:09Z", user:"j.reyes", host:"10.4.2.19", message:"Account locked after repeated failed login attempts"},
    {timestamp:"2026-07-12T03:04:55Z", user:"unknown", host:"185.220.101.7", message:"Unauthorized access attempt blocked by firewall rule 402"},
    {timestamp:"2026-07-12T03:07:31Z", user:"admin", host:"10.1.1.5", message:"Suspicious PowerShell execution with encoded command"},
    {timestamp:"2026-07-12T03:09:02Z", user:"admin", host:"10.1.1.5", message:"Potential ransomware behavior detected — mass file encryption pattern"},
    {timestamp:"2026-07-12T03:11:18Z", user:"m.chen", host:"10.4.2.30", message:"New user account created by admin"},
    {timestamp:"2026-07-12T03:12:40Z", user:"svc-web", host:"10.1.2.8", message:"Response time anomaly detected on /api/checkout endpoint"},
    {timestamp:"2026-07-12T03:15:03Z", user:"unknown", host:"91.203.5.44", message:"Data exfiltration attempt flagged — large outbound transfer to unknown host"},
    {timestamp:"2026-07-12T03:17:59Z", user:"system", host:"10.1.1.1", message:"Routine health check passed on all nodes"},
    {timestamp:"2026-07-12T03:22:14Z", user:"unknown", host:"185.220.101.7", message:"Port scan detected across internal subnet 10.4.2.0/24"},
    {timestamp:"2026-07-12T03:26:40Z", user:"unknown", host:"45.155.205.13", message:"Backdoor process detected communicating with known C2 infrastructure"}
  ];

  document.querySelectorAll('.tape-item').forEach(item=>{
    item.addEventListener('click', ()=>{
      document.querySelectorAll('.tape-item').forEach(t=>t.classList.remove('active'));
      item.classList.add('active'); activeSev = item.dataset.sev; render();
    });
    item.addEventListener('keydown', ev=>{ if(ev.key==='Enter') item.click(); });
  });

  document.querySelectorAll('.tab').forEach(tab=>{
    tab.addEventListener('click', ()=>{
      document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
      document.querySelectorAll('.view').forEach(v=>v.classList.remove('active'));
      tab.classList.add('active');
      document.getElementById('view-'+tab.dataset.view).classList.add('active');
      if(tab.dataset.view === 'analytics') renderAnalytics();
    });
  });

  document.getElementById('search').addEventListener('input', render);
  document.getElementById('btn-load').addEventListener('click', ()=> document.getElementById('modal-bg').classList.add('show'));
  document.getElementById('modal-x').addEventListener('click', ()=> document.getElementById('modal-bg').classList.remove('show'));
  document.getElementById('modal-cancel').addEventListener('click', ()=> document.getElementById('modal-bg').classList.remove('show'));
  document.getElementById('modal-ingest').addEventListener('click', ()=>{
    ingest(document.getElementById('paste-area').value);
    document.getElementById('modal-bg').classList.remove('show');
    document.getElementById('paste-area').value = '';
  });
  document.getElementById('modal-bg').addEventListener('click', e=>{ if(e.target.id==='modal-bg') document.getElementById('modal-bg').classList.remove('show'); });

  document.getElementById('btn-sample').addEventListener('click', ()=>{ entries = []; ingest(JSON.stringify(SAMPLE)); });

  document.getElementById('btn-clear').addEventListener('click', ()=>{
    entries = []; selectedId = null; activeSev='all';
    document.querySelectorAll('.tape-item').forEach(t=>t.classList.remove('active'));
    document.querySelector('.tape-item[data-sev="all"]').classList.add('active');
    document.querySelector('.empty-title').textContent = 'No tape loaded';
    document.querySelector('.empty-sub').textContent = 'Paste or upload logs (JSON array, CSV, or plain text) to begin triage. Everything stays in this browser tab.';
    render(); showEvidence();
  });

  document.getElementById('btn-file').addEventListener('click', ()=> document.getElementById('file-input').click());
  document.getElementById('file-input').addEventListener('change', e=>{
    Array.from(e.target.files).forEach(f=>{
      const reader = new FileReader();
      reader.onload = ev => ingest(ev.target.result);
      reader.readAsText(f);
    });
    e.target.value = '';
  });

  render();
})();
</script>
</body>
</html>
