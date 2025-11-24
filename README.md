<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>포드백(Podback) - AI 포트폴리오 분석기</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f7fafc; }
        .container { max-width: 1200px; }
        .card { background-color: #ffffff; border-radius: 1rem; box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); }
        .analysis-section { border-left: 4px solid; padding-left: 1rem; margin-top: 1rem; }
        .result-title { font-weight: 600; font-size: 1.125rem; }
        .custom-file-input { border: 2px dashed #cbd5e1; cursor: pointer; transition: border-color 0.3s; }
        .custom-file-input:hover { border-color: #4a5568; }
        .result-box-container { display: grid; grid-template-columns: repeat(2, 1fr); gap: 1rem; }
        @media (max-width: 768px) {
            .result-box-container { grid-template-columns: 1fr; }
        }
        /* Custom styles for structured output */
        .analysis-box { padding: 1rem; border-radius: 0.75rem; margin-bottom: 1rem; }
        .analysis-box ul { list-style-type: disc; margin-left: 1.5rem; padding-left: 0.5rem; }
        .analysis-box li { margin-bottom: 0.5rem; }
        
        /* Specific Styles for each section for visual distinction */
        .problem-area { background-color: #fef2f2; border: 1px solid #fecaca; color: #dc2626; } /* Red for critical issues */
        .improvement-guide { background-color: #f0fdf4; border: 1px solid #dcfce7; color: #15803d; } /* Green for actionable steps */
        .layout-proposal { background-color: #eff6ff; border: 1px solid #bfdbfe; color: #2563eb; } /* Blue for structural proposals */
        .color-palette { background-color: #fdf2f8; border: 1px solid #fce7f3; color: #be185d; } /* Pink/Violet for aesthetic proposals */

        .section-icon { margin-right: 0.5rem; font-size: 1.25rem; vertical-align: middle; }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="container mx-auto">
        <header class="text-center mb-8">
            <h1 class="text-4xl font-extrabold text-gray-800">포드백 (Podback)</h1>
            <p class="text-xl text-gray-600 mt-2">AI 자동 포트폴리오 분석기 (실내건축/건축 전공자용)</p>
        </header>

        <main class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <!-- 1. Upload Section -->
            <section class="lg:col-span-1 card p-6 h-full">
                <h2 class="text-2xl font-semibold mb-4 text-gray-700">1. 포트폴리오 커버 업로드</h2>
                <div class="bg-gray-50 p-4 rounded-lg mb-4 text-sm text-gray-600 border border-gray-200">
                    <p class="font-bold mb-1">💡 중요 안내: 시뮬레이션 모드</p>
                    <p>현재 환경 제약으로 인해 AI 분석 기능이 시뮬레이션으로 작동합니다. 이미지를 업로드하고 '분석 시작' 버튼을 누르면, 전문가 수준의 가이드가 즉시 생성됩니다.</p>
                </div>
                
                <label for="file-upload" class="custom-file-input flex flex-col items-center justify-center p-6 mb-4 text-gray-500 hover:text-gray-700 h-40">
                    <svg class="w-10 h-10 mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12"></path></svg>
                    <span id="file-name" class="text-center">PNG 또는 JPEG 커버 이미지 업로드</span>
                    <input id="file-upload" type="file" accept="image/png, image/jpeg" class="hidden">
                </label>

                <button id="analyze-button" class="w-full bg-blue-600 text-white font-bold py-3 rounded-xl hover:bg-blue-700 transition duration-200 disabled:bg-blue-300" disabled>
                    AI 분석 시작
                </button>
                <div id="loading-indicator" class="mt-4 text-center text-blue-600 hidden">
                    <svg class="animate-spin h-5 w-5 mr-3 inline-block" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                    분석 중... (시뮬레이션)
                </div>
            </section>

            <!-- 2. AI Analysis Result Section -->
            <section class="lg:col-span-2 card p-6">
                <h2 class="text-2xl font-semibold mb-4 text-gray-700">2. AI 분석 결과 및 개선 가이드</h2>
                <div id="results-container" class="space-y-6">
                    <div id="initial-message" class="text-center text-gray-500 p-8 border border-gray-200 rounded-xl bg-gray-50">
                        여기에 포트폴리오 분석 결과가 나타납니다. 커버 이미지를 업로드하고 분석을 시작해 주세요.
                    </div>

                    <!-- AI Generated Content -->
                    <div id="ai-analysis-output" class="hidden">
                        <div class="result-box-container">
                            <div id="problem-output" class="analysis-box problem-area"></div>
                            <div id="guide-output" class="analysis-box improvement-guide"></div>
                        </div>
                        <div class="result-box-container">
                             <div id="layout-output" class="analysis-box layout-proposal"></div>
                            <div id="color-output" class="analysis-box color-palette"></div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 3. Sample Image Section -->
            <section class="lg:col-span-3 card p-6 mt-4">
                <h2 class="text-2xl font-semibold mb-4 text-gray-700">3. 디자인 개선 샘플 이미지 제안</h2>
                <div id="sample-image-container" class="flex flex-col md:flex-row items-center justify-around space-y-4 md:space-y-0 md:space-x-8 p-4 border border-gray-200 rounded-xl bg-gray-50">
                    <div class="flex-1 w-full text-center">
                        <p class="font-medium text-lg mb-2 text-gray-700">현재 커버 (업로드 이미지)</p>
                        <img id="uploaded-image-preview" src="https://placehold.co/400x550/e0e0e0/505050?text=Upload+Image" alt="업로드된 포트폴리오 커버 이미지" class="mx-auto w-full max-w-xs h-auto object-cover rounded-lg shadow-lg border border-gray-300">
                    </div>
                    <div class="flex-1 w-full text-center">
                        <p class="font-medium text-lg mb-2 text-gray-700">AI 제안 개선 샘플</p>
                        <img id="generated-image" src="https://placehold.co/400x550/1d4ed8/ffffff?text=AI+Generated+Sample" alt="AI 생성 개선 샘플 이미지" class="mx-auto w-full max-w-xs h-auto object-cover rounded-lg shadow-lg border border-gray-300">
                    </div>
                </div>
            </section>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const fileUpload = document.getElementById('file-upload');
            const fileNameDisplay = document.getElementById('file-name');
            const analyzeButton = document.getElementById('analyze-button');
            const loadingIndicator = document.getElementById('loading-indicator');
            const uploadedImagePreview = document.getElementById('uploaded-image-preview');
            const initialMessage = document.getElementById('initial-message');
            const aiAnalysisOutput = document.getElementById('ai-analysis-output');
            const problemOutput = document.getElementById('problem-output');
            const guideOutput = document.getElementById('guide-output');
            const layoutOutput = document.getElementById('layout-output');
            const colorOutput = document.getElementById('color-output');
            const generatedImage = document.getElementById('generated-image');

            let uploadedBase64Image = '';

            // 1. 파일 업로드 및 프리뷰
            fileUpload.addEventListener('change', (event) => {
                const file = event.target.files[0];
                if (file) {
                    fileNameDisplay.textContent = file.name;
                    analyzeButton.disabled = false;
                    
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        uploadedBase64Image = e.target.result.split(',')[1];
                        uploadedImagePreview.src = e.target.result;
                    };
                    reader.readAsDataURL(file);
                } else {
                    fileNameDisplay.textContent = 'PNG 또는 JPEG 커버 이미지 업로드';
                    analyzeButton.disabled = true;
                    uploadedImagePreview.src = 'https://placehold.co/400x550/e0e0e0/505050?text=Upload+Image';
                }
            });

            // 2. Markdown 파서 함수
            function parseMarkdown(markdown) {
                // List (<ul>)
                let html = markdown.replace(/^(-|\*|\d+\.)\s+(.*)$/gm, (match, p1, p2) => `<li>${p2.trim()}</li>`);
                html = `<ul>${html}</ul>`;
                html = html.replace(/<\/ul>\s*<ul>/g, ''); // Fix for multiple lists
                
                // Bold (<strong>)
                html = html.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
                
                // Headings (<h3>) - simple, line break based
                html = html.replace(/^### (.*)$/gm, '<h3>$1</h3>');
                
                // Paragraphs and remaining text
                html = html.split('\n').map(line => {
                    if (line.trim() === '' || line.startsWith('<ul>') || line.startsWith('<li')) {
                        return line;
                    }
                    return `<p>${line.trim()}</p>`;
                }).join('');

                return html;
            }

            // 3. 시뮬레이션 데이터 생성 (AI 역할을 대체)
            function generateSimulatedAnalysis() {
                const simulatedMarkdown = `
### 문제점 (Critical Issues)
- **과도한 텍스트 사용**: 표지에 컨셉 설명이 너무 많아 시선이 분산됩니다. 핵심 키워드 3가지 이내로 축약해야 합니다.
- **색상 팔레트 불일치**: 배경의 차가운 파란색과 프로젝트 이미지의 따뜻한 조명이 충돌합니다. 메인 컨셉에 맞는 색상 통일이 필요합니다.
- **수직적 레이아웃의 부재**: 제목, 이름, 이미지가 중앙에 무질서하게 배치되어 시각적 안정감이 부족합니다.
- **전체 페이지 구성**: 프로젝트 5개를 넣었는데, 각 프로젝트당 페이지 수가 불균형하여 포트폴리오의 볼륨감이 일정하지 않습니다.

### 개선 가이드 (Improvement Guide)
- **컨셉 비주얼 강화**: 텍스트 대신 컨셉을 대표하는 강력한 하나의 이미지나 다이어그램을 전면에 배치하세요.
- **그리드 시스템 도입**: 3x3 또는 4x4 그리드를 도입하여 이미지와 텍스트를 규칙적으로 배치하고 여백을 확보해야 합니다.
- **프로젝트 스토리 보강**: 각 프로젝트마다 '문제 정의 - 솔루션 제안 - 결과'의 3단 구성을 명확히 하여 스토리텔링의 깊이를 더하세요.
- **폰트 위계 확립**: 제목, 부제, 본문의 폰트 크기(위계)를 명확히 하여 정보 전달력을 높여야 합니다.

### 표지 레이아웃 제안
- **좌측 정렬(Left-Aligned)** 구성으로 제목의 가시성을 극대화합니다.
- 제목 폰트는 'Noto Sans Bold'를 사용하여 가독성과 현대적인 느낌을 살립니다.
- 배경은 미니멀한 **'웜 그레이(#F5F5F5)'** 단색으로 처리하고, 이미지 영역을 1/3로 제한합니다.

### 색감 조합 제안
- **메인 컬러**: 톤다운된 코발트 블루 (#004C99)
- **보조 컬러**: 웜 톤의 크림색 (#FFFDD0)
- **강조 컬러**: 톤앤톤의 연한 그레이 (#CCCCCC)
- **조합 의도**: 전문성과 신뢰도를 나타내는 블루와, 건축 재료의 따뜻함을 나타내는 크림색의 조화로 균형을 잡습니다.
                `;

                // Markdown을 HTML 구조로 파싱 및 출력
                problemOutput.innerHTML = `<span class="section-icon">❌</span> <span class="result-title">문제점 및 컨셉 분석</span>` + parseMarkdown(simulatedMarkdown.split('### 문제점 (Critical Issues)')[1].split('### 개선 가이드 (Improvement Guide)')[0]);
                guideOutput.innerHTML = `<span class="section-icon">✅</span> <span class="result-title">개선 가이드 및 스토리텔링</span>` + parseMarkdown(simulatedMarkdown.split('### 개선 가이드 (Improvement Guide)')[1].split('### 표지 레이아웃 제안')[0]);
                layoutOutput.innerHTML = `<span class="section-icon">📐</span> <span class="result-title">표지 레이아웃 제안</span>` + parseMarkdown(simulatedMarkdown.split('### 표지 레이아웃 제안')[1].split('### 색감 조합 제안')[0]);
                colorOutput.innerHTML = `<span class="section-icon">🎨</span> <span class="result-title">색감 조합 제안</span>` + parseMarkdown(simulatedMarkdown.split('### 색감 조합 제안')[1]);

                // 시뮬레이션 이미지 생성 (제안된 색상 및 레이아웃 반영)
                const layoutText = '좌측 정렬(Left-Aligned) 구성, 웜 그레이 배경, 코발트 블루 하이라이트, 미니멀 건축 포트폴리오 표지 디자인';
                const simulatedImageUrl = `https://placehold.co/400x550/004C99/FFFDD0?text=${encodeURIComponent('AI 제안: ' + layoutText)}`;
                generatedImage.src = simulatedImageUrl;
            }

            // 4. 분석 시작 버튼 클릭 핸들러
            analyzeButton.addEventListener('click', () => {
                if (!uploadedBase64Image) {
                    alert('이미지를 먼저 업로드해 주세요.');
                    return;
                }

                // UI 상태 변경: 로딩 시작
                analyzeButton.disabled = true;
                loadingIndicator.classList.remove('hidden');
                initialMessage.classList.add('hidden');
                aiAnalysisOutput.classList.add('hidden'); 

                // --- 시뮬레이션 실행 ---
                // 실제 API 호출 대신 2초 지연 후 시뮬레이션 결과 출력
                setTimeout(() => {
                    generateSimulatedAnalysis();
                    
                    // UI 상태 변경: 로딩 끝, 결과 표시
                    loadingIndicator.classList.add('hidden');
                    analyzeButton.disabled = false;
                    aiAnalysisOutput.classList.remove('hidden');
                }, 2000); // 2초 대기
            });
        });
    </script>
</body>
</html>
