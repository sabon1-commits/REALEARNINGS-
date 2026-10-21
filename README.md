
<!DOCTYPE html>
<html><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>REALEARNINGS Drag Merge</title><script src="https://telegram.org/js/telegram-web-app.js"></script><style>body{background:#0a0a0a;color:#fff;font-family:Arial;text-align:center;margin:0}#top{display:flex;justify-content:space-around;padding:10px;background:#111;border-bottom:2px solid #0f0}#board{width:340px;height:460px;margin:10px auto;background:#1a1a1a;position:relative;border-radius:15px;border:2px solid #333;overflow:hidden;touch-action:none}.fruit{position:absolute;width:56px;height:56px;background:#2a2a2a;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:32px;border:2px solid transparent;user-select:none;transition:transform 0.1s}.fruit.same{border-color:yellow;box-shadow:0 0 15px yellow}.fruit.drag{transform:scale(1.3);z-index:100;box-shadow:0 0 20px #0f0}.btn{width:90%;padding:12px;border-radius:10px;border:none;font-weight:bold;margin:5px}.box{background:#111;border:2px solid #0f0;border-radius:12px;margin:10px;padding:10px}input{width:92%;padding:10px;border-radius:8px;border:1px solid #333;background:#222;color:#fff;margin-top:5px}</style></head><body>
<div id="top"><div>🪙 <span id="coinEl">0</span></div><div>🏆 <span id="scoreEl">0</span></div><div>⭐ LVL <span id="lvlEl">1</span></div></div>
<div style="padding:6px;font-size:13px">👉 HOLD fruit DRAG am put on top SAME fruit to MERGE! All fruits dey one place!</div>
<div id="board"></div>
<button class="btn" style="background:#333;color:#fff" onclick="addFruit()">➕ Add Fruit</button>
<button class="btn" style="background:#0f0;color:#000" onclick="watchAd()">📺 Watch Ad +500</button>
<div class="box"><h3>💸 REALEARNINGS Withdraw</h3><input id="tonAddr" placeholder="TON Address UQ..."><input id="tonMemo" placeholder="Memo optional"><button class="btn" style="background:#333;color:#fff" onclick="saveW()">💾 Save Wallet</button><button class="btn" style="background:#0f0;color:#000" onclick="withdraw()">Withdraw Now</button><p id="status">Drag same fruits together to merge!</p></div>
<script>
let types=[{e:'🍒',v:10},{e:'🍓',v:20},{e:'🍇',v:40},{e:'🍊',v:80},{e:'🍎',v:160},{e:'🍐',v:320},{e:'🍑',v:640},{e:'🍍',v:1280},{e:'🍈',v:2560},{e:'🍉',v:5120}];
let board=document.getElementById('board'),fruits=[],coins=+localStorage.getItem('coins')||0,score=+localStorage.getItem('score')||0;
document.getElementById('coinEl').innerText=coins;document.getElementById('scoreEl').innerText=score;
document.getElementById('tonAddr').value=localStorage.getItem('tonAddr')||'';document.getElementById('tonMemo').value=localStorage.getItem('tonMemo')||'';
function createFruit(x,y,t){
 let el=document.createElement('div');el.className='fruit';el.innerText=types[t].e;
 el.style.left=x+'px';el.style.top=y+'px';let obj={el,x,y,t,drag:false};
 let sx,sy;
 function start(e){obj.drag=true;el.classList.add('drag');let p=e.touches?e.touches[0]:e;sx=p.clientX-x;sy=p.clientY-y;highlight(t);}
 function move(e){if(!obj.drag)return;let p=e.touches?e.touches[0]:e;obj.x=p.clientX-sx;obj.y=p.clientY-sy;obj.x=Math.max(0,Math.min(284,obj.x));obj.y=Math.max(0,Math.min(404,obj.y));el.style.left=obj.x+'px';el.style.top=obj.y+'px';}
 function end(e){
  obj.drag=false;el.classList.remove('drag');clearHighlight();
  fruits.forEach(o=>{if(o===obj)return;let dx=obj.x-o.x,dy=obj.y-o.y,d=Math.hypot(dx,dy);
   if(d<50&&o.t===obj.t&&o.t<9){
    // MERGE!
    let nt=o.t+1;coins+=types[nt].v;score+=types[nt].v;localStorage.setItem('coins',coins);localStorage.setItem('score',score);
    document.getElementById('coinEl').innerText=coins;document.getElementById('scoreEl').innerText=score;
    document.getElementById('lvlEl').innerText=nt+1;document.getElementById('status').innerText=`💥 MERGED to ${types[nt].e}! +${types[nt].v}`;
    o.t=nt;o.el.innerText=types[nt].e;o.el.style.background='#0a3a0a';setTimeout(()=>o.el.style.background='#2a2a2a',300);
    el.remove();fruits=fruits.filter(f=>f!==obj);
    // POP
    o.el.animate([{transform:'scale(1.5)'},{transform:'scale(1)'}],{duration:200});
    return;
   }
  });
 }
 el.addEventListener('mousedown',start);window.addEventListener('mousemove',move);window.addEventListener('mouseup',end);
 el.addEventListener('touchstart',start,{passive:false});window.addEventListener('touchmove',move,{passive:false});window.addEventListener('touchend',end);
 board.appendChild(el);fruits.push(obj);
}
function highlight(t){fruits.forEach(f=>{if(f.t===t)f.el.classList.add('same');});}
function clearHighlight(){fruits.forEach(f=>f.el.classList.remove('same'));}
function addFruit(){let x=Math.random()*260,y=Math.random()*380,t=Math.floor(Math.random()*3);if(score>500)t=Math.floor(Math.random()*4);createFruit(x,y,t);}
function init(){for(let i=0;i<10;i++)addFruit();}
init();
function watchAd(){coins+=500;localStorage.setItem('coins',coins);document.getElementById('coinEl').innerText=coins;document.getElementById('status').innerText='+500 BONUS!';}
function saveW(){let a=document.getElementById('tonAddr').value.trim();if(a.length<20){alert('Address short');return;}localStorage.setItem('tonAddr',a);localStorage.setItem('tonMemo',document.getElementById('tonMemo').value.trim());document.getElementById('status').innerText='✅ Wallet Saved';}
function withdraw(){if(coins<5000){alert('Min 5000 = $1');return;}let addr=localStorage.getItem('tonAddr');if(!addr){alert('Save wallet first');return;}let usd=(coins/5000).toFixed(2);let user=window.Telegram?.WebApp?.initDataUnsafe?.user;fetch('/api/withdraw',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({addr,memo:localStorage.getItem('tonMemo'),coins,usd,user})}).then(r=>r.json()).then(j=>{if(j.ok){document.getElementById('status').innerText=`✅ $${usd} sent!`;coins=0;localStorage.setItem('coins',0);document.getElementById('coinEl').innerText=0;}else alert(j.error)})}
</script></body></html>

