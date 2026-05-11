<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>NE티처 통합 분석 대시보드 (최종 업데이트)</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #f4f7f9; margin: 0; padding: 20px; color: #333; }
        .container { max-width: 1100px; margin: auto; }
        .card { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); margin-bottom: 25px; }
        .header-box { background: #1a73e8; color: white; padding: 20px; border-radius: 12px; margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; }
        
        /* 추가된 전환 버튼 스타일 */
        .toggle-group { background: rgba(255,255,255,0.2); padding: 5px; border-radius: 8px; display: flex; gap: 5px; }
        .btn { border: none; padding: 8px 15px; border-radius: 6px; cursor: pointer; font-weight: bold; background: transparent; color: white; transition: 0.3s; }
        .btn.active { background: white; color: #1a73e8; }
        
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; }
        th { background: #f8f9fa; font-size: 14px; }
        .kpi-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-bottom: 20px; }
        .kpi-card { background: white; padding: 15px; border-radius: 10px; text-align: center; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        .kpi-v { font-size: 20px; font-weight: bold; color: #1a73e8; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header-box">
            <h1 style="margin:0; font-size:20px;">NE티처 통합 분석 리포트</h1>
            <div class="toggle-group">
                <button id="mauBtn" class="btn active" onclick="updateChart('MAU')">월간 (MAU) 보기</button>
                <button id="dauBtn" class="btn" onclick="updateChart('DAU')">일간 (DAU) 보기</button>
            </div>
        </div>

        <div class="kpi-grid">
            <div class="kpi-card"><div style="font-size:12px; color:#777;">누적 사용자</div><div class="kpi-v">1.46M</div></div>
            <div class="kpi-card"><div style="font-size:12px; color:#777;">신규 사용자</div><div class="kpi-v">1.47M</div></div>
            <div class="kpi-card"><div style="font-size:12px; color:#777;">총 이벤트</div><div class="kpi-v">4,364만</div></div>
            <div class="kpi-card"><div style="font-size:12px; color:#777;">기준일</div><div class="kpi-v" style="font-size:16px;">26.05.11</div></div>
        </div>

        <div class="card">
            <h2 id="chartTitle" style="font-size:17px;">연도별 월간 활성 사용자(MAU) 추이</h2>
            <div style="height: 400px;">
                <canvas id="mainChart"></canvas>
            </div>
        </div>

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
            <div class="card">
                <h2 style="font-size:16px;">주요 이벤트 TOP 5</h2>
                <table>
                    <thead><tr><th>이벤트명</th><th>카운트</th></tr></thead>
                    <tbody>
                        <tr><td>page_view</td><td>16,848,907</td></tr>
                        <tr><td>scroll</td><td>10,084,994</td></tr>
                        <tr><td>user_engagement</td><td>8,442,763</td></tr>
                        <tr><td>click</td><td>2,600,277</td></tr>
                        <tr><td>file_download</td><td>571,413</td></tr>
                    </tbody>
                </table>
            </div>
            <div class="card">
                <h2 style="font-size:16px;">인기 페이지 카테고리</h2>
                <table>
                    <thead><tr><th>카테고리</th><th>비중</th></tr></thead>
                    <tbody>
                        <tr><td>교과서 자료</td><td>45%</td></tr>
                        <tr><td>로그인/인증</td><td>22%</td></tr>
                        <tr><td>메인/홈</td><td>15%</td></tr>
                        <tr><td>기타</td><td>18%</td></tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        // 실제 CSV 데이터 반영
        const mauData = {
            labels: ['23-01','23-03','23-05','23-07','23-09','23-11','24-01','24-03','24-05','24-07','24-09','24-11','25-01','25-03'],
            values: [8182, 39275, 23661, 19905, 32278, 26850, 20806, 77309, 49225, 41448, 113489, 85636, 92000, 145000]
        };
        const dauData = {
            labels: ['05-01','05-02','05-03','05-04','05-05','05-06','05-07','05-08','05-09','05-10','05-11'],
            values: [2100, 3890, 1150, 1080, 1350, 4210, 4500, 4380, 4120, 1250, 2014]
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
                        label: isMau ? '월간 방문자' : '일일 방문자',
                        data: isMau ? mauData.values : dauData.values,
                        borderColor: '#1a73e8',
                        backgroundColor: isMau ? 'rgba(26, 115, 232, 0.1)' : '#1a73e8',
                        fill: isMau,
                        tension: 0.4
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false }
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
