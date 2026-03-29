<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Trading Diary</title>
<style>
    body { font-family: Arial; background:#0f172a; color:#fff; padding:20px; }
    h1 { text-align:center; }
    table { width:100%; border-collapse:collapse; background:#1e293b; }
    th, td { padding:8px; border:1px solid #334155; text-align:center; }
    th { background:#0ea5e9; }
    input { width:90px; padding:4px; }
    .profit { color:lightgreen; }
    .loss { color:red; }
</style>
</head>
<body>

<h1>📊 Trading Diary</h1>

<table id="tradeTable">
<thead>
<tr>
<th>Script</th>
<th>Entry</th>
<th>SL</th>
<th>SL Gap</th>
<th>Planned Qty</th>
<th>Target 1 (1R)</th>
<th>Target 2 (2R)</th>
<th>Investment Plan</th>
<th>Actual Qty</th>
<th>Actual Investment</th>
<th>Exit 1</th>
<th>Sell Qty 1</th>
<th>Exit 2</th>
<th>Sell Qty 2</th>
<th>Profit</th>
<th>Return %</th>
<th>Remarks</th>
<th>Date</th>
</tr>
</thead>
<tbody>
<tr>
<td><input value="INDUSIND"></td>
<td><input type="number" class="entry" value="819.77"></td>
<td><input type="number" class="sl" value="809"></td>
<td class="slGap"></td>
<td class="qtyPlan"></td>
<td class="t1"></td>
<td class="t2"></td>
<td class="invPlan"></td>
<td><input type="number" class="qtyActual" value="50"></td>
<td class="invActual"></td>
<td><input type="number" class="exit1" value="832.5"></td>
<td><input type="number" class="sell1" value="50"></td>
<td><input type="number" class="exit2"></td>
<td><input type="number" class="sell2"></td>
<td class="profit"></td>
<td class="return"></td>
<td><input value="Bought mixed prices"></td>
<td><input type="date"></td>
</tr>
</tbody>
</table>

<script>
function calculate() {
    document.querySelectorAll("tbody tr").forEach(row => {
        let entry = parseFloat(row.querySelector(".entry").value) || 0;
        let sl = parseFloat(row.querySelector(".sl").value) || 0;
        let qtyActual = parseFloat(row.querySelector(".qtyActual").value) || 0;
        let exit1 = parseFloat(row.querySelector(".exit1").value) || 0;
        let sell1 = parseFloat(row.querySelector(".sell1").value) || 0;
        let exit2 = parseFloat(row.querySelector(".exit2").value) || 0;
        let sell2 = parseFloat(row.querySelector(".sell2").value) || 0;

        let slGap = entry - sl;
        let t1 = entry + slGap;
        let t2 = entry + (2 * slGap);

        let qtyPlan = (500 / slGap).toFixed(2); // risk ₹500 per trade
        let invPlan = (qtyPlan * entry).toFixed(0);

        let invActual = (qtyActual * entry);

        let profit = ((exit1 - entry) * sell1) + ((exit2 - entry) * sell2);
        let returnPct = (profit / invActual) * 100;

        row.querySelector(".slGap").innerText = slGap.toFixed(2);
        row.querySelector(".t1").innerText = t1.toFixed(2);
        row.querySelector(".t2").innerText = t2.toFixed(2);
        row.querySelector(".qtyPlan").innerText = qtyPlan;
        row.querySelector(".invPlan").innerText = invPlan;
        row.querySelector(".invActual").innerText = invActual.toFixed(0);
        row.querySelector(".profit").innerText = profit.toFixed(2);
        row.querySelector(".return").innerText = returnPct.toFixed(2);

        if (profit >= 0) {
            row.querySelector(".profit").className = "profit";
        } else {
            row.querySelector(".profit").className = "loss";
        }
    });
}

setInterval(calculate, 500);
</script>

</body>
</html>
