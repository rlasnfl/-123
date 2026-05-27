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





const SAMPLE_TEXT = "안녕하세요. 검찰 수사관입니다. 귀하의 계좌에서 이상 거래가 발견되어 지금 즉시 송금해 주셔야 합니다. 이 사실은 누구에게도 말하지 마세요.";
const RISK_WEIGHTS = {
    "검찰": 2.0,
    "수사관": 2.0,
    "계좌": 2.0,
    "송금": 2.0,
    "지금 즉시": 1.5,
    "오늘까지": 1.5,
    "비밀": 1.5,
    "누구에게도 말하지 마세요": 3.0,
    "이체": 2.0,
    "구속": 1.5,
    "수사": 1.5,
    "발설": 1.5,
    "말하지": 1.5,
};
const LEGAL_KEYWORDS = ["검찰", "수사관", "계좌", "송금"];
const URGENT_KEYWORDS = ["지금 즉시", "오늘까지", "비밀"];
const ISOLATE_PHRASE = "누구에게도 말하지 마세요";
const RISK_THRESHOLD = 10.0;

const statusEl = document.getElementById('voice-status');
const speakButton = document.getElementById('speak-button');
const startButton = document.getElementById('start-voice-button');
const stopButton = document.getElementById('stop-voice-button');
const recognizedTextEl = document.getElementById('recognized-text');
const resultSection = document.getElementById('result-section');
const riskScoreEl = document.getElementById('risk-score');
const riskLabelEl = document.getElementById('risk-label');
const detailListEl = document.getElementById('detail-list');
const riskBarEl = document.getElementById('risk-bar');
const graphValueEl = document.getElementById('graph-value');

let recognition = null;
let latestResultText = '';
let userStopped = false;
let isRecognizing = false;
let lastAnalysis = null;

function normalizeText(text) {
    return text.trim().replace(/\s+/g, ' ').toLowerCase();
}

function calculateRiskScore(text) {
    const normalized = normalizeText(text);
    const detailScores = {};
    let totalScore = 0.0;

    Object.keys(RISK_WEIGHTS).forEach((phrase) => {
        const weight = RISK_WEIGHTS[phrase];
        let count = 0;

        if (phrase.length > 0) {
            const escaped = phrase.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
            const regex = new RegExp(escaped, 'g');
            const matches = normalized.match(regex);
            count = matches ? matches.length : 0;
        }

        if (count === 0 && phrase.length <= 4 && normalized.includes(phrase)) {
            count = 1;
        }

        if (count > 0) {
            detailScores[phrase] = count * weight;
            totalScore += detailScores[phrase];
        }
    });

    const sentences = normalized.split(/[.!?]\s*|\n+/).filter(Boolean);
    sentences.forEach((sentence) => {
        const hasLegal = LEGAL_KEYWORDS.some((keyword) => sentence.includes(keyword));
        const hasUrgent = URGENT_KEYWORDS.some((keyword) => sentence.includes(keyword));
        const hasIsolate = sentence.includes(ISOLATE_PHRASE);

        if (hasLegal && hasUrgent) totalScore += 2.0;
        if (hasLegal && hasIsolate) totalScore += 3.0;
        if (hasUrgent && hasIsolate) totalScore += 2.5;
    });

    const label = totalScore > RISK_THRESHOLD
        ? '이 전화는 보이스피싱 가능성이 매우 높습니다'
        : '보이스피싱일 가능성이 낮습니다';

    return { score: totalScore, label, detailScores };
}

function renderResult({ score, label, detailScores }) {
    resultSection.style.display = 'block';
    riskScoreEl.textContent = score.toFixed(1);
    riskLabelEl.textContent = label;
    detailListEl.innerHTML = '';

    Object.entries(detailScores)
        .filter(([, value]) => value > 0)
        .forEach(([phrase, value]) => {
            const li = document.createElement('li');
            li.textContent = `${phrase}: ${value.toFixed(1)}`;
            detailListEl.appendChild(li);
        });

    const maxScore = Math.max(15, score * 1.1, RISK_THRESHOLD);
    const heightPercent = Math.min(100, (score / maxScore) * 100);
    riskBarEl.style.height = `${Math.max(12, heightPercent)}%`;
    riskBarEl.style.background = score > RISK_THRESHOLD ? '#ef4444' : '#34d399';
    graphValueEl.textContent = `${score.toFixed(1)}점 (${Math.round(heightPercent)}%)`;
    speakButton.style.display = 'inline-block';
}

function supportsSpeechRecognition() {
    return !!(window.SpeechRecognition || window.webkitSpeechRecognition);
}

function initVoiceStatus() {
    if (supportsSpeechRecognition()) {
        statusEl.textContent = '음성 인식을 사용할 수 있습니다. 버튼을 눌러 말해주세요.';
    } else {
        statusEl.textContent = '이 브라우저에서는 음성 인식이 지원되지 않습니다.';
    }
}

function startVoiceInput() {
    if (!supportsSpeechRecognition()) {
        statusEl.textContent = '이 브라우저에서는 음성 인식이 지원되지 않습니다.';
        return;
    }

    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    recognition = new SpeechRecognition();
    recognition.lang = 'ko-KR';
    recognition.interimResults = true;
    recognition.continuous = true;
    recognition.maxAlternatives = 1;

    userStopped = false;
    isRecognizing = true;
    startButton.disabled = true;
    stopButton.style.display = 'inline-block';
    statusEl.textContent = '음성 인식 중... 말을 계속하실 수 있습니다. 멈추려면 음성 인식 종료 버튼을 누르세요.';
    recognizedTextEl.textContent = '';
    latestResultText = '';

    recognition.onresult = (event) => {
        let transcript = '';
        for (let i = event.resultIndex; i < event.results.length; i += 1) {
            transcript += event.results[i][0].transcript;
        }
        transcript = transcript.trim();
        latestResultText = transcript;
        recognizedTextEl.textContent = transcript || SAMPLE_TEXT;
        statusEl.textContent = '음성 인식 중... (중지 버튼을 누르면 분석을 실행합니다.)';
    };

    recognition.onerror = (event) => {
        statusEl.textContent = '음성 인식 중 오류가 발생했습니다: ' + event.error;
        isRecognizing = false;
        startButton.disabled = false;
        stopButton.style.display = 'none';
    };

    recognition.onend = () => {
        isRecognizing = false;
        startButton.disabled = false;
        stopButton.style.display = 'none';

        if (userStopped) {
            submitAnalysis();
        } else {
            statusEl.textContent = '음성 인식이 중단되었습니다. 다시 시도해 주세요.';
        }
    };

    recognition.start();
}

function stopVoiceInput() {
    if (recognition && isRecognizing) {
        userStopped = true;
        statusEl.textContent = '음성 인식을 종료하는 중입니다...';
        recognition.stop();
    } else {
        statusEl.textContent = '현재 음성 인식이 실행 중이 아닙니다.';
    }
}

function submitAnalysis() {
    const text = latestResultText || SAMPLE_TEXT;
    lastAnalysis = calculateRiskScore(text);
    renderResult(lastAnalysis);
    statusEl.textContent = '분석이 완료되었습니다.';
}

function speakResult() {
    if (!lastAnalysis) {
        statusEl.textContent = '먼저 분석을 완료한 뒤 결과를 음성으로 들을 수 있습니다.';
        return;
    }
    const utterance = new SpeechSynthesisUtterance(`총 위험 점수는 ${lastAnalysis.score.toFixed(1)}점입니다. ${lastAnalysis.label}`);
    utterance.lang = 'ko-KR';
    window.speechSynthesis.speak(utterance);
}

startButton.addEventListener('click', startVoiceInput);
stopButton.addEventListener('click', stopVoiceInput);
speakButton.addEventListener('click', speakResult);
initVoiceStatus();
