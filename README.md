
<html lang="id">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>User - Chat Host </title>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
*{font-family:'Space Grotesk',sans-serif; box-sizing:border-box}
body{margin:0; min-height:100vh; background:radial-gradient(600px at 100% 0%, rgba(204,255,0,0.15) 0%, transparent 50%),radial-gradient(500px at 0% 100%, rgba(0,255,170,0.12) 0%, transparent 50%),#0a0a0a; display:flex; justify-content:center; align-items:center; padding:16px; color:#fff}
.card{width:100%; max-width:480px; height:88vh; max-height:760px; background:#141414; border:1px solid #262626; border-radius:22px; overflow:hidden; box-shadow:0 20px 60px rgba(0,0,0,0.5); display:flex; flex-direction:column}
.header{padding:16px; background:#0f0f0f; display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid #262626}
.header b{font-size:17px; letter-spacing:-0.5px}
.header b span{color:#ccff00}
#conn{font-size:10px; color:#ccff00}
.iconBtn{width:36px; height:36px; border-radius:10px; border:1px solid #333; background:#1f1f1f; color:#fff; cursor:pointer; display:grid; place-items:center}
#chat{flex:1; overflow-y:auto; padding:16px; display:flex; flex-direction:column; gap:12px}
.bubble{max-width:82%; padding:10px 14px; border-radius:18px; font-size:13.5px; word-wrap:break-word; animation:pop 0.25s ease; line-height:1.5}
@keyframes pop{from{transform:scale(0.8); opacity:0} to{transform:scale(1); opacity:1}}
.me{align-self:flex-end; background:#ccff00; color:#000; border-bottom-right-radius:6px; font-weight:500}
.you{align-self:flex-start; background:#1f1f1f; border:1px solid #2a2a2a; border-bottom-left-radius:6px}
.replyTag{font-size:10px; opacity:0.7; border-left:2px solid currentColor; padding-left:6px; margin-bottom:4px}
.meta{font-size:9px; opacity:0.6; margin-top:4px; text-align:right}
.system{align-self:center; background:rgba(204,255,0,0.12); border:1px solid rgba(204,255,0,0.35); color:#ccff00; font-size:11px; padding:6px 14px; border-radius:20px}
.footer{padding:10px; background:#0f0f0f; display:flex; flex-direction:column; gap:8px; border-top:1px solid #262626}
.row{display:flex; gap:8px; align-items:center}
.footer input{flex:1; background:#1f1f1f; border:1px solid #333; border-radius:12px; padding:12px; color:#fff; outline:none}
.footer input::placeholder{color:#666}
.btn{padding:12px 18px; border:none; border-radius:12px; background:#ccff00; color:#000; font-weight:700; cursor:pointer}
.emojiBox{position:absolute; bottom:90px; right:20px; background:#1f1f1f; border:1px solid #333; border-radius:14px; padding:8px; display:none; grid-template-columns:repeat(6,1fr); gap:6px; z-index:10}
.emojiBox.show{display:grid}
.toast{position:fixed; bottom:20px; left:50%; transform:translateX(-50%); background:#ccff00; color:#000; padding:10px 18px; border-radius:20px; font-size:12px; font-weight:700; display:none; z-index:99}
.toast.show{display:block}
#overlay{position:fixed; inset:0; background:rgba(0,0,0,0.6); display:none; align-items:center; justify-content:center; z-index:99}
#overlay.show{display:flex}
.modal{background:#1a1a1a; border:1px solid #333; border-radius:18px; padding:20px; width:90%; max-width:320px; text-align:center; color:#fff}
.avatar{width:42px; height:42px; border-radius:12px; background:#ccff00; color:#000; display:grid; place-items:center; font-weight:700; flex-shrink:0}

/* MODAL NAMA - YANG UDAH DIBENERIN */
#namaModal{position:fixed; inset:0; background:rgba(0,0,0,0.85); display:none; align-items:center; justify-content:center; z-index:100; padding:20px}
.namaCard{background:#141414; border:1px solid #333; border-radius:22px; padding:26px; width:100%; max-width:340px; text-align:center}
.namaCard h3{margin:0; font-size:20px}
.namaCard p{font-size:13px; color:#a3a3a3; margin:8px 0 16px; line-height:1.5}
#inputNama{width:100%; background:#1f1f1f; border:1px solid #333; border-radius:14px; padding:14px; color:#fff; outline:none; font-size:15px; text-align:center}
#inputNama:focus{border-color:#ccff00}
.btnNama{width:100%; margin-top:12px; padding:15px; border:none; border-radius:14px; background:#ccff00; color:#000; font-weight:700; font-size:15px; cursor:pointer}
#namaError{display:none; color:#ff5c5c; font-size:12px; margin-top:10px}
</style>
</head>
<body>
<div class="card">
  <div class="header">
    <div style="display:flex; gap:10px; align-items:center">
      <div class="avatar">👤</div>
      <div><b>Chat Dengan <span> Host </span></b><div id="conn" style="font-size:10px">● Menghubungkan...</div></div>
    </div>
    <div style="display:flex; gap:8px">
      <button class="iconBtn" onclick="gantiNama()">✏️</button>
      <button class="iconBtn" onclick="clearChat()">🗑️</button>
    </div>
  </div>
  <div id="chat"></div>
  <div class="footer">
    <div id="replyPreview" style="display:none; background:rgba(204,255,0,0.12); border-left:3px solid #ccff00; padding:6px 10px; border-radius:8px; font-size:11px"><span id="replyText"></span><span style="float:right; cursor:pointer" onclick="cancelReply()">✕</span></div>
    <div class="row">
      <button class="iconBtn" onclick="document.getElementById('fileInput').click()">📎</button>
      <input id="pesan" placeholder="Tumpahin aja di sini...">
      <button class="iconBtn" onclick="toggleEmoji()">😊</button>
      <button class="btn" onclick="kirim()">➤</button>
    </div>
    <div id="emojiBox" class="emojiBox"></div>
  </div>
</div>

<!-- MODAL HAPUS -->
<div id="overlay" onclick="this.classList.remove('show')"><div class="modal" onclick="event.stopPropagation()"><h3>Hapus Pesan?</h3><p style="font-size:11px; opacity:0.7">Akan dihapus untuk kamu</p><button class="btn" style="background:#ff3b3b; color:#fff; margin-top:10px" onclick="confirmDelete()">Hapus</button></div></div>
<div id="toast" class="toast"></div>
<input type="file" id="fileInput" hidden accept="image/*" onchange="kirimGambar(this)">

<!-- MODAL NAMA - BARU, UDAH BENER -->
<div id="namaModal">
  <div class="namaCard">
    <div style="font-size:40px; margin-bottom:10px">🥷</div>
    <h3 id="judulNama">Siapa nama lu?</h3>
    <p id="descNama">Bebas, bisa nama samaran.<br>Ga ada yang tau nama asli lu.</p>
    <input id="inputNama" placeholder="Contoh: Si Paling Galau" maxlength="20">
    <button id="btnNama" class="btnNama" onclick="simpanNama()">GAS, MASUK CHAT →</button>
    <div id="namaError">Isi nama dulu cuy, minimal 2 huruf</div>
  </div>
</div>

<script>
const TOPIC="vortexsione_chat_2026_host_user";
let nama = localStorage.getItem('nama_vortex') || '';
let isGantiNama = false;
let semuaPesan = JSON.parse(localStorage.getItem('pesan_'+TOPIC) || '[]');
let deleteKey=null, es=null, replyTo=null;

function norm(s){ return (s||'').toString().trim().toLowerCase() }
function showToast(m){ let t=document.getElementById('toast'); t.innerText=m; t.classList.add('show'); setTimeout(()=>t.classList.remove('show'),2000) }

// MODAL NAMA - LOGIC BARU
if(!nama){
  document.getElementById('namaModal').style.display='flex';
  setTimeout(()=>document.getElementById('inputNama').focus(), 200);
}
function simpanNama(){
  let val = document.getElementById('inputNama').value.trim();
  if(val.length < 2){
    document.getElementById('namaError').style.display='block';
    return;
  }
  document.getElementById('namaError').style.display='none';
  if(isGantiNama){
    let oldNama = nama;
    nama = val;
    localStorage.setItem('nama_vortex', nama);
    document.getElementById('namaModal').style.display='none';
    publish({nama:'system', pesan: oldNama+' ganti nama jadi '+nama, from:'system', oldNama:oldNama, newNama:nama, deletedBy:[]});
    showToast('Nama diganti jadi '+nama);
    tampilkan();
  } else {
    nama = val;
    localStorage.setItem('nama_vortex', nama);
    document.getElementById('namaModal').style.display='none';
    tampilkan();
  }
  isGantiNama = false;
}
function gantiNama(){
  isGantiNama = true;
  document.getElementById('judulNama').innerText = 'Ganti Nama?';
  document.getElementById('descNama').innerHTML = 'Ganti jadi nama baru.<br>Host bakal tau lu ganti nama.';
  document.getElementById('btnNama').innerText = 'SIMPAN NAMA BARU';
  document.getElementById('inputNama').value = nama;
  document.getElementById('namaError').style.display='none';
  document.getElementById('namaModal').style.display='flex';
  document.getElementById('inputNama').focus();
}
document.getElementById('inputNama').addEventListener('keydown', e=>{ if(e.key==='Enter') simpanNama(); });

function isRelevant(msg){
  if(msg.from==='system') return norm(msg.oldNama)===norm(nama) || norm(msg.newNama)===norm(nama);
  if(msg.from==='pengirim' && norm(msg.nama)===norm(nama)) return true;
  if(msg.from==='penerima' && (norm(msg.to)===norm(nama) || norm(msg.to)==='all')) return true;
  return false;
}
function tampilkan(){
  let box=document.getElementById('chat'); box.innerHTML='';
  let filtered = semuaPesan.filter(m=>{
    if(m.deletedBy && m.deletedBy.map(norm).includes(norm(nama))) return false;
    if(m.deletedBy && m.deletedBy.includes(nama)) return false;
    return isRelevant(m);
  }).sort((a,b)=>a.timestamp-b.timestamp);
  if(!filtered.length){ box.innerHTML=`<div style="text-align:center; opacity:0.5; margin-top:60px; font-size:13px; line-height:1.6">Halo ${nama || 'cuy'} 🫠<br>Udah, tumpahin aja.<br>Ga ada yang nge-judge di sini.</div>`; return; }
  filtered.forEach(item=>{
    if(item.from==='system'){ box.innerHTML+=`<div style="width:100%; display:flex; justify-content:center"><div class="system">🔄 ${item.pesan}</div></div>`; }
    else{
      let isMe = item.from==='pengirim';
      let replyHtml = item.reply ? `<div class="replyTag">↪ ${item.reply}</div>` : '';
      let imgHtml = item.image ? `<img src="${item.image}" style="max-width:100%; border-radius:12px; margin-top:6px">` : '';
      box.innerHTML+=`<div style="width:100%; display:flex; ${isMe?'justify-content:flex-end':'justify-content:flex-start'}"><div class="bubble ${isMe?'me':'you'}" onclick="setReply('${item.id}','${(item.pesan||'').replace(/'/g,'')}')" oncontextmenu="openDel('${item.id}'); return false;">${replyHtml}${item.pesan}${imgHtml}<div class="meta">${item.jam} ${isMe?'✓✓':''}</div></div></div>`;
    }
  });
  box.scrollTop = box.scrollHeight;
}
function publish(msg){
  msg.id = Date.now()+'_'+Math.random().toString(36).substr(2,6); msg.timestamp = Date.now();
  fetch('https://ntfy.sh/'+TOPIC,{method:'POST', body:JSON.stringify(msg)});
  semuaPesan.push(msg); localStorage.setItem('pesan_'+TOPIC, JSON.stringify(semuaPesan.slice(-500))); tampilkan();
}
function kirim(){
  let p=document.getElementById('pesan').value.trim(); if(!p) return;
  publish({nama:nama, pesan:p, jam:new Date().toLocaleTimeString('id-ID',{hour:'2-digit',minute:'2-digit'}), from:'pengirim', reply:replyTo, deletedBy:[]});
  document.getElementById('pesan').value=''; cancelReply();
}
function kirimGambar(input){
  let file=input.files[0]; if(!file) return;
  let r=new FileReader(); r.onload=()=>{ publish({nama:nama, pesan:'📷 Gambar', image:r.result, jam:new Date().toLocaleTimeString('id-ID',{hour:'2-digit',minute:'2-digit'}), from:'pengirim', deletedBy:[]}) }; r.readAsDataURL(file);
}
function clearChat(){
  if(!confirm('Hapus chat di layar kamu?')) return;
  semuaPesan.forEach(m=>{ if(isRelevant(m)){ if(!m.deletedBy) m.deletedBy=[]; m.deletedBy.push(nama); }});
  localStorage.setItem('pesan_'+TOPIC, JSON.stringify(semuaPesan)); tampilkan(); showToast('Chat dibersihkan');
}
function buildEmoji(){ let e=["😀","😂","❤️","🔥","👍","😭","😍","🙏","✨","💀","😎","🥺"]; let box=document.getElementById('emojiBox'); box.innerHTML=e.map(x=>`<span style="cursor:pointer; font-size:18px" onclick="addEmoji('${x}')">${x}</span>`).join(''); }
function toggleEmoji(){ document.getElementById('emojiBox').classList.toggle('show') }
function addEmoji(e){ document.getElementById('pesan').value+=e; toggleEmoji(); document.getElementById('pesan').focus() }
function setReply(id,text){ replyTo=text.slice(0,40); document.getElementById('replyText').innerText='Balas: '+replyTo; document.getElementById('replyPreview').style.display='block'; }
function cancelReply(){ replyTo=null; document.getElementById('replyPreview').style.display='none' }
function openDel(id){ deleteKey=id; document.getElementById('overlay').classList.add('show') }
function confirmDelete(){
  let idx=semuaPesan.findIndex(m=>m.id===deleteKey);
  if(idx>-1){ if(!semuaPesan[idx].deletedBy) semuaPesan[idx].deletedBy=[]; semuaPesan[idx].deletedBy.push(nama);
  publish({nama:'system', from:'system', type:'delete', targetId:deleteKey, deleter:nama, pesan:nama+' menghapus', jam:new Date().toLocaleTimeString('id-ID'), deletedBy:[], timestamp:Date.now(), id:Date.now()+'_del'}) }
  localStorage.setItem('pesan_'+TOPIC, JSON.stringify(semuaPesan.slice(-500)));
  document.getElementById('overlay').classList.remove('show'); showToast('Dihapus'); tampilkan();
}
function connectNtfy(){
  if(es) es.close();
  es=new EventSource('https://ntfy.sh/'+TOPIC+'/sse');
  es.onopen=()=>document.getElementById('conn').innerText='● Terhubung - host online';
  es.onmessage=(e)=>{
    try{
      let outer=JSON.parse(e.data); if(!outer.message) return;
      let msg=JSON.parse(outer.message);
      if(semuaPesan.find(m=>m.id===msg.id)) return;
      if(msg.type==='delete'){ let t=semuaPesan.find(x=>x.id===msg.targetId); if(t){ if(!t.deletedBy) t.deletedBy=[]; t.deletedBy.push(msg.deleter); } }
      else{ semuaPesan.push(msg); }
      localStorage.setItem('pesan_'+TOPIC, JSON.stringify(semuaPesan.slice(-500)));
      tampilkan();
    }catch{}
  };
  es.onerror=()=>{ document.getElementById('conn').innerText='🔴 reconnect...'; setTimeout(connectNtfy,3000); }
}
document.getElementById('pesan').addEventListener('keydown',e=>{ if(e.key==='Enter') kirim() });
buildEmoji(); tampilkan(); connectNtfy();
</script>
</body>
</html>
