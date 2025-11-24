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
                <div class="bg-red-50 p-4 rounded-lg mb-4 text-sm text-red-700 border border-red-200">
                    <p class="font-bold mb-1">🚨 중요 안내: 실제 AI 모드</p>
                    <p>현재 AI 모델을 직접 호출하도록 복구했습니다. 만약 **'403' 인증 오류**가 발생하면 이는 환경 문제이므로, 개발자에게 문의해주세요.</p>
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
                    AI 분석 중... (최대 30초 소요)
                </div>
            </section>

            <!-- 2. AI Analysis Result Section -->
            <section class="lg:col-span-2 card p-6">
                <h2 class="text-2xl font-semibold mb-4 text-gray-700">2. AI 분석 결과 및 개선 가이드</h2>
                <div id="results-container" class="space-y-6">
                    <div id="initial-message" class="text-center text-gray-500 p-8 border border-gray-200 rounded-xl bg-gray-50">
                        여기에 포트폴리오 분석 결과가 나타납니다. 커버 이미지를 업로드하고 분석을 시작해 주세요.
                    </div>
                    <div id="error-message" class="text-center text-red-600 p-4 border border-red-300 bg-red-100 rounded-xl hidden">
                        API 오류: 분석에 실패했습니다. (자세한 내용은 콘솔 확인)
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
            const errorMessage = document.getElementById('error-message');
            const aiAnalysisOutput = document.getElementById('ai-analysis-output');
            const problemOutput = document.getElementById('problem-output');
            const guideOutput = document.getElementById('guide-output');
            const layoutOutput = document.getElementById('layout-output');
            const colorOutput = document.getElementById('color-output');
            const generatedImage = document.getElementById('generated-image');

            let uploadedBase64Image = '';

            const TEXT_MODEL_URL = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent';
            const IMAGE_MODEL_URL = 'https://generativelanguage.googleapis.com/v1beta/models/imagen-4.0-generate-001:predict';

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
                html = html.replace(/<\/ul>\s*<ul>/g, ''); 
                
                // Bold (<strong>)
                html = html.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
                
                // Headings (<h3>)
                html = html.replace(/^### (.*)$/gm, '<h3>$1</h3>');
                
                // Paragraphs and remaining text
                html = html.split('\n').map(line => {
                    if (line.trim() === '' || line.startsWith('<ul>') || line.startsWith('<li') || line.startsWith('<h3')) {
                        return line;
                    }
                    return `<p>${line.trim()}</p>`;
                }).join('');

                return html;
            }

            // 3. 지수 백오프를 사용한 Fetch 함수
            async function fetchWithBackoff(url, options, retries = 3) {
                for (let i = 0; i < retries; i++) {
                    try {
                        const response = await fetch(url, options);
                        if (response.ok) {
                            return response;
                        }

                        // 403 오류 처리 (권한 문제)
                        if (response.status === 403) {
                            const errorText = await response.text();
                            console.error("API Error: 403 - Permission Denied.", errorText);
                            throw new Error(`API 오류: 403 - ${errorText}. API 호출 중 문제가 발생했습니다.`);
                        }
                        
                        // 4xx, 5xx 에러는 재시도 (429 Too Many Requests 대비)
                        const errorBody = await response.text();
                        console.warn(`Attempt ${i + 1} failed with status ${response.status}. Retrying...`, errorBody);
                        
                    } catch (error) {
                        if (i === retries - 1) {
                            console.error("Final API call failed after retries:", error);
                            throw new Error(`API 호출 중 문제가 발생했습니다. (${error.message})`);
                        }
                    }
                    // 지수 백오프 지연 (1s, 2s, 4s...)
                    await new Promise(resolve => setTimeout(resolve, 1000 * Math.pow(2, i)));
                }
                throw new Error("API 호출이 최대 재시도 횟수를 초과했습니다.");
            }


            // 4. AI 텍스트 분석 (Gemini)
            async function analyzePortfolio(base64Image) {
                const systemPrompt = "당신은 건축 및 실내건축 포트폴리오의 품질을 평가하는 세계적인 전문가입니다. 업로드된 이미지를 포트폴리오 표지로 간주하고, 전문적인 시각으로 구조(Story), 레이아웃, 색감의 문제점을 진단하고 구체적인 개선 가이드를 한국어로 Markdown 형식에 맞춰 제공하세요. 특히, 전체 포트폴리오 구조 및 스토리텔링 방향을 제시해야 합니다.";
                
                const userQuery = "이 이미지를 실내건축 포트폴리오 표지로 보고, 디자인 구조 및 스토리, 색감 문제를 진단하고 개선 가이드를 Markdown 섹션으로 나누어 제공하세요. 응답은 오직 Markdown 내용으로만 구성해야 합니다. 섹션은 반드시 '### 문제점 (Critical Issues)', '### 개선 가이드 (Improvement Guide)', '### 표지 레이아웃 제안', '### 색감 조합 제안' 네 가지로 구성되어야 합니다.";

                const payload = {
                    contents: [{
                        parts: [
                            { text: userQuery },
                            {
                                inlineData: {
                                    mimeType: "image/jpeg", // Assume jpeg/png is safe
                                    data: base64Image
                                }
                            }
                        ]
                    }],
                    systemInstruction: {
                        parts: [{ text: systemPrompt }]
                    },
                };

                const response = await fetchWithBackoff(TEXT_MODEL_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                
                const result = await response.json();
                const text = result.candidates?.[0]?.content?.parts?.[0]?.text || "";
                
                if (!text) {
                    console.error("Gemini analysis failed or returned empty response:", result);
                    throw new Error("AI 텍스트 분석 결과가 비어있습니다.");
                }

                return text;
            }

            // 5. AI 이미지 생성 (Imagen)
            async function generateSampleImage(layoutPrompt) {
                const prompt = `실내 건축 포트폴리오 표지 디자인, 제안된 레이아웃 및 색상 컨셉: "${layoutPrompt}". A4 비율, 미니멀하고 전문적인 스타일.`;
                
                const payload = { 
                    instances: [{ prompt: prompt }], 
                    parameters: { "sampleCount": 1 } 
                };

                const response = await fetchWithBackoff(IMAGE_MODEL_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();
                
                const base64Data = result.predictions?.[0]?.bytesBase64Encoded;
                if (!base64Data) {
                    console.error("Imagen generation failed or returned empty response:", result);
                    throw new Error("AI 샘플 이미지 생성에 실패했습니다.");
                }
                
                return `data:image/png;base64,${base64Data}`;
            }

            // 6. 분석 결과 UI 업데이트
            function updateUIWithAnalysis(markdownText) {
                // 마크다운에서 각 섹션 추출
                const sections = {
                    problem: (markdownText.match(/### 문제점 \([\s\S]*?(?=### 개선 가이드)/) || [''])[0].trim(),
                    guide: (markdownText.match(/### 개선 가이드 \([\s\S]*?(?=### 표지 레이아웃)/) || [''])[0].trim(),
                    layout: (markdownText.match(/### 표지 레이아웃 제안[\s\S]*?(?=### 색감 조합)/) || [''])[0].trim(),
                    color: (markdownText.match(/### 색감 조합 제안[\s\S]*/)?.[0] || '').trim()
                };

                problemOutput.innerHTML = `<span class="section-icon">❌</span> <span class="result-title">문제점 및 컨셉 분석</span>` + parseMarkdown(sections.problem);
                guideOutput.innerHTML = `<span class="section-icon">✅</span> <span class="result-title">개선 가이드 및 스토리텔링</span>` + parseMarkdown(sections.guide);
                layoutOutput.innerHTML = `<span class="section-icon">📐</span> <span class="result-title">표지 레이아웃 제안</span>` + parseMarkdown(sections.layout);
                colorOutput.innerHTML = `<span class="section-icon">🎨</span> <span class="result-title">색감 조합 제안</span>` + parseMarkdown(sections.color);
                
                // 이미지 생성에 필요한 레이아웃 제안 텍스트 추출 (첫 번째 문단만 사용)
                const layoutPrompt = (sections.layout.match(/제안\n\s*-\s*(.*)/i) || sections.layout.match(/제안\s*\n*\s*(.*)/i) || [null, '전문적인 포트폴리오 표지 디자인'])[1].trim();
                
                return layoutPrompt;
            }

            // 7. 분석 시작 버튼 클릭 핸들러
            analyzeButton.addEventListener('click', async () => {
                if (!uploadedBase64Image) {
                    alert('이미지를 먼저 업로드해 주세요.');
                    return;
                }
                
                // UI 상태 변경: 로딩 시작 및 초기화
                analyzeButton.disabled = true;
                loadingIndicator.classList.remove('hidden');
                initialMessage.classList.add('hidden');
                aiAnalysisOutput.classList.add('hidden');
                errorMessage.classList.add('hidden');
                generatedImage.src = 'https://placehold.co/400x550/1d4ed8/ffffff?text=Generating...'; // 이미지 로딩 표시

                try {
                    // 1단계: 텍스트 분석 (Gemini)
                    const markdownResult = await analyzePortfolio(uploadedBase64Image);
                    
                    // 2단계: UI 업데이트 및 이미지 생성 프롬프트 추출
                    const layoutPrompt = updateUIWithAnalysis(markdownResult);
                    
                    // 3단계: 이미지 생성 (Imagen)
                    const imageUrl = await generateSampleImage(layoutPrompt);
                    generatedImage.src = imageUrl;

                    // 성공 시 UI 표시
                    aiAnalysisOutput.classList.remove('hidden');

                } catch (error) {
                    console.error("Critical Analysis Error:", error);
                    errorMessage.textContent = `API 오류: 분석에 실패했습니다. (${error.message})`;
                    errorMessage.classList.remove('hidden');
                    // 오류 시 이미지 초기화
                    generatedImage.src = 'https://placehold.co/400x550/e0e0e0/505050?text=Generation+Failed';
                } finally {
                    // UI 상태 변경: 로딩 끝
                    loadingIndicator.classList.add('hidden');
                    analyzeButton.disabled = false;
                }
            });
        });
    </script>
</body>
</html>
