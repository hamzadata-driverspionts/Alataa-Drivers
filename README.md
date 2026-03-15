<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>الاستعلام عن النقاط</title>
<link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;900&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #0d1117; --surface: #161b22; --surface2: #1c2128; --border: #30363d;
    --green: #3fb950; --green-dim: #238636; --green-glow: rgba(63,185,80,0.18);
    --red: #f85149; --red-dim: #da3633; --gold: #e3b341;
    --text: #e6edf3; --text-muted: #7d8590; --text-sub: #484f58;
  }
  body {
    font-family: 'Tajawal', sans-serif; background: var(--bg);
    min-height: 100vh; display: flex; align-items: center;
    justify-content: center; padding: 24px 16px; position: relative;
  }
  body::before {
    content:''; position:fixed; top:-200px; left:50%; transform:translateX(-50%);
    width:700px; height:500px;
    background:radial-gradient(ellipse, rgba(63,185,80,0.08) 0%, transparent 70%);
    pointer-events:none;
  }
  .card {
    width:100%; max-width:430px; background:var(--surface);
    border:1px solid var(--border); border-radius:16px; overflow:hidden;
    box-shadow:0 0 0 1px rgba(255,255,255,0.03), 0 24px 60px rgba(0,0,0,0.5);
    animation:cardIn 0.5s cubic-bezier(.22,1,.36,1) both;
  }
  @keyframes cardIn { from{opacity:0;transform:translateY(24px) scale(0.97)} to{opacity:1;transform:none} }

  .header {
    padding:28px 24px 22px; text-align:center; border-bottom:1px solid var(--border);
    background:linear-gradient(180deg, rgba(63,185,80,0.06) 0%, transparent 100%);
  }
  .badge {
    display:inline-flex; align-items:center; gap:6px;
    background:rgba(63,185,80,0.12); border:1px solid rgba(63,185,80,0.3);
    color:var(--green); font-size:11px; font-weight:700;
    padding:4px 12px; border-radius:99px; letter-spacing:1px; margin-bottom:14px;
  }
  .badge::before { content:'●'; font-size:8px; animation:blink 1.8s ease infinite; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
  .logo-img {
    display:block; max-width:160px; max-height:56px; object-fit:contain;
    margin:0 auto 12px; filter:brightness(0) invert(1); opacity:0.85;
  }
  .header h1 { font-size:20px; font-weight:900; color:var(--text); }
  .header p  { font-size:13px; color:var(--text-muted); margin-top:4px; }

  .body { padding:24px; }
  .field-label {
    display:block; font-size:12px; font-weight:700; color:var(--text-muted);
    text-transform:uppercase; letter-spacing:0.8px; margin-bottom:8px;
  }
  .input-wrap { position:relative; margin-bottom:14px; }
  .input-wrap input {
    width:100%; padding:13px 16px 13px 48px; background:var(--surface2);
    border:1px solid var(--border); border-radius:10px;
    font-family:'Tajawal',sans-serif; font-size:16px; color:var(--text); outline:none;
    transition:border-color .2s, box-shadow .2s;
  }
  .input-wrap input::placeholder { color:var(--text-sub); }
  .input-wrap input:focus { border-color:var(--green-dim); box-shadow:0 0 0 3px rgba(63,185,80,0.15); }
  .input-icon { position:absolute; left:14px; top:50%; transform:translateY(-50%); color:var(--text-sub); width:18px; height:18px; }

  .btn {
    width:100%; padding:13px; background:var(--green-dim); color:#fff;
    font-family:'Tajawal',sans-serif; font-size:15px; font-weight:700;
    border:none; border-radius:10px; cursor:pointer;
    display:flex; align-items:center; justify-content:center; gap:8px;
    transition:background .2s, transform .15s, box-shadow .2s;
    box-shadow:0 4px 20px rgba(35,134,54,0.4);
  }
  .btn:hover { background:var(--green); box-shadow:0 6px 28px rgba(63,185,80,0.35); transform:translateY(-1px); }
  .btn:active { transform:translateY(0); }
  .btn svg { width:16px; height:16px; }

  #result { margin-top:20px; }
  .loading { display:flex; align-items:center; justify-content:center; gap:10px; padding:20px; color:var(--text-muted); font-size:14px; }
  .spin { width:18px; height:18px; border:2px solid var(--border); border-top-color:var(--green); border-radius:50%; animation:spin .7s linear infinite; }
  @keyframes spin { to { transform:rotate(360deg); } }

  .alert { padding:13px 16px; border-radius:10px; font-size:14px; font-weight:500; display:flex; align-items:center; gap:10px; }
  .alert.warn  { background:rgba(232,179,65,0.1); border:1px solid rgba(232,179,65,0.3); color:var(--gold); }
  .alert.error { background:rgba(248,81,73,0.1);  border:1px solid rgba(248,81,73,0.3);  color:var(--red); }

  .profile {
    background:var(--surface2); border:1px solid var(--border); border-radius:12px;
    padding:14px 16px; display:flex; align-items:center; gap:14px; margin-bottom:16px;
    animation:fadeUp .3s ease both;
  }
  .avatar {
    width:44px; height:44px; border-radius:50%;
    background:linear-gradient(135deg, var(--green-dim), var(--green));
    display:flex; align-items:center; justify-content:center;
    font-size:18px; flex-shrink:0; box-shadow:0 0 16px var(--green-glow);
  }
  .profile-name { font-size:16px; font-weight:800; color:var(--text); }
  .profile-id   { font-size:12px; color:var(--text-muted); margin-top:3px; }

  .gauge {
    background:var(--surface2); border:1px solid var(--border); border-radius:12px;
    padding:16px; margin-bottom:16px; animation:fadeUp .35s ease both;
  }
  .gauge-top { display:flex; justify-content:space-between; align-items:flex-end; margin-bottom:12px; }
  .gauge-label { font-size:12px; font-weight:700; color:var(--text-muted); text-transform:uppercase; letter-spacing:.6px; }
  .gauge-num { font-size:28px; font-weight:900; line-height:1; }
  .gauge-num.hi  { color:var(--green); }
  .gauge-num.mid { color:var(--gold); }
  .gauge-num.lo  { color:var(--red); }
  .gauge-num sup { font-size:14px; color:var(--text-muted); font-weight:500; }
  .track { height:8px; background:var(--bg); border-radius:99px; overflow:hidden; }
  .fill { height:100%; border-radius:99px; transition:width 1s cubic-bezier(.4,0,.2,1); }
  .fill.hi  { background:linear-gradient(90deg, var(--green-dim), var(--green)); }
  .fill.mid { background:linear-gradient(90deg, #b08800, var(--gold)); }
  .fill.lo  { background:linear-gradient(90deg, var(--red-dim), var(--red)); }

  .section-title { font-size:11px; font-weight:700; color:var(--text-sub); text-transform:uppercase; letter-spacing:1px; margin-bottom:10px; }
  .tx-list { display:flex; flex-direction:column; gap:7px; }
  .tx {
    display:flex; align-items:center; gap:12px;
    background:var(--surface2); border:1px solid var(--border); border-radius:10px;
    padding:11px 14px; animation:fadeUp .3s ease both;
  }
  .tx-dot { width:8px; height:8px; border-radius:50%; flex-shrink:0; margin-top:1px; }
  .tx.add    .tx-dot { background:var(--green); box-shadow:0 0 6px var(--green-glow); }
  .tx.deduct .tx-dot { background:var(--red);   box-shadow:0 0 6px rgba(248,81,73,0.4); }
  .tx-body { flex:1; min-width:0; }
  .tx-reason { font-size:13px; font-weight:600; color:var(--text); white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .tx-date   { font-size:11px; color:var(--text-muted); margin-top:3px; }
  .tx-pts { font-size:14px; font-weight:800; flex-shrink:0; }
  .tx.add    .tx-pts { color:var(--green); }
  .tx.deduct .tx-pts { color:var(--red); }

  @keyframes fadeUp { from{opacity:0;transform:translateY(8px)} to{opacity:1;transform:none} }
</style>
</head>
<body>
<div class="card">
  <div class="header">
    <div class="badge">نظام النقاط</div>
    <img src="logo.png" class="logo-img" alt="الشعار" onerror="this.style.display='none'">
    <h1>الاستعلام عن النقاط</h1>
    <p>أدخل رقمك التعريفي لعرض نقاطك</p>
  </div>
  <div class="body">
    <label class="field-label" for="searchId">الرقم التعريفي</label>
    <div class="input-wrap">
      <input type="text" id="searchId" placeholder="ادخل رقمك التعريفي: " autocomplete="off">
      <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/>
      </svg>
    </div>
    <button class="btn" onclick="doSearch()">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
        <circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/>
      </svg>
      بحث
    </button>
    <div id="result"></div>
  </div>
</div>
<script>
const SHEET = "https://docs.google.com/spreadsheets/d/e/2PACX-1vTq27e_VfyW4FIqgMTj3zv3kcHqBrUfQWJU1wf4rskMFD1nlT50PGDLzu7gbvxseHJFq69o64d3ENJa/pub?output=csv";
document.getElementById("searchId").addEventListener("keydown", e => { if(e.key==="Enter") doSearch(); });
function alertBox(cls,icon,msg){ return `<div class="alert ${cls}">${icon} ${msg}</div>`; }
async function doSearch(){
  const id=document.getElementById("searchId").value.trim();
  const box=document.getElementById("result");
  if(!id){box.innerHTML=alertBox("warn","⚠️","الرجاء إدخال الرقم التعريفي");return;}
  box.innerHTML=`<div class="loading"><div class="spin"></div> جاري البحث…</div>`;
  try{
    const csv=await(await fetch(SHEET)).text();
    const rows=csv.split("\n").slice(1);
    let total=12,found=false,name="",txs=[];
    rows.forEach(row=>{
      if(!row.trim())return;
      const c=row.split(",");
      if((c[0]||"").trim()!==id)return;
      found=true; name=(c[1]||"").trim();
      const date=(c[2]||"").trim();
      const type=(c[3]||"").trim().replace(/[إأآ]/g,"ا");
      const reason=(c[4]||"").trim();
      const pts=parseFloat(c[5])||0;
      if(type==="خصم"){total-=pts;txs.push({kind:"deduct",date,reason,pts});}
      else if(type==="اضافة"){total+=pts;txs.push({kind:"add",date,reason,pts});}
    });
    if(!found){box.innerHTML=alertBox("warn","🔍","لا توجد بيانات لهذا الرقم التعريفي");return;}
    total=Math.max(0,Math.min(12,total));
    const pct=Math.round(total/12*100);
    const g=total>=9?"hi":total>=5?"mid":"lo";
    const txHTML=txs.map((t,i)=>`
      <div class="tx ${t.kind}" style="animation-delay:${0.05+i*0.06}s">
        <div class="tx-dot"></div>
        <div class="tx-body">
          <div class="tx-reason">${t.reason||"—"}</div>
          <div class="tx-date">${t.date}</div>
        </div>
        <div class="tx-pts">${t.kind==="add"?"+":"−"}${t.pts}</div>
      </div>`).join("");
    box.innerHTML=`
      <div class="profile">
        <div class="avatar">👤</div>
        <div><div class="profile-name">${name}</div><div class="profile-id"># ${id}</div></div>
      </div>
      <div class="gauge">
        <div class="gauge-top">
          <span class="gauge-label">رصيد النقاط</span>
          <span class="gauge-num ${g}">${total}<sup> / 12</sup></span>
        </div>
        <div class="track"><div class="fill ${g}" id="bar" style="width:0%"></div></div>
      </div>
      ${txs.length?`<div class="section-title">سجل المعاملات</div><div class="tx-list">${txHTML}</div>`
        :`<div class="loading" style="justify-content:flex-start;padding:8px 4px;color:var(--text-sub)">لا توجد معاملات</div>`}
    `;
    requestAnimationFrame(()=>requestAnimationFrame(()=>{
      const bar=document.getElementById("bar");
      if(bar)bar.style.width=pct+"%";
    }));
  }catch(e){
    box.innerHTML=alertBox("error","⚠️","فشل الاتصال بملف البيانات");
    console.error(e);
  }
}
</script>
</body>
</html>
