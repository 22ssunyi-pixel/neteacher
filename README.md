<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NE티처 통합 분석 대시보드 (전환형)</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #f4f7f9; margin: 0; padding: 20px; }
        .container { max-width: 1100px; margin: auto; }
        .header { background: #1a73e8; color: white; padding: 20px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .toggle-group { background: rgba(255,255,255,0.2); padding: 5px; border-radius: 8px; display: flex; gap: 5px; }
        .btn { border: none; padding: 8px 15px; border-radius: 6px; cursor: pointer; font-weight: bold; background: transparent; color: white; transition: 0.3s; }
        .btn.active { background: white; color: #1a73e8; }
        .card { background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h2 { margin-top: 0; color: #333; font-size: 18px; }
        #chartWrapper { height: 450px; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1 style="margin:0; font-size:20px;">NE티처 트래픽 통합 대시보드</h1>
            <div class="toggle-group">
                <button id="mauBtn" class="btn active" onclick="updateChart('MAU')">월간 (MAU)</button>
                <button id="dauBtn" class="btn" onclick="updateChart('DAU')">일간 (DAU)</button>
            </div>
        </div>

        <div class="card">
            <h2 id="chartTitle">연도별 월간 활성 사용자(MAU) 추이</h2>
            <div id="chartWrapper">
                <canvas id="mainChart"></canvas>
            </div>
        </div>
    </div>

    <script>
        // 데이터 준비 (이전 CSV 기반 데이터)
        const mauData = {
            labels: ['23-01','23-03','23-09','24-03','24-09','25-03','25-09','26-03'],
            values: [8182, 39275, 113489, 77309, 113489, 108878, 120541, 145000] // 예시 수치
        };
        const dauData = {
            labels: ['2026-04-01', '2026-04-15', '2026-05-01', '2026-05-11'],
            values: [1500, 2357, 1980, 2014] // 예시 수치
        };

        let myChart;
        function render(type) {
            const ctx = document.getElementById('mainChart').getContext('2d');
            if (myChart) myChart.destroy();

            const isMau = type === 'MAU';
            document.getElementById('chartTitle').innerText = isMau ? '연도별 월간 활성 사용자(MAU) 추이' : '상세 일별 활성 사용자(DAU) 추이';
            
            myChart = new Chart(ctx, {
                type: isMau ? 'line' : 'bar',
                data: {
                    labels: isMau ? mauData.labels : dauData.labels,
                    datasets: [{
                        label: isMau ? 'MAU' : 'DAU',
                        data: isMau ? mauData.values : dauData.values,
                        borderColor: '#1a73e8',
                        backgroundColor: isMau ? 'rgba(26, 115, 232, 0.1)' : '#1a73e8',
                        fill: isMau,
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });
        }

        function updateChart(type) {
            document.querySelectorAll('.btn').forEach(b => b.classList.remove('active'));
            if(type === 'MAU') document.getElementById('mauBtn').classList.add('active');
            else document.getElementById('dauBtn').classList.add('active');
            render(type);
        }

        // 초기 실행
        render('MAU');
    </script>
</body>
</html>
