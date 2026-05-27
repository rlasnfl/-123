<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>보이스챗 보이스피싱 위험도 미리보기</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>
    <div class="container">
        <header>
            <h1>보이스챗 보이스피싱 위험도 미리보기</h1>
            <p class="description">음성 입력으로 보이스피싱 의심 문장을 감지하고 결과를 바로 확인할 수 있는 공개 웹 페이지입니다.</p>
        </header>

        <section class="control-card">
            <div class="controls">
                <button id="start-voice-button" type="button">음성 입력 시작</button>
                <button id="stop-voice-button" type="button" style="display:none;">음성 인식 종료</button>
                <button id="speak-button" type="button" style="display:none;">결과 음성 듣기</button>
            </div>
            <div class="recognized">
                <label class="note" for="recognized-text">인식된 텍스트(실시간):</label>
                <div id="recognized-text" class="recognized-box"></div>
            </div>
            <p id="voice-status" class="note">음성 인식 지원 여부를 확인 중입니다. 버튼을 누르면 바로 음성 입력이 시작됩니다.</p>
        </section>

        <section id="result-section" class="result" style="display:none;">
            <div class="card">
                <h2>분석 결과</h2>
                <p><strong>총 위험 점수:</strong> <span id="risk-score"></span></p>
                <p><strong>판단:</strong> <span id="risk-label"></span></p>
                <ul id="detail-list" class="detail-list"></ul>
                <div class="graph">
                    <div class="graph-container">
                        <div class="threshold-line"></div>
                        <div class="graph-bar" id="risk-bar"></div>
                    </div>
                    <p id="graph-value"></p>
                </div>
                <p class="note">음성 입력을 마친 뒤 '음성 인식 종료'를 누르면 자동으로 분석됩니다.</p>
            </div>
        </section>

        <div class="footer">
            이 사이트는 GitHub Pages로 공개되어 누구나 접속할 수 있습니다.
        </div>
    </div>

    <script src="script.js"></script>

    
</body>
</html>
body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans KR", Arial, sans-serif;
    background: #f4f6f8;
    color: #222;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 860px;
    margin: 0 auto;
    padding: 28px;
}

header {
    margin-bottom: 24px;
}

h1 {
    margin: 0 0 8px;
    font-size: 2rem;
}

p.description {
    margin: 4px 0 18px;
    color: #555;
    line-height: 1.6;
}

.control-card,
.card {
    background: #fff;
    border-radius: 14px;
    padding: 24px;
    box-shadow: 0 14px 34px rgba(0, 0, 0, 0.08);
    margin-bottom: 24px;
}

.controls {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    margin-top: 14px;
}

button {
    background: #2563eb;
    border: none;
    color: #fff;
    padding: 12px 18px;
    border-radius: 10px;
    font-size: 1rem;
    cursor: pointer;
    transition: background 0.2s ease, transform 0.2s ease;
}

button:hover {
    background: #1d4ed8;
    transform: translateY(-1px);
}

.recognized {
    margin-top: 18px;
}

.recognized-box {
    min-height: 100px;
    background: #f8fafc;
    border: 1px solid #d8dee4;
    border-radius: 12px;
    padding: 14px;
    white-space: pre-wrap;
    line-height: 1.6;
}

.result h2 {
    margin-top: 0;
}

.detail-list {
    list-style: none;
    padding: 0;
    margin: 14px 0 0;
}

.detail-list li {
    margin-bottom: 10px;
}

.graph {
    text-align: center;
    margin-top: 18px;
}

.graph-container {
    position: relative;
    width: 120px;
    height: 240px;
    margin: 0 auto;
    border: 1px solid #d8dee4;
    border-radius: 18px;
    background: #eef2ff;
    display: flex;
    align-items: flex-end;
    justify-content: center;
}

.graph-bar {
    width: 60px;
    border-radius: 12px 12px 0 0;
    background: #34d399;
    transition: height 0.4s ease, background 0.4s ease;
}

.threshold-line {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 40%;
    height: 2px;
    background: #f59e0b;
}

#graph-value {
    margin-top: 10px;
    font-weight: 600;
}

.note {
    color: #6b7280;
    margin-top: 14px;
    font-size: 0.95rem;
}

.footer {
    text-align: center;
    margin-top: 20px;
    color: #6b7280;
    font-size: 0.95rem;
}
