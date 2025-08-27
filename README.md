<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Bounty Football – Signal Bot</title>
<style>
  body{font-family:Arial, sans-serif; background:#0c1a20; color:#fff; padding:20px;}
  .card{max-width:700px; margin:auto; background:#122832; border-radius:16px; padding:20px;}
  input,select,button{width:100%; padding:10px; margin:5px 0; border-radius:8px; border:none;}
  button{background:#e63946; color:#fff; font-weight:bo kild; cursor:pointer;}
  .badge{display:inline-block; background:#1d3b4b; padding:6px 12px; border-radius:12px; margin:3px;}
</style>
</head>
<body>
<div class="card">
  <h2>Bounty Football – Signal Bot (Demo)</h2>
  <p>⚠️ Ye sirf demo hai, guarantee nahi deta. Aap apne outcomes ka data yahan daalo aur “Get Signal” dabao.</p>

  <label>Bankroll (Kitna balance hai): <input id="bank" type="number" value="1000"></label>
  <label>Kelly Fraction:
    <select id="kf">
      <option value="0.25">0.25 (Safe)</option>
      <option value="0.5">0.50</option>
      <option value="1">1.00 (Aggressive)</option>
    </select>
  </label>

  <h3>Outcomes Count (Kitni baar aaya):</h3>
  <div>
    <label>×2: <input id="c_x2" type="number" value="0"></label>
    <label>×5: <input id="c_x5" type="number" value="0"></label>
    <label>×8: <input id="c_x8" type="number" value="0"></label>
    <label>×18: <input id="c_x18" type="number" value="0"></label>
    <label>×30: <input id="c_x30" type="number" value="0"></label>
    <label>×50: <input id="c_x50" type="number" value="0"></label>
    <label>×66: <input id="c_x66" type="number" value="0"></label>
    <label>×88: <input id="c_x88" type="number" value="0"></label>
    <label>×100: <input id="c_x100" type="number" value="0"></label>
    <label>Miss (0): <input id="c_miss" type="number" value="0"></label>
  </div>

  <button id="btn">Get Signal</button>
  <div id="out"></div>
</div>

<script>
const buckets = [
  {id:"x2", label:"×2", payout:2},
  {id:"x5", label:"×5", payout:5},
  {id:"x8", label:"×8", payout:8},
  {id:"x18", label:"×18", payout:18},
  {id:"x30", label:"×30", payout:30},
  {id:"x50", label:"×50", payout:50},
  {id:"x66", label:"×66", payout:66},
  {id:"x88", label:"×88", payout:88},
  {id:"x100", label:"×100", payout:100},
];

function kelly(p, payout, frac){
  const b = payout-1;
  const numer = p*(b+1)-1;
  const full = b>0 ? numer/b : 0;
  const k = Math.max(0, Math.min(full,1));
  return { stakeFrac: Math.max(0, Math.min(k*frac,1)), ev: numer };
}

function fmt(n){return Number.isFinite(n)? n.toFixed(3):"-";}

btn.onclick = () => {
  const counts = {
    x2:+c_x2.value, x5:+c_x5.value, x8:+c_x8.value, x18:+c_x18.value,
    x30:+c_x30.value, x50:+c_x50.value, x66:+c_x66.value, x88:+c_x88.value,
    x100:+c_x100.value, miss:+c_miss.value
  };
  const total = Object.values(counts).reduce((a,b)=>a+b,0) || 1;
  const probs = {}; for(const [k,v] of Object.entries(counts)) probs[k]=v/total;

  const bank = +bank.value; const frac=+kf.value;
  const ranked = buckets.map(b=>{
    const p = probs[b.id]||0.001;
    const {stakeFrac,ev} = kelly(p,b.payout,frac);
    return {...b,p,ev,stake:bank*stakeFrac};
  }).sort((a,b)=>b.ev-a.ev);

  const best = ranked[0];
  out.innerHTML = `
    <h3>🎯 Signal</h3>
    <p class="badge">Target: ${best.label}</p>
    <p class="badge">Prob: ${fmt(best.p)}</p>
    <p class="badge">EV: ${fmt(best.ev)}</p>
    <p class="badge">Stake ≈ ${best.stake.toFixed(2)}</p>
  `;
};
</script>
</body>
</html># Bountyfoodball-bot
