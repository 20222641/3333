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
    
    /* 1. 기본 설정: 쏠림 방지 및 유연한 너비 */
    html, body { 
      margin: 0; 
      padding: 0; 
      width: 100%; 
      max-width: 100%;
      overflow-x: hidden; /* 가로 스크롤 절대 방지 */
    }

    body { font-family: 'Pretendard', sans-serif; background: var(--bg); color: var(--text); }
    
    /* 2. 배너: 꽉 차지만 화면 밖으로 나가지 않게 */
    .hero { 
      background: linear-gradient(135deg, #1e375a 0%, #3498db 100%); 
      color: var(--white); 
      padding: 50px 15px; 
      text-align: center;
      width: 100%;
      box-sizing: border-box; /* 패딩이 너비에 포함되게 */
    }
    
    .hero h1 { margin: 0 0 10px 0; font-size: 1.8em; font-weight: 800; word-break: keep-all; }
    .hero p { margin: 0; font-size: 1em; opacity: 0.9; word-break: keep-all; line-height: 1.4; }
    
    /* 3. 메인 컨텐츠 영역 */
    .container { 
      width: 100%;
      max-width: 900px; 
      margin: 20px auto; 
      padding: 0 15px; 
      box-sizing: border-box; 
    }
    
    /* 탭 메뉴: 모바일에서 버튼이 안 깨지게 */
    .tab-menu { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
    .tab-btn { 
      flex: 1; /* 똑같은 너비로 나눔 */
      background: var(--white); border: 2px solid #ddd; color: #777; 
      padding: 12px 5px; border-radius: 30px; font-size: 0.9em; font-weight: bold; 
      cursor: pointer; transition: 0.3s;
    }
    .tab-btn.active { background: var(--primary); border-color: var(--primary); color: var(--white); }

    /* 탭 내용 */
    .tab-content { display: none; background: var(--white); padding: 20px; border-radius: 15px; box-shadow: 0 5px 20px rgba(0,0,0,0.05); }
    .tab-content.active { display: block; }
    
    /* 구글 폼: 모바일에서 잘리지 않게 100% 설정 */
    .iframe-wrapper { width: 100%; overflow: hidden; border-radius: 8px; }
    iframe { width: 100%; height: 800px; border: none; }

    /* 검색 영역: 가로 너비 꽉 차게 */
    .search-container { text-align: center; padding: 20px; background: #f8f9fa; border-radius: 12px; box-sizing: border-box; }
    .search-input { 
      width: 100%; padding: 15px; font-size: 1em; border: 2px solid #ddd; 
      border-radius: 30px; margin-bottom: 12px; box-sizing: border-box; outline: none;
    }
    .search-btn { 
      width: 100%; padding: 15px; font-size: 1.1em; background: var(--primary); 
      color: white; border: none; border-radius: 30px; cursor: pointer; font-weight: bold; 
    }

    /* 결과 페이지 */
    #myResultPage { 
      display: none; background: #fff; border: 2px solid var(--primary); 
      border-radius: 12px; padding: 20px; margin-top: 20px; word-break: break-all;
    }

    /* 모바일 폰트 조절 */
    @media (max-width: 480px) {
      .hero h1 { font-size: 1.4em; }
      .tab-btn { font-size: 0.85em; }
    }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
</head>
<body>

  <header class="hero">
    <h1>🚀 ANIME-BIZ AI 컨설턴트</h1>
    <p>기획안 제출 및 내 분석 결과 확인</p>
  </header>

  <div class="container">
    <div class="tab-menu">
      <button class="tab-btn active" onclick="openTab('submitTab', this)">기획안 제출</button>
      <button class="tab-btn" onclick="openTab('resultTab', this); fetchSheetData();">결과 확인</button>
    </div>

    <div id="submitTab" class="tab-content active">
      <div class="iframe-wrapper">
        <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSfGfqgT9i3M97qU-rwgqz7jklNgUnA-WernWtEOPq825fGNDQ/viewform?embedded=true">로드 중...</iframe>
      </div>
    </div>

    <div id="resultTab" class="tab-content">
      <div class="search-container">
        <h3>내 기획안 검색</h3>
        <input type="text" id="searchInput" class="search-input" placeholder="기획안 제목을 입력하세요" onkeypress="handleEnter(event)">
        <button class="search-btn" onclick="searchMyResult()">결과 보기</button>
      </div>
      <div id="myResultPage"></div>
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
      if (!searchTitle) return alert("제목을 입력하세요!");

      let myData = allData.find(row => row['기획안 제목'] && row['기획안 제목'].trim() === searchTitle);

      if (myData) {
        resultPage.style.display = 'block';
        resultPage.innerHTML = `
          <h3 style="text-align:center; color:var(--primary);">${myData['기획안 제목']}</h3>
          <div style="background:var(--secondary); color:#fff; text-align:center; padding:8px; border-radius:20px; font-weight:bold; margin:15px 0;">🎯 예상 성공률: ${myData['AI 예상 성공 확률'] || '분석 중'}</div>
          <div>
            <strong>🤖 AI 평가</strong><p style="background:#f8f9fa; padding:10px; border-radius:5px; font-size:0.9em;">${myData['AI 구조화 평가'] || '분석 중'}</p>
            <strong>💡 보완점</strong><p style="background:#f8f9fa; padding:10px; border-radius:5px; font-size:0.9em;">${myData['AI 핵심 보완 피드백'] || '분석 중'}</p>
          </div>`;
      } else { alert("검색 결과가 없습니다."); }
    }
  </script>
</body>
</html>
