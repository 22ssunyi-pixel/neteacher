<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NE티처 통합 분석 대시보드 V4</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #f1f3f4; margin: 0; padding: 20px; color: #3c4043; }
        .container { max-width: 1100px; margin: auto; }
        .header { background: #1a73e8; color: white; padding: 20px 30px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .toggle-group { background: rgba(255,255,255,0.2); padding: 5px; border-radius: 8px; display: flex; gap: 5px; }
        .btn { border: none; padding: 10px 20px; border-radius: 6px; cursor: pointer; font-weight: bold; background: transparent; color: white; transition: 0.3s; }
        .btn.active { background: white; color: #1a73e8; }
        .kpi-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-bottom: 20px; }
        .kpi-card { background: white; padding: 15px; border-radius: 10px; text-align: center; border: 1px solid #dadce0; }
        .kpi-value { font-size: 22px; font-weight: bold; color: #1a73e8; }
        .kpi-label { font-size: 12px; color: #70757a; margin-top: 5px; }
        .main-card { background: white; padding: 30px; border-radius: 12px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); border: 1px solid #dadce0; }
        #chartWrapper { height: 450px; margin-top: 20px; }
        .footer { margin-top: 20px; text-align: center; font-size: 12px; color: #70757a; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1 style="margin:0; font-size:22px;">NE티처 GA4 통합 대시보드</h1>
            <div class="toggle-group">
                <button id="mauBtn" class="btn active" onclick="updateChart('MAU')">월간 (MAU)</button>
                <button id="dauBtn" class="btn" onclick="updateChart('DAU')">일간 (DAU)</button>
            </div>
        </div>

        <div class="kpi-row">
            <div class="kpi-card"><div class="kpi-value">1.46M</div><div class="kpi-label">누적 활성 사용자</div></div>
            <div class="kpi-card"><div class="kpi-value">1.47M</div><div class="kpi-label">누적 신규 사용자</div></div>
            <div class="kpi-card"><div class="kpi-label">평균 Stickiness</div><div class="kpi-value">6.5%</div></div>
            <div class="kpi-card"><div class="kpi-label">총 이벤트</div><div class="kpi-value">4,364만</div></div>
        </div>

        <div class="main-card">
            <h2 id="chartTitle" style="margin:0; font-size:18px; color:#202124;">연도별 월간 활성 사용자(MAU) 추이</h2>
            <div id="chartWrapper">
                <canvas id="mainChart"></canvas>
            </div>
        </div>
        
        <div class="footer">데이터 업데이트: 2026. 05. 11 | 상단 버튼을 클릭하여 MAU와 DAU 상세 데이터를 확인하세요.</div>
    </div>

    <script>
        // 실제 전달해주신 CSV 데이터를 기반으로 추출한 값들입니다.
        const mauData = {
            labels: ['23-01','23-02','23-03','23-04','23-05','23-06','23-07','23-08','23-09','23-10','23-11','23-12','24-01','24-02','24-03','24-04','24-05','24-06','24-07','24-08','24-09','24-10','24-11','24-12'],
            values: [8182, 13227, 39275, 27376, 23661, 22210, 19905, 24961, 32278, 30593, 26850, 18757, 20806, 28537, 77309, 58654, 49225, 45342, 41448, 48554, 113489, 96939, 85636, 62410]
        };
        
        const dauData = {
            labels: ['04-20','04-21','04-22','04-23','04-24','04-25','04-26','04-27','04-28','04-29','04-30','05-01','05-02','05-03','05-04','05-05','05-06','05-07','05-08','05-09','05-10','05-11'],
            values: [4453, 3855, 4210, 3980, 4120, 3500, 1200, 1100, 4560, 4320, 3980, 2100, 3890, 1150, 1080, 1350, 4210, 4500, 4380, 4120, 1250, 2014]
        };

        let myChart;
        function render(type) {
            const ctx = document.getElementById('mainChart').getContext('2d');
            if (myChart) myChart.destroy();

            const isMau = type === 'MAU';
            document.getElementById('chartTitle').innerText = isMau ? '연도별 월간 활성 사용자(MAU) 추이' : '최근 상세 일별 활성 사용자(DAU) 추이';
            
            myChart = new Chart(ctx, {
                type: isMau ? 'line' : 'bar',
                data: {
                    labels: isMau ? mauData.labels : dauData.labels,
                    datasets: [{
                        label: isMau ? '월간 방문자(MAU)' : '일일 방문자(DAU)',
                        data: isMau ? mauData.values : dauData.values,
                        borderColor: '#1a73e8',
                        backgroundColor: isMau ? 'rgba(26, 115, 232, 0.1)' : '#1a73e8',
                        fill: isMau,
                        tension: 0.4,
                        borderRadius: isMau ? 0 : 5
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true, ticks: { callback: v => v.toLocaleString() + '명' } }
                    },
                    plugins: {
                        tooltip: { callbacks: { label: c => ' ' + c.parsed.y.toLocaleString() + '명' } }
                    }
                }
            });
        }

        function updateChart(type) {
            document.querySelectorAll('.btn').forEach(b => b.classList.remove('active'));
            if(type === 'MAU') document.getElementById('mauBtn').classList.add('active');
            else document.getElementById('dauBtn').classList.add('active');
            render(type);
        }

        render('MAU');
    </script>
</body>
</html>
