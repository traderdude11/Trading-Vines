<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pro Trading Journal</title>
<style>
  body { font-family: Arial, sans-serif; background:#0b1220; color:#e5e7eb; margin:0; padding:16px; }
  h1 { text-align:center; margin-bottom:10px; }
  .topbar { display:flex; gap:12px; flex-wrap:wrap; justify-content:center; margin-bottom:12px; }
  .card { background:#111827; border:1px solid #1f2937; border-radius:12px; padding:10px 12px; }
  .card b { color:#93c5fd; }
  button { background:#2563eb; color:#fff; border:none; padding:8px 12px; border-radius:10px; cursor:pointer; }
  button.secondary { background:#374151; }
  table { width:100%; border-collapse:collapse; background:#0f172a; }
  th, td { border:1px solid #1f2937; padding:6px; text-align:center; }
  th { background:#1d4ed8; }
  input { width:100%; box-sizing:border-box; padding:4px; border-radius:6px; border:1px solid #374151; background:#020617; color:#e5e7eb; }
  .profit { color:#22c55e; }
  .loss { color:#ef4444; }
  .small { font-size:12px; color:#9ca3af; }
</style>
</head>
<body>

<h1>📊 Pro Trading Journal</h1>

<div class="topbar">
  <div class="card">Capital: ₹<input id="capital" type="number" value="200000"></div>
  <div class="card">Risk / Trade: ₹<input id="risk" type="number" value="1000"></div>
  <button onclick="addRow()">➕ Add Trade</button>
  <button class="secondary" onclick="saveData()">💾 Save</button>
  <button class="secondary" onclick="loadData()">📂 Load</button>
  <button class="secondary" onclick="exportCSV()">⬇️ Export CSV</button>
</div>

<div class="topbar">
  <div class="card">Net P&L: <b id="netPnl">0</b></div>
  <div class="card">Win Rate: <b id="winRate">0%</b></div>
  <div class="card">Avg R: <b id="avgR">0</b></div>
  <div class="card">Expectancy: <b id="exp">0</b></div>
</div>

<table id="tbl">
<thead>
<tr>
<th>Script</th><th>Entry</th><th>SL</th><th>SL Gap</th><th>Qty Plan</th>
<th>1R</th><th>2R</th><th>Inv Plan</th>
<th>Qty Act</th><th>Inv Act</th>
<th>Exit1</th><th>Sell1</th><th>Exit2</th><th>Sell2</th>
<th>P&L</th><th>R</th><th>Return %</th>
<th>Remarks</th><th>Date</th><th>❌</th>
</tr>
</thead>
<tbody></tbody>
</table>

<script>
const tbody = document.querySelector('#tbl tbody');

function newRow(data={}){
  const tr = document.createElement('tr');
  tr.innerHTML = `
  <td><input value="${data.script||''}"></td>
  <td><input class="entry" type="number" value="${data.entry||''}"></td>
  <td><input class="sl" type="number" value="${data.sl||''}"></td>
  <td class="slGap"></td>
  <td class="qtyPlan"></td>
  <td class="t1"></td>
  <td class="t2"></td>
  <td class="invPlan"></td>
  <td><input class="qtyAct" type="number" value="${data.qtyAct||''}"></td>
  <td class="invAct"></td>
  <td><input class="exit1" type="number" value="${data.exit1||''}"></td>
  <td><input class="sell1" type="number" value="${data.sell1||''}"></td>
  <td><input class="exit2" type="number" value="${data.exit2||''}"></td>
  <td><input class="sell2" type="number" value="${data.sell2||''}"></td>
  <td class="pnl"></td>
  <td class="r"></td>
  <td class="ret"></td>
  <td><input value="${data.remarks||''}"></td>
  <td><input type="date" value="${data.date||''}"></td>
  <td><button onclick="this.closest('tr').remove(); calc();">X</button></td>`;
  tbody.appendChild(tr);
  tr.querySelectorAll('input').forEach(i=>i.addEventListener('input', calc));
}

function addRow(){ newRow(); }

function calc(){
  const risk = +document.getElementById('risk').value || 0;
  let totalPnl=0, wins=0, trades=0, totalR=0;

  tbody.querySelectorAll('tr').forEach(r=>{
    const entry=+r.querySelector('.entry').value||0;
    const sl=+r.querySelector('.sl').value||0;
    const qtyAct=+r.querySelector('.qtyAct').value||0;
    const exit1=+r.querySelector('.exit1').value||0;
    const sell1=+r.querySelector('.sell1').value||0;
    const exit2=+r.querySelector('.exit2').value||0;
    const sell2=+r.querySelector('.sell2').value||0;

    if(!entry || !sl || entry===sl){
      r.querySelector('.slGap').innerText='-';
      return;
    }

    const gap = entry-sl;
    const t1 = entry+gap;
    const t2 = entry+2*gap;
    const qtyPlan = (risk/gap)||0;
    const invPlan = qtyPlan*entry;
    const invAct = qtyAct*entry;

    const pnl = (exit1-entry)*sell1 + (exit2-entry)*sell2;
    const R = risk? pnl/risk : 0;
    const ret = invAct? (pnl/invAct*100):0;

    r.querySelector('.slGap').innerText=gap.toFixed(2);
    r.querySelector('.t1').innerText=t1.toFixed(2);
    r.querySelector('.t2').innerText=t2.toFixed(2);
    r.querySelector('.qtyPlan').innerText=qtyPlan.toFixed(2);
    r.querySelector('.invPlan').innerText=invPlan.toFixed(0);
    r.querySelector('.invAct').innerText=invAct.toFixed(0);
    r.querySelector('.pnl').innerText=pnl.toFixed(2);
    r.querySelector('.r').innerText=R.toFixed(2);
    r.querySelector('.ret').innerText=ret.toFixed(2);

    r.querySelector('.pnl').className = pnl>=0 ? 'pnl profit':'pnl loss';

    if(qtyAct>0){
      trades++;
      totalPnl += pnl;
      totalR += R;
      if(pnl>0) wins++;
    }
  });

  document.getElementById('netPnl').innerText = totalPnl.toFixed(2);
  document.getElementById('winRate').innerText = trades? ((wins/trades*100).toFixed(1)+'%'):'0%';
  document.getElementById('avgR').innerText = trades? (totalR/trades).toFixed(2):0;
  document.getElementById('exp').innerText = trades? ((totalPnl/trades).toFixed(2)):0;
}

function saveData(){
  const data=[];
  tbody.querySelectorAll('tr').forEach(r=>{
    const inputs=r.querySelectorAll('input');
    data.push([...inputs].map(i=>i.value));
  });
  localStorage.setItem('trades', JSON.stringify(data));
}

function loadData(){
  tbody.innerHTML='';
  const data = JSON.parse(localStorage.getItem('trades')||'[]');
  data.forEach(d=> newRow({
    script:d[0], entry:d[1], sl:d[2], qtyAct:d[3], exit1:d[4], sell1:d[5], exit2:d[6], sell2:d[7], remarks:d[8], date:d[9]
  }));
  calc();
}

function exportCSV(){
  let csv='Script,Entry,SL,PnL\n';
  tbody.querySelectorAll('tr').forEach(r=>{
    const tds=r.querySelectorAll('td');
    csv+=`${tds[0].innerText},${tds[1].innerText},${tds[2].innerText},${tds[14].innerText}\n`;
  });
  const blob=new Blob([csv]);
  const a=document.createElement('a');
  a.href=URL.createObjectURL(blob);
  a.download='trades.csv';
  a.click();
}

// init
addRow();
</script>

</body>
</html>

