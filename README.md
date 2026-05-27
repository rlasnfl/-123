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
