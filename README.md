# dashboard
<!doctype html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="utf-8" />
  <title>Dashboard</title>

  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>

  <style>
    body{
      margin:0;
      font-family: system-ui, -apple-system, "Segoe UI", Tahoma, Arial;
      background:#fff;
    }

    .wrap{
      max-width: 800px;
      margin: 30px auto;
      display:flex;
      flex-direction:column;
      align-items:center;
      gap:20px;
    }

    .inputs{
      display:flex;
      gap:12px;
    }

    input{
      width:160px;
      height:38px;
      padding:0 12px;
      font-size:16px;
      border-radius:10px;
      border:1px solid #ddd;
    }

    .chartBox{
      width:380px;          /* ✅ حجم الدائرة */
      aspect-ratio:1/1;
    }

    .legend{
      display:flex;
      gap:20px;
      padding:10px 16px;
      border:1px solid #e6e6e6;
      border-radius:8px;
      font-size:14px;
    }

    .item{
      display:flex;
      align-items:center;
      gap:8px;
    }

    .box{
      width:14px;
      height:14px;
      border-radius:3px;
    }

    .blue{ background:#1d6dff; }
    .gray{ background:#e6e6e6; }
  </style>
</head>

<body>
<div class="wrap">

  <!-- Inputs -->
  <div class="inputs">
    <input id="total" type="number" value="250" placeholder="الإجمالي">
    <input id="sales" type="number" value="68" placeholder="المبيعات">
  </div>

  <!-- Chart -->
  <div class="chartBox">
    <canvas id="chart"></canvas>
  </div>

  <!-- Legend -->
  <div class="legend">
    <div class="item">
      <div class="box blue"></div>
      <span id="salesText">Sales: 68</span>
    </div>
    <div class="item">
      <div class="box gray"></div>
      <span id="remainText">Remaining: 182</span>
    </div>
  </div>

</div>

<script>
  const centerText = {
    id: 'centerText',
    afterDraw(chart) {
      const {ctx} = chart;
      const meta = chart.getDatasetMeta(0);
      if (!meta.data.length) return;

      const x = meta.data[0].x;
      const y = meta.data[0].y;

      ctx.save();
      ctx.font = '800 56px system-ui';
      ctx.fillStyle = '#111';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText(chart.options.plugins.centerText.text, x, y);
      ctx.restore();
    }
  };

  Chart.register(centerText);

  const totalInput = document.getElementById('total');
  const salesInput = document.getElementById('sales');

  const salesText = document.getElementById('salesText');
  const remainText = document.getElementById('remainText');

  const chart = new Chart(document.getElementById('chart'), {
    type: 'doughnut',
    data: {
      datasets: [{
        data: [68, 182],
        backgroundColor: ['#1d6dff', '#e6e6e6'],
        borderWidth: 0
      }]
    },
    options: {
      cutout: '78%',       /* ✅ سماكة الحلقة */
      plugins: {
        legend: { display: false },
        centerText: { text: '27.20%' }
      }
    }
  });

  function update(){
    let total = Number(totalInput.value) || 0;
    let sales = Number(salesInput.value) || 0;

    if (sales > total) sales = total;

    const remaining = total - sales;
    const percent = total ? ((sales / total) * 100).toFixed(2) : '0.00';

    chart.data.datasets[0].data = [sales, remaining];
    chart.options.plugins.centerText.text = percent + '%';
    chart.update();

    salesText.textContent = `Sales: ${sales}`;
    remainText.textContent = `Remaining: ${remaining}`;
  }

  totalInput.oninput = update;
  salesInput.oninput = update;

  update();
</script>

</body>
</html>
