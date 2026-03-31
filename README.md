<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pro Trading Journal - Analytics</title>
<style>
  body { font-family: Arial, sans-serif; background:#f8fafc; color:#0f172a; margin:0; padding:16px; }
  h1 { text-align:center; margin-bottom:10px; }
  .topbar { display:flex; gap:12px; flex-wrap:wrap; justify-content:center; margin-bottom:12px; }
  .card { background:#ffffff; border:1px solid #cbd5f5; border-radius:12px; padding:10px 16px; color:#0f172a; font-size:14px; }
  button { background:#2563eb; color:#fff; border:none; padding:10px 16px; border-radius:10px; cursor:pointer; font-size:14px; }

  table { width:100%; border-collapse:collapse; background:#ffffff; font-size:14px; }
  th, td { border:1px solid #cbd5f5; padding:10px; text-align:center; }
  th { background:#3b82f6; color:#ffffff; position:sticky; top:0; }

  input, select { width:120px; padding:6px; border-radius:6px; border:1px solid #94a3b8; background:#ffffff; text-align:center; }

  .profit { color:#16a34a; font-weight:bold; }
  .loss { color:#dc2626; font-weight:bold; }
</style>
</head>
<body>

<h1>📊 Pro Trading Journal + Analytics</h1>

<div class="topbar">
  <div class="card">Risk/Trade ₹ <input id="risk" type="number" value="1000"></div>
  <button onclick="addRow()">➕ Add Trade</button>
</div>

<div class="topbar">
  <div class="card">Net P&L: <b id="netPnl">0</b></div>
  <div class="card">Win Rate: <b id="winRate">0%</b></div>
  <div class="card">Avg R: <b id="avgR">0</b></div>
  <div class="card">Best Setup: <b id="bestSetup">-</b></div>
  <div class="card">Worst Day: <b id="worstDay">-</b></div>
</div>

<table id="tbl">
<thead>
<tr>
<th>Script</th><th>Setup</th><th>Entry</th><th>SL</th><th>Gap</th>
<th>Qty</th><th>Exit</th><th>Sell</th>
<th>P&L</th><th>R</th><th>Date</th><th>X</th>
</tr>
</thead>
<tbody></tbody>
</table>

<script>
const tbody = document.querySelector('#tbl tbody');

function newRow(){
  const tr = document.createElement('tr');
  tr.innerHTML = `
  <td><input></td>
  <td>
    <select class="setup">
      <option>Breakout</option>
      <option>Pullback</option>
      <option>Reversal</option>
    </select>
  </td>
  <td><input class="entry" type="number"></td>
  <td><input class="sl" type="number"></td>
  <td class="gap"></td>
  <td><input class="qty" type="number"></td>
  <td><input class="exit" type="number"></td>
  <td><input class="sell" type="number"></td>
  <td class="pnl"></td>
  <td class="r"></td>
  <td><input type="date"></td>
  <td><button onclick="this.closest('tr').remove(); calc();">X</button></td>`;
  tbody.appendChild(tr);
  tr.querySelectorAll('input, select').forEach(i=>i.addEventListener('input', calc));
}

function addRow(){ newRow(); }

function calc(){
  const risk = +document.getElementById('risk').value || 0;
  let totalPnl=0, wins=0, trades=0, totalR=0;
  let setupPerf = {};
  let dayPerf = {};

  tbody.querySelectorAll('tr').forEach(r=>{
    const setup = r.querySelector('.setup').value;
    const entry = +r.querySelector('.entry').value;
    const sl = +r.querySelector('.sl').value;
    const qty = +r.querySelector('.qty').value;
    const exit = +r.querySelector('.exit').value;
    const sell = +r.querySelector('.sell').value;
    const date = r.querySelector('input[type=date]').value;

    if(!entry || !sl) return;

    const gap = Math.abs(entry - sl);
    const pnl = (exit - entry) * sell;
    const R = risk ? pnl / risk : 0;

    r.querySelector('.gap').innerText = gap.toFixed(2);
    r.querySelector('.pnl').innerText = pnl.toFixed(2);
    r.querySelector('.r').innerText = R.toFixed(2);

    r.querySelector('.pnl').className = pnl >= 0 ? 'pnl profit' : 'pnl loss';

    if(qty>0){
      trades++;
      totalPnl += pnl;
      totalR += R;
      if(pnl>0) wins++;

      // setup performance
      if(!setupPerf[setup]) setupPerf[setup]=0;
      setupPerf[setup]+=pnl;

      // day performance
      if(date){
        if(!dayPerf[date]) dayPerf[date]=0;
        dayPerf[date]+=pnl;
      }
    }
  });

  document.getElementById('netPnl').innerText = totalPnl.toFixed(2);
  document.getElementById('winRate').innerText = trades? ((wins/trades*100).toFixed(1)+'%'):'0%';
  document.getElementById('avgR').innerText = trades? (totalR/trades).toFixed(2):0;

  // best setup
  let bestSetup='-'; let max=-Infinity;
  for(let k in setupPerf){ if(setupPerf[k]>max){ max=setupPerf[k]; bestSetup=k; }}
  document.getElementById('bestSetup').innerText = bestSetup;

  // worst day
  let worstDay='-'; let min=Infinity;
  for(let k in dayPerf){ if(dayPerf[k]<min){ min=dayPerf[k]; worstDay=k; }}
  document.getElementById('worstDay').innerText = worstDay;
}

addRow();
</script>

</body>
</html>
