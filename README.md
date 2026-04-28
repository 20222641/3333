<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>지역 활성화 기획안 AI 분석 시스템</title>
    <style>
        /* 웹사이트 디자인 (CSS) */
        body { 
            font-family: 'Pretendard', 'Malgun Gothic', sans-serif; 
            background-color: #0f0f15; 
            color: #fff; 
            margin: 0; 
            padding: 10px; /* 모바일에서 좌우 여백 확보 */
        }
        .container { 
            max-width: 800px; 
            margin: 0 auto; 
            background-color: #1a1a24; 
            padding: 20px; 
            border-radius: 12px; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.5);
        }
        h1 { text-align: center; color: #4a90e2; font-size: 1.5rem; }
        
        /* 구글 폼 영역 - 모바일에서도 꽉 차게 조절 */
        .form-section { margin-top: 20px; width: 100%; }
        .form-section iframe { 
            width: 100%; 
            height: 800px; /* 높이는 넉넉히 */
            border: none; 
            border-radius: 8px; 
            background-color: #f0f2f5; 
        }

        /* 버튼 디자인 */
        .btn-group { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .btn { 
            padding: 10px 20px; 
            border-radius: 20px; 
            border: none; 
            cursor: pointer; 
            font-weight: bold;
            font-size: 14px;
        }
        .btn-primary { background-color: #007bff; color: white; }
        .btn-secondary { background-color: #444; color: #ccc; }
    </style>
</head>
<body>

    <div class="container">
        <h1>🚀 ANIME-BIZ AI 컨설턴트</h1>
        <p style="text-align: center; font-size: 0.9rem; color: #aaa;">기획안 제출 및 AI 분석 결과 확인 시스템</p>

        <div class="btn-group">
            <button class="btn btn-primary" onclick="location.href='#form'">📝 기획안 제출</button>
            <button class="btn btn-secondary">🔍 분석 결과 찾기</button>
        </div>

        <div id="form" class="form-section">
            <h2 style="font-size: 1.1rem; border-left: 4px solid #007bff; padding-left: 10px;">1. 기획안 제출하기</h2>
            <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSfGfqgT9i3M97qU-rwgqz7jklNgUnA-WernWtEOf/viewform?embedded=true">로딩 중...</iframe>
        </div>
    </div>

</body>
</html>
