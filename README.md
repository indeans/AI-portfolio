<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI 자동 포트폴리오 분석기</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Inter 폰트 적용 */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght=100..900&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f7f9;
        }
        .loading-animation {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-top: 4px solid #3b82f6;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="p-4 sm:p-8">

    <div class="max-w-6xl mx-auto bg-white rounded-xl shadow-2xl p-6 md:p-10">

        <!-- Header -->
        <header class="mb-8 border-b pb-4">
            <h1 class="text-3xl font-bold text-gray-900 flex items-center">
                <svg class="w-8 h-8 mr-2 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m-5 3h4a2 2 0 002-2V7a2 2 0 00-2-2H9a2 2 0 00-2 2v10a2 2 0 002 2z"></path></svg>
                AI 자동 포트폴리오 분석기
            </h1>
            <p class="text-gray-500 mt-2">건축/인테리어 전공자를 위한 AI 기반 포트폴리오 커버 분석 및 디자인 개선 가이드</p>
            <div class="mt-4 p-3 bg-yellow-50 text-sm text-yellow-700 rounded-lg">
                <!-- Warning about PDF analysis limitations in current version -->
                **안내:** 현재는 **포트폴리오의 커버 페이지(PNG/JPEG)**를 업로드하여 구조, 색감, 레이아웃을 분석합니다. 다중 페이지 PDF 분석은 다음 버전에서 지원될 예정입니다.
            </div>
        </header>

        <!-- Main Content Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <!-- Left Panel: Input & Controls -->
            <div class="lg:col-span-1 space-y-6">
                <section class="p-5 border border-gray-200 rounded-xl bg-gray-50 shadow-sm">
                    <h2 class="text-xl font-semibold text-gray-700 mb-4">1. 포트폴리오 이미지 업로드</h2>
                    
                    <input type="file" id="portfolioFile" accept="image/png, image/jpeg" class="w-full text-sm text-gray-500
                        file:mr-4 file:py-2 file:px-4
                        file:rounded-full file:border-0
                        file:text-sm file:font-semibold
                        file:bg-blue-50 file:text-blue-700
                        hover:file:bg-blue-100
                    ">
                    
                    <div id="filePreviewContainer" class="mt-4 hidden">
                        <p class="text-xs text-gray-500 mb-2">업로드된 커버 페이지 미리보기:</p>
                        <img id="filePreview" src="#" alt="업로드 미리보기" class="w-full max-h-60 object-contain rounded-lg border border-gray-300">
                    </div>

                    <button id="analyzeButton" class="mt-6 w-full flex items-center justify-center bg-blue-600 text-white py-3 px-4 rounded-full text-lg font-bold hover:bg-blue-700 transition duration-150 shadow-md disabled:bg-gray-400" disabled>
                        <span id="analyzeText">AI 분석 시작</span>
                        <div id="loadingSpinner" class="loading-animation ml-3 hidden"></div>
                    </button>
                    <p id="errorMessage" class="text-red-500 text-sm mt-3 hidden"></p>
                </section>
                
                <!-- Prompt Customization (Optional/Advanced) -->
                <section class="p-5 border border-gray-200 rounded-xl bg-gray-50 shadow-sm">
                    <h2 class="text-xl font-semibold text-gray-700 mb-4">2. 추가 분석 요청 (선택 사항)</h2>
                    <textarea id="customPrompt" rows="3" placeholder="예: '친환경 디자인을 강조해 주세요.' 혹은 '모던하고 미니멀한 스타일로 분석해 주세요.'" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500 resize-none text-sm"></textarea>
                </section>

            </div>

            <!-- Right Panel: Results -->
            <div class="lg:col-span-2 space-y-8">
                
                <!-- AI Analysis Result -->
                <section class="p-6 border border-gray-200 rounded-xl shadow-lg bg-white min-h-[300px]">
                    <h2 class="text-2xl font-bold text-blue-600 mb-4 border-b pb-2">AI 포트폴리오 분석 결과</h2>
                    <!-- Removed whitespace-pre-wrap to allow styled HTML structure -->
                    <div id="analysisResult" class="text-gray-700 leading-relaxed">
                        <p class="text-gray-500 italic">여기에 포트폴리오의 구조, 스토리, 구성에 대한 AI 분석 결과가 표시됩니다.</p>
                        <ul class="list-disc list-inside mt-4 text-sm text-gray-500">
                            <li>업로드 후 'AI 분석 시작' 버튼을 눌러주세요.</li>
                            <li>분석에는 약 15~30초가 소요될 수 있습니다.</li>
                        </ul>
                    </div>
                </section>
                
                <!-- Design Improvement Sample -->
                <section class="p-6 border border-gray-200 rounded-xl shadow-lg bg-white">
                    <h2 class="text-2xl font-bold text-green-600 mb-4 border-b pb-2">디자인 개선 샘플 이미지 제안</h2>
                    <div id="imageSuggestionContainer" class="flex flex-col items-center justify-center min-h-[300px] bg-gray-50 rounded-lg p-4">
                        <img id="suggestedImage" src="" alt="개선 샘플 이미지" class="max-w-full max-h-[500px] object-contain rounded-lg shadow-xl border border-gray-300 hidden">
                        <p id="imagePlaceholder" class="text-gray-500 italic">AI 분석 완료 후, 개선 방향을 반영한 디자인 샘플 이미지가 여기에 생성됩니다.</p>
                        <div id="imageLoadingSpinner" class="loading-animation w-10 h-10 hidden mt-4"></div>
                    </div>
                    <p id="imageGenMessage" class="text-sm text-gray-600 mt-3 text-center"></p>
                </section>
            </div>
        </div>
    </div>

    <script>
        // API_KEY를 빈 문자열로 설정하여 Canvas 런타임이 자동으로 API 키를 제공하도록 합니다.
        const API_KEY = ""; 
        
        // API URL에 API_KEY 파라미터를 추가하지 않고, Canvas 런타임이 인증을 처리하도록 합니다.
        const TEXT_MODEL_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${API_KEY}`;
        const IMAGE_MODEL_URL = `https://generativelanguage.googleapis.com/v1beta/models/imagen-4.0-generate-001:predict?key=${API_KEY}`;
        
        const fileInput = document.getElementById('portfolioFile');
        const analyzeButton = document.getElementById('analyzeButton');
        const filePreview = document.getElementById('filePreview');
        const filePreviewContainer = document.getElementById('filePreviewContainer');
        const analysisResultDiv = document.getElementById('analysisResult');
        const loadingSpinner = document.getElementById('loadingSpinner');
        const analyzeText = document.getElementById('analyzeText');
        const errorMessage = document.getElementById('errorMessage');
        const customPrompt = document.getElementById('customPrompt');
        const suggestedImage = document.getElementById('suggestedImage');
        const imagePlaceholder = document.getElementById('imagePlaceholder');
        const imageLoadingSpinner = document.getElementById('imageLoadingSpinner');
        const imageGenMessage = document.getElementById('imageGenMessage');

        let portfolioBase64 = null;
        let lastAnalysisResult = null;
        let isAnalyzing = false;
        let isGeneratingImage = false;

        // --- Utility Functions ---

        /**
         * Converts File object to Base64 string.
         * @param {File} file 
         * @returns {Promise<string>} Base64 data string
         */
        function fileToBase64(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = () => resolve(reader.result.split(',')[1]); // Only the Base64 part
                reader.onerror = error => reject(error);
                reader.readAsDataURL(file);
            });
        }
        
        /**
         * Generic fetch utility with Exponential Backoff
         * @param {string} url 
         * @param {object} options 
         * @param {number} maxRetries 
         * @returns {Promise<object>} JSON response body
         */
        async function fetchWithBackoff(url, options, maxRetries = 5) {
            for (let i = 0; i < maxRetries; i++) {
                try {
                    // API_KEY가 빈 문자열이면 Canvas 런타임이 자동으로 키를 주입합니다.
                    const finalUrl = url; 
                    const response = await fetch(finalUrl, options);

                    if (response.status === 429 && i < maxRetries - 1) {
                        const delay = Math.pow(2, i) * 1000 + Math.random() * 1000;
                        console.log(`Rate limit exceeded. Retrying in ${Math.round(delay / 1000)}s...`);
                        await new Promise(resolve => setTimeout(resolve, delay));
                        continue;
                    }
                    if (!response.ok) {
                        const errorBody = await response.json();
                        throw new Error(`API Error: ${response.status} - ${JSON.stringify(errorBody)}`);
                    }
                    return response.json();
                } catch (error) {
                    if (i === maxRetries - 1) throw error;
                    const delay = Math.pow(2, i) * 1000 + Math.random() * 1000;
                    console.log(`Fetch error. Retrying in ${Math.round(delay / 1000)}s...`);
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }
        }


        // --- UI State Management ---

        function setAnalysisLoading(loading) {
            isAnalyzing = loading;
            analyzeButton.disabled = loading || !portfolioBase64;
            loadingSpinner.classList.toggle('hidden', !loading);
            analyzeText.textContent = loading ? '분석 중...' : 'AI 분석 시작';
        }

        function setImageLoading(loading) {
            isGeneratingImage = loading;
            imageLoadingSpinner.classList.toggle('hidden', !loading);
            imagePlaceholder.classList.toggle('hidden', loading);
            if (loading) {
                suggestedImage.classList.add('hidden');
                imageGenMessage.textContent = "AI가 새로운 디자인 샘플 이미지를 생성 중입니다. (최대 1분 소요)";
            } else {
                imageGenMessage.textContent = "";
            }
            analyzeButton.disabled = loading || isAnalyzing || !portfolioBase64;
        }

        // --- Event Handlers ---

        fileInput.addEventListener('change', async (e) => {
            const file = e.target.files[0];
            if (file) {
                if (file.size > 4 * 1024 * 1024) { // 4MB limit for demonstration
                    errorMessage.textContent = '파일 크기가 너무 큽니다. 4MB 이하의 이미지를 업로드해 주세요.';
                    errorMessage.classList.remove('hidden');
                    analyzeButton.disabled = true;
                    filePreviewContainer.classList.add('hidden');
                    portfolioBase64 = null;
                    return;
                }
                
                try {
                    portfolioBase64 = await fileToBase64(file);
                    filePreview.src = URL.createObjectURL(file);
                    filePreviewContainer.classList.remove('hidden');
                    analyzeButton.disabled = false;
                    errorMessage.classList.add('hidden');
                } catch (error) {
                    errorMessage.textContent = '파일을 읽는 데 오류가 발생했습니다.';
                    errorMessage.classList.remove('hidden');
                    analyzeButton.disabled = true;
                    portfolioBase64 = null;
                }
            } else {
                filePreviewContainer.classList.add('hidden');
                analyzeButton.disabled = true;
                portfolioBase64 = null;
            }
        });

        analyzeButton.addEventListener('click', async () => {
            if (!portfolioBase64 || isAnalyzing || isGeneratingImage) return;

            // 1. Reset results
            analysisResultDiv.innerHTML = '<p class="text-gray-500 italic">AI 분석 중입니다. 잠시만 기다려 주세요...</p>';
            suggestedImage.classList.add('hidden');
            imagePlaceholder.classList.remove('hidden');
            suggestedImage.src = '';
            errorMessage.classList.add('hidden');
            imageGenMessage.textContent = "";
            lastAnalysisResult = null;

            setAnalysisLoading(true);

            try {
                // --- Step 1: AI Text Analysis (Multimodal Input) ---
                const userText = customPrompt.value.trim();
                
                // Define the system instruction to guide the AI persona and output format
                // **전체 구조 분석 항목 포함**
                const systemPrompt = `
                    당신은 세계적인 실내 건축 및 디자인 포트폴리오 리뷰 전문가입니다. 
                    업로드된 포트폴리오 커버 이미지(첫 페이지)를 분석하여 해당 커버가 암시하는 컨셉을 바탕으로 아래의 여섯 가지 항목에 대해 전문적인 한국어 가이드를 Markdown 형식으로 작성해 주세요.

                    1. **문제점 (Problems):** 현재 디자인이 가지고 있는 명확한 문제점(레이아웃, 폰트, 정보 계층, 색상 등)을 3가지 이상 상세하게 진단합니다.
                    2. **개선 가이드 (Improvement Guide):** 문제점을 해결하기 위한 구체적이고 실질적인 개선 방안을 3가지 이상 제안합니다.
                    3. **표지 레이아웃 (Cover Layout Suggestion):** 개선 방향을 반영한 새로운 표지 레이아웃 아이디어를 글로 설명합니다.
                    4. **색감 조합 (Color Scheme Suggestion):** 새로운 디자인에 사용할 주조색, 보조색, 강조색의 헥스 코드를 포함한 전문적인 색감 조합을 제안합니다. (예: 주조색: #FFFFFF, 보조색: #1F2937, 강조색: #3B82F6)
                    5. **전체 페이지 구성 제안 (Overall Page Structure):** 포트폴리오의 내용적 구조(목차 구성, 프로젝트 흐름, 각 프로젝트별 페이지 분배 등)에 대한 구체적인 제안을 합니다.
                    6. **스토리텔링 및 컨셉 분석 (Storytelling & Concept Analysis):** 커버 이미지에서 암시되는 테마를 바탕으로 포트폴리오 전체를 관통하는 설득력 있는 스토리텔링 전략을 제안합니다.

                    **출력 형식은 반드시 아래의 Markdown 헤딩을 포함해야 합니다.**
                    ## 문제점
                    ## 개선 가이드
                    ## 표지 레이아웃
                    ## 색감 조합
                    ## 전체 페이지 구성 제안
                    ## 스토리텔링 및 컨셉 분석
                `;

                const payload = {
                    contents: [
                        {
                            role: "user",
                            parts: [
                                { text: `업로드된 건축/인테리어 포트폴리오 커버 이미지를 분석하고 개선 방향을 제시해 주세요. ${userText ? `추가 요청 사항: ${userText}` : ''}` },
                                {
                                    inlineData: {
                                        mimeType: fileInput.files[0].type,
                                        data: portfolioBase64
                                    }
                                }
                            ]
                        }
                    ],
                    systemInstruction: {
                        parts: [{ text: systemPrompt }]
                    }
                };

                const result = await fetchWithBackoff(TEXT_MODEL_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const textResult = result.candidates?.[0]?.content?.parts?.[0]?.text || '분석 결과를 받아오지 못했습니다.';
                lastAnalysisResult = textResult; // Save result for image generation

                // --- 새로운 분석 결과 파싱 및 스타일링 로직 ---
                const sections = {
                    '문제점': { title: '문제점 (Problems)', color: 'border-red-500 bg-red-50', icon: '<svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.398 16c-.77 1.333.192 3 1.732 3z"/></svg>'},
                    '개선 가이드': { title: '개선 가이드 (Improvement)', color: 'border-green-500 bg-green-50', icon: '<svg class="w-6 h-6 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>'},
                    '표지 레이아웃': { title: '표지 레이아웃 (Layout Idea)', color: 'border-blue-500 bg-blue-50', icon: '<svg class="w-6 h-6 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z"/></svg>'},
                    '색감 조합': { title: '색감 조합 (Color Scheme)', color: 'border-purple-500 bg-purple-50', icon: '<svg class="w-6 h-6 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm0 0h12a2 2 0 002-2v-4a2 2 0 00-2-2h-2.5m-11.25-2L13 7.5l-3.375-3.375m2.25 10.5h.375v.375H13v-.375zm-8.875 1.125l2.25 2.25L10 16.5m-3.375 1.125h.375v.375h-.375v-.375z"/></svg>'},
                    '전체 페이지 구성 제안': { title: '전체 페이지 구성 제안 (Page Structure)', color: 'border-orange-500 bg-orange-50', icon: '<svg class="w-6 h-6 text-orange-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 11a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1v-2zM4 17a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1v-2z"/></svg>'},
                    '스토리텔링 및 컨셉 분석': { title: '스토리텔링 및 컨셉 분석 (Storytelling & Concept)', color: 'border-indigo-500 bg-indigo-50', icon: '<svg class="w-6 h-6 text-indigo-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a2.5 2.5 0 010 3.536l-3.536 3.536a2.5 2.5 0 01-3.536 0L4.464 12.536a2.5 2.5 0 010-3.536 2.5 2.5 0 013.536 0L12 12.036l-.036-.036 3.536-3.536z"/></svg>'}
                };
                
                let htmlOutput = '';
                let lines = textResult.split('\n');
                
                // Simple state machine for parsing
                let currentContent = '';
                let currentHeader = null;

                const renderSection = (headerKey, content) => {
                    if (!headerKey || !content.trim()) return;

                    const sectionInfo = sections[headerKey] || { title: headerKey, color: 'border-gray-500 bg-gray-50', icon: '' };
                    let contentHtml = '';
                    let inList = false;

                    content.split('\n').forEach(line => {
                        const trimmedLine = line.trim();
                        if (!trimmedLine) return;

                        if (trimmedLine.match(/^[*-]\s/)) {
                            // Start or continue list
                            const listItem = `<li>${trimmedLine.replace(/^[*-]\s*/, '')}</li>`;
                            if (!inList) {
                                contentHtml += '<ul class="list-disc list-inside ml-4 space-y-1">';
                                inList = true;
                            }
                            contentHtml += listItem;
                        } else {
                            // Close list and start paragraph
                            if (inList) {
                                contentHtml += '</ul>';
                                inList = false;
                            }
                            // Add content as paragraph, handling strong tags if they are plain text
                            contentHtml += `<p class="mt-2">${trimmedLine.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')}</p>`;
                        }
                    });

                    // Close list if it was open at the end
                    if (inList) {
                        contentHtml += '</ul>';
                    }

                    htmlOutput += `
                        <div class="border-l-4 ${sectionInfo.color} p-4 mb-6 rounded-lg shadow-sm">
                            <h3 class="flex items-center text-lg font-bold text-gray-800 mb-2">
                                ${sectionInfo.icon}
                                <span class="ml-2">${sectionInfo.title}</span>
                            </h3>
                            <div class="text-gray-700 text-sm leading-relaxed">
                                ${contentHtml}
                            </div>
                        </div>
                    `;
                };

                lines.forEach(line => {
                    const trimmedLine = line.trim();
                    if (trimmedLine.startsWith('## ')) {
                        // Render previous section if one was open
                        renderSection(currentHeader, currentContent);
                        
                        // Start new section
                        currentHeader = trimmedLine.substring(3).trim();
                        currentContent = '';
                    } else if (currentHeader) {
                        // Append content to current section
                        currentContent += line + '\n';
                    }
                });

                // Render the last section
                renderSection(currentHeader, currentContent);

                // 최종 결과 표시
                analysisResultDiv.innerHTML = htmlOutput || textResult;
                // --- End New Parsing and Styling Logic ---


                // --- Step 2: AI Image Generation (Text Output from Step 1) ---
                setAnalysisLoading(false); 
                setImageLoading(true);

                // Extract key ideas for image prompt generation
                // Note: Image generation prompt still only uses visual/design elements (Layout & Color Scheme)
                const layoutMatch = textResult.match(/## 표지 레이아웃\n([\s\S]*?)(?=\n##|$)/);
                const colorMatch = textResult.match(/## 색감 조합\n([\s\S]*?)(?=\n##|$)/);
                
                let imagePrompt = "A professional, minimalist architecture/interior design portfolio cover layout concept. ";
                
                if (layoutMatch) {
                    imagePrompt += `Layout description: ${layoutMatch[1].trim()}. `;
                }
                if (colorMatch) {
                    imagePrompt += `Use the following color scheme strictly: ${colorMatch[1].trim()}. `;
                }
                
                imagePrompt += "Focus on clean typography, negative space, and a clear hierarchy. Modern, cinematic lighting, ultra-detailed, 8K, high contrast.";

                const imagePayload = {
                    instances: [{ prompt: imagePrompt }],
                    parameters: { sampleCount: 1 }
                };

                const imageResult = await fetchWithBackoff(IMAGE_MODEL_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(imagePayload)
                });

                const base64Data = imageResult.predictions?.[0]?.bytesBase64Encoded;

                if (base64Data) {
                    suggestedImage.src = `data:image/png;base64,${base64Data}`;
                    suggestedImage.classList.remove('hidden');
                    imagePlaceholder.classList.add('hidden');
                } else {
                    imagePlaceholder.textContent = '이미지 생성에 실패했습니다.';
                }

            } catch (error) {
                console.error("Analysis/Image Generation Error:", error);
                errorMessage.textContent = `오류 발생: ${error.message}. API 호출 중 문제가 발생했습니다.`;
                errorMessage.classList.remove('hidden');
                analysisResultDiv.innerHTML = '<p class="text-red-500 font-semibold">AI 분석 중 치명적인 오류가 발생했습니다. 콘솔을 확인해 주세요.</p>';
                imagePlaceholder.textContent = '이미지 생성에 실패했습니다.';
            } finally {
                setAnalysisLoading(false);
                setImageLoading(false);
            }
        });

    </script>

</body>
</html>
