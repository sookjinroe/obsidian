<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MetaZen — 메타데이터 자동 증강 Agent</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#080d18;--surface:#0f1623;--card:#141d2e;--card2:#1a2438;
  --border:#1e2d45;--border2:#253550;
  --teal:#00c9a7;--teal-dim:#00c9a718;--teal-mid:#00c9a740;
  --blue:#3b82f6;--blue-dim:#3b82f618;
  --amber:#f59e0b;--amber-dim:#f59e0b18;
  --purple:#a855f7;--purple-dim:#a855f718;--purple-mid:#a855f740;
  --red:#ef4444;--red-dim:#ef444418;
  --green:#22c55e;--green-dim:#22c55e18;
  --text:#e2e8f0;--text-muted:#64748b;--text-dim:#94a3b8;
  --font:'Noto Sans KR',sans-serif;--mono:'JetBrains Mono',monospace;
  --lnb-w:214px;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:var(--font);background:var(--bg);color:var(--text);min-height:100vh;font-size:13px;line-height:1.6;}

/* ── TOPBAR ── */
.topbar{position:fixed;top:0;left:0;right:0;z-index:200;background:rgba(8,13,24,.96);backdrop-filter:blur(12px);border-bottom:1px solid var(--border);height:56px;display:flex;align-items:center;padding:0 20px 0 0;}
.tb-brand{width:var(--lnb-w);display:flex;align-items:center;gap:10px;padding:0 18px;flex-shrink:0;border-right:1px solid var(--border);height:100%;}
.tb-logo{font-size:16px;font-weight:800;color:var(--teal);letter-spacing:.04em;}
.tb-logo-sub{font-size:9px;color:var(--text-muted);letter-spacing:.06em;text-transform:uppercase;margin-top:-2px;}
.tb-title{font-size:12px;color:var(--text-dim);padding:0 20px;}
.tb-right{display:flex;align-items:center;gap:12px;margin-left:auto;}
.ctx-pill{display:flex;align-items:center;gap:8px;background:var(--card);border:1px solid var(--border);border-radius:6px;padding:5px 12px;font-family:var(--mono);font-size:11px;}
.ctx-dot{width:6px;height:6px;border-radius:50%;background:var(--teal);}
.ctx-l{color:var(--text-muted);}.ctx-v{color:var(--text-dim);}

/* ── LNB ── */
.lnb{position:fixed;top:56px;left:0;bottom:0;width:var(--lnb-w);background:var(--surface);border-right:1px solid var(--border);z-index:99;display:flex;flex-direction:column;overflow-y:auto;}
.lnb-section{padding:10px 0;}
.lnb-sec-label{padding:6px 16px 3px;font-size:10px;font-weight:700;color:var(--text-muted);letter-spacing:.08em;text-transform:uppercase;}
.lnb-item{display:flex;align-items:center;gap:10px;padding:9px 16px;font-size:12px;font-weight:500;color:var(--text-dim);cursor:pointer;transition:all .15s;border-left:2px solid transparent;user-select:none;}
.lnb-item:hover{background:rgba(255,255,255,.03);color:var(--text);}
.lnb-item.active{color:var(--teal);border-left-color:var(--teal);background:var(--teal-dim);}
.lnb-ico{font-size:15px;flex-shrink:0;width:18px;text-align:center;}
.lnb-subnav{padding:2px 0 4px 44px;}
.lnb-sub{padding:5px 12px;font-size:11px;color:var(--text-muted);cursor:pointer;border-radius:4px;transition:all .15s;}
.lnb-sub:hover{color:var(--text-dim);}
.lnb-sub.active{color:var(--teal);font-weight:600;}
.lnb-divider{height:1px;background:var(--border);margin:4px 0;}
.lnb-bottom{margin-top:auto;border-top:1px solid var(--border);padding:8px 0;}
.lnb-workspace-item{display:flex;align-items:center;gap:10px;padding:8px 16px 8px 40px;font-size:12px;color:var(--text-dim);cursor:pointer;transition:all .15s;border-left:2px solid transparent;}
.lnb-workspace-item:hover{background:rgba(255,255,255,.03);color:var(--text);}
.lnb-workspace-item.active{color:var(--teal);border-left-color:var(--teal);background:var(--teal-dim);}

/* ── STEP BAR ── */
.step-bar{position:fixed;top:56px;left:var(--lnb-w);right:0;z-index:99;background:var(--surface);border-bottom:1px solid var(--border);padding:11px 24px;display:none;align-items:center;justify-content:center;}
.step-bar.show{display:flex;}
.si{display:flex;align-items:center;gap:8px;cursor:pointer;opacity:.38;transition:opacity .2s;}
.si.active{opacity:1;}.si.done{opacity:.65;}
.sn{width:20px;height:20px;border-radius:50%;border:1.5px solid var(--text-muted);display:flex;align-items:center;justify-content:center;font-size:10px;font-weight:600;color:var(--text-muted);transition:all .3s;flex-shrink:0;}
.si.active .sn{background:var(--teal);border-color:var(--teal);color:#000;box-shadow:0 0 10px var(--teal-mid);}
.si.done .sn{border-color:var(--teal);color:var(--teal);}
.sl{font-size:11px;font-weight:500;color:var(--text-dim);white-space:nowrap;}
.si.active .sl{color:var(--teal);}
.sarr{color:var(--border2);margin:0 8px;font-size:12px;}

/* ── LAYOUT ── */
.app{margin-left:var(--lnb-w);}
.view{display:none;}.view.active{display:block;}
.main{padding:24px 28px;}
.main.with-steps{margin-top:104px;}.main.no-steps{margin-top:72px;}
@keyframes fadeIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
.page{display:none;animation:fadeIn .3s ease;}.page.active{display:block;}
@keyframes spin{to{transform:rotate(360deg)}}

/* ── COMMON ── */
.sh{margin-bottom:20px;}.sh-t{font-size:17px;font-weight:700;}.sh-s{font-size:12px;color:var(--text-muted);margin-top:4px;}
.card{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:18px 20px;}
.card-title{font-size:11px;font-weight:600;color:var(--text-muted);letter-spacing:.08em;text-transform:uppercase;margin-bottom:12px;}
.btn{display:inline-flex;align-items:center;gap:6px;padding:8px 16px;border-radius:7px;border:1px solid;font-size:12px;font-weight:600;cursor:pointer;transition:all .18s;font-family:var(--font);}
.btn-primary{background:var(--teal);border-color:var(--teal);color:#000;}
.btn-primary:hover{background:#00e5c4;box-shadow:0 0 12px var(--teal-mid);}
.btn-primary:disabled{opacity:.4;cursor:not-allowed;}
.btn-ghost{background:transparent;border-color:var(--border2);color:var(--text-dim);}
.btn-ghost:hover{border-color:var(--teal-mid);color:var(--teal);}
.btn-danger{background:transparent;border-color:var(--border2);color:var(--red);}
.btn-danger:hover{background:var(--red-dim);border-color:var(--red);}
.btn-amber{background:var(--amber-dim);border-color:rgba(245,158,11,.3);color:var(--amber);}
.btn-amber:hover{border-color:var(--amber);}
.btn-blue{background:var(--blue-dim);border-color:rgba(59,130,246,.3);color:var(--blue);}
.btn-blue:hover{border-color:var(--blue);}
.btn-teal-o{background:transparent;border-color:var(--teal-mid);color:var(--teal);}
.btn-teal-o:hover{background:var(--teal-dim);}
.btn-regen{background:var(--purple-dim);border-color:var(--purple-mid);color:var(--purple);}
.btn-regen:hover{border-color:var(--purple);}
.btn-sm{padding:5px 12px;font-size:11px;border-radius:5px;}
.btn-xs{padding:3px 9px;font-size:10px;border-radius:4px;}
.ar{display:flex;align-items:center;gap:10px;margin-top:20px;}
.ar.right{justify-content:flex-end;}
.cb{width:14px;height:14px;accent-color:var(--teal);cursor:pointer;}
.ell{max-width:160px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;display:inline-block;}
.rlink{color:var(--blue);cursor:pointer;text-decoration:underline;font-size:11px;}

/* badges */
.cbadge{display:inline-flex;align-items:center;padding:3px 8px;border-radius:4px;font-size:11px;font-weight:600;font-family:var(--mono);}
.ch{background:var(--green-dim);color:var(--green);}.cm{background:var(--amber-dim);color:var(--amber);}.cl{background:var(--red-dim);color:var(--red);}
.sbadge{display:inline-block;padding:3px 8px;border-radius:4px;font-size:10px;font-weight:600;}
.sp{background:var(--blue-dim);color:var(--blue);}.sa{background:var(--green-dim);color:var(--green);}
.sm{background:var(--amber-dim);color:var(--amber);}.sr{background:var(--red-dim);color:var(--red);}
.srn{background:var(--purple-dim);color:var(--purple);}
.col-b{display:inline-block;padding:2px 7px;border-radius:3px;font-size:10px;font-weight:600;background:var(--purple-dim);color:var(--purple);}
.cls-badge{display:inline-flex;align-items:center;gap:4px;padding:3px 8px;border-radius:4px;font-size:10px;font-weight:600;}
.cls-new{background:var(--teal-dim);border:1px solid var(--teal-mid);color:var(--teal);}
.cls-re{background:var(--amber-dim);border:1px solid rgba(245,158,11,.3);color:var(--amber);}
.cls-done{background:var(--card2);border:1px solid var(--border2);color:var(--text-muted);}

/* table */
.twrap{background:var(--card);border:1px solid var(--border);border-radius:0 0 8px 8px;overflow:hidden;}
.twrap-r{background:var(--card);border:1px solid var(--border);border-radius:8px;overflow:hidden;}
table.gt{width:100%;border-collapse:collapse;}
table.gt th{padding:9px 12px;text-align:left;font-size:10px;font-weight:600;color:var(--text-muted);letter-spacing:.06em;text-transform:uppercase;border-bottom:1px solid var(--border);background:var(--card2);}
table.gt td{padding:9px 12px;font-size:12px;color:var(--text-dim);border-bottom:1px solid var(--border);vertical-align:middle;}
table.gt tr:last-child td{border-bottom:none;}
table.gt tr:hover td{background:rgba(255,255,255,.012);}
table.gt tr.row-sel td{background:rgba(0,201,167,.035);}
.bulk-bar{display:flex;align-items:center;gap:10px;padding:9px 14px;background:var(--card2);border:1px solid var(--border);border-radius:8px 8px 0 0;border-bottom:none;}
.filter-row{display:flex;align-items:center;gap:10px;margin-bottom:14px;}
.fb{padding:5px 14px;border-radius:5px;border:1px solid var(--border);background:var(--card2);color:var(--text-muted);font-size:11px;cursor:pointer;font-family:var(--font);transition:all .15s;}
.fb:hover{border-color:var(--border2);color:var(--text-dim);}.fb.act{background:var(--teal-dim);border-color:var(--teal-mid);color:var(--teal);}

/* miss bar */
.miss-mini{display:flex;align-items:center;gap:6px;}
.mt{width:56px;height:3px;background:var(--border);border-radius:2px;overflow:hidden;flex-shrink:0;}
.mf{height:100%;border-radius:2px;}
.mf.red{background:var(--red);}.mf.amber{background:var(--amber);}.mf.green{background:var(--green);}

/* mode select */
.mode-sel{font-size:11px;background:var(--card2);border:1px solid var(--border2);color:var(--text-dim);border-radius:5px;padding:4px 8px;font-family:var(--font);cursor:pointer;outline:none;width:100%;}
.mode-sel.empty-only{border-color:var(--teal-mid);color:var(--teal);}
.mode-sel.full-regen{border-color:var(--amber);color:var(--amber);}
.mode-disabled{font-size:11px;color:var(--text-muted);font-style:italic;}
.override-note{font-size:10px;color:var(--amber);margin-left:4px;}

/* stat cards */
.upload-stat{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:16px;}
.ustat-card{background:var(--card2);border:1px solid var(--border);border-radius:8px;padding:12px 14px;text-align:center;cursor:pointer;transition:border-color .2s;}
.ustat-card:hover{border-color:var(--border2);}
.ustat-card.act{border-color:var(--teal-mid);background:var(--teal-dim);}
.ustat-v{font-size:22px;font-weight:700;font-family:var(--mono);}
.ustat-l{font-size:11px;color:var(--text-muted);margin-top:2px;}

/* policy banner */
.policy-banner{background:var(--card2);border:1px solid var(--border);border-left:3px solid var(--blue);border-radius:0 8px 8px 0;padding:12px 16px;margin-bottom:16px;}
.pb-title{font-size:11px;font-weight:700;color:var(--blue);margin-bottom:8px;}
.pb-rules{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;}
.pb-rule{background:var(--card);border:1px solid var(--border);border-radius:6px;padding:10px 12px;}
.pb-rule-desc{font-size:11px;color:var(--text-muted);margin-top:5px;line-height:1.5;}

/* upload */
.upload-tabs{display:flex;gap:0;margin-bottom:18px;}
.utab{padding:9px 20px;font-size:12px;font-weight:600;border:1px solid var(--border);cursor:pointer;color:var(--text-muted);background:var(--card2);}
.utab:first-child{border-radius:7px 0 0 7px;}
.utab:last-child{border-radius:0 7px 7px 0;border-left:none;}
.utab.active{background:var(--teal-dim);border-color:var(--teal-mid);color:var(--teal);}
.drop-zone{border:2px dashed var(--border2);border-radius:10px;padding:36px 24px;text-align:center;cursor:pointer;transition:all .2s;}
.drop-zone:hover{border-color:var(--teal-mid);background:var(--teal-dim);}
.fmt-pill{padding:3px 10px;border-radius:4px;border:1px solid var(--border2);font-size:11px;color:var(--text-muted);font-family:var(--mono);}
.db-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:16px;}
.db-field label{display:block;font-size:11px;color:var(--text-muted);margin-bottom:5px;}
.db-input{width:100%;background:var(--card);border:1px solid var(--border2);border-radius:6px;padding:8px 10px;color:var(--text-dim);font-size:12px;font-family:var(--font);outline:none;}
.db-input:focus{border-color:var(--teal-mid);}
.sel-summary{margin-top:12px;padding:10px 16px;background:var(--card2);border:1px solid var(--border);border-radius:8px;display:none;}
.sel-inner{display:flex;align-items:center;gap:16px;flex-wrap:wrap;}

/* step 2 */
.s2-wrap{display:grid;grid-template-columns:1fr 320px;gap:16px;align-items:start;}
.s2-src-panel{background:var(--card);border:1px solid var(--border);border-radius:10px;overflow:hidden;position:sticky;top:120px;}
.s2-src-header{padding:12px 16px;border-bottom:1px solid var(--border);background:var(--card2);display:flex;align-items:center;gap:10px;}
.s2-src-title{font-size:12px;font-weight:600;color:var(--text);flex:1;}
.s2-src-body{padding:14px 16px;}
.s2-src-placeholder{text-align:center;padding:32px 16px;color:var(--text-muted);font-size:12px;}
.s2-src-row{display:flex;align-items:center;gap:8px;padding:8px 10px;background:var(--card2);border:1px solid var(--border);border-radius:6px;margin-bottom:6px;}
.s2-src-row:last-child{margin-bottom:0;}
.s2-src-name{font-size:12px;color:var(--text-dim);flex:1;}
.s2-src-ver{font-size:10px;color:var(--text-muted);font-family:var(--mono);}
.s2-src-auto{font-size:9px;background:var(--teal-dim);border:1px solid var(--teal-mid);color:var(--teal);border-radius:3px;padding:2px 5px;font-weight:600;}
.s2-src-note{font-size:11px;color:var(--text-muted);margin-top:12px;padding-top:10px;border-top:1px solid var(--border);line-height:1.5;}
.domain-sel{width:100%;background:var(--card2);border:1px solid var(--border2);border-radius:5px;padding:5px 8px;color:var(--text-dim);font-size:11px;font-family:var(--font);outline:none;cursor:pointer;}
.domain-sel.asgn{border-color:var(--teal-mid);color:var(--teal);}
.src-btn{font-size:10px;background:transparent;border:1px solid var(--border2);color:var(--text-muted);border-radius:4px;padding:3px 8px;cursor:pointer;white-space:nowrap;font-family:var(--font);}
.src-btn:hover{border-color:var(--teal-mid);color:var(--teal);}
.src-btn.active{border-color:var(--teal-mid);color:var(--teal);background:var(--teal-dim);}
.bulk-domain-bar{display:flex;align-items:center;gap:10px;padding:9px 14px;background:var(--card2);border:1px solid var(--border);border-radius:8px 8px 0 0;border-bottom:none;}

/* step 3 progress */
.prog-hdr{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px;}
.prog-track{height:6px;background:var(--border);border-radius:3px;overflow:hidden;margin-bottom:22px;}
.prog-fill{height:100%;background:linear-gradient(90deg,var(--teal),#00e5c4);border-radius:3px;transition:width .4s ease;}
.ph-title{font-size:11px;font-weight:600;color:var(--text-muted);letter-spacing:.08em;text-transform:uppercase;margin-bottom:10px;padding-left:4px;}
.ph-block{margin-bottom:18px;}
.ps-row{display:flex;align-items:center;gap:12px;padding:9px 14px;border-radius:7px;transition:background .2s;}
.ps-row.ps-done{background:rgba(0,201,167,.04);}.ps-row.ps-active{background:rgba(0,201,167,.08);}
.ps-ico{width:20px;height:20px;flex-shrink:0;display:flex;align-items:center;justify-content:center;font-size:14px;}
.ps-label{font-size:12px;color:var(--text-dim);flex:1;}
.ps-row.ps-active .ps-label{color:var(--text);font-weight:500;}.ps-row.ps-done .ps-label{color:var(--text-muted);}
.ps-status{font-size:10px;font-family:var(--mono);}
.ps-status.done{color:var(--teal);}.ps-status.active{color:var(--amber);}

/* step 4 table results */
.rs-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:22px;}
.rs-card{background:var(--card);border:1px solid var(--border);border-radius:8px;padding:14px 16px;text-align:center;}
.rs-v{font-size:26px;font-weight:700;font-family:var(--mono);}
.rs-card.hl{border-color:var(--teal-mid);}.rs-card.hl .rs-v{color:var(--teal);}
.rs-card.wn .rs-v{color:var(--amber);}
.rs-l{font-size:11px;color:var(--text-muted);margin-top:3px;}
.tsm{font-size:11px;font-family:var(--mono);padding:2px 6px;border-radius:3px;}
.tsm.g{background:var(--green-dim);color:var(--green);}
.tsm.r{background:var(--red-dim);color:var(--red);}
.tsm.s{background:var(--blue-dim);color:var(--blue);}
/* table-level review completion status */
.tbl-status{display:inline-flex;align-items:center;gap:6px;padding:4px 10px;border-radius:5px;font-size:11px;font-weight:600;}
.tbl-status.done{background:var(--green-dim);border:1px solid rgba(34,197,94,.3);color:var(--green);}
.tbl-status.partial{background:var(--amber-dim);border:1px solid rgba(245,158,11,.3);color:var(--amber);}
.tbl-status.none{background:var(--card2);border:1px solid var(--border2);color:var(--text-muted);}
.tbl-status-bar{width:80px;height:5px;background:var(--border);border-radius:3px;overflow:hidden;margin-top:4px;}
.tbl-status-fill{height:100%;border-radius:3px;transition:width .4s ease;}

/* step 4t column list */
.back-nav{display:flex;align-items:center;gap:10px;margin-bottom:18px;}
.back-link{display:flex;align-items:center;gap:6px;cursor:pointer;color:var(--text-muted);font-size:12px;}
.back-link:hover{color:var(--teal);}
.back-tname{font-family:var(--mono);font-size:14px;font-weight:600;color:var(--text);}
.regen-inline{display:inline-flex;align-items:center;gap:5px;font-size:11px;color:var(--purple);}
.regen-spin{width:11px;height:11px;border:1.5px solid var(--purple-mid);border-top-color:var(--purple);border-radius:50%;animation:spin .8s linear infinite;flex-shrink:0;}

/* step 4d column detail */
.dnav{display:flex;align-items:center;gap:10px;margin-bottom:18px;}
.dback{cursor:pointer;color:var(--text-muted);font-size:12px;}.dback:hover{color:var(--teal);}
.dname{font-family:var(--mono);font-size:14px;font-weight:600;}
.cp{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px;}

/* panel header */
.ph2{display:flex;align-items:center;gap:8px;padding:9px 14px;border-radius:7px 7px 0 0;border:1px solid;}
.ph2-lbl{font-size:10px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;flex:1;}
.ph2.before{background:var(--card2);border-color:var(--border);color:var(--text-muted);}
.ph2.after{background:var(--teal-dim);border-color:var(--teal-mid);color:var(--teal);}
.ph2.after.emode{background:var(--amber-dim);border-color:rgba(245,158,11,.3);color:var(--amber);}
.ph2.after.regen-mode{background:var(--purple-dim);border-color:var(--purple-mid);color:var(--purple);}

/* JSON toggle button */
.json-toggle{font-size:9px;font-weight:600;font-family:var(--mono);background:rgba(0,0,0,.2);border:1px solid rgba(255,255,255,.1);color:rgba(255,255,255,.5);border-radius:3px;padding:2px 7px;cursor:pointer;transition:all .15s;margin-left:4px;}
.json-toggle:hover{color:rgba(255,255,255,.8);border-color:rgba(255,255,255,.2);}
.json-toggle.on{background:rgba(59,130,246,.15);border-color:rgba(59,130,246,.4);color:var(--blue);}

/* panel body */
.pb2{background:var(--card);border:1px solid;border-top:none;border-radius:0 0 8px 8px;padding:0;position:relative;overflow:hidden;}
.pb2.before{border-color:var(--border);}
.pb2.after{border-color:var(--teal-mid);}
.pb2.after.emode{border-color:var(--amber);box-shadow:0 0 0 2px rgba(245,158,11,.07);}
.pb2.after.regen-mode{border-color:var(--purple-mid);}

/* panel content area (scrollable, padded) */
.panel-content{padding:16px;}

/* fields */
.fr{margin-bottom:14px;}.fr:last-child{margin-bottom:0;}
.fl{font-size:10px;font-weight:600;color:var(--text-muted);letter-spacing:.06em;text-transform:uppercase;margin-bottom:5px;display:flex;align-items:center;gap:6px;}
.fv{font-size:12px;color:var(--text);background:var(--card2);border:1px solid var(--border);border-radius:6px;padding:8px 10px;min-height:38px;line-height:1.6;}
.fv.empty{color:var(--text-muted);font-style:italic;}
.fta{width:100%;font-size:12px;color:var(--text);background:var(--card2);border:1px solid var(--amber);border-radius:6px;padding:8px 10px;min-height:62px;resize:vertical;font-family:var(--font);line-height:1.6;outline:none;}
.fsel{width:100%;font-size:12px;color:var(--text);background:var(--card2);border:1px solid var(--amber);border-radius:6px;padding:8px 10px;font-family:var(--font);outline:none;cursor:pointer;}
.chip-mod{display:inline-flex;font-size:9px;background:var(--amber-dim);color:var(--amber);border-radius:3px;padding:2px 6px;font-weight:600;}
.chip-regen{display:inline-flex;font-size:9px;background:var(--purple-dim);color:var(--purple);border-radius:3px;padding:2px 6px;font-weight:600;}
.chip-approved{display:inline-flex;align-items:center;gap:3px;font-size:10px;background:var(--green-dim);color:var(--green);border:1px solid rgba(34,197,94,.3);border-radius:3px;padding:2px 8px;font-weight:600;}

/* JSON view */
.json-view{background:var(--card2);border-radius:6px;padding:12px 14px;font-family:var(--mono);font-size:11px;color:var(--text-dim);line-height:1.7;overflow-x:auto;white-space:pre;margin:0;}
.json-key{color:var(--blue);}
.json-str{color:var(--green);}
.json-null{color:var(--text-muted);}

/* regen overlay (inside pb2) */
.regen-overlay{position:absolute;inset:0;background:rgba(8,13,30,.87);backdrop-filter:blur(4px);display:none;flex-direction:column;align-items:center;justify-content:center;gap:14px;z-index:10;border-radius:0 0 8px 8px;}
.regen-overlay.vis{display:flex;}
.regen-spinner{width:28px;height:28px;border:2.5px solid var(--purple-dim);border-top-color:var(--purple);border-radius:50%;animation:spin .8s linear infinite;}
.regen-prog-wrap{width:160px;}
.regen-prog-track{height:4px;background:rgba(168,85,247,.15);border-radius:2px;overflow:hidden;margin-bottom:6px;}
.regen-prog-fill{height:100%;background:var(--purple);border-radius:2px;transition:width .4s ease;}
.regen-prog-label{font-size:11px;color:var(--purple);text-align:center;}

/* edit bar */
.ebar{background:var(--amber-dim);border:1px solid rgba(245,158,11,.25);border-radius:8px;padding:10px 16px;margin-bottom:14px;display:none;}
.ebar.vis{display:block;}
.regen-banner{background:var(--purple-dim);border:1px solid var(--purple-mid);border-radius:8px;padding:10px 16px;margin-bottom:14px;font-size:12px;color:var(--purple);display:none;align-items:center;gap:8px;}
.regen-banner.vis{display:flex;}

/* evidence */
.evpanel{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:16px 18px;margin-bottom:14px;}
.ev-title{font-size:11px;font-weight:700;color:var(--text-muted);letter-spacing:.08em;text-transform:uppercase;margin-bottom:14px;}
.cmeter{display:flex;align-items:center;gap:14px;margin-bottom:16px;}
.ccircle{width:52px;height:52px;border-radius:50%;display:flex;flex-direction:column;align-items:center;justify-content:center;border:2px solid;}
.ccircle.high{border-color:var(--green);background:var(--green-dim);}
.ccircle.mid{border-color:var(--amber);background:var(--amber-dim);}
.ccircle.low{border-color:var(--red);background:var(--red-dim);}
.cnum{font-size:15px;font-weight:700;font-family:var(--mono);}
.high .cnum{color:var(--green);}.mid .cnum{color:var(--amber);}.low .cnum{color:var(--red);}
.cgl{font-size:9px;color:var(--text-muted);}
.cright .cl2{font-size:12px;color:var(--text);font-weight:500;}
.cright .cs{font-size:11px;color:var(--text-muted);margin-top:2px;}
.srcrow{display:flex;align-items:center;gap:10px;padding:7px 0;border-bottom:1px solid var(--border);}
.srcrow:last-child{border-bottom:none;}
.srcp{width:20px;height:20px;border-radius:4px;background:var(--card2);border:1px solid var(--border2);display:flex;align-items:center;justify-content:center;font-size:9px;font-weight:700;color:var(--text-muted);font-family:var(--mono);}
.srcn{font-size:12px;color:var(--text-dim);flex:1;}
.srcv2{font-size:10px;color:var(--text-muted);font-family:var(--mono);}
.srcbar{width:70px;height:4px;background:var(--border);border-radius:2px;overflow:hidden;}
.srcbar-f{height:100%;background:var(--teal);border-radius:2px;}
.areasoning{background:var(--card2);border:1px solid var(--border);border-left:3px solid var(--teal);border-radius:0 6px 6px 0;padding:10px 14px;font-size:12px;color:var(--text-muted);line-height:1.7;}

/* RAG */
.rag-layout{display:grid;grid-template-columns:1fr 340px;gap:18px;align-items:start;}
.rag-tabs{display:flex;gap:0;margin-bottom:18px;}
.rtab{padding:9px 18px;font-size:12px;font-weight:600;border:1px solid var(--border);cursor:pointer;color:var(--text-muted);background:var(--card2);}
.rtab:first-child{border-radius:7px 0 0 7px;}
.rtab:last-child{border-radius:0 7px 7px 0;border-left:none;}
.rtab.active{background:var(--teal-dim);border-color:var(--teal-mid);color:var(--teal);}
.rag-detail-panel{background:var(--card);border:1px solid var(--border);border-radius:10px;overflow:hidden;position:sticky;top:80px;}
.rdp-header{padding:14px 16px;border-bottom:1px solid var(--border);background:var(--card2);}
.rdp-title{font-size:13px;font-weight:600;color:var(--text);}
.rdp-sub{font-size:11px;color:var(--text-muted);margin-top:3px;}
.rdp-body{padding:16px;}
.rdp-row{display:flex;justify-content:space-between;padding:7px 0;border-bottom:1px solid var(--border);font-size:12px;}
.rdp-row:last-child{border-bottom:none;}
.rdp-k{color:var(--text-muted);}.rdp-v{color:var(--text-dim);font-family:var(--mono);font-size:11px;}
.rag-active{background:var(--green-dim);color:var(--green);border-radius:4px;font-size:10px;font-weight:600;padding:2px 8px;}
.rdp-domains{display:flex;flex-wrap:wrap;gap:6px;margin-top:10px;}
.rdp-dtag{padding:3px 9px;border-radius:4px;font-size:11px;background:var(--teal-dim);border:1px solid var(--teal-mid);color:var(--teal);}

/* step 5 */
.done-ctr{text-align:center;padding:40px 0 28px;}
.done-icon{font-size:56px;margin-bottom:18px;}
.done-title{font-size:22px;font-weight:700;color:var(--teal);margin-bottom:8px;}
.done-sub{font-size:14px;color:var(--text-muted);}
.done-stats{display:grid;grid-template-columns:repeat(3,1fr);gap:14px;max-width:500px;margin:26px auto 30px;}
.dst{background:var(--card);border:1px solid var(--border);border-radius:8px;padding:16px;text-align:center;}
.dstv{font-size:24px;font-weight:700;font-family:var(--mono);}
.dstv.g{color:var(--green);}.dstv.a{color:var(--amber);}.dstv.b{color:var(--blue);}
.dstl{font-size:11px;color:var(--text-muted);margin-top:3px;}
.done-sess{background:var(--card);border:1px solid var(--border);border-radius:8px;padding:14px 18px;max-width:500px;margin:0 auto 26px;}
.ds-title{font-size:11px;color:var(--text-muted);letter-spacing:.07em;text-transform:uppercase;margin-bottom:10px;}
.ds-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid var(--border);font-size:12px;}
.ds-row:last-child{border-bottom:none;}
.ds-k{color:var(--text-muted);}.ds-v{color:var(--text-dim);font-family:var(--mono);font-size:11px;}

::-webkit-scrollbar{width:6px;height:6px;}
::-webkit-scrollbar-track{background:var(--bg);}
::-webkit-scrollbar-thumb{background:var(--border2);border-radius:3px;}
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="topbar">
  <div class="tb-brand">
    <div>
      <div class="tb-logo">MetaZen</div>
    </div>
  </div>
  <div class="tb-title" id="tbTitle">RAG 관리</div>
  <div class="tb-right">
    <div class="ctx-pill"><div class="ctx-dot"></div><span class="ctx-l">선택 테이블</span><span class="ctx-v" id="ctxTables">—</span></div>
    <div class="ctx-pill"><span class="ctx-l">SESSION</span><span class="ctx-v">2026-04-29 14:32</span></div>
  </div>
</div>

<!-- LNB -->
<nav class="lnb">
  <div class="lnb-section">
    <div class="lnb-sec-label">데이터 관리</div>
    <div class="lnb-item" id="lnb-rag" onclick="switchView('rag')">
      <span class="lnb-ico">📚</span><span>RAG 관리</span>
    </div>
    <div class="lnb-subnav">
      <div class="lnb-sub active" onclick="setRagTab('glossary',this)">비즈니스 용어집</div>
      <div class="lnb-sub" onclick="setRagTab('semantic',this)">시맨틱 레이어</div>
      <div class="lnb-sub" onclick="setRagTab('catalog',this)">데이터 카탈로그</div>
      <div class="lnb-sub" onclick="setRagTab('standard',this)">산업 표준</div>
    </div>
    <div class="lnb-divider"></div>
    <div class="lnb-sec-label">워크스페이스</div>
    <div class="lnb-item active" id="lnb-aug" onclick="switchView('aug')">
      <span class="lnb-ico">⚡</span><span>S전자 디지털사업부</span>
    </div>
  </div>
  <div class="lnb-bottom">
    <div class="lnb-item"><span class="lnb-ico">⚙️</span><span>설정</span></div>
  </div>
</nav>

<!-- STEP BAR -->
<div class="step-bar" id="stepBar">
  <div class="si active" id="sn1" onclick="goStep(1)"><div class="sn">1</div><div class="sl">테이블 업로드</div></div>
  <span class="sarr">›</span>
  <div class="si" id="sn2" onclick="goStep(2)"><div class="sn">2</div><div class="sl">도메인 & 소스 매핑</div></div>
  <span class="sarr">›</span>
  <div class="si" id="sn3" onclick="goStep(3)"><div class="sn">3</div><div class="sl">증강 실행 중</div></div>
  <span class="sarr">›</span>
  <div class="si" id="sn4" onclick="goStep(4)"><div class="sn">4</div><div class="sl">결과 검토</div></div>
  <span class="sarr">›</span>
  <div class="si" id="sn5" onclick="goStep(5)"><div class="sn">5</div><div class="sl">완료</div></div>
</div>

<div class="app">

<!-- ══ RAG VIEW ══ -->
<div id="viewRag" class="view active">
  <div class="main no-steps">
    <div class="sh"><div class="sh-t">RAG 관리</div><div class="sh-s">테이블 증강 Agent가 참조할 공통 소스를 도메인별로 관리합니다.</div></div>
    <div class="rag-tabs">
      <div class="rtab active" onclick="setRagTab('glossary',null,this)">📖 비즈니스 용어집</div>
      <div class="rtab" onclick="setRagTab('semantic',null,this)">🔗 시맨틱 레이어</div>
      <div class="rtab" onclick="setRagTab('catalog',null,this)">🗂️ 데이터 카탈로그</div>
      <div class="rtab" onclick="setRagTab('standard',null,this)">🏛️ 산업 표준</div>
    </div>
    <div class="rag-layout">
      <div>
        <div class="bulk-bar" style="border-radius:8px 8px 0 0;">
          <span style="font-size:11px;color:var(--text-muted);flex:1;" id="ragTabLabel">비즈니스 용어집 — 4개 소스</span>
          <button class="btn btn-primary btn-sm">+ 소스 추가</button>
        </div>
        <div class="twrap">
          <table class="gt">
            <thead><tr><th>소스명</th><th>적용 도메인</th><th>버전</th><th>항목 수</th><th>최종 업데이트</th><th>상태</th><th>액션</th></tr></thead>
            <tbody id="ragTableBody"></tbody>
          </table>
        </div>
      </div>
      <div class="rag-detail-panel">
        <div class="rdp-header"><div class="rdp-title" id="rdpTitle">소스를 선택하세요</div><div class="rdp-sub" id="rdpSub">클릭하면 상세 정보를 확인합니다</div></div>
        <div class="rdp-body" id="rdpBody"><div style="text-align:center;padding:30px 0;color:var(--text-muted);font-size:12px;">좌측 목록에서 소스를 선택하세요.</div></div>
      </div>
    </div>
  </div>
</div>

<!-- ══ TABLE AUG VIEW ══ -->
<div id="viewAug" class="view">
  <div class="main with-steps" id="augMain">

    <!-- STEP 1 -->
    <div class="page active" id="page1">
      <div class="sh"><div class="sh-t">대상 테이블 업로드</div><div class="sh-s">메타데이터를 증강할 테이블 목록을 업로드하세요. Agent가 각 테이블을 분석하여 증강 대상과 모드를 자동 제안합니다.</div></div>
      <div class="upload-tabs">
        <div class="utab active" id="tab-file" onclick="switchTab('file')">📁 파일 업로드</div>
        <div class="utab" id="tab-db" onclick="switchTab('db')">🔌 DB 직접 연결</div>
      </div>
      <div id="panel-file">
        <div class="drop-zone" id="dropZone" onclick="simulateUpload()">
          <div style="font-size:40px;margin-bottom:12px;">📂</div>
          <div style="font-size:15px;font-weight:600;margin-bottom:6px;">파일을 드래그하거나 클릭하여 업로드</div>
          <div style="font-size:12px;color:var(--text-muted);">테이블 목록이 담긴 파일을 업로드하세요</div>
          <div style="display:flex;justify-content:center;gap:8px;margin-top:12px;"><span class="fmt-pill">XLSX</span><span class="fmt-pill">CSV</span><span class="fmt-pill">JSON</span></div>
        </div>
      </div>
      <div id="panel-db" style="display:none;">
        <div style="background:var(--card2);border:1px solid var(--border);border-radius:10px;padding:20px;">
          <div class="card-title">DB 연결 정보</div>
          <div class="db-grid">
            <div class="db-field"><label>DB 타입</label><select class="db-input"><option>Oracle</option><option>PostgreSQL</option><option>MySQL</option></select></div>
            <div class="db-field"><label>Host</label><input class="db-input" value="ora-dw-prod.internal"></div>
            <div class="db-field"><label>Port</label><input class="db-input" value="1521"></div>
            <div class="db-field"><label>Database / SID</label><input class="db-input" value="DW_PROD"></div>
            <div class="db-field"><label>사용자명</label><input class="db-input" value="META_AGENT_USR"></div>
            <div class="db-field"><label>비밀번호</label><input class="db-input" type="password" value="••••••••"></div>
          </div>
          <button class="btn btn-blue btn-sm" onclick="simulateUpload()">🔍 스키마 탐색</button>
        </div>
      </div>
      <div id="uploadedSection" style="display:none;margin-top:20px;">
        <div class="policy-banner">
          <div class="pb-title">🤖 Agent 자동 분류 기준 — 체크박스와 증강 모드를 수동으로 변경할 수 있습니다</div>
          <div class="pb-rules">
            <div class="pb-rule"><div><span class="cls-badge cls-new">신규 대상</span></div><div class="pb-rule-desc">증강 이력 없음 → <b style="color:var(--teal);">자동 선택</b> · 빈 항목만 채우기</div></div>
            <div class="pb-rule"><div><span class="cls-badge cls-re">재증강 검토</span></div><div class="pb-rule-desc">증강했으나 빈 항목 여전히 존재 → <b style="color:var(--amber);">미선택 (사람 판단)</b></div></div>
            <div class="pb-rule"><div><span class="cls-badge cls-done">처리 완료</span></div><div class="pb-rule-desc">증강 완료 · 빈 항목 없음 → <b style="color:var(--text-muted);">제외 권고</b></div></div>
          </div>
        </div>
        <div class="upload-stat">
          <div class="ustat-card act" onclick="filterTable('all',this)"><div class="ustat-v" style="color:var(--text)">8</div><div class="ustat-l">전체 테이블</div></div>
          <div class="ustat-card" onclick="filterTable('new',this)"><div class="ustat-v" style="color:var(--teal)">5</div><div class="ustat-l">신규 대상</div></div>
          <div class="ustat-card" onclick="filterTable('re',this)"><div class="ustat-v" style="color:var(--amber)">2</div><div class="ustat-l">재증강 검토</div></div>
          <div class="ustat-card" onclick="filterTable('done',this)"><div class="ustat-v" style="color:var(--text-muted)">1</div><div class="ustat-l">처리 완료</div></div>
        </div>
        <div class="bulk-bar">
          <input type="checkbox" class="cb" id="selectAll" onchange="toggleAll(this)">
          <span style="font-size:11px;color:var(--text-muted);">전체 선택</span>
          <div style="width:1px;height:14px;background:var(--border2);margin:0 4px;"></div>
          <button class="fb act" style="font-size:10px;padding:3px 10px;" onclick="setF2(this,'all')">전체</button>
          <button class="fb" style="font-size:10px;padding:3px 10px;" onclick="setF2(this,'new')">신규 대상</button>
          <button class="fb" style="font-size:10px;padding:3px 10px;" onclick="setF2(this,'re')">재증강 검토</button>
          <button class="fb" style="font-size:10px;padding:3px 10px;" onclick="setF2(this,'done')">처리 완료</button>
          <div style="margin-left:auto;display:flex;align-items:center;gap:8px;">
            <span style="font-size:11px;color:var(--text-muted);">증강 모드:</span>
            <select id="bulkModeSel" style="font-size:11px;background:var(--card2);border:1px solid var(--border2);color:var(--text-dim);border-radius:5px;padding:4px 8px;font-family:var(--font);cursor:pointer;">
              <option value="empty">빈 항목만 채우기</option><option value="full">전체 재생성</option>
            </select>
            <button class="btn btn-ghost btn-xs" onclick="bulkApplyMode()">일괄 적용</button>
          </div>
        </div>
        <div class="twrap">
          <table class="gt">
            <thead><tr><th style="width:36px;"></th><th>테이블명 / 스키마</th><th>컬럼</th><th>빈 항목 비율</th><th>Agent 분류</th><th>증강 모드</th><th>마지막 증강</th></tr></thead>
            <tbody id="uploadTableBody"></tbody>
          </table>
        </div>
        <div class="sel-summary" id="selSummary">
          <div class="sel-inner">
            <span style="font-size:12px;">선택: <b id="selCountLabel" style="color:var(--teal);font-family:var(--mono);">0</b>개</span>
            <span style="color:var(--border2);">|</span>
            <span style="font-size:12px;color:var(--text-muted);">빈 항목만: <b id="modeEmptyCount" style="color:var(--teal);font-family:var(--mono);">0</b></span>
            <span style="font-size:12px;color:var(--text-muted);">전체 재생성: <b id="modeFullCount" style="color:var(--amber);font-family:var(--mono);">0</b></span>
            <span style="font-size:11px;color:var(--text-muted);margin-left:auto;">💡 토큰 절약 위해 <b style="color:var(--teal);">빈 항목만 채우기</b> 권장</span>
          </div>
        </div>
      </div>
      <div class="ar right" style="margin-top:18px;">
        <button class="btn btn-ghost">취소</button>
        <button class="btn btn-primary" id="toStep2Btn" disabled onclick="goToStep2()">다음 — 도메인 & 소스 매핑 →</button>
      </div>
    </div>

    <!-- STEP 2 -->
    <div class="page" id="page2">
      <div class="sh"><div class="sh-t">도메인 지정 & 참조 소스 확인</div><div class="sh-s">선택한 테이블에 업무 도메인을 지정하세요. 참조 소스는 RAG 관리 메뉴에서 도메인별로 설정됩니다.</div></div>
      <div class="s2-wrap">
        <div>
          <div class="bulk-domain-bar">
            <input type="checkbox" class="cb" onchange="p2ToggleAll(this)">
            <span style="font-size:11px;color:var(--text-muted);">선택 일괄 지정:</span>
            <select id="bulkDomainSel" style="font-size:11px;background:var(--card2);border:1px solid var(--border2);color:var(--text-dim);border-radius:5px;padding:4px 8px;font-family:var(--font);cursor:pointer;">
              <option value="">-- 도메인 선택 --</option>
              <option>금융 / 여신</option><option>금융 / 수신</option><option>리스크 관리</option>
              <option>고객 정보</option><option>회계 / 결산</option><option>규제 / 보고</option>
            </select>
            <button class="btn btn-ghost btn-xs" onclick="bulkAssignDomain()">일괄 적용</button>
            <div style="margin-left:auto;"><button class="btn btn-blue btn-xs" onclick="autoSuggestDomains()">✨ AI 도메인 자동 추천</button></div>
          </div>
          <div class="twrap">
            <table class="gt">
              <thead><tr><th style="width:36px;"></th><th>테이블명</th><th>증강 모드</th><th>AI 추천</th><th>지정 도메인</th><th style="width:80px;">참조 소스</th></tr></thead>
              <tbody id="domainTableBody"></tbody>
            </table>
          </div>
          <div id="aiSuggestToast" style="display:none;margin-top:10px;background:var(--blue-dim);border:1px solid rgba(59,130,246,.3);border-radius:8px;padding:10px 14px;font-size:12px;color:var(--blue);">
            ✨ AI가 테이블명 패턴을 분석하여 도메인을 자동 추천했습니다.
          </div>
        </div>
        <div class="s2-src-panel">
          <div class="s2-src-header"><span style="font-size:14px;">📋</span><span class="s2-src-title" id="s2SrcTitle">참조 소스 확인</span></div>
          <div class="s2-src-body" id="s2SrcBody"><div class="s2-src-placeholder"><div style="font-size:28px;margin-bottom:10px;">🔍</div>테이블의 <b style="color:var(--teal);">소스 확인</b> 버튼을<br>클릭하면 참조 소스를 확인합니다</div></div>
        </div>
      </div>
      <div class="ar right">
        <button class="btn btn-ghost" onclick="goStep(1)">← 이전</button>
        <button class="btn btn-primary" id="startAugBtn" disabled onclick="goToStep3()" style="opacity:.4;cursor:not-allowed;">증강 시작 →</button>
      </div>
    </div>

    <!-- STEP 3 -->
    <div class="page" id="page3">
      <div class="sh"><div class="sh-t">증강 실행 중</div><div class="sh-s">Agent가 선택된 테이블의 메타데이터를 자동으로 수집·분석·생성합니다.</div></div>
      <div class="card">
        <div class="prog-hdr"><span style="font-size:13px;font-weight:600;" id="pLabel">준비 중...</span><span style="font-size:11px;color:var(--text-muted);font-family:var(--mono);" id="pEta">예상 완료: 약 2분</span></div>
        <div class="prog-track"><div class="prog-fill" id="pFill" style="width:0%"></div></div>
        <div style="margin-bottom:20px;"><div class="ph-title">테이블별 진행 현황</div><div style="display:grid;grid-template-columns:repeat(3,1fr);gap:8px;" id="tableProgGrid"></div></div>
        <div class="ph-block"><div class="ph-title">Phase 1 · 자동 수집</div>
          <div class="ps-row" id="ps1-1"><div class="ps-ico">○</div><div class="ps-label">테이블 프로파일링 (샘플 통계, 패턴 분석)</div><div class="ps-status">대기 중</div></div>
          <div class="ps-row" id="ps1-2"><div class="ps-ico">○</div><div class="ps-label">DDL / 소스코드 분석</div><div class="ps-status">대기 중</div></div>
        </div>
        <div class="ph-block"><div class="ph-title">Phase 2 · 내부 지식 연동</div>
          <div class="ps-row" id="ps2-1"><div class="ps-ico">○</div><div class="ps-label">비즈니스 용어집 매핑 (도메인별 적용)</div><div class="ps-status">대기 중</div></div>
          <div class="ps-row" id="ps2-2"><div class="ps-ico">○</div><div class="ps-label">참조 문서 분석 — 지정 소스 적용</div><div class="ps-status">대기 중</div></div>
        </div>
        <div class="ph-block"><div class="ph-title">Phase 3 · 메타데이터 생성</div>
          <div class="ps-row" id="ps3-1"><div class="ps-ico">○</div><div class="ps-label">테이블 레벨 설명·태그·분류 생성</div><div class="ps-status">대기 중</div></div>
          <div class="ps-row" id="ps3-2"><div class="ps-ico">○</div><div class="ps-label">컬럼별 설명·비즈니스 용어 매핑 생성</div><div class="ps-status">대기 중</div></div>
          <div class="ps-row" id="ps3-3"><div class="ps-ico">○</div><div class="ps-label">신뢰도 점수 산정 및 검토 필요 항목 플래그</div><div class="ps-status">대기 중</div></div>
        </div>
      </div>
    </div>

    <!-- STEP 4: Table list -->
    <div class="page" id="page4">
      <div class="sh"><div class="sh-t">증강 결과 검토</div><div class="sh-s">테이블을 선택하면 컬럼별 증강 결과를 검토할 수 있습니다.</div></div>
      <div class="rs-grid">
        <div class="rs-card hl"><div class="rs-v">3</div><div class="rs-l">증강된 테이블</div></div>
        <div class="rs-card"><div class="rs-v" style="color:var(--teal)">144</div><div class="rs-l">전체 증강 항목</div></div>
        <div class="rs-card wn"><div class="rs-v">14</div><div class="rs-l">검토 권고 (&lt;70)</div></div>
        <div class="rs-card"><div class="rs-v" style="color:var(--green)" id="p4ApprovedCnt">0</div><div class="rs-l">승인 완료 항목</div></div>
      </div>
      <div class="twrap-r">
        <table class="gt">
          <thead><tr><th>테이블명</th><th>스키마</th><th>증강 컬럼</th><th>평균 신뢰도</th><th>검토 상태</th><th>승인</th><th>검토 대기</th><th>반려(재생성)</th><th>액션</th></tr></thead>
          <tbody id="tblResultBody"></tbody>
        </table>
      </div>
      <div class="ar right"><button class="btn btn-primary" id="finishBtn" disabled onclick="finish()" style="opacity:.4;cursor:not-allowed;">증강 검토 완료</button></div>
    </div>

    <!-- STEP 4t: Column list for a table -->
    <div class="page" id="page4t">
      <div class="back-nav">
        <div class="back-link" onclick="backToTableList()">← 테이블 목록</div>
        <span style="color:var(--border2);margin:0 6px;">|</span>
        <div class="back-tname" id="colListTableName"></div>
        <span id="colListBadge" style="margin-left:8px;font-size:11px;color:var(--text-muted);"></span>
      </div>
      <div class="filter-row">
        <span style="font-size:11px;color:var(--text-muted);margin-right:4px;">필터</span>
        <button class="fb act" onclick="setColF(this,'all')">전체</button>
        <button class="fb" onclick="setColF(this,'pending')">검토 대기</button>
        <button class="fb" onclick="setColF(this,'approved')">승인</button>
        <button class="fb" onclick="setColF(this,'regen')">재생성됨</button>
        <button class="fb" onclick="setColF(this,'rejected')">반려</button>
        <div style="margin-left:auto;"><button class="btn btn-ghost btn-sm" onclick="approveAllCols()">✅ 신뢰도 80+ 일괄 승인</button></div>
      </div>
      <div class="bulk-bar">
        <input type="checkbox" class="cb" id="colSelectAll" onchange="toggleAllCols(this)">
        <span style="font-size:11px;color:var(--text-muted);margin-right:4px;">선택 항목:</span>
        <button class="btn btn-ghost btn-xs" onclick="bulkApproveSelected()">✓ 승인</button>
        <button class="btn btn-regen btn-xs" onclick="bulkRejectRegen()">↺ 반려 & 자동 재생성</button>
      </div>
      <div class="twrap">
        <table class="gt">
          <thead><tr><th style="width:30px;"></th><th>컬럼명</th><th>증강 항목</th><th>변경 전</th><th>변경 후</th><th>신뢰도</th><th>상태</th><th>액션</th></tr></thead>
          <tbody id="colListBody"></tbody>
        </table>
      </div>
      <div class="ar"><button class="btn btn-ghost" onclick="backToTableList()">← 테이블 목록</button></div>
    </div>

    <!-- STEP 4d: Column detail (simplified) -->
    <div class="page" id="page4d">
      <div class="dnav">
        <div class="dback" onclick="backToColList()">← 컬럼 목록</div>
        <span style="color:var(--border2);margin:0 6px;">|</span>
        <div class="dname" id="dColName"></div>
        <span id="dBadge" style="margin-left:8px;"></span>
        <span id="dChip" style="margin-left:6px;"></span>
        <span id="dApprovalChip" style="margin-left:6px;"></span>
        <div style="margin-left:auto;display:flex;align-items:center;gap:8px;">
          <button class="btn btn-ghost btn-xs" onclick="prevD()">← 이전</button>
          <span style="font-size:11px;color:var(--text-muted);" id="dPos"></span>
          <button class="btn btn-ghost btn-xs" onclick="nextD()">다음 →</button>
        </div>
      </div>

      <div class="regen-banner" id="regenBanner"><span style="font-size:16px;">♻️</span><span>반려로 인해 메타데이터가 자동 재생성되었습니다. 검토 후 승인하세요.</span></div>
      <div class="ebar" id="eBar">
        <div style="display:flex;align-items:center;gap:12px;">
          <span style="font-size:14px;">✏️</span>
          <span style="font-size:12px;color:var(--amber);font-weight:600;">직접 수정 모드</span>
          <span style="font-size:11px;color:var(--text-muted);">필드를 직접 편집한 후 저장하세요.</span>
          <div style="margin-left:auto;display:flex;gap:8px;">
            <button class="btn btn-ghost btn-sm" onclick="cancelEdit()">취소</button>
            <button class="btn btn-amber btn-sm" onclick="saveEdit()">💾 수정 저장</button>
          </div>
        </div>
      </div>

      <div class="cp">
        <!-- BEFORE PANEL -->
        <div>
          <div class="ph2 before">
            <span style="font-size:14px;">📋</span>
            <span class="ph2-lbl">변경 전 — 현재 메타데이터</span>
            <button class="json-toggle" id="beforeJsonBtn" onclick="toggleJsonView('before')">{ } JSON</button>
          </div>
          <div class="pb2 before">
            <div class="panel-content" id="panBeforeContent"></div>
          </div>
        </div>

        <!-- AFTER PANEL -->
        <div>
          <div class="ph2 after" id="phAfter">
            <span style="font-size:14px;" id="phAfterIco">✨</span>
            <span class="ph2-lbl" id="phAfterLbl">변경 후 — Agent 생성</span>
            <button class="json-toggle" id="afterJsonBtn" onclick="toggleJsonView('after')">{ } JSON</button>
            <button class="btn btn-amber btn-xs" id="editBtn" onclick="toggleEdit()" style="margin-left:6px;">✏️ 직접 수정</button>
          </div>
          <div class="pb2 after" id="panAfter">
            <!-- Content wrapper — never cleared -->
            <div class="panel-content" id="panAfterContent"></div>
            <!-- Regen overlay — always present -->
            <div class="regen-overlay" id="regenOverlay">
              <div class="regen-spinner"></div>
              <div class="regen-prog-wrap">
                <div class="regen-prog-track"><div class="regen-prog-fill" id="regenProgFill" style="width:0%"></div></div>
                <div class="regen-prog-label" id="regenProgLabel">참조 소스 재탐색 중...</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="evpanel">
        <div class="ev-title">근거 및 신뢰도</div>
        <div class="cmeter" id="cmeter"></div>
        <div id="srcList"></div>
        <div style="font-size:11px;color:var(--text-muted);font-weight:600;letter-spacing:.06em;text-transform:uppercase;margin-bottom:6px;">Agent 판단 요약</div>
        <div class="areasoning" id="areasoning"></div>
      </div>

      <div class="ar">
        <button class="btn btn-primary" id="approveBtn" onclick="approveD()">✅ 승인</button>
        <button class="btn btn-regen" id="rejectBtn" onclick="rejectD()">↺ 반려 & 자동 재생성</button>
        <div style="margin-left:auto;display:flex;gap:8px;">
          <button class="btn btn-ghost btn-sm" onclick="prevD()">← 이전</button>
          <button class="btn btn-ghost btn-sm" onclick="nextD()">다음 →</button>
        </div>
      </div>
    </div>

    <!-- STEP 5 -->
    <div class="page" id="page5">
      <div class="done-ctr">
        <div class="done-icon">✅</div>
        <div class="done-title">증강 검토 완료</div>
        <div class="done-sub">승인된 메타데이터 증강 결과를 Export할 수 있습니다.</div>
      </div>
      <div class="done-stats" style="grid-template-columns:repeat(1,1fr);max-width:220px;">
        <div class="dst"><div class="dstv g" id="dA">0</div><div class="dstl">승인 완료</div></div>
      </div>
      <div class="done-sess">
        <div class="ds-title">증강 이력</div>
        <div class="ds-row"><span class="ds-k">세션 ID</span><span class="ds-v">AUG-2026-042901</span></div>
        <div class="ds-row"><span class="ds-k">대상 테이블</span><span class="ds-v" id="doneTableList">—</span></div>
        <div class="ds-row"><span class="ds-k">처리 시각</span><span class="ds-v">2026-04-29 14:52:08</span></div>
        <div class="ds-row"><span class="ds-k">총 증강 항목</span><span class="ds-v">144건</span></div>
      </div>
      <div class="ar" style="justify-content:center;gap:12px;">
        <button class="btn btn-primary" style="font-size:13px;padding:10px 22px;">⬇️ 증강 결과 JSON으로 Export</button>
        <button class="btn btn-ghost" onclick="goStep(1)">+ 새 증강 작업</button>
        <button class="btn btn-ghost">대시보드로 이동</button>
      </div>
    </div>

  </div><!-- /augMain -->
</div><!-- /viewAug -->
</div><!-- /app -->

<script>
// ════════ DATA ════════
const TABLES=[
  {name:'LOAN_APPL_HIST',schema:'DW_CORE.CREDIT',  cols:52,emptyPct:87,augmented:false,lastAug:null,        cls:'new', autoSel:true, mode:'empty'},
  {name:'LOAN_EXEC_HIST',schema:'DW_CORE.CREDIT',  cols:38,emptyPct:79,augmented:false,lastAug:null,        cls:'new', autoSel:true, mode:'empty'},
  {name:'CRDT_SCRG_HIST',schema:'DW_CORE.RISK',    cols:29,emptyPct:93,augmented:false,lastAug:null,        cls:'new', autoSel:true, mode:'empty'},
  {name:'COLL_INFO',     schema:'DW_CORE.CREDIT',  cols:41,emptyPct:88,augmented:false,lastAug:null,        cls:'new', autoSel:true, mode:'empty'},
  {name:'REPAY_SCHED',   schema:'DW_CORE.CREDIT',  cols:24,emptyPct:71,augmented:false,lastAug:null,        cls:'new', autoSel:true, mode:'empty'},
  {name:'CUST_MSTR',     schema:'DW_CORE.CUSTOMER',cols:67,emptyPct:34,augmented:true, lastAug:'2026-01-15',cls:'re',  autoSel:false,mode:'empty'},
  {name:'TX_HIST',       schema:'DW_CORE.ACCOUNT', cols:33,emptyPct:61,augmented:true, lastAug:'2025-11-03',cls:'re',  autoSel:false,mode:'empty'},
  {name:'ACCT_MSTR',     schema:'DW_CORE.ACCOUNT', cols:55,emptyPct:8, augmented:true, lastAug:'2026-03-22',cls:'done',autoSel:false,mode:'full'},
];
TABLES.forEach(t=>t.userSel=t.autoSel);

const CLS_META={
  new:{label:'신규 대상',cls:'cls-new',tip:'증강 이력 없음'},
  re:{label:'재증강 검토',cls:'cls-re',tip:'빈 항목 남아있음'},
  done:{label:'처리 완료',cls:'cls-done',tip:'증강 완료 · 제외 권고'},
};
const DOMAIN_OPTS=['금융 / 여신','금융 / 수신','리스크 관리','고객 정보','회계 / 결산','규제 / 보고'];
const AI_SUGGEST={LOAN_APPL_HIST:'금융 / 여신',LOAN_EXEC_HIST:'금융 / 여신',CRDT_SCRG_HIST:'리스크 관리',COLL_INFO:'금융 / 여신',REPAY_SCHED:'금융 / 여신',CUST_MSTR:'고객 정보',TX_HIST:'회계 / 결산',ACCT_MSTR:'회계 / 결산'};
const DOMAIN_SOURCES={
  '금융 / 여신':[{ico:'📖',n:'비즈니스 용어집 — 금융/여신',v:'v3.1'},{ico:'📄',n:'여신심사업무규정',v:'v2.3'},{ico:'📄',n:'데이터표준화지침_2024',v:'v1.0'},{ico:'🔗',n:'시맨틱 레이어 — 여신 메트릭',v:'연결됨'}],
  '리스크 관리':[{ico:'📖',n:'비즈니스 용어집 — 리스크',v:'v2.0'},{ico:'📄',n:'신용리스크관리지침',v:'v1.4'}],
  '고객 정보':[{ico:'📖',n:'비즈니스 용어집 — 고객',v:'v2.1'},{ico:'📄',n:'개인정보처리방침_2024',v:'v3.0'}],
  '회계 / 결산':[{ico:'📖',n:'비즈니스 용어집 — 회계',v:'v1.8'},{ico:'📄',n:'계정과목체계표_2024',v:'v2.0'}],
};

// Column data
const COL_DATA={
  'LOAN_APPL_HIST':[
    {col:'LOAN_APPL_NO',item:'컬럼 설명 · 용어 매핑',before:'(없음)',after:'여신 신청 고유번호.',conf:91,cc:'high',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'여신 신청 고유번호. 심사 요청 건별로 부여되는 식별자로 중복 없이 단일 신청 건을 식별한다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'여신신청번호 (BT-LOAN-001)'}],
       conf:91,cc:'high',grade:'High',
       srcs:[{p:'P1',n:'비즈니스 용어집',v:'v3.1',pct:80},{p:'P2',n:'DDL 분석',v:'자동',pct:40}],
       reason:'컬럼명이 여신신청번호 표준 용어와 정확히 매핑됩니다. NOT NULL 제약으로 식별자 역할을 확인하였습니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'여신 신청 건의 시스템 고유 PK. 연도+일련번호 형식(예: 202604290001)으로 채번되며 심사 전 과정에서 건 추적 키로 사용된다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'여신신청번호 (BT-LOAN-001)'}],conf:94,cc:'high',grade:'High',reason:'DDL PK 확인 및 용어집 재매핑으로 정확도를 높였습니다.'}}},
    {col:'LOAN_AMT',item:'컬럼 설명 · 용어 매핑',before:'(없음)',after:'신청 여신 금액 (원).',conf:79,cc:'mid',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'고객이 신청한 여신 금액. 단위는 원(KRW)이며 소수점 없이 정수로 저장된다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'여신신청금액 (BT-LOAN-020)'}],
       conf:79,cc:'mid',grade:'Medium',
       srcs:[{p:'P1',n:'비즈니스 용어집',v:'v3.1',pct:60},{p:'P2',n:'프로파일링',v:'자동',pct:40}],
       reason:'LOAN_AMT는 용어집에서 여신신청금액으로 매핑되나 음수값(-999) 패턴이 존재합니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'고객 신청 여신 원금(KRW). -999는 미입력 처리값으로 유효 금액 산정에서 제외. 최소 100만원~최대 50억원 범위.'},{l:'비즈니스 용어 매핑',t:'ta',v:'여신신청금액 (BT-LOAN-020)'}],conf:85,cc:'high',grade:'High',reason:'특수값(-999) 처리 기준을 명시하고 프로파일링 기반 범위를 추가하였습니다.'}}},
    {col:'APPL_DT',item:'컬럼 설명',before:'(없음)',after:'신청 일자 (YYYYMMDD).',conf:88,cc:'high',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'여신 신청이 접수된 일자. YYYYMMDD 형식으로 저장되며 심사 시효 산정의 기준 일자로 사용된다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'신청일자 (BT-DATE-012)'}],
       conf:88,cc:'high',grade:'High',
       srcs:[{p:'P1',n:'DDL 분석',v:'자동',pct:65},{p:'P2',n:'여신심사업무규정',v:'v2.3',pct:50}],
       reason:'CHAR(8) 타입과 컬럼명 패턴으로 날짜 형식을 확인하고, 규정 문서의 시효 조항과 연결하였습니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'여신 신청 접수일(YYYYMMDD). 여신심사업무규정 제8조 기준 심사 시효(30일) 산정의 기산점.'},{l:'비즈니스 용어 매핑',t:'ta',v:'신청일자 (BT-DATE-012)'}],conf:92,cc:'high',grade:'High',reason:'규정 문서에서 심사 시효 기산 기준을 확인하여 설명을 보강하였습니다.'}}},
  ],
  'CRDT_SCRG_HIST':[
    {col:'SCRG_CD',item:'컬럼 설명 · 허용값',before:'(없음)',after:'신용평가 등급 코드 (불확실).',conf:52,cc:'low',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'신용평가 등급 코드. 허용값 체계가 불명확하여 코드 정의서 확인이 필요합니다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'신용등급코드 (BT-RISK-008) — 확인 필요'}],
       conf:52,cc:'low',grade:'Low',
       srcs:[{p:'P1',n:'프로파일링 통계',v:'자동',pct:35},{p:'P2',n:'비즈니스 용어집',v:'v3.1',pct:25}],
       reason:'프로파일링에서 A/B/C와 1/2/3 혼재 패턴 발견. 코드 체계가 불명확합니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'내부 신용평가 등급 코드. A(최우량)~H(요주의) 8단계. 숫자 1~8은 레거시 이관값(A=1 대응). 여신심사업무규정 별표2 참조.'},{l:'비즈니스 용어 매핑',t:'ta',v:'신용등급코드 (BT-RISK-008)'}],conf:78,cc:'mid',grade:'Medium',reason:'참조 문서 별표2에서 레거시 코드 대응 체계를 확인하여 재생성하였습니다.'}}},
    {col:'EVAL_DT',item:'컬럼 설명',before:'(없음)',after:'신용평가 실시 일자.',conf:85,cc:'high',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'신용평가가 실시된 일자. YYYYMMDD 형식으로 저장된다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'평가일자 (BT-DATE-021)'}],
       conf:85,cc:'high',grade:'High',
       srcs:[{p:'P1',n:'DDL 분석',v:'자동',pct:65},{p:'P2',n:'신용리스크관리지침',v:'v1.4',pct:45}],
       reason:'EVAL_DT 패턴과 CHAR(8) 타입으로 날짜 형식을 추론하였습니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'신용평가 실시 일자(YYYYMMDD). 리스크관리지침 제7조 정기평가(반기별) 기준 일자로 활용.'},{l:'비즈니스 용어 매핑',t:'ta',v:'평가일자 (BT-DATE-021)'}],conf:89,cc:'high',grade:'High',reason:'리스크관리지침 정기평가 주기 조항을 근거로 설명을 보완하였습니다.'}}},
  ],
  'CUST_MSTR':[
    {col:'CUST_NM',item:'컬럼 설명 · 용어 매핑',before:'(없음)',after:'고객 성명.',conf:88,cc:'high',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'고객의 법적 성명. 실명 확인 기준으로 마스킹 처리가 필요한 직접 식별자이다.'},{l:'비즈니스 용어 매핑',t:'ta',v:'고객성명 (BT-CUST-010)'}],
       conf:88,cc:'high',grade:'High',
       srcs:[{p:'P1',n:'비즈니스 용어집',v:'v2.1',pct:75},{p:'P2',n:'개인정보처리방침_2024',v:'v3.0',pct:60}],
       reason:'CUST_NM은 개인정보처리방침에서 직접 식별자로 분류됩니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'고객 법적 성명. 화면 표시 시 앞 1자리 외 마스킹(예: 김**) 필수. 개인정보처리방침 제9조 적용 대상.'},{l:'비즈니스 용어 매핑',t:'ta',v:'고객성명 (BT-CUST-010)'}],conf:92,cc:'high',grade:'High',reason:'개인정보처리방침 제9조에서 마스킹 기준을 확인하여 설명을 구체화하였습니다.'}}},
    {col:'CUST_TEL',item:'컬럼 설명 · 용어 매핑',before:'(없음)',after:'고객 연락처.',conf:85,cc:'high',
     detail:{
       before:[{l:'컬럼 설명',v:'(미입력)',e:true},{l:'비즈니스 용어 매핑',v:'(미입력)',e:true}],
       after:[{l:'컬럼 설명',t:'ta',v:'고객의 연락 전화번호. 010-XXXX-XXXX 형식으로 저장.'},{l:'비즈니스 용어 매핑',t:'ta',v:'고객전화번호 (BT-CUST-015)'}],
       conf:85,cc:'high',grade:'High',
       srcs:[{p:'P1',n:'비즈니스 용어집',v:'v2.1',pct:70},{p:'P2',n:'개인정보처리방침_2024',v:'v3.0',pct:55}],
       reason:'전화번호는 개인정보보호법상 직접 식별자에 해당합니다.',
       regen:{after:[{l:'컬럼 설명',t:'ta',v:'고객 연락처(전화번호). 조회 시 뒷 4자리 마스킹(예: 010-****-1234) 적용 필수.'},{l:'비즈니스 용어 매핑',t:'ta',v:'고객전화번호 (BT-CUST-015)'}],conf:90,cc:'high',grade:'High',reason:'전화번호 마스킹 기준을 명시하여 재생성하였습니다.'}}},
  ]
};

const TABLE_RESULTS=[
  {name:'LOAN_APPL_HIST',schema:'DW_CORE.CREDIT',avgConf:88},
  {name:'CRDT_SCRG_HIST',schema:'DW_CORE.RISK',avgConf:69},
  {name:'CUST_MSTR',schema:'DW_CORE.CUSTOMER',avgConf:87},
];

// Per-column statuses: colStatuses[tableName][colIdx] = status string
const colStatuses={};
TABLE_RESULTS.forEach(t=>{colStatuses[t.name]={};});

const RAG_DATA={
  glossary:[
    {name:'비즈니스 용어집 — 금융/여신',domain:'금융/여신',version:'v3.1',updated:'2026-03-15',items:1240},
    {name:'비즈니스 용어집 — 리스크',domain:'리스크 관리',version:'v2.0',updated:'2026-01-10',items:387},
    {name:'비즈니스 용어집 — 고객',domain:'고객 정보',version:'v2.1',updated:'2025-12-05',items:524},
    {name:'비즈니스 용어집 — 회계',domain:'회계/결산',version:'v1.8',updated:'2025-11-20',items:892},
  ],
  semantic:[
    {name:'시맨틱 레이어 — 여신 메트릭',domain:'금융/여신',version:'v1.2',updated:'2026-02-28',items:43},
    {name:'시맨틱 레이어 — 전사 KPI',domain:'전사 공통',version:'v2.0',updated:'2026-01-15',items:128},
  ],
  catalog:[
    {name:'데이터 카탈로그 — 핵심 테이블',domain:'전사 공통',version:'v4.0',updated:'2026-04-01',items:3847},
    {name:'데이터 카탈로그 — 참조 테이블',domain:'전사 공통',version:'v2.3',updated:'2026-02-15',items:924},
  ],
  standard:[
    {name:'BCBS 239 데이터 원칙',domain:'리스크 관리',version:'2013',updated:'2026-01-01',items:14},
    {name:'금융위원회 데이터 표준화 지침',domain:'금융 공통',version:'2024',updated:'2026-03-01',items:287},
    {name:'개인정보보호법 가이드라인',domain:'고객 정보',version:'2024',updated:'2026-04-01',items:56},
  ],
};

// ════════ STATE ════════
let uploadDone=false, currentFilter='all', currentRagTab='glossary';
let tableDomains={}, aiSuggested={};
let curTableName='', curColIdx=0, editMode=false;
let colFilter='all';
let s2SelectedRow=null;
const edits={}; // key = tableName+'_'+colIdx
let jsonView={before:false,after:false}; // JSON view toggle state per panel

// ════════ VIEW SWITCH ════════
function switchView(v){
  document.getElementById('viewRag').classList.toggle('active',v==='rag');
  document.getElementById('viewAug').classList.toggle('active',v==='aug');
  document.getElementById('lnb-rag').classList.toggle('active',v==='rag');
  document.getElementById('lnb-aug').classList.toggle('active',v==='aug');
  document.getElementById('stepBar').classList.toggle('show',v==='aug');
  document.getElementById('tbTitle').textContent=v==='rag'?'RAG 관리':'S전자 디지털사업부 — 메타데이터 자동 증강';
  if(v==='rag') renderRagTable(currentRagTab);
}

// ════════ STEP NAV ════════
function goStep(n){
  document.querySelectorAll('#viewAug .page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.si').forEach((s,i)=>{
    s.classList.remove('active','done');
    if(i+1<n)s.classList.add('done');
    if(i+1===n)s.classList.add('active');
  });
  const m={1:'page1',2:'page2',3:'page3',4:'page4',5:'page5'};
  if(m[n])document.getElementById(m[n]).classList.add('active');
}

// ════════ RAG ════════
function setRagTab(tab,lnbEl,tabEl){
  currentRagTab=tab;
  document.querySelectorAll('.lnb-sub').forEach(el=>el.classList.remove('active'));
  if(lnbEl)lnbEl.classList.add('active');
  document.querySelectorAll('.rtab').forEach(el=>el.classList.remove('active'));
  if(tabEl)tabEl.classList.add('active');
  renderRagTable(tab);
}
function renderRagTable(tab){
  const data=RAG_DATA[tab]||[];
  const labels={glossary:'비즈니스 용어집',semantic:'시맨틱 레이어',catalog:'데이터 카탈로그',standard:'산업 표준'};
  document.getElementById('ragTabLabel').textContent=`${labels[tab]} — ${data.length}개 소스`;
  document.getElementById('ragTableBody').innerHTML=data.map((s,i)=>`
    <tr style="cursor:pointer;" onclick="showRagDetail(${i},'${tab}')">
      <td><b style="font-family:var(--mono);font-size:11px;">${s.name}</b></td>
      <td style="font-size:11px;color:var(--text-muted);">${s.domain}</td>
      <td style="font-family:var(--mono);font-size:11px;">${s.version}</td>
      <td style="font-family:var(--mono);font-size:12px;color:var(--teal);">${s.items.toLocaleString()}</td>
      <td style="font-size:11px;color:var(--text-muted);">${s.updated}</td>
      <td><span class="rag-active">활성</span></td>
      <td><div style="display:flex;gap:6px;"><button class="btn btn-ghost btn-xs">편집</button><button class="btn btn-danger btn-xs">삭제</button></div></td>
    </tr>`).join('');
}
function showRagDetail(i,tab){
  const s=RAG_DATA[tab][i];
  document.getElementById('rdpTitle').textContent=s.name;
  document.getElementById('rdpSub').textContent=`${s.version} · 최종 업데이트 ${s.updated}`;
  document.getElementById('rdpBody').innerHTML=`
    <div class="rdp-row"><span class="rdp-k">소스 유형</span><span class="rdp-v">${{glossary:'비즈니스 용어집',semantic:'시맨틱 레이어',catalog:'데이터 카탈로그',standard:'산업 표준'}[tab]}</span></div>
    <div class="rdp-row"><span class="rdp-k">적용 도메인</span><span class="rdp-v">${s.domain}</span></div>
    <div class="rdp-row"><span class="rdp-k">항목 수</span><span class="rdp-v">${s.items.toLocaleString()}개</span></div>
    <div class="rdp-row"><span class="rdp-k">버전</span><span class="rdp-v">${s.version}</span></div>
    <div class="rdp-row"><span class="rdp-k">최종 업데이트</span><span class="rdp-v">${s.updated}</span></div>
    <div class="rdp-row"><span class="rdp-k">증강 사용</span><span class="rdp-v">${Math.floor(Math.random()*80+20)}회</span></div>
    <div style="font-size:10px;color:var(--text-muted);font-weight:600;letter-spacing:.06em;text-transform:uppercase;margin-top:12px;margin-bottom:8px;">연결 도메인</div>
    <div class="rdp-domains"><span class="rdp-dtag">${s.domain}</span></div>`;
}

// ════════ STEP 1 ════════
function switchTab(t){
  document.getElementById('tab-file').classList.toggle('active',t==='file');
  document.getElementById('tab-db').classList.toggle('active',t==='db');
  document.getElementById('panel-file').style.display=t==='file'?'block':'none';
  document.getElementById('panel-db').style.display=t==='db'?'block':'none';
}
function simulateUpload(){
  if(uploadDone)return; uploadDone=true;
  document.getElementById('dropZone').innerHTML=`<div style="font-size:40px;margin-bottom:12px;">✅</div><div style="font-size:15px;font-weight:600;color:var(--teal);">테이블 목록 로드 완료</div><div style="font-size:12px;color:var(--text-muted);">tables_list_2026Q2.xlsx — 8개 테이블 · Agent 자동 분류 완료</div>`;
  document.getElementById('uploadedSection').style.display='block';
  renderUploadTable(); updateSelSummary();
}
function getFiltered(){return currentFilter==='all'?TABLES:TABLES.filter(t=>t.cls===currentFilter);}
function renderUploadTable(){
  const f=getFiltered();
  document.getElementById('uploadTableBody').innerHTML=f.map(t=>{
    const ri=TABLES.indexOf(t),ec=t.emptyPct>=80?'red':t.emptyPct>=50?'amber':'green';
    const cm=CLS_META[t.cls],isOvr=t.userSel!==t.autoSel;
    const modeHtml=t.userSel
      ?`<select class="mode-sel ${t.mode==='empty'?'empty-only':'full-regen'}" onchange="onModeChange(${ri},this.value)"><option value="empty" ${t.mode==='empty'?'selected':''}>빈 항목만 채우기</option><option value="full" ${t.mode==='full'?'selected':''}>전체 재생성</option></select>`
      :`<span class="mode-disabled">—</span>`;
    return `<tr class="${t.userSel?'row-sel':''}">
      <td><input type="checkbox" class="cb upload-cb" data-idx="${ri}" ${t.userSel?'checked':''} onchange="onTableCheck(${ri},this.checked)"></td>
      <td><div style="font-family:var(--mono);font-size:11px;color:var(--teal);">${t.name}</div><div style="font-size:10px;color:var(--text-muted);">${t.schema}</div></td>
      <td style="font-family:var(--mono);font-size:12px;">${t.cols}</td>
      <td><div class="miss-mini"><div class="mt"><div class="mf ${ec}" style="width:${t.emptyPct}%"></div></div><span style="font-size:11px;color:var(--${ec});">${t.emptyPct}%</span></div></td>
      <td><span class="cls-badge ${cm.cls}" title="${cm.tip}">${cm.label}</span>${isOvr?'<span class="override-note">⚠ 수동변경</span>':''}</td>
      <td style="min-width:155px;">${modeHtml}</td>
      <td style="font-size:11px;color:var(--text-muted);font-family:var(--mono);">${t.lastAug||'—'}</td>
    </tr>`;
  }).join('');
}
function onTableCheck(idx,checked){TABLES[idx].userSel=checked;renderUploadTable();updateSelSummary();}
function toggleAll(cb){getFiltered().forEach(t=>t.userSel=cb.checked);renderUploadTable();updateSelSummary();}
function onModeChange(idx,val){TABLES[idx].mode=val;renderUploadTable();updateSelSummary();}
function bulkApplyMode(){const m=document.getElementById('bulkModeSel').value;TABLES.filter(t=>t.userSel).forEach(t=>t.mode=m);renderUploadTable();updateSelSummary();}
function setF2(btn,f){
  document.querySelectorAll('#page1 .fb').forEach(b=>b.classList.remove('act'));btn.classList.add('act');
  currentFilter=f; renderUploadTable();
}
function filterTable(f,card){
  document.querySelectorAll('#page1 .ustat-card').forEach(c=>c.classList.remove('act'));card.classList.add('act');
  currentFilter=f; renderUploadTable();
}
function updateSelSummary(){
  const sel=TABLES.filter(t=>t.userSel),n=sel.length;
  document.getElementById('ctxTables').textContent=n>0?`${n}개 선택`:'—';
  document.getElementById('toStep2Btn').disabled=n===0;
  const sumEl=document.getElementById('selSummary');
  if(n>0){
    sumEl.style.display='block';
    document.getElementById('selCountLabel').textContent=n;
    document.getElementById('modeEmptyCount').textContent=sel.filter(t=>t.mode==='empty').length;
    document.getElementById('modeFullCount').textContent=sel.filter(t=>t.mode==='full').length;
  } else sumEl.style.display='none';
}
function goToStep2(){renderDomainTable();updateStartAugBtn();goStep(2);}
function getSelTables(){return TABLES.filter(t=>t.userSel);}

// ════════ STEP 2 ════════
function renderDomainTable(){
  document.getElementById('domainTableBody').innerHTML=getSelTables().map(t=>{
    const sug=aiSuggested[t.name]||'',asgn=tableDomains[t.name]||'';
    const sugTag=sug?`<span style="font-size:10px;background:var(--teal-dim);border:1px solid var(--teal-mid);color:var(--teal);border-radius:3px;padding:2px 7px;">✨ ${sug}</span>`:`<span style="font-size:10px;color:var(--text-muted);">추천 없음</span>`;
    const modeLabel=t.mode==='empty'?`<span style="font-size:10px;color:var(--teal);">빈 항목만</span>`:`<span style="font-size:10px;color:var(--amber);">전체 재생성</span>`;
    const opts=DOMAIN_OPTS.map(d=>`<option value="${d}" ${d===asgn?'selected':''}>${d}</option>`).join('');
    return `<tr>
      <td><input type="checkbox" class="cb p2-cb" data-name="${t.name}"></td>
      <td><div style="font-family:var(--mono);font-size:11px;color:var(--teal);">${t.name}</div><div style="font-size:10px;color:var(--text-muted);">${t.cols}컬럼</div></td>
      <td>${modeLabel}</td>
      <td>${sugTag}</td>
      <td><select class="domain-sel ${asgn?'asgn':''}" data-name="${t.name}" onchange="onDomainChange(this)"><option value="">-- 선택 --</option>${opts}</select></td>
      <td><button class="src-btn ${s2SelectedRow===t.name?'active':''}" onclick="showSrcPanel('${t.name}',this)">소스 확인 ›</button></td>
    </tr>`;
  }).join('');
}
function onDomainChange(sel){
  const name=sel.dataset.name,val=sel.value;
  if(val){tableDomains[name]=val;sel.classList.add('asgn');}
  else{delete tableDomains[name];sel.classList.remove('asgn');}
  if(s2SelectedRow===name)showSrcPanel(name,null);
  updateStartAugBtn();
}

function updateStartAugBtn(){
  const tables=getSelTables();
  const allAssigned=tables.length>0 && tables.every(t=>!!tableDomains[t.name]);
  const btn=document.getElementById('startAugBtn');
  if(!btn)return;
  btn.disabled=!allAssigned;
  btn.style.opacity=allAssigned?'1':'0.4';
  btn.style.cursor=allAssigned?'pointer':'not-allowed';
}
function showSrcPanel(name,btn){
  s2SelectedRow=name;
  document.querySelectorAll('.src-btn').forEach(b=>b.classList.remove('active'));
  if(btn)btn.classList.add('active');
  const domain=tableDomains[name];
  document.getElementById('s2SrcTitle').textContent=`${name} 참조 소스`;
  if(!domain){document.getElementById('s2SrcBody').innerHTML=`<div class="s2-src-placeholder"><div style="font-size:24px;margin-bottom:8px;">⚠️</div>도메인을 먼저 지정하면<br>참조 소스를 확인할 수 있습니다</div>`;return;}
  const srcs=DOMAIN_SOURCES[domain]||[];
  document.getElementById('s2SrcBody').innerHTML=`
    <div style="font-size:11px;color:var(--text-muted);margin-bottom:10px;">도메인: <b style="color:var(--teal);">${domain}</b></div>
    ${srcs.map(s=>`<div class="s2-src-row"><span>${s.ico}</span><span class="s2-src-name">${s.n}</span><span class="s2-src-ver">${s.v}</span><span class="s2-src-auto">자동</span></div>`).join('')}
    <div class="s2-src-note">💡 참조 소스 추가·변경은 <b style="color:var(--teal);">RAG 관리</b> 메뉴에서 설정할 수 있습니다.</div>`;
}
function bulkAssignDomain(){
  const d=document.getElementById('bulkDomainSel').value;if(!d)return;
  document.querySelectorAll('.p2-cb:checked').forEach(cb=>{tableDomains[cb.dataset.name]=d;});
  renderDomainTable();
  updateStartAugBtn();
}
function autoSuggestDomains(){
  getSelTables().forEach(t=>{if(AI_SUGGEST[t.name])aiSuggested[t.name]=AI_SUGGEST[t.name];});
  getSelTables().forEach(t=>{if(!tableDomains[t.name]&&aiSuggested[t.name])tableDomains[t.name]=aiSuggested[t.name];});
  renderDomainTable();
  updateStartAugBtn();
  document.getElementById('aiSuggestToast').style.display='block';
}
function p2ToggleAll(cb){document.querySelectorAll('.p2-cb').forEach(el=>el.checked=cb.checked);}

// ════════ STEP 3 ════════
function goToStep3(){
  const tables=getSelTables();
  document.getElementById('tableProgGrid').innerHTML=tables.map(t=>`
    <div style="background:var(--card2);border:1px solid var(--border);border-radius:6px;padding:8px 10px;">
      <div style="font-family:var(--mono);font-size:11px;color:var(--teal);">${t.name}</div>
      <div style="font-size:10px;color:var(--text-muted);margin-top:2px;">${tableDomains[t.name]||'도메인 미지정'} · ${t.mode==='empty'?'빈 항목만':'전체 재생성'}</div>
      <div style="height:3px;background:var(--border);border-radius:2px;overflow:hidden;margin-top:6px;"><div class="tprog-fill" style="height:100%;width:0%;background:var(--teal);border-radius:2px;transition:width .5s;"></div></div>
    </div>`).join('');
  goStep(3);
  document.querySelectorAll('.tprog-fill').forEach((f,i)=>setTimeout(()=>f.style.width='100%',i*600+400));
  runProg();
}
const PS=[
  {id:'ps1-1',pct:15,label:'Phase 1 — 테이블 프로파일링',eta:'약 1분 45초'},
  {id:'ps1-2',pct:28,label:'Phase 1 — DDL 분석',eta:'약 1분 20초'},
  {id:'ps2-1',pct:48,label:'Phase 2 — 용어집 매핑',eta:'약 55초'},
  {id:'ps2-2',pct:68,label:'Phase 2 — 참조 문서 분석',eta:'약 35초'},
  {id:'ps3-1',pct:82,label:'Phase 3 — 테이블 메타데이터 생성',eta:'약 20초'},
  {id:'ps3-2',pct:93,label:'Phase 3 — 컬럼 메타데이터 생성',eta:'약 7초'},
  {id:'ps3-3',pct:100,label:'Phase 3 — 신뢰도 산정 완료',eta:'완료'},
];
function setPsState(id,st){
  const row=document.getElementById(id);if(!row)return;
  row.className='ps-row'+(st==='active'?' ps-active':st==='done'?' ps-done':'');
  const ico={done:'●',active:'◎',wait:'○'},col={done:'var(--teal)',active:'var(--amber)',wait:'var(--text-muted)'};
  row.querySelector('.ps-ico').textContent=ico[st]||'○';
  row.querySelector('.ps-ico').style.color=col[st]||'var(--text-muted)';
  const s=row.querySelector('.ps-status');
  s.textContent=st==='done'?'완료':st==='active'?'처리 중...':'대기 중';
  s.className='ps-status'+(st==='done'?' done':st==='active'?' active':'');
}
function runProg(){
  let i=0;
  function tick(){
    if(i>=PS.length){setTimeout(()=>{goStep(4);renderTableResults();},700);return;}
    const s=PS[i];
    if(i>0)setPsState(PS[i-1].id,'done');
    setPsState(s.id,'active');
    document.getElementById('pFill').style.width=s.pct+'%';
    document.getElementById('pLabel').textContent=s.label;
    document.getElementById('pEta').textContent='예상 완료: '+s.eta;
    i++;setTimeout(tick,900+Math.random()*500);
  }
  tick();
}

// ════════ STEP 4: TABLE RESULTS ════════
function cbadge(c,cc){return `<span class="cbadge c${cc[0]}">${c}</span>`;}
function sbadge(st){
  const m={pending:['sp','검토 대기'],approved:['sa','승인'],modified:['sm','수정 후 승인'],regen:['srn','재생성됨'],rejected:['sr','반려']};
  const[cl,lb]=m[st]||['sp','검토 대기'];
  return `<span class="sbadge ${cl}">${lb}</span>`;
}
function getColStat(name){
  const s=colStatuses[name]||{};
  const vals=Object.values(s);
  return {
    approved:vals.filter(v=>['approved','regen','modified'].includes(v)).length,
    pending: vals.filter(v=>v==='pending').length,
    rejected:vals.filter(v=>v==='rejected').length,
  };
}
function renderTableResults(){
  document.getElementById('tblResultBody').innerHTML=TABLE_RESULTS.map(t=>{
    const cc=t.avgConf>=80?'high':t.avgConf>=60?'mid':'low';
    const total=COL_DATA[t.name]?.length||0;
    const s=getColStat(t.name);
    // Completion status
    const pct=total>0?Math.round(s.approved/total*100):0;
    let statusHtml;
    if(s.approved===total && total>0){
      statusHtml=`<span class="tbl-status done">✅ 완료</span>`;
    } else if(s.approved>0){
      const fillColor='var(--amber)';
      statusHtml=`<div>
        <span class="tbl-status partial">⬤ 진행 중 ${s.approved}/${total}</span>
        <div class="tbl-status-bar"><div class="tbl-status-fill" style="width:${pct}%;background:var(--amber);"></div></div>
      </div>`;
    } else {
      statusHtml=`<div>
        <span class="tbl-status none">○ 미시작</span>
        <div class="tbl-status-bar"><div class="tbl-status-fill" style="width:0%;background:var(--teal);"></div></div>
      </div>`;
    }
    return `<tr>
      <td style="font-family:var(--mono);font-size:12px;color:var(--teal);">${t.name}</td>
      <td style="font-size:11px;color:var(--text-muted);">${t.schema}</td>
      <td style="font-family:var(--mono);font-size:12px;">${total}개</td>
      <td>${cbadge(t.avgConf,cc)}</td>
      <td>${statusHtml}</td>
      <td><span class="tsm g">${s.approved}</span></td>
      <td><span class="tsm s">${total-s.approved-s.rejected}</span></td>
      <td><span class="tsm r">${s.rejected}</span></td>
      <td><button class="btn btn-teal-o btn-sm" onclick="openColList('${t.name}')">컬럼 검토 →</button></td>
    </tr>`;
  }).join('');
  updateP4ApprovedCnt();
  updateFinishBtn();
}
function updateP4ApprovedCnt(){
  let total=0;
  TABLE_RESULTS.forEach(t=>{total+=getColStat(t.name).approved;});
  const el=document.getElementById('p4ApprovedCnt');
  if(el)el.textContent=total;
  updateFinishBtn();
}

function updateFinishBtn(){
  // All columns across all augmented tables must be approved
  let allTotal=0, allApproved=0;
  TABLE_RESULTS.forEach(t=>{
    const rows=COL_DATA[t.name]||[];
    allTotal+=rows.length;
    allApproved+=getColStat(t.name).approved;
  });
  const btn=document.getElementById('finishBtn');
  if(!btn)return;
  const ready=allTotal>0 && allApproved===allTotal;
  btn.disabled=!ready;
  btn.style.opacity=ready?'1':'0.4';
  btn.style.cursor=ready?'pointer':'not-allowed';
}

// ════════ STEP 4t: COLUMN LIST ════════
function openColList(tblName){
  curTableName=tblName;
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  document.getElementById('colListTableName').textContent=tblName;
  document.getElementById('colListBadge').textContent=`${COL_DATA[tblName]?.length||0}개 컬럼`;
  colFilter='all';
  document.querySelectorAll('#page4t .fb').forEach(b=>b.classList.remove('act'));
  document.querySelector('#page4t .fb').classList.add('act');
  renderColList();
  document.querySelectorAll('#viewAug .page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page4t').classList.add('active');
}
function backToTableList(){
  renderTableResults();
  document.querySelectorAll('#viewAug .page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page4').classList.add('active');
}
function setColF(btn,f){
  document.querySelectorAll('#page4t .fb').forEach(b=>b.classList.remove('act'));
  btn.classList.add('act'); colFilter=f; renderColList();
}
function renderColList(){
  const rows=COL_DATA[curTableName]||[];
  const statMap=colStatuses[curTableName]||{};
  // Build filtered rows with original index
  const indexed=rows.map((r,i)=>({r,i}));
  const filtered=colFilter==='all'?indexed:indexed.filter(({r,i})=>{
    const st=statMap[i]||'pending';
    if(colFilter==='approved') return ['approved','regen','modified'].includes(st);
    return st===colFilter;
  });
  document.getElementById('colListBody').innerHTML=filtered.map(({r,i})=>{
    const st=statMap[i]||'pending';
    return `<tr id="col-row-${i}">
      <td><input type="checkbox" class="cb col-cb" data-idx="${i}"></td>
      <td style="font-family:var(--mono);font-size:11px;color:var(--teal);">${r.col}</td>
      <td>${r.item}</td>
      <td><span style="max-width:130px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;display:inline-block;color:var(--text-muted);">${r.before}</span></td>
      <td><span style="max-width:130px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;display:inline-block;">${r.after}</span></td>
      <td>${cbadge(r.conf,r.cc)}</td>
      <td id="col-st-${i}">${sbadge(st)}</td>
      <td><div style="display:flex;gap:5px;">
        <button class="btn btn-ghost btn-xs" onclick="approveCol(${i})">✓ 승인</button>
        <button class="btn btn-regen btn-xs" onclick="rejectAndRegenCol(${i})">↺ 반려·재생성</button>
        <span class="rlink" onclick="openColDetail(${i})">상세</span>
      </div></td>
    </tr>`;
  }).join('');
}
function approveCol(idx){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  colStatuses[curTableName][idx]='approved';
  renderColList(); updateP4ApprovedCnt();
}

// Per-row inline reject & regen
function rejectAndRegenCol(idx){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  // Show spinner in status cell
  const stCell=document.getElementById('col-st-'+idx);
  if(stCell)stCell.innerHTML=`<span class="regen-inline"><span class="regen-spin"></span>재생성 중...</span>`;
  // Disable row buttons temporarily
  const row=document.getElementById('col-row-'+idx);
  if(row)row.querySelectorAll('button').forEach(b=>b.disabled=true);
  setTimeout(()=>{
    colStatuses[curTableName][idx]='regen';
    renderColList(); updateP4ApprovedCnt();
  },1600);
}

function toggleAllCols(cb){document.querySelectorAll('.col-cb').forEach(el=>el.checked=cb.checked);}
function bulkApproveSelected(){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  document.querySelectorAll('.col-cb:checked').forEach(cb=>{
    const idx=parseInt(cb.dataset.idx);
    colStatuses[curTableName][idx]='approved';
  });
  renderColList(); updateP4ApprovedCnt();
}
function bulkRejectRegen(){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  const checked=[...document.querySelectorAll('.col-cb:checked')];
  if(!checked.length)return;
  const indices=checked.map(cb=>parseInt(cb.dataset.idx));
  // Show spinners
  indices.forEach(idx=>{
    const stCell=document.getElementById('col-st-'+idx);
    if(stCell)stCell.innerHTML=`<span class="regen-inline"><span class="regen-spin"></span>재생성 중...</span>`;
    const row=document.getElementById('col-row-'+idx);
    if(row)row.querySelectorAll('button').forEach(b=>b.disabled=true);
  });
  setTimeout(()=>{
    indices.forEach(idx=>{colStatuses[curTableName][idx]='regen';});
    renderColList(); updateP4ApprovedCnt();
  },1700);
}
// *** FIX: Bulk approve - also handles regen items ***
function approveAllCols(){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  const rows=COL_DATA[curTableName]||[];
  rows.forEach((r,i)=>{
    const cur=colStatuses[curTableName][i]||'pending';
    // Approve if conf>=80 AND not already in a final approved/modified state
    if(r.conf>=80 && !['approved','modified'].includes(cur)){
      colStatuses[curTableName][i]='approved';
    }
  });
  renderColList();
  updateP4ApprovedCnt();
}

// ════════ STEP 4d: COLUMN DETAIL ════════
function openColDetail(idx){
  curColIdx=idx; editMode=false;
  jsonView={before:false,after:false};
  updateJsonToggleUI();
  renderDetail();
  document.getElementById('regenOverlay').classList.remove('vis');
  document.getElementById('approveBtn').disabled=false;
  document.getElementById('rejectBtn').disabled=false;
  document.querySelectorAll('#viewAug .page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page4d').classList.add('active');
}
function backToColList(){
  exitEdit();
  document.getElementById('regenOverlay').classList.remove('vis');
  document.querySelectorAll('#viewAug .page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page4t').classList.add('active');
  renderColList();
}

function getRowData(){
  const rows=COL_DATA[curTableName]||[];
  return rows[curColIdx];
}
function getStatus(){
  return (colStatuses[curTableName]||{})[curColIdx]||'pending';
}
function getSaved(){
  return edits[curTableName+'_'+curColIdx];
}

function renderDetail(){
  const r=getRowData(); if(!r)return;
  const d=r.detail;
  const st=getStatus(), isRegen=st==='regen';
  const saved=getSaved();

  // Nav
  document.getElementById('dColName').textContent=r.col;
  document.getElementById('dBadge').className='col-b';
  document.getElementById('dBadge').textContent='컬럼';
  document.getElementById('dPos').textContent=(curColIdx+1)+' / '+(COL_DATA[curTableName]||[]).length;

  // Chip
  const ch=document.getElementById('dChip');
  if(saved)ch.innerHTML='<span class="chip-mod">✏️ 수정됨</span>';
  else if(isRegen)ch.innerHTML='<span class="chip-regen">♻️ 재생성됨</span>';
  else ch.innerHTML='';

  // Approval chip
  const ac=document.getElementById('dApprovalChip');
  const isApproved=['approved','modified'].includes(st);
  if(ac){
    ac.innerHTML=isApproved?'<span class="chip-approved">✅ 승인됨</span>':'';
  }
  // Approve button state
  const aBtn=document.getElementById('approveBtn');
  if(aBtn){
    if(isApproved){
      aBtn.textContent='✅ 승인됨';
      aBtn.style.background='var(--green-dim)';
      aBtn.style.borderColor='rgba(34,197,94,.4)';
      aBtn.style.color='var(--green)';
    } else {
      aBtn.textContent='✅ 승인';
      aBtn.style.background=''; aBtn.style.borderColor=''; aBtn.style.color='';
      aBtn.className='btn btn-primary';
    }
  }

  // Regen banner
  document.getElementById('regenBanner').classList.toggle('vis',isRegen);

  // Before panel
  renderBeforePanel(d);

  // After panel header
  const ph=document.getElementById('phAfter');
  if(editMode){
    ph.className='ph2 after emode';
    document.getElementById('phAfterIco').textContent='✏️';
    document.getElementById('phAfterLbl').textContent='직접 수정 중';
    document.getElementById('editBtn').style.display='none';
    document.getElementById('panAfter').className='pb2 after emode';
  } else if(isRegen){
    ph.className='ph2 after regen-mode';
    document.getElementById('phAfterIco').textContent='♻️';
    document.getElementById('phAfterLbl').textContent='변경 후 — Agent 재생성';
    document.getElementById('editBtn').style.display='inline-flex';
    document.getElementById('panAfter').className='pb2 after regen-mode';
  } else {
    ph.className='ph2 after';
    document.getElementById('phAfterIco').textContent='✨';
    document.getElementById('phAfterLbl').textContent='변경 후 — Agent 생성';
    document.getElementById('editBtn').style.display='inline-flex';
    document.getElementById('panAfter').className='pb2 after';
  }

  // Edit bar
  document.getElementById('eBar').classList.toggle('vis',editMode);

  // After panel content
  renderAfterContent(d, st, saved);

  // Evidence
  renderEvidence(d, st);
}

function renderBeforePanel(d){
  const contentEl=document.getElementById('panBeforeContent');
  if(jsonView.before){
    const obj={};
    d.before.forEach(f=>obj[f.l]=f.v);
    contentEl.innerHTML=`<pre class="json-view">${syntaxHighlight(JSON.stringify(obj,null,2))}</pre>`;
  } else {
    contentEl.innerHTML=d.before.map(f=>`
      <div class="fr">
        <div class="fl">${f.l}</div>
        <div class="fv ${f.e?'empty':''}">${f.v}</div>
      </div>`).join('');
  }
}

function renderAfterContent(d, st, saved){
  const isRegen=st==='regen';
  const afterData=isRegen?d.regen.after:d.after;
  const contentEl=document.getElementById('panAfterContent');

  if(jsonView.after && !editMode){
    const obj={};
    afterData.forEach(f=>{
      const cv=saved?.[f.l]!==undefined?saved[f.l]:f.v;
      obj[f.l]=cv;
    });
    contentEl.innerHTML=`<pre class="json-view">${syntaxHighlight(JSON.stringify(obj,null,2))}</pre>`;
    return;
  }

  if(editMode){
    contentEl.innerHTML=afterData.map(f=>{
      const cv=saved?.[f.l]!==undefined?saved[f.l]:f.v;
      return `<div class="fr"><div class="fl">${f.l}</div><textarea class="fta" data-field="${f.l}">${cv}</textarea></div>`;
    }).join('');
  } else {
    contentEl.innerHTML=afterData.map(f=>{
      const cv=saved?.[f.l]!==undefined?saved[f.l]:f.v;
      const mod=saved?.[f.l]!==undefined;
      return `<div class="fr">
        <div class="fl">${f.l}${mod?'<span class="chip-mod">수정됨</span>':''}</div>
        <div class="fv">${cv}</div>
      </div>`;
    }).join('');
  }
}

function syntaxHighlight(json){
  return json.replace(/("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+\-]?\d+)?)/g, match=>{
    if(/^"/.test(match)){
      if(/:$/.test(match)) return `<span class="json-key">${match}</span>`;
      return `<span class="json-str">${match}</span>`;
    }
    if(/null/.test(match)) return `<span class="json-null">${match}</span>`;
    return match;
  });
}

function renderEvidence(d, st){
  const isRegen=st==='regen';
  const ev=isRegen?{conf:d.regen.conf,cc:d.regen.cc,grade:d.regen.grade,reason:d.regen.reason}:{conf:d.conf,cc:d.cc,grade:d.grade,reason:d.reason};
  document.getElementById('cmeter').innerHTML=`
    <div class="ccircle ${ev.cc}"><span class="cnum">${ev.conf}</span><span class="cgl">/ 100</span></div>
    <div class="cright">
      <div class="cl2">신뢰도 등급: ${ev.grade==='High'?'<span style="color:var(--green)">High</span>':ev.grade==='Medium'?'<span style="color:var(--amber)">Medium</span>':'<span style="color:var(--red)">Low — 검토 권고</span>'}</div>
      <div class="cs">${ev.grade==='Low'?'신뢰도가 낮습니다. 반려 시 자동으로 재생성됩니다.':'참조 소스와의 일치도가 높습니다.'}</div>
    </div>`;
  document.getElementById('srcList').innerHTML=`
    <div style="font-size:11px;color:var(--text-muted);font-weight:600;letter-spacing:.06em;text-transform:uppercase;margin-bottom:8px;">참조 소스</div>
    ${d.srcs.map(s=>`<div class="srcrow"><div class="srcp">${s.p}</div><div class="srcn">${s.n}</div><div class="srcv2">${s.v}</div><div class="srcbar"><div class="srcbar-f" style="width:${s.pct}%"></div></div></div>`).join('')}`;
  document.getElementById('areasoning').textContent=ev.reason;
}

// JSON toggle
function toggleJsonView(panel){
  jsonView[panel]=!jsonView[panel];
  updateJsonToggleUI();
  const r=getRowData(); if(!r)return;
  const st=getStatus(), saved=getSaved();
  if(panel==='before') renderBeforePanel(r.detail);
  else renderAfterContent(r.detail, st, saved);
}
function updateJsonToggleUI(){
  const bb=document.getElementById('beforeJsonBtn'),ab=document.getElementById('afterJsonBtn');
  if(bb)bb.classList.toggle('on',jsonView.before);
  if(ab)ab.classList.toggle('on',jsonView.after);
}

// Edit mode
function toggleEdit(){
  if(!editMode){
    editMode=true;
    jsonView.after=false; // exit JSON mode when entering edit
    updateJsonToggleUI();
    renderDetail();
  } else cancelEdit();
}
function exitEdit(){editMode=false;document.getElementById('editBtn').style.display='inline-flex';}
function cancelEdit(){exitEdit();renderDetail();}
function saveEdit(){
  const saved=edits[curTableName+'_'+curColIdx]||{};
  document.querySelectorAll('#panAfterContent [data-field]').forEach(el=>{
    saved[el.dataset.field]=el.value;
  });
  edits[curTableName+'_'+curColIdx]=saved;
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  colStatuses[curTableName][curColIdx]='modified';
  exitEdit();
  renderDetail();
}

// Approve / Reject
function approveD(){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  colStatuses[curTableName][curColIdx]='approved';
  updateP4ApprovedCnt();
  renderDetail(); // re-render to show approved state in view
}

function rejectD(){
  if(!colStatuses[curTableName])colStatuses[curTableName]={};
  exitEdit();
  // Show regen overlay on after panel
  const overlay=document.getElementById('regenOverlay');
  const fill=document.getElementById('regenProgFill');
  const lbl=document.getElementById('regenProgLabel');
  overlay.classList.add('vis');
  fill.style.width='0%';
  document.getElementById('approveBtn').disabled=true;
  document.getElementById('rejectBtn').disabled=true;
  document.getElementById('editBtn').style.display='none';
  const steps=['참조 소스 재탐색 중...','힌트 컨텍스트 적용 중...','메타데이터 재생성 중...','신뢰도 재산정 중...'];
  let i=0;
  function tick(){
    if(i>=steps.length){
      fill.style.width='100%'; lbl.textContent='재생성 완료!';
      setTimeout(()=>{
        overlay.classList.remove('vis');
        // Clear manual edits if any
        delete edits[curTableName+'_'+curColIdx];
        colStatuses[curTableName][curColIdx]='regen';
        jsonView={before:false,after:false};
        updateJsonToggleUI();
        renderDetail();
        document.getElementById('approveBtn').disabled=false;
        document.getElementById('rejectBtn').disabled=false;
      },500);
      return;
    }
    fill.style.width=Math.round((i+1)/steps.length*90)+'%';
    lbl.textContent=steps[i];
    i++; setTimeout(tick,600+Math.random()*300);
  }
  tick();
}

function prevD(){moveDetail(-1);}
function nextD(){moveDetail(1);}
function moveDetail(dir){
  const rows=COL_DATA[curTableName]||[], next=curColIdx+dir;
  if(next>=0&&next<rows.length){
    exitEdit();
    document.getElementById('regenOverlay').classList.remove('vis');
    curColIdx=next;
    jsonView={before:false,after:false};
    updateJsonToggleUI();
    renderDetail();
    document.getElementById('approveBtn').disabled=false;
    document.getElementById('rejectBtn').disabled=false;
  } else backToColList();
}

// ════════ STEP 5 ════════
function finish(){
  let approved=0;
  TABLE_RESULTS.forEach(t=>{
    const s=colStatuses[t.name]||{};
    Object.values(s).forEach(v=>{
      if(['approved','regen','modified'].includes(v))approved++;
    });
  });
  document.getElementById('dA').textContent=approved;
  document.getElementById('doneTableList').textContent=TABLE_RESULTS.map(t=>t.name).join(', ');
  goStep(5);
}

// ════════ INIT ════════
switchView('aug');
renderRagTable('glossary');
</script>
</body>
</html>
