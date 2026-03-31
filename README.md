<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pro Trading Journal</title>
<style>
  body { font-family: Arial, sans-serif; background:#f8fafc; color:#0f172a; margin:0; padding:16px; }
  h1 { text-align:center; margin-bottom:10px; }
  .topbar { display:flex; gap:12px; flex-wrap:wrap; justify-content:center; margin-bottom:12px; }
  .card { background:#ffffff; border:1px solid #cbd5f5; border-radius:12px; padding:10px 16px; color:#0f172a; font-size:14px; }
  button { background:#2563eb; color:#fff; border:none; padding:10px 16px; border-radius:10px; cursor:pointer; font-size:14px; }
  button.secondary { background:#374151; }

  table { width:100%; border-collapse:collapse; background:#ffffff; font-size:14px; }
  th, td { border:1px solid #cbd5f5; padding:10px; text-align:center; color:#0f172a; }
  th { background:#3b82f6; color:#ffffff; position:sticky; top:0; }

  input { width:110px; padding:6px; border-radius:6px; border:1px solid #94a3b8; background:#ffffff; color:#0f172a; text-align:center; }

  .wide { width:160px; }
  .profit { color:#16a34a; font-weight:bold; }
  .loss { color:#dc2626; font-weight:bold; }
</style>
</head>
<body>

<h1>📊 Pro Trading Journal</h1>

<div class="topbar">
  <div class="card">Capital ₹ <input id="capital" type="number" value="200000"></div>
  <div class="card">Risk/Trade ₹ <input id="risk" type="number" value="1000"></div>
  <button onclick="addRow()">➕ Add Trade</button>
</div>

<table id="tbl">
<thead>
<tr>
<th>Script</th><th>Entry</th><th>SL</th><th>Gap</th><th>Qty Plan</th>
<th>1R</th><th>2R</th><th>Inv Plan</th>
<th>Qty</th><th>Inv</th>
<th>Exit1</th><th>Sell1</th><th>Exit2</th><th>Sell2</th>
<th>P&L</th><th>R</th><th>%</th>
<th>Remarks</th><th>Date</th><th>X</th>
</tr>
</thead>
<tbody></tbody>
</table>

<script>
const tbody = document.querySelector('#tbl tbody');

function newRow(){
  const tr = document.createElement('tr');
  tr.innerHTML = `
  <td><input class="wide"></td>
  <td><input class="entry" type="number"></td>
  <td><input class="sl" type="number"></td>
  <td class="gap"></td>
  <td class="qtyPlan"></td>
  <td class="t1"></td>
  <td class="t2"></td>
  <td class="invPlan"></td>
  <td><input class="qty" type="number"></td>
  <td class="inv"></td>
  <td><input class="exit1" type="number"></td>
  <td><input class="sell1" type="number"></td>
  <td><input class="exit2" type="number"></td>
  <td><input class="sell2" type="number"></td>
  <td class="pnl"></td>
  <td class="r"></td>
  <td class="ret"></td>
  <td><input class="wide"></td>
  <td><input type="date"></td>
  <td><button onclick="this.closest('tr').remove(); calc();">X</button></td>`;
  tbody.appendChild(tr);
  tr.querySelectorAll('input').forEach(i=>i.addEventListener('input', calc));
}

function addRow(){ newRow(); }

function calc(){
  const risk = +document.getElementById('risk').value || 0;

  tbody.querySelectorAll('tr').forEach(r=>{
    const entry = +r.querySelector('.entry').value;
    const sl = +r.querySelector('.sl').value;
    const qty = +r.querySelector('.qty').value;
    const exit1 = +r.querySelector('.exit1').value;
    const sell1 = +r.querySelector('.sell1').value;
    const exit2 = +r.querySelector('.exit2').value;
    const sell2 = +r.querySelector('.sell2').value;

    if(!entry || !sl || entry === sl) return;

    const gap = Math.abs(entry - sl);
    const t1 = entry + gap;
    const t2 = entry + (2 * gap);
    const qtyPlan = risk / gap;

    const invPlan = qtyPlan * entry;
    const inv = qty * entry;

    const pnl = ((exit1 - entry) * sell1 || 0) + ((exit2 - entry) * sell2 || 0);
    const R = risk ? pnl / risk : 0;
    const ret = inv ? (pnl / inv) * 100 : 0;

    r.querySelector('.gap').innerText = gap.toFixed(2);
    r.querySelector('.t1').innerText = t1.toFixed(2);
    r.querySelector('.t2').innerText = t2.toFixed(2);
    r.querySelector('.qtyPlan').innerText = qtyPlan.toFixed(1);
    r.querySelector('.invPlan').innerText = invPlan.toFixed(0);
    r.querySelector('.inv').innerText = inv.toFixed(0);
    r.querySelector('.pnl').innerText = pnl.toFixed(2);
    r.querySelector('.r').innerText = R.toFixed(2);
    r.querySelector('.ret').innerText = ret.toFixed(2);

    r.querySelector('.pnl').className = pnl >= 0 ? 'pnl profit' : 'pnl loss';
  });
}

addRow();
</script>

</body>
</html>
