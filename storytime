<!DOCTYPE html>
<html lang="hi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>📖 Auto Storytelling (GitHub Pages Ready)</title>
  <style>
    :root{
      --brand:#ff7a18;
      --brand-2:#ffb74d;
      --ink:#0f172a;
      --panel:#ffffff;
      --muted:#64748b;
    }
    html,body{height:100%}
    body{
      margin:0; font-family:system-ui,-apple-system,Segoe UI,Roboto,Helvetica,Arial,sans-serif;
      background:linear-gradient(135deg,#0e1233,#2a2e52);
      color:#fff; display:flex; flex-direction:column; align-items:center;
    }
    h1{margin:16px 8px;text-align:center;text-shadow:0 2px 12px rgba(255,204,0,.55)}
    .wrap{width:min(960px,96%);}
    .controls{display:flex;flex-wrap:wrap;gap:8px;align-items:center;justify-content:center;margin:10px 0}
    .controls label{font-size:.9rem;color:#ffeab0}
    button,input[type="file"],select{
      background:var(--brand); color:#111; border:0; padding:10px 14px; border-radius:10px; cursor:pointer; font-weight:600;
      box-shadow:0 0 0 2px rgba(255,255,255,.15) inset, 0 6px 14px rgba(0,0,0,.25);
      transition:.2s transform,.2s filter,.2s background;
    }
    button:hover{transform:translateY(-1px);}
    button:disabled{opacity:.55;filter:grayscale(.6);cursor:not-allowed}
    textarea{
      width:100%;min-height:120px;border-radius:12px;border:0;padding:12px;font-size:16px;resize:vertical;
      box-shadow:inset 0 0 0 2px rgba(255,255,255,.12);
    }
    #storyScreenContainer{
      position:relative; width:100%; height:420px; border-radius:16px; overflow:hidden; margin:12px 0 8px;
      background:rgba(0,0,0,.5);
      box-shadow:0 10px 30px rgba(0,0,0,.35), inset 0 0 0 2px rgba(255,255,255,.1);
    }
    #storyScreen{height:100%; overflow-y:auto; padding:20px; font-size:20px; line-height:1.65; scroll-behavior:smooth; white-space:pre-wrap}
    #cornerImage{position:absolute;bottom:10px;right:10px;max-width:120px;max-height:120px;border-radius:12px;box-shadow:0 0 20px rgba(255,255,0,.8);display:none}
    #musicPlayer{display:block;margin:6px auto 0}
    .row{display:flex;flex-wrap:wrap;gap:8px;justify-content:center}
    .badge{font-size:.85rem;color:#111;background:#ffd166;border-radius:8px;padding:6px 10px;display:inline-flex;align-items:center;gap:6px}
    .warn{color:#ffeb3b;font-weight:700}
    .status{font-size:.9rem;color:#c8d1ff;margin:6px 0;text-align:center}
    .grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:8px}
    input[type="range"]{vertical-align:middle}
    .sr-only{position:absolute;width:1px;height:1px;padding:0;margin:-1px;overflow:hidden;clip:rect(0,0,0,0);white-space:nowrap;border:0}
  </style>
</head>
<body>
  <div class="wrap">
    <h1>📖 Auto Storytelling</h1>

    <div class="badge">⚠️ <span>यह ऐप ब्राउज़र APIs (MediaRecorder, AudioContext) का उपयोग करता है। GitHub Pages पर HTTPS में पूरी तरह काम करेगा। Export में <strong>Voice (speech)</strong> merge नहीं होगी — केवल स्क्रीन + बैकग्राउंड म्यूज़िक शामिल होगा।</span></div>

    <textarea id="storyInput" placeholder="✍️ अपनी कहानी यहाँ लिखें..."></textarea>

    <div class="controls row">
      <button id="startAuto">▶ Start</button>
      <button id="pauseAuto" disabled>⏸ Pause</button>
      <button id="resumeAuto" disabled>▶ Resume</button>
      <button id="resetAuto" disabled>⏹ Reset</button>
      <button id="exportVideo" disabled>🎬 Export Video</button>
      <button id="downloadText">💾 Download Text</button>
    </div>

    <div class="controls grid" aria-label="Settings">
      <label>Voice:
        <select id="voiceSelect"></select>
      </label>
      <label>Speed:
        <input type="range" id="speed" min="0.5" max="2" step="0.1" value="1">
      </label>
      <label>Pitch:
        <input type="range" id="pitch" min="0.5" max="2" step="0.1" value="1">
      </label>
      <label>Background Color:
        <input type="color" id="bgColor" value="#0e1233">
      </label>
      <label>Upload Background:
        <input type="file" id="bgImage" accept="image/*">
      </label>
      <label>Upload Corner Image:
        <input type="file" id="uploadImage" accept="image/*">
      </label>
      <label>Upload Background Music:
        <input type="file" id="bgMusic" accept="audio/*">
      </label>
      <label>Volume:
        <input type="range" id="musicVolume" min="0" max="1" step="0.05" value="0.7">
      </label>
    </div>

    <div id="storyScreenContainer" style="background:rgba(0,0,0,.55)">
      <div id="storyScreen" tabindex="0" aria-label="Story output will appear here"></div>
      <img id="cornerImage" alt="corner" />
    </div>

    <div class="status" id="status">Ready.</div>

    <audio id="musicPlayer" loop></audio>
  </div>

  <script>
    // --- Element refs
    const el = (id)=>document.getElementById(id);
    const storyInput=el('storyInput');
    const storyScreen=el('storyScreen');
    const cornerImage=el('cornerImage');
    const bgColor=el('bgColor');
    const bgImage=el('bgImage');
    const uploadImage=el('uploadImage');
    const bgMusic=el('bgMusic');
    const musicPlayer=el('musicPlayer');
    const voiceSelect=el('voiceSelect');
    const speed=el('speed');
    const pitch=el('pitch');
    const startBtn=el('startAuto');
    const pauseBtn=el('pauseAuto');
    const resumeBtn=el('resumeAuto');
    const resetBtn=el('resetAuto');
    const downloadTextBtn=el('downloadText');
    const exportBtn=el('exportVideo');
    const statusEl=el('status');

    // --- State
    let isPaused=false; let voices=[]; let utter=null; let rafId=null; let rec=null; let chunks=[];
    let audioCtx=null, mixDest=null, musicNode=null; // audio graph

    // --- Helpers
    function setStatus(msg){ statusEl.textContent=msg; }
    function enableRunState(running){
      startBtn.disabled=running; pauseBtn.disabled=!running; resetBtn.disabled=!running; exportBtn.disabled=false; resumeBtn.disabled=true;
    }

    // --- Voices
    function loadVoices(){
      voices = speechSynthesis.getVoices();
      voiceSelect.innerHTML='';
      voices.forEach((v,i)=>{
        const o=document.createElement('option');
        o.value=i; o.textContent=`${v.name} (${v.lang})`;
        voiceSelect.appendChild(o);
      });
    }
    speechSynthesis.onvoiceschanged=loadVoices; loadVoices();

    // --- Scroll loop using rAF for smoothness
    function scrollLoop(){
      if(!isPaused){
        storyScreen.scrollTop += 1; // adjust speed here if needed
        if(storyScreen.scrollTop < storyScreen.scrollHeight - storyScreen.clientHeight){
          rafId = requestAnimationFrame(scrollLoop);
        } else {
          cancelAnimationFrame(rafId);
        }
      }
    }

    // --- Audio setup (for export music mix)
    function ensureAudioGraph(){
      if(!audioCtx){ audioCtx=new (window.AudioContext||window.webkitAudioContext)(); }
      if(!mixDest){ mixDest=audioCtx.createMediaStreamDestination(); }
      if(musicPlayer.src && !musicNode){
        musicNode=audioCtx.createMediaElementSource(musicPlayer);
        musicNode.connect(mixDest);
        musicNode.connect(audioCtx.destination); // monitor
      }
    }

    // --- Actions
    startBtn.addEventListener('click',()=>{
      const text = storyInput.value.trim();
      if(!text){ alert('कृपया कहानी लिखें'); return; }
      storyScreen.textContent = text; storyScreen.scrollTop = 0; isPaused=false; setStatus('Playing…');

      // Voice (note: won't be in exported file)
      speechSynthesis.cancel();
      utter = new SpeechSynthesisUtterance(text);
      utter.rate = parseFloat(speed.value); utter.pitch = parseFloat(pitch.value);
      if(voices[voiceSelect.value]) utter.voice = voices[voiceSelect.value];
      utter.onend = ()=>{ setStatus('Voice finished.'); };
      speechSynthesis.speak(utter);

      // Music (requires user gesture — this click counts)
      if(musicPlayer.src){ musicPlayer.play().catch(()=>{}); }

      // Start smooth scroll
      cancelAnimationFrame(rafId); rafId=requestAnimationFrame(scrollLoop);

      // Buttons
      enableRunState(true); resumeBtn.disabled=true;
    });

    pauseBtn.addEventListener('click',()=>{
      isPaused=true; speechSynthesis.pause(); if(musicPlayer.src) musicPlayer.pause();
      resumeBtn.disabled=false; pauseBtn.disabled=true; setStatus('Paused.');
    });

    resumeBtn.addEventListener('click',()=>{
      isPaused=false; speechSynthesis.resume(); if(musicPlayer.src) musicPlayer.play().catch(()=>{});
      cancelAnimationFrame(rafId); rafId=requestAnimationFrame(scrollLoop);
      resumeBtn.disabled=true; pauseBtn.disabled=false; setStatus('Resumed.');
    });

    resetBtn.addEventListener('click',()=>{
      isPaused=false; speechSynthesis.cancel(); cancelAnimationFrame(rafId);
      storyScreen.textContent=''; storyScreen.scrollTop=0; if(musicPlayer.src){ musicPlayer.pause(); musicPlayer.currentTime=0; }
      startBtn.disabled=false; pauseBtn.disabled=true; resumeBtn.disabled=true; resetBtn.disabled=true; setStatus('Ready.');
    });

    // Enable buttons when user types
    storyInput.addEventListener('input',()=>{
      const has = storyInput.value.trim().length>0; startBtn.disabled=!has; resetBtn.disabled=!has; exportBtn.disabled=!has;
    });

    // --- File inputs
    uploadImage.addEventListener('change',e=>{
      const f=e.target.files[0]; if(!f) return; const r=new FileReader();
      r.onload = ev=>{ cornerImage.src=ev.target.result; cornerImage.style.display='block'; };
      r.readAsDataURL(f);
    });
    bgImage.addEventListener('change',e=>{
      const f=e.target.files[0]; if(!f) return; const r=new FileReader();
      r.onload = ev=>{ el('storyScreenContainer').style.background=`url(${ev.target.result}) center/cover no-repeat`; };
      r.readAsDataURL(f);
    });
    bgColor.addEventListener('input',()=>{ el('storyScreenContainer').style.background=bgColor.value; });
    bgMusic.addEventListener('change',e=>{
      const f=e.target.files[0]; if(!f) return; const r=new FileReader();
      r.onload = ev=>{ musicPlayer.src=ev.target.result; musicPlayer.volume=parseFloat(musicVolume.value); };
      r.readAsDataURL(f);
    });
    el('musicVolume').addEventListener('input',()=>{ musicPlayer.volume=parseFloat(el('musicVolume').value); });

    // --- Download text
    downloadTextBtn.addEventListener('click',()=>{
      const blob=new Blob([storyInput.value||''],{type:'text/plain'});
      const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download='MyStory.txt'; a.click();
    });

    // --- Export video (screen + background music). Voice cannot be recorded.
    exportBtn.addEventListener('click',async()=>{
      alert('⚠️ यह वीडियो केवल ब्राउज़र मेमोरी में बनेगी। बड़ी फाइल से ब्राउज़र धीमा हो सकता है।\nनोट: Text-to-Speech आवाज़ एक्सपोर्ट में शामिल नहीं होगी।');

      // Ensure audio graph so music mixes in
      ensureAudioGraph();
      // Must be resumed due to autoplay policies
      try{ await audioCtx.resume(); }catch{}

      const container = el('storyScreenContainer');
      const vStream = container.captureStream(30); // 30 FPS
      const aStream = mixDest.stream; // music only
      const mixed = new MediaStream([...vStream.getVideoTracks(), ...aStream.getAudioTracks()]);

      chunks=[];
      rec = new MediaRecorder(mixed,{mimeType:'video/webm'});
      rec.ondataavailable = e=>{ if(e.data?.size>0) chunks.push(e.data); };
      rec.onstop = ()=>{
        const blob = new Blob(chunks,{type:'video/webm'});
        const url = URL.createObjectURL(blob);
        const a=document.createElement('a'); a.href=url; a.download='MyStoryVideo.webm'; a.click();
        setStatus('Export complete.');
      };

      // Start recording for the duration of the voice (if speaking), else until scroll end or 15s fallback
      const maxMs = 15000; // fallback cap
      rec.start(); setStatus('Recording…');

      // If currently speaking, stop when voice ends
      let stopped=false;
      const stopRec = ()=>{ if(stopped) return; stopped=true; try{rec.stop();}catch{} };

      if(utter && speechSynthesis.speaking){
        const prevOnEnd = utter.onend;
        utter.onend = (ev)=>{ prevOnEnd && prevOnEnd(ev); stopRec(); };
      } else {
        // Stop when scroll reaches end
        const checkEnd = ()=>{
          const atEnd = storyScreen.scrollTop >= (storyScreen.scrollHeight - storyScreen.clientHeight - 2);
          if(atEnd) stopRec(); else requestAnimationFrame(checkEnd);
        };
        requestAnimationFrame(checkEnd);
      }
      // Hard timeout safeguard
      setTimeout(stopRec, maxMs);

      // Make sure music is playing during export (requires gesture)
      if(musicPlayer.src){ try{ await musicPlayer.play(); }catch{} }
    });

    // Keyboard support
    window.addEventListener('keydown',(e)=>{
      if(e.key===' '){ e.preventDefault(); if(!pauseBtn.disabled) pauseBtn.click(); else if(!resumeBtn.disabled) resumeBtn.click(); }
    });
  </script>
</body>
</html>
