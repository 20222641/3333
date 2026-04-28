<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>ANIME-BIZ AI 컨설턴트 | 캡스톤 프로젝트</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.css" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    :root { --primary: #3498db; --secondary: #e67e22; --text: #2c3e50; --bg: #f0f2f5; --white: #fff; }
    
    /* 1. 전체 화면 너비 고정 (쏠림 방지 핵심) */
    html, body { 
      margin: 0; 
      padding: 0; 
      width: 100%; 
      overflow-x: hidden; /* 가로 스크롤 방지 */
      -webkit-text-size-adjust: none;
    }

    body { font-family: 'Pretendard', sans-serif; background: var(--bg); color: var(--text); }
    
    /* 2. 배너가 무조건 화면 꽉 차게 설정 */
    .hero { 
      background: linear-gradient(135deg, #1e375a 0%, #3498db 100%); 
      color: var(--white); 
      padding: 60px 20px; 
      text-align: center;
      width: 100vw; /* Viewport Width 사용: 화면 너비만큼 꽉 채움 */
      position: relative;
      left: 50%;
      right: 50%;
      margin-left: -50vw;
      margin-right: -50vw;
      box-sizing: border-box;
    }
    
    .hero h1 { margin: 0 0 10px 0; font-size: 2.2em; font-weight: 800; }
    .hero p { margin: 0; font-size: 1.1em; opacity: 0.9; word-break: keep-all; }
    
    .container { max-width: 900px; margin: 20px auto 50px; padding: 0 15px; box-sizing: border-box; }
    
    .tab-menu { display: flex; justify-content: center; gap: 10px; margin-bottom: 30px; flex-wrap: wrap; }
    .tab-btn { background: var(--white); border: 2px solid #ddd; color: #777; padding: 12px 20px; border-radius: 30px; font-size: 1em; font-weight: bold; cursor: pointer; transition: 0.3s; flex: 1; min-width: 140px; }
    .tab-btn.active { background: var(--primary); border-color: var(--primary); color: var(--white); }

    .tab-content { display: none; background: var(--white); padding: 25px; border-radius: 15px; box-shadow: 0 5px 20px rgba(0,0,0,0.05); animation: fadeIn 0.4s; }
    .tab-content.active { display: block; }
    
    iframe { width: 100%; height: 800px; border: none; border-radius: 8px; }

    .search-container { text-align: center; margin-bottom: 30px; padding: 20px; background: #f8f9fa; border-radius: 12px; }
    .search-input { width: 100%; max-width: 500px; padding: 15px; font-size: 1em; border: 2px solid #ddd; border-radius: 30px; margin-bottom: 10px; box-sizing: border-box; }
    .search-btn { width: 100%; max-width: 500px; padding: 15px; font-size: 1.1em; background: var(--primary); color: white; border: none; border-radius: 30px; cursor: pointer; font-weight: bold; }

    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

    /* 📱 모바일 긴급 보정 */
    @media (max-width: 768px) {
      .hero { padding: 40px 15px; margin-left: -50vw; margin-right: -50vw; }
      .hero h1 { font-size: 1.6em; }
      .tab-btn { min-width: 120px; font-size: 0.9em; }
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
</head>
<body>

  <header class="hero">
    <h1>🚀 ANIME-BIZ AI 컨설턴트</h1>
    <p>기획안 제출 및 내 분석 결과 확인 시스템</p>
  </header>

  <div class="container">
    <div class="tab-menu">
      <button class="tab-btn active" onclick="openTab('submitTab', this)"><i class="fas fa-pen"></i> 기획안 제출</button>
      <button class="tab-btn" onclick="openTab('resultTab', this); fetchSheetData();"><i class="fas fa-search"></i> 분석 결과 확인</button>
    </div>

    <div id="submitTab" class="tab-content active">
      <iframe id="googleForm" src="https://docs.google.com/forms/d/e/1FAIpQLSfGfqgT9i3M97qU-rwgqz7jklNgUnA-WernWtEOPq825fGNDQ/viewform?embedded=true">로드 중...</iframe>
    </div>

    <div id="resultTab" class="tab-content">
      <div class="search-container">
        <h2>내 기획안 검색하기</h2>
        <input type="text" id="searchInput" class="search-input" placeholder="기획안 제목을 입력하세요" onkeypress="handleEnter(event)">
        <button class="search-btn" onclick="searchMyResult()">결과 보기</button>
      </div>
      <div id="myResultPage" style="display:none; background:#fff; border:2px solid var(--primary); border-radius:12px; padding:20px; margin-top:20px;"></div>
    </div>
  </div>

  <script>
    const csvUrl = 'https://docs.google.com/spreadsheets/d/1WbBuZokGh77HuEmz8pP9YDtpnYyRjz1kA9IhObQl45w/export?format=csv';
    let allData = [];

    function openTab(tabName, btn) {
      document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));
      document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
      document.getElementById(tabName).classList.add('active');
      btn.classList.add('active');
    }

    function fetchSheetData() {
      Papa.parse(csvUrl, { download: true, header: true, transformHeader: (h) => h.trim(), complete: function(results) { allData = results.data; } });
    }

    function handleEnter(e) { if (e.key === 'Enter') searchMyResult(); }

    function searchMyResult() {
      const searchTitle = document.getElementById('searchInput').value.trim();
      const resultPage = document.getElementById('myResultPage');
      let myData = allData.find(row => row['기획안 제목'] && row['기획안 제목'].trim() === searchTitle);

      if (myData) {
        resultPage.style.display = 'block';
        resultPage.innerHTML = `
          <h2 style="text-align:center; color:var(--primary); border-bottom:1px solid #eee; padding-bottom:10px;">${myData['기획안 제목']}</h2>
          <div style="background:var(--secondary); color:#fff; text-align:center; padding:8px; border-radius:20px; font-weight:bold; margin:15px 0;">🎯 예상 성공률: ${myData['AI 예상 성공 확률'] || '분석 중'}</div>
          <div style="margin-top:15px;">
            <strong style="color:var(--primary); display:block;">🤖 AI 구조화 평가</strong><p style="background:#f8f9fa; padding:10px; border-radius:5px;">${myData['AI 구조화 평가'] || '준비 중'}</p>
            <strong style="color:var(--primary); display:block;">💡 보완 피드백</strong><p style="background:#f8f9fa; padding:10px; border-radius:5px;">${myData['AI 핵심 보완 피드백'] || '준비 중'}</p>
          </div>`;
      } else {
        alert("일치하는 기획안이 없습니다.");
      }
    }
  </script>
</body>
</html>
