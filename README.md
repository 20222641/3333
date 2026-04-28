<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ANIME-BIZ AI 컨설턴트 | 캡스톤 프로젝트</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.css" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    :root { --primary: #3498db; --secondary: #e67e22; --text: #2c3e50; --bg: #f0f2f5; --white: #fff; }
    body { font-family: 'Pretendard', sans-serif; background: var(--bg); color: var(--text); margin: 0; }
    
    .hero { background: linear-gradient(135deg, #1e375a 0%, #3498db 100%); color: var(--white); padding: 40px 20px; text-align: center; }
    .hero h1 { margin: 0; font-size: 2.2em; font-weight: 800; }
    
    .container { max-width: 900px; margin: 20px auto 50px; padding: 0 20px; }
    
    .tab-menu { display: flex; justify-content: center; gap: 15px; margin-bottom: 30px; }
    .tab-btn { background: var(--white); border: 2px solid #ddd; color: #777; padding: 12px 25px; border-radius: 30px; font-size: 1.1em; font-weight: bold; cursor: pointer; transition: 0.3s; }
    .tab-btn.active { background: var(--primary); border-color: var(--primary); color: var(--white); box-shadow: 0 4px 10px rgba(52, 152, 219, 0.3); }
    
    .tab-content { display: none; background: var(--white); padding: 30px; border-radius: 15px; box-shadow: 0 5px 20px rgba(0,0,0,0.05); animation: fadeIn 0.4s; }
    .tab-content.active { display: block; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    
    iframe { width: 100%; height: 800px; border: none; border-radius: 8px; }
    
    /* 검색창 디자인 */
    .search-container { text-align: center; margin-bottom: 30px; padding: 20px; background: #f8f9fa; border-radius: 12px; }
    .search-input { width: 60%; padding: 15px 20px; font-size: 1.1em; border: 2px solid #ddd; border-radius: 30px; outline: none; transition: 0.3s; box-sizing: border-box; }
    .search-input:focus { border-color: var(--primary); box-shadow: 0 0 8px rgba(52, 152, 219, 0.2); }
    .search-btn { padding: 15px 30px; font-size: 1.1em; background: var(--primary); color: white; border: none; border-radius: 30px; cursor: pointer; font-weight: bold; margin-left: 10px; transition: 0.3s; box-sizing: border-box; }
    
    .single-result-page { display: none; background: #fff; border: 2px solid var(--primary); border-radius: 12px; padding: 30px; margin-top: 20px; position: relative; }
    .page-title { color: var(--primary); font-size: 1.8em; border-bottom: 2px solid #eee; padding-bottom: 15px; margin-top: 0; text-align: center; }
    .prob-badge { display: inline-block; background: var(--secondary); color: white; padding: 8px 20px; border-radius: 20px; font-weight: bold; font-size: 1.1em; margin: 15px 0; text-align: center; width: 100%; box-sizing: border-box; }
    .res-box { margin-top: 20px; background: #f8f9fa; padding: 20px; border-radius: 8px; border-left: 5px solid var(--primary); }
    .res-box strong { color: var(--primary); display: block; margin-bottom: 8px; font-size: 1.1em; }
    .error-msg { text-align: center; color: #e74c3c; font-weight: bold; display: none; margin-top: 15px; }

    /* 📱 모바일 반응형 코드 (화면이 768px보다 작아질 때 작동) */
    @media (max-width: 768px) {
      .hero { padding: 30px 15px; }
      .hero h1 { font-size: 1.6em; }
      
      /* 탭 버튼 세로 정렬 */
      .tab-menu { flex-direction: column; gap: 10px; margin-bottom: 20px; }
      .tab-btn { width: 100%; padding: 12px; }
      
      /* 검색창 레이아웃 세로 정렬 및 꽉 차게 */
      .search-input { width: 100%; margin-bottom: 10px; }
      .search-btn { width: 100%; margin-left: 0; }
      
      /* 여백 줄이기 */
      .tab-content { padding: 15px; }
      .single-result-page { padding: 15px; }
      .page-title { font-size: 1.4em; line-height: 1.4; }
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
      <button class="tab-btn" onclick="openTab('resultTab', this); fetchSheetData();"><i class="fas fa-search"></i> 내 분석 결과 찾기</button>
    </div>

    <div id="submitTab" class="tab-content active">
      <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSfGfqgT9i3M97qU-rwgqz7jklNgUnA-WernWtEOPq825fGNDQ/viewform?embedded=true">로드 중...</iframe>
    </div>

    <div id="resultTab" class="tab-content">
      <div class="search-container">
        <h2>내 기획안 검색하기</h2>
        <p>제출하신 <strong>'기획안 제목'</strong>을 정확히 입력해주세요.</p>
        <input type="text" id="searchInput" class="search-input" placeholder="예: 다카야마 애니메이션 활성화 방안" onkeypress="handleEnter(event)">
        <button class="search-btn" onclick="searchMyResult()">결과 보기</button>
        <div id="errorMsg" class="error-msg">❌ 일치하는 기획안을 찾을 수 없습니다. (제목을 다시 확인해주세요)</div>
      </div>

      <div id="myResultPage" class="single-result-page"></div>
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
      
      document.getElementById('myResultPage').style.display = 'none';
      document.getElementById('errorMsg').style.display = 'none';
      document.getElementById('searchInput').value = '';
    }

    function fetchSheetData() {
      Papa.parse(csvUrl, {
        download: true,
        header: true,
        transformHeader: (h) => h.trim(),
        complete: function(results) {
          allData = results.data;
        }
      });
    }

    function handleEnter(event) {
      if (event.key === 'Enter') {
        searchMyResult();
      }
    }

    function searchMyResult() {
      const searchTitle = document.getElementById('searchInput').value.trim();
      const errorMsg = document.getElementById('errorMsg');
      const resultPage = document.getElementById('myResultPage');
      
      if (searchTitle === "") {
        alert("기획안 제목을 입력해주세요!");
        return;
      }

      let myData = null;
      for (let i = allData.length - 1; i >= 0; i--) {
        const row = allData[i];
        if (row['기획안 제목'] && row['기획안 제목'].trim() === searchTitle) {
          myData = row;
          break;
        }
      }

      if (myData) {
        errorMsg.style.display = 'none';
        resultPage.style.display = 'block';
        
        const prob = myData['AI 예상 성공 확률'] || myData['성공 확률'] || 'AI 분석 준비 중 ⏳';
        const structure = myData['AI 구조화 평가'] || myData['구조화 평가'] || 'AI가 열심히 분석을 진행하고 있습니다. 잠시 후 다시 검색해 보세요!';
        const feedback = myData['AI 핵심 보완 피드백'] || myData['보완 피드백'] || '잠시만 기다려주세요.';

        resultPage.innerHTML = `
          <h2 class="page-title"><i class="fas fa-file-alt"></i> ${myData['기획안 제목']}</h2>
          <div class="prob-badge">🎯 예상 성공률: ${prob}</div>
          
          <div class="res-box">
            <strong><i class="fas fa-microchip"></i> AI 구조화 평가</strong>
            <p>${structure}</p>
          </div>
          
          <div class="res-box">
            <strong><i class="fas fa-lightbulb"></i> 핵심 보완 피드백</strong>
            <p>${feedback}</p>
          </div>
        `;
      } else {
        resultPage.style.display = 'none';
        errorMsg.style.display = 'block';
      }
    }
  </script>
</body>
</html>
