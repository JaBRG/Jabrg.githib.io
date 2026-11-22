<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Reflexionsrad – Was habe ich heute gelernt?</title>
<style>
  :root{
    --bg:#EFE6FB; --ink:#2B1A45; --muted:#6B5A86; --card:#ffffff; --line:#D9C8F1;
    --accent:#5B2A91; --accent2:#7E45C4; --dark:#34145F;
  }
  *{box-sizing:border-box}
  html,body{margin:0;height:100%}
  body{font-family:Calibri,Carlito,"Segoe UI",system-ui,sans-serif;background:radial-gradient(circle at 30% 20%,#F7F1FE 0%,var(--bg) 55%,#E4D6F8 100%);color:var(--ink);
    display:flex;flex-direction:column;min-height:100vh}
  header{display:flex;align-items:center;justify-content:center;padding:14px 24px 6px;height:64px}
  header h1{text-align:center;font-size:clamp(1.3rem,2.6vw,2rem);margin:0;font-weight:700;letter-spacing:.2px;color:var(--accent)}
  header .tools{display:flex;gap:8px;flex-wrap:wrap}
  button{font-family:inherit;cursor:pointer}
  .ghost{background:rgba(255,255,255,.6);border:1px solid var(--line);color:var(--accent);border-radius:999px;padding:7px 16px;font-size:.95rem;font-weight:600}
  .ghost:hover{background:#fff}
  main{min-height:calc(100vh - 64px);min-height:calc(100svh - 64px);display:flex;flex-direction:column;justify-content:center;gap:6px;padding:12px 24px 20px;align-items:center;max-width:1400px;width:100%;margin:0 auto}
  .wheelwrap{position:relative;width:100%;max-width:min(860px, calc(100vh - 110px));max-width:min(860px, calc(100svh - 110px));aspect-ratio:1;justify-self:center}
  canvas{width:100%;height:100%;display:block;cursor:pointer;border-radius:50%;box-shadow:0 18px 40px rgba(52,20,95,.22)}
  .pointer{position:absolute;top:-6px;left:50%;transform:translateX(-50%);width:0;height:0;
    border-left:20px solid transparent;border-right:20px solid transparent;border-top:36px solid var(--accent);
    filter:drop-shadow(0 3px 3px rgba(52,20,95,.35));pointer-events:none;z-index:2}
  .hub{position:absolute;left:50%;top:50%;width:18%;aspect-ratio:1;transform:translate(-50%,-50%);border-radius:50%;
    background:#E6D7FA;border:5px solid var(--accent);color:var(--accent);box-shadow:0 4px 14px rgba(52,20,95,.25);display:flex;align-items:center;justify-content:center;
    font-weight:700;font-size:clamp(1rem,2.6vmin,1.6rem);pointer-events:none;text-align:center;line-height:1.1}
  .panel{display:flex;flex-direction:column;gap:18px}
  .card{background:var(--card);border:1px solid var(--line);border-radius:16px;padding:28px 30px;min-height:240px;
    display:flex;flex-direction:column;justify-content:center;gap:14px;box-shadow:0 10px 30px rgba(52,20,95,.10);border-radius:22px}
  .badge{align-self:flex-start;color:#fff;font-weight:700;font-size:.95rem;padding:4px 12px;border-radius:999px}
  .sentence{font-size:clamp(1.8rem,3.4vw,3rem);line-height:1.18;font-weight:700;margin:0}
  .hint{color:var(--muted);font-size:1.1rem;margin:0}
  .card.pop{animation:pop .35s ease-out}
  @keyframes pop{0%{transform:scale(.96);opacity:.4}100%{transform:scale(1);opacity:1}}
  .actions{display:flex;gap:12px;flex-wrap:wrap;align-items:center}
  .spin{background:linear-gradient(135deg,var(--accent2),var(--accent));color:#fff;border:none;border-radius:999px;padding:16px 40px;font-size:1.4rem;font-weight:700;box-shadow:0 8px 20px rgba(91,42,145,.35);transition:transform .15s}
  .spin:hover{transform:translateY(-2px)}
  .spin:disabled{opacity:.5;cursor:default}
  .timer{font-variant-numeric:tabular-nums;font-size:1.4rem;font-weight:700;min-width:3.2em;text-align:center}
  .timer.done{color:#D4493A}
  .opts{display:flex;gap:18px;flex-wrap:wrap;color:var(--muted);font-size:1rem}
  .opts label{display:flex;align-items:center;gap:6px;cursor:pointer}
  .count{color:var(--muted);font-size:.95rem}
  .overlay{position:fixed;inset:0;background:rgba(20,22,26,.45);display:none;align-items:center;justify-content:center;padding:20px;z-index:10}
  .overlay.open{display:flex}
  .dialog{background:var(--card);border-radius:14px;padding:22px;width:min(760px,100%);max-height:90vh;display:flex;flex-direction:column;gap:12px}
  .dialog h2{margin:0;font-size:1.3rem}
  .dialog p{margin:0;color:var(--muted)}
  textarea{width:100%;min-height:340px;font-family:inherit;font-size:1.05rem;padding:12px;border:1px solid var(--line);border-radius:8px;resize:vertical}
  .row{display:flex;gap:8px;justify-content:flex-end;flex-wrap:wrap}
  .primary{background:var(--accent);color:#fff;border:none;border-radius:8px;padding:8px 16px;font-size:1rem}
  .result{position:fixed;inset:0;z-index:20;display:none;flex-direction:column;align-items:center;justify-content:center;
    gap:3vh;padding:6vh 6vw;background:radial-gradient(circle at 50% 35%,#4A1F80 0%,var(--dark) 60%,#250C47 100%);color:#fff;text-align:center;cursor:pointer}
  .result.open{display:flex;animation:pop .35s ease-out}
  .result .badge{align-self:center;min-width:150px;text-align:center;box-sizing:border-box;font-size:1.2rem;font-weight:600;line-height:normal;padding:10px 18px;border:1px solid rgba(255,255,255,.35);border-radius:999px}
  .result .big{font-size:clamp(2.6rem,6.5vw,6.5rem);line-height:1.12;font-weight:700;margin:0 0 5vh;max-width:20ch}
  .result .rtools{display:flex;gap:14px;align-items:center;flex-wrap:wrap;justify-content:center}
  .result .rtimer{font-size:clamp(1.6rem,3vw,2.6rem);font-weight:700;font-variant-numeric:tabular-nums}
  .result #rTimerBtn{font-variant-numeric:tabular-nums}
  .result #rTimerBtn.done{color:#FF9DB5;border-color:#FF9DB5}
  .result .close{color:#CDB6EE;font-size:1.1rem}
  .result .ghost{background:rgba(255,255,255,.12);border-color:rgba(255,255,255,.35);color:#fff}
  .result .spin{background:#fff;color:var(--dark);box-shadow:0 8px 24px rgba(0,0,0,.3)}
  .names{display:none;flex-direction:column;align-items:center;gap:1.4vh}
  .names.show{display:flex;animation:pop .4s ease-out}
  .names .lbl{color:#CDB6EE;font-size:clamp(1rem,1.8vw,1.5rem);font-weight:600;letter-spacing:.5px}
  .names .pills{display:flex;gap:18px;flex-wrap:wrap;justify-content:center}
  .names .pill{background:#E6D7FA;color:var(--dark);font-weight:700;font-size:clamp(2rem,4.6vw,4.4rem);
    padding:.15em .7em;border-radius:999px;box-shadow:0 10px 28px rgba(0,0,0,.35)}
  .seg{display:inline-flex;background:rgba(255,255,255,.6);border:1px solid var(--line);border-radius:999px;padding:3px}
  .seg button{border:none;background:transparent;color:var(--accent);font-weight:700;font-size:.95rem;padding:6px 14px;border-radius:999px}
  .seg button.on{background:var(--accent);color:#fff}
  .tosettings{color:var(--muted);text-decoration:none;font-size:.95rem;align-self:center;margin-top:4px}
  .status{color:var(--muted);margin:-6px 0 12px}
  .status:empty{display:none}
  .status .hint{font-size:1rem}
  .tosettings span{color:var(--accent);font-weight:700}
  .settings{max-width:1100px;width:calc(100% - 48px);margin:10px auto 40px;background:rgba(255,255,255,.7);border:1px solid var(--line);
    border-radius:22px;padding:22px 26px}
  .settings h2{margin:0 0 14px;color:var(--accent);font-size:1.3rem}
  .setgrid{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:18px 28px}
  .setitem{display:flex;flex-direction:column;gap:8px}
  .setitem .seg{align-self:flex-start}
  html{background:#EFE6FB}
  body{background-attachment:fixed}
  .setlbl{color:var(--muted);font-weight:700;font-size:.9rem;text-transform:uppercase;letter-spacing:.5px}
  .setrow{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
  .chk{display:flex;align-items:center;gap:6px;color:var(--ink);cursor:pointer}

</style>
</head>
<body>
<header>
  <h1>Reflexionsrad – Was habe ich heute gelernt?</h1>
</header>

<main>
  <div class="wheelwrap">
    <canvas id="wheel" aria-label="Glücksrad mit Satzanfängen"></canvas>
    <div class="pointer"></div>
    <div class="hub">Dreh<br>mich!</div>
  </div>

  <a class="tosettings" href="#einstellungen">Einstellungen ↓ <span id="activeCls"></span></a>
</main>

<section class="settings" id="einstellungen">
  <h2>Einstellungen</h2>
  <div class="status" id="card"></div>
  <div class="setgrid">
    <div class="setitem"><span class="setlbl">Klasse für die Namen</span>
      <div class="seg" id="classSeg"><button data-k="1">Klasse 1</button><button data-k="2">Klasse 2</button></div></div>
    <div class="setitem"><span class="setlbl">Listen</span>
      <div class="setrow"><button class="ghost" id="namesBtn">Namensliste</button><button class="ghost" id="editBtn">Satzanfänge bearbeiten</button></div></div>
    <div class="setitem"><span class="setlbl">Rad</span>
      <div class="setrow"><label class="chk"><input type="checkbox" id="removeChk"> Gezogene Sätze entfernen</label><button class="ghost" id="resetBtn">Alle zurück aufs Rad</button></div></div>
    <div class="setitem"><span class="setlbl">Anzeige</span>
      <div class="setrow"><button class="ghost" id="soundBtn">Ton: an</button><button class="ghost" id="fsBtn">Vollbild</button></div></div>
  </div>
</section>

<div class="result" id="result" role="dialog" aria-live="polite">
  <span class="badge" id="rBadge"></span>
  <p class="big" id="rText"></p>
  <div class="rtools">
    <button class="ghost" id="rTimerBtn" style="font-size:1.2rem;padding:10px 18px;min-width:150px">Denkzeit 20 s</button>
  </div>
  <div class="names" id="rNames">
    <div class="pills" id="rPills"></div>
    <button class="ghost" id="rOtherBtn" style="margin-top:.6vh">Andere Namen</button>
  </div>
</div>

<div class="overlay" id="namesOverlay">
  <div class="dialog">
    <div style="display:flex;align-items:center;justify-content:space-between;gap:10px;flex-wrap:wrap"><h2>Namensliste</h2><div class="seg" id="dlgSeg"><button data-k="1">Klasse 1</button><button data-k="2">Klasse 2</button></div></div>
    <p>Ein Name pro Zeile, oben wählst du die Klasse. Die Listen werden nur auf diesem Gerät im Browser gespeichert und nirgendwohin übertragen. Nach Ablauf der Denkzeit werden zwei Namen gezogen, und zwar so, dass erst alle einmal drankommen, bevor sich jemand wiederholt.</p>
    <textarea id="namesEditor" spellcheck="false" placeholder="Anna&#10;Ben&#10;Can&#10;…"></textarea>
    <div class="row">
      <button class="ghost" id="namesClear">Liste leeren</button>
      <button class="ghost" id="namesCancel">Abbrechen</button>
      <button class="primary" id="namesSave">Speichern</button>
    </div>
  </div>
</div>

<div class="overlay" id="overlay">
  <div class="dialog">
    <h2>Satzanfänge bearbeiten</h2>
    <p>Ein Satzanfang pro Zeile. Optional mit Kategorie davor: <em>Kategorie | Satzanfang</em>. Gleiche Kategorien bekommen die gleiche Farbe.</p>
    <textarea id="editor" spellcheck="true"></textarea>
    <div class="row">
      <button class="ghost" id="defaultBtn">Standardliste</button>
      <button class="ghost" id="cancelBtn">Abbrechen</button>
      <button class="primary" id="saveBtn">Übernehmen</button>
    </div>
  </div>
</div>

<script>
(function(){
  const DEFAULT = [
    ["Gelernt","Ich habe heute gelernt, wie … Das macht man, indem man …"],
    ["Gelernt","Ich habe heute gelernt, wie … Der wichtigste Schritt dabei ist …"],
    ["Gelernt","Die wichtigste Erkenntnis heute für mich war …"],
    ["Strategie","Geholfen hat mir besonders …, weil …"],
    ["Strategie","Das neue Thema / Die neue Regel merke ich mir so: …"],
    ["Selbsteinschätzung","Sicher bin ich mir schon bei …"],
    ["Selbsteinschätzung","Noch unsicher bin ich bei …, weil …"],
    ["Selbsteinschätzung","Eine Aufgabe, die ich seit heute lösen könnte, wäre … Die Lösung ist …"],
    ["Fehler","Ein typischer Fehler bei diesem Thema ist …"],
    ["Fehler","Aufpassen muss man vor allem bei …"],
    ["Nächste Schritte","Eine Frage, die ich noch habe, ist …"]
  ].map(([c,t])=>({c,t}));

  const PALETTE = ["#7B3FC4","#E0457B","#E8771E","#25A57E","#2F86D6","#B04FCF","#D4493A","#4E5BC9"];
  const THINK = 20;
  const NAMES_STORE = "reflexionsrad-namen-v1";
  const STORE = "reflexionsrad-items-v3";

  let all = load();
  let items = all.slice();
  let colorOf = {};
  let rot = 0, spinning = false, pendingRemove = -1, sound = true, audio = null;

  const canvas = document.getElementById("wheel");
  const ctx = canvas.getContext("2d");
  const card = document.getElementById("card");
  const spinBtn = {disabled:false};
  const removeChk = document.getElementById("removeChk");

  function load(){
    try{
      const s = localStorage.getItem(STORE);
      if(s){ const a = JSON.parse(s); if(Array.isArray(a) && a.length) return a; }
    }catch(e){}
    return DEFAULT.slice();
  }
  function save(){ try{ localStorage.setItem(STORE, JSON.stringify(all)); }catch(e){} }

  function assignColors(){
    colorOf = {}; let i = 0;
    all.forEach(it=>{ if(!(it.c in colorOf)){ colorOf[it.c] = PALETTE[i++ % PALETTE.length]; } });
  }

  function resize(){
    const r = canvas.getBoundingClientRect();
    const dpr = window.devicePixelRatio || 1;
    canvas.width = Math.round(r.width*dpr); canvas.height = Math.round(r.height*dpr);
    ctx.setTransform(dpr,0,0,dpr,0,0);
    draw();
  }

  function draw(){
    const w = canvas.getBoundingClientRect().width;
    const cx = w/2, cy = w/2, R = w/2 - 6;
    ctx.clearRect(0,0,w,w);
    const n = items.length;
    if(!n){
      ctx.beginPath(); ctx.arc(cx,cy,R,0,Math.PI*2); ctx.fillStyle="#e3dfd6"; ctx.fill();
      return;
    }
    const seg = Math.PI*2/n;
    for(let i=0;i<n;i++){
      const a0 = rot + i*seg - Math.PI/2, a1 = a0 + seg;
      ctx.beginPath(); ctx.moveTo(cx,cy); ctx.arc(cx,cy,R,a0,a1); ctx.closePath();
      ctx.fillStyle = colorOf[items[i].c] || PALETTE[i%PALETTE.length];
      ctx.globalAlpha = (i%2) ? 0.85 : 1;
      ctx.fill(); ctx.globalAlpha = 1;
      ctx.strokeStyle = "#ffffff"; ctx.lineWidth = 3; ctx.stroke();

      // label
      ctx.save();
      ctx.translate(cx,cy); ctx.rotate(a0 + seg/2);
      const fs = Math.max(12, Math.min(26, w/(n>16? 34 : 24)));
      ctx.font = "600 "+fs+"px Calibri, Carlito, 'Segoe UI', sans-serif";
      ctx.fillStyle = "#fff"; ctx.textAlign = "right"; ctx.textBaseline = "middle";
      const maxW = R*0.74;
      let label = items[i].c;
      if(ctx.measureText(label).width > maxW){
        while(label.length>3 && ctx.measureText(label+"…").width > maxW) label = label.slice(0,-1);
        label = label.trim()+"…";
      }
      let mid = ((a0 + seg/2) % (Math.PI*2) + Math.PI*2) % (Math.PI*2);
      if(mid > Math.PI/2 && mid < Math.PI*1.5){ ctx.rotate(Math.PI); ctx.textAlign = "left"; ctx.fillText(label, -(R-14), 0); }
      else ctx.fillText(label, R-14, 0);
      ctx.restore();
    }
    ctx.beginPath(); ctx.arc(cx,cy,R,0,Math.PI*2); ctx.lineWidth = 8; ctx.strokeStyle = "#5B2A91"; ctx.stroke();
    for(let i=0;i<n;i++){ const a = rot + i*seg - Math.PI/2; ctx.beginPath(); ctx.arc(cx+Math.cos(a)*(R-1), cy+Math.sin(a)*(R-1), Math.max(3,w/130), 0, Math.PI*2); ctx.fillStyle="#fff"; ctx.fill(); }
  }

  function indexAtPointer(){
    const n = items.length, seg = Math.PI*2/n;
    let a = (-rot) % (Math.PI*2); if(a<0) a += Math.PI*2;
    return Math.floor(a/seg) % n;
  }

  function tick(){
    if(!sound) return;
    try{
      if(!audio) audio = new (window.AudioContext||window.webkitAudioContext)();
      const o = audio.createOscillator(), g = audio.createGain();
      o.type="triangle"; o.frequency.value = 900;
      g.gain.setValueAtTime(0.08, audio.currentTime);
      g.gain.exponentialRampToValueAtTime(0.0001, audio.currentTime+0.05);
      o.connect(g).connect(audio.destination); o.start(); o.stop(audio.currentTime+0.06);
    }catch(e){}
  }
  function ding(){
    if(!sound) return;
    try{
      if(!audio) audio = new (window.AudioContext||window.webkitAudioContext)();
      [660,880].forEach((f,k)=>{
        const o = audio.createOscillator(), g = audio.createGain(), t = audio.currentTime + k*0.12;
        o.type="sine"; o.frequency.value=f;
        g.gain.setValueAtTime(0.0001,t); g.gain.exponentialRampToValueAtTime(0.15,t+0.02);
        g.gain.exponentialRampToValueAtTime(0.0001,t+0.4);
        o.connect(g).connect(audio.destination); o.start(t); o.stop(t+0.45);
      });
    }catch(e){}
  }

  function updateCount(){}

  function spin(){
    if(spinning) return;
    if(pendingRemove >= 0 && removeChk.checked){
      items.splice(pendingRemove,1); pendingRemove = -1; draw(); updateCount();
    }
    pendingRemove = -1;
    if(!items.length){
      showEmpty(); return;
    }
    stopTimer(); hideNames();
    spinning = true; spinBtn.disabled = true;
    const n = items.length, seg = Math.PI*2/n, TAU = Math.PI*2;
    const k = Math.floor(Math.random()*n);
    const off = seg*(0.2 + Math.random()*0.6);
    let desired = (TAU - (k*seg + off)) % TAU; if(desired<0) desired += TAU;
    let base = rot - (((rot%TAU)+TAU)%TAU);
    const spins = 5 + Math.floor(Math.random()*3);
    const start = rot, end = base + spins*TAU + desired;
    const dur = 4200 + Math.random()*1200, t0 = performance.now();
    let lastSeg = Math.floor(start/seg);
    function frame(now){
      const p = Math.min(1,(now-t0)/dur);
      const e = 1 - Math.pow(1-p, 4);
      rot = start + (end-start)*e;
      const s = Math.floor(rot/seg);
      if(s !== lastSeg){ tick(); lastSeg = s; }
      draw();
      if(p<1) requestAnimationFrame(frame);
      else { spinning = false; spinBtn.disabled = false; reveal(indexAtPointer()); }
    }
    requestAnimationFrame(frame);
  }

  function esc(s){ return s.replace(/[&<>"]/g, ch=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[ch])); }

  function reveal(i){
    const it = items[i];
    pendingRemove = i;
    ding();
    document.getElementById("rBadge").style.display = "";
    document.getElementById("rBadge").textContent = it.c;
    document.getElementById("rBadge").style.background = colorOf[it.c]||"#4F5D75";
    document.getElementById("rText").textContent = it.t;
    resultEl.classList.add("open");
    lastShown = true; hideNames(); startTimer();
  }

  function showEmpty(){
    stopTimer(); hideNames(); lastShown = false;
    document.getElementById("rBadge").style.display = "none";
    document.getElementById("rText").textContent = "Alle Satzanfänge wurden gezogen. Unter Einstellungen: „Alle zurück aufs Rad“.";
    resultEl.classList.add("open");
  }

  // Timer
  let timerId = null;
  const rTimerBtn = document.getElementById("rTimerBtn");
  function setT(txt,done){ rTimerBtn.textContent = txt || ("Denkzeit " + THINK + " s"); rTimerBtn.classList.toggle("done",!!done); }
  function stopTimer(){ if(timerId){ clearInterval(timerId); timerId=null; } setT(""); }
  function startTimer(e){
    if(e) e.stopPropagation();
    stopTimer(); let left = THINK; setT(left+" s");
    timerId = setInterval(()=>{
      left--; setT(left+" s");
      if(left<=0){ clearInterval(timerId); timerId=null; setT("Zeit!",true); showNames(); ding(); }
    },1000);
  }
  document.getElementById("rTimerBtn").addEventListener("click", e=>{ hideNames(); startTimer(e); });
  const resultEl = document.getElementById("result");
  function closeResult(){ resultEl.classList.remove("open"); }
  resultEl.addEventListener("click", closeResult);

  // Buttons
  canvas.addEventListener("click", spin);
  document.getElementById("resetBtn").addEventListener("click", ()=>{
    if(spinning) return;
    items = all.slice(); pendingRemove = -1; draw(); updateCount();
    card.innerHTML = '<p class="hint">Alle Satzanfänge sind wieder auf dem Rad.</p>';
  });
  removeChk.addEventListener("change", ()=>{ if(!removeChk.checked) pendingRemove = -1; });
  document.getElementById("soundBtn").addEventListener("click", e=>{
    sound = !sound; e.target.textContent = "Ton: " + (sound ? "an" : "aus");
  });
  document.getElementById("fsBtn").addEventListener("click", ()=>{
    try{
      if(document.fullscreenElement) document.exitFullscreen();
      else { const p = document.documentElement.requestFullscreen(); if(p && p.catch) p.catch(()=>{}); }
    }catch(e){}
  });
  document.addEventListener("keydown", e=>{
    if(namesOverlay.classList.contains("open")) { if(e.key==="Escape") closeNames(); return; }
    if(overlay.classList.contains("open")) { if(e.key==="Escape") closeEditor(); return; }
    if(resultEl.classList.contains("open")){
      if(e.key==="Escape"){ closeResult(); }
      else if(e.code==="Space" || e.key==="Enter"){ e.preventDefault(); closeResult(); spin(); }
      return;
    }
    if(e.code==="Space" || e.key==="Enter"){ e.preventDefault(); spin(); }
  });

  // Namen (zwei Klassen)
  let lastShown = false;
  const NAMES_STORE2 = "reflexionsrad-klassen-v1";
  let cls = loadClasses(), pools = {"1":[],"2":[]};
  function loadClasses(){
    try{
      const s = localStorage.getItem(NAMES_STORE2);
      if(s){ const o = JSON.parse(s); if(o && o.lists) return {active: o.active==="2"?"2":"1", lists:{"1":o.lists["1"]||[], "2":o.lists["2"]||[]}}; }
      const old = localStorage.getItem(NAMES_STORE);
      if(old){ const a = JSON.parse(old); if(Array.isArray(a)) return {active:"1", lists:{"1":a, "2":[]}}; }
    }catch(e){}
    return {active:"1", lists:{"1":[], "2":[]}};
  }
  function saveClasses(){ try{ localStorage.setItem(NAMES_STORE2, JSON.stringify(cls)); return true; }catch(e){ return false; } }
  function names(){ return cls.lists[cls.active]; }
  function shuffle(a){ for(let i=a.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]]; } return a; }
  function pickTwo(){
    const list = names(), out = [];
    let pool = pools[cls.active];
    while(out.length < Math.min(2, list.length)){
      if(!pool.length) pool = pools[cls.active] = shuffle(list.filter(n=>!out.includes(n)));
      if(!pool.length) break;
      out.push(pool.pop());
    }
    return out;
  }
  function markSeg(el, k){ el.querySelectorAll("button").forEach(b=>b.classList.toggle("on", b.dataset.k===k)); }
  const classSeg = document.getElementById("classSeg");
  markSeg(classSeg, cls.active);
  function showActive(){ const el = document.getElementById("activeCls"); if(el) el.textContent = "· Namen aus Klasse " + cls.active; }
  showActive();
  classSeg.addEventListener("click", e=>{
    const k = e.target.dataset && e.target.dataset.k; if(!k) return;
    cls.active = k; saveClasses(); markSeg(classSeg, k); showActive();
    const n = names().length;
    card.innerHTML = '<p class="hint">Klasse ' + k + ' ausgewählt' + (n ? ' (' + n + ' Namen).' : ' – noch keine Namensliste eingetragen.') + '</p>';
  });
  const namesEl = document.getElementById("rNames"), pillsEl = document.getElementById("rPills");
  function hideNames(){ namesEl.classList.remove("show"); pillsEl.innerHTML = ""; }
  function showNames(){
    const two = pickTwo();
    if(!two.length) return;
    pillsEl.innerHTML = two.map(n=>'<span class="pill">'+esc(n)+'</span>').join("");
    namesEl.classList.remove("show"); void namesEl.offsetWidth; namesEl.classList.add("show");
    if(!resultEl.classList.contains("open") && lastShown) resultEl.classList.add("open");
  }
  document.getElementById("rOtherBtn").addEventListener("click", e=>{ e.stopPropagation(); showNames(); });
  const namesOverlay = document.getElementById("namesOverlay"), namesEditor = document.getElementById("namesEditor");
  const dlgSeg = document.getElementById("dlgSeg");
  let draft = null, dlgK = "1";
  function parse(txt){ const seen = new Set(); return txt.split("\n").map(l=>l.trim()).filter(n=>n && !seen.has(n) && seen.add(n)); }
  function closeNames(){ namesOverlay.classList.remove("open"); }
  document.getElementById("namesBtn").addEventListener("click", ()=>{
    draft = {"1":cls.lists["1"].join("\n"), "2":cls.lists["2"].join("\n")};
    dlgK = cls.active; markSeg(dlgSeg, dlgK); namesEditor.value = draft[dlgK];
    namesOverlay.classList.add("open"); namesEditor.focus();
  });
  dlgSeg.addEventListener("click", e=>{
    const k = e.target.dataset && e.target.dataset.k; if(!k || k===dlgK) return;
    draft[dlgK] = namesEditor.value; dlgK = k; markSeg(dlgSeg, k); namesEditor.value = draft[k]; namesEditor.focus();
  });
  document.getElementById("namesCancel").addEventListener("click", closeNames);
  document.getElementById("namesClear").addEventListener("click", ()=>{ namesEditor.value = ""; });
  document.getElementById("namesSave").addEventListener("click", ()=>{
    draft[dlgK] = namesEditor.value;
    cls.lists = {"1":parse(draft["1"]), "2":parse(draft["2"])};
    cls.active = dlgK; pools = {"1":[],"2":[]};
    markSeg(classSeg, cls.active); showActive();
    const ok = saveClasses();
    closeNames();
    const n1 = cls.lists["1"].length, n2 = cls.lists["2"].length;
    card.innerHTML = '<p class="hint">Klasse 1: ' + n1 + ' Namen, Klasse 2: ' + n2 + ' Namen. Aktiv: Klasse ' + cls.active + '. ' +
      (ok ? 'Auf diesem Gerät gespeichert.' : 'Dieser Browser erlaubt kein Speichern – die Listen gelten nur bis zum Schließen.') + '</p>';
  });
  namesOverlay.addEventListener("click", e=>{ if(e.target===namesOverlay) closeNames(); });

  // Editor
  const overlay = document.getElementById("overlay");
  const editor = document.getElementById("editor");
  function toText(list){ return list.map(it=>it.c+" | "+it.t).join("\n"); }
  function openEditor(){ editor.value = toText(all); overlay.classList.add("open"); editor.focus(); }
  function closeEditor(){ overlay.classList.remove("open"); }
  document.getElementById("editBtn").addEventListener("click", openEditor);
  document.getElementById("cancelBtn").addEventListener("click", closeEditor);
  document.getElementById("defaultBtn").addEventListener("click", ()=>{ editor.value = toText(DEFAULT); });
  document.getElementById("saveBtn").addEventListener("click", ()=>{
    const list = editor.value.split("\n").map(l=>l.trim()).filter(Boolean).map(l=>{
      const p = l.indexOf("|");
      return p>=0 ? {c:l.slice(0,p).trim()||"Reflexion", t:l.slice(p+1).trim()} : {c:"Reflexion", t:l};
    }).filter(it=>it.t);
    if(list.length < 2){ editor.style.borderColor = "#B5473F"; return; }
    editor.style.borderColor = "";
    all = list; items = all.slice(); pendingRemove = -1;
    assignColors(); save(); draw(); updateCount(); closeEditor();
    card.innerHTML = '<p class="hint">Neue Liste übernommen – los geht’s!</p>';
  });
  overlay.addEventListener("click", e=>{ if(e.target===overlay) closeEditor(); });

  assignColors();
  updateCount();
  window.addEventListener("resize", resize);
  resize();
})();
</script>
</body>
</html>
