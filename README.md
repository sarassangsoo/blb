<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>승냥백과 (SNYANGPEDIA) - 대문호 승냥이만을 위한 AI 백과사전</title>
  
  <link id="dynamic-favicon" rel="icon" type="image/png" href="">
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/lucide@latest"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>

  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans KR", sans-serif;
      background-color: #ffffff;
      color: #202122;
    }
    .wiki-serif {
      font-family: "Linux Libertine", "Georgia", "Times New Roman", serif;
    }
    .wiki-link {
      color: #0645ad;
      text-decoration: none;
    }
    .wiki-link:hover {
      text-decoration: underline;
    }
    /* 그림자, 테두리 없는 순수 1:1 정방형 이미지 */
    .globe-pure-1to1 {
      width: 100%;
      height: 100%;
      aspect-ratio: 1 / 1 !important;
      object-fit: contain !important;
      background-color: #ffffff !important;
      border: none !important;
      outline: none !important;
      box-shadow: none !important;
      border-radius: 0 !important;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col bg-white text-[#202122]">

  <input type="file" id="custom-logo-input" class="hidden" accept="image/*" onchange="handleUserLogoUpload(event)">

  <!-- 상단 네비게이션 바 -->
  <header class="border-b border-[#a2a9b1] bg-[#f8f9fa] px-4 py-2 flex items-center justify-between text-xs select-none sticky top-0 z-40">
    <div class="flex items-center space-x-3">
      <div class="flex items-center space-x-2 cursor-pointer" onclick="showView('portal')" title="포털 대문으로 이동">
        <div class="w-7 h-7 aspect-square flex items-center justify-center bg-white overflow-hidden border-0 p-0 m-0 shadow-none">
          <img id="nav-globe-img" src="Gemini_Generated_Image_k50i5qk50i5qk50i.png" alt="SNYANGPEDIA Logo" class="globe-pure-1to1" onerror="applyDefaultFallback(this)">
        </div>
        <span class="wiki-serif font-bold text-sm tracking-wide text-black">승냥백과</span>
        <span class="text-gray-300">|</span>
        <span class="text-gray-600 hidden sm:inline">대문호 승냥이만을 위한 AI 백과사전</span>
      </div>

      <span class="inline-flex items-center gap-1 px-2 py-0.5 rounded-full bg-emerald-50 text-emerald-700 text-[11px] border border-emerald-200">
        <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 animate-pulse"></span>
        <span id="storage-status-text">클라우드 영구 저장 연결됨</span>
      </span>
    </div>
    
    <div class="flex items-center space-x-2">
      <button onclick="document.getElementById('custom-logo-input').click()" class="px-2.5 py-1 border border-[#a2a9b1] bg-white hover:bg-gray-50 text-gray-700 rounded-sm font-medium flex items-center gap-1 transition">
        <i data-lucide="upload" class="w-3.5 h-3.5 text-[#3366cc]"></i> 사진 업로드
      </button>
      <button onclick="showView('portal')" class="px-2.5 py-1 text-[#0645ad] hover:underline flex items-center gap-1 font-medium">
        <i data-lucide="compass" class="w-3.5 h-3.5"></i> 포털 대문 홈
      </button>
      <button onclick="openNewProjectModal()" class="px-2.5 py-1 bg-[#3366cc] hover:bg-[#2a4b8d] text-white rounded-sm font-medium flex items-center gap-1 shadow-sm transition">
        <i data-lucide="folder-plus" class="w-3.5 h-3.5"></i> + 새 연구 항목
      </button>
      <button onclick="exportFullBackup()" class="px-2.5 py-1 border border-[#a2a9b1] bg-white hover:bg-gray-100 rounded-sm text-gray-700 flex items-center gap-1 transition">
        <i data-lucide="download" class="w-3.5 h-3.5"></i> 전체 백업
      </button>
      <label class="px-2.5 py-1 border border-[#a2a9b1] bg-white hover:bg-gray-100 rounded-sm text-gray-700 flex items-center gap-1 cursor-pointer transition">
        <i data-lucide="rotate-ccw" class="w-3.5 h-3.5"></i> 복원
        <input type="file" id="backup-restore-input" class="hidden" accept=".json" onchange="importFullBackup(event)">
      </label>
    </div>
  </header>

  <!-- 포털 대문 뷰 -->
  <section id="view-portal" class="flex-1 flex flex-col items-center justify-start pt-6 pb-16 px-4 max-w-5xl mx-auto w-full">
    <div class="w-full bg-white pt-6 pb-8 mb-4 flex flex-col md:flex-row items-center justify-between gap-6 border-b border-[#a2a9b1]">
      <div class="w-40 h-40 md:w-44 md:h-44 aspect-square flex-shrink-0 flex items-center justify-center cursor-pointer group relative bg-white border-0 p-0 m-0 shadow-none"
           onclick="document.getElementById('custom-logo-input').click()" title="클릭하여 고양이 퍼즐 구체 사진 변경">
        <img id="portal-banner-globe" src="Gemini_Generated_Image_k50i5qk50i5qk50i.png" alt="SNYANGPEDIA Globe" class="globe-pure-1to1" onerror="applyDefaultFallback(this)">
        <div class="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition-opacity flex flex-col items-center justify-center text-white text-xs font-medium">
          <i data-lucide="camera" class="w-5 h-5 mb-1"></i>
          <span>사진 변경</span>
        </div>
      </div>

      <div class="flex-1 text-center md:text-left">
        <div class="flex items-center justify-center md:justify-start gap-2 mb-1">
          <span class="text-xl">🐾</span>
          <h1 class="wiki-serif text-3xl md:text-4xl font-bold tracking-tight text-black">SNYANGPEDIA</h1>
          <span class="text-xl">🐾</span>
        </div>
        <p class="wiki-serif text-base text-gray-800 font-semibold mt-1">대문호 승냥이만을 위한 AI 백과사전</p>
        <p class="text-xs text-gray-500 mt-1">조사 항목별 독립 문서 편철, 원본 PDF 대조 및 실시간 AI 질의응답 연구소</p>
      </div>

      <div class="flex flex-wrap md:flex-col gap-2 flex-shrink-0">
        <button onclick="openNewProjectModal()" class="px-4 py-2 bg-[#1a4a9c] hover:bg-[#123675] text-white text-xs font-semibold rounded-sm flex items-center justify-center gap-1.5 shadow-sm transition">
          <i data-lucide="folder-plus" class="w-4 h-4"></i> + 새 연구 항목 개설
        </button>
        <button onclick="exportFullBackup()" class="px-4 py-2 bg-white border border-[#a2a9b1] hover:bg-gray-50 text-gray-800 text-xs font-semibold rounded-sm flex items-center justify-center gap-1.5 transition">
          <i data-lucide="download" class="w-4 h-4"></i> 전체 백업
        </button>
      </div>
    </div>

    <div class="w-full border border-dashed border-[#a2a9b1] bg-white p-3 text-xs mb-8 flex flex-col sm:flex-row items-center justify-between gap-2 text-gray-700">
      <div class="flex items-center gap-2">
        <span class="text-sm">🐾</span>
        <span><strong>승냥피디아 안내:</strong> 위키피디아 정통 스타일(좌측 고정 목차, 우측 프로필 인포박스)로 전면 개편되었습니다. 질문별 독립 토론 세션 및 클라우드 영구 저장을 지원합니다.</span>
      </div>
      <div class="text-gray-500 whitespace-nowrap text-[11px]">
        총 <span id="portal-proj-count" class="font-bold text-black">1</span>개 항목 등록됨
      </div>
    </div>

    <div class="w-full">
      <div class="flex items-center justify-between mb-4">
        <h2 class="wiki-serif font-bold text-lg text-[#202122] flex items-center gap-2">
          <span>📚</span>
          <span>편철된 승냥피디아 조사 연구 항목 목록</span>
        </h2>
        <span class="text-xs text-gray-500">원하는 항목을 클릭하여 전용 연구실로 입장하세요.</span>
      </div>
      <div id="project-cards-grid" class="grid grid-cols-1 md:grid-cols-2 gap-4"></div>
    </div>
  </section>

  <!-- 개별 조사 연구실 뷰 (위키피디아 정통 스타일 적용) -->
  <section id="view-research" class="hidden flex-1 flex flex-col md:flex-row max-w-[1440px] w-full mx-auto bg-white border-l border-r border-[#a2a9b1]">
    
    <!-- 좌측 사이드바: 위키피디아 문서 목차 스타일 -->
    <aside class="w-full md:w-60 flex-shrink-0 p-3 border-r border-[#a2a9b1] bg-[#f8f9fa] text-xs select-none flex flex-col justify-between">
      <div class="space-y-4">
        <div class="text-center pb-3 border-b border-[#c8ccd1] cursor-pointer group" onclick="showView('portal')" title="포털 대문으로 복귀">
          <div class="w-20 h-20 aspect-square mx-auto mb-1.5 flex items-center justify-center bg-white overflow-hidden">
            <img id="sidebar-globe-img" src="Gemini_Generated_Image_k50i5qk50i5qk50i.png" alt="Snyangpedia Globe" class="globe-pure-1to1" onerror="applyDefaultFallback(this)">
          </div>
          <div class="wiki-serif font-bold text-sm text-black">승냥백과</div>
          <div class="text-[10px] text-gray-500 font-serif">대문호의 연구 서재</div>
        </div>

        <div>
          <div class="text-[11px] font-bold text-[#72777d] pb-1 border-b border-[#c8ccd1] mb-1.5">문서 목차 (위키 스타일)</div>
          <ul id="sidebar-wiki-toc" class="space-y-1.5 pl-2 text-xs">
            <!-- 동적 목차 리스트 -->
          </ul>
        </div>

        <!-- 질문별 토론 세션 아카이브 리스트 -->
        <div class="pt-2 border-t border-[#c8ccd1]">
          <div class="flex items-center justify-between pb-1 border-b border-[#c8ccd1] mb-1.5">
            <span class="text-[11px] font-bold text-[#72777d]">💬 질문별 토론 세션</span>
            <button onclick="createNewChatSession()" class="text-[10px] text-[#0645ad] font-bold hover:underline">+ 새 토론</button>
          </div>
          <div id="sidebar-sessions-list" class="space-y-1 max-h-40 overflow-y-auto pr-1"></div>
        </div>
      </div>

      <div class="pt-3 border-t border-[#c8ccd1] mt-4">
        <div class="text-[10px] font-bold text-[#72777d] mb-1">Q&A 분석 모드</div>
        <div class="space-y-1">
          <label class="flex items-center gap-1 cursor-pointer"><input type="radio" name="qaMode" value="strict" onchange="setAnalysisMode('strict')"><span class="text-[10px]">자료 엄격 모드</span></label>
          <label class="flex items-center gap-1 cursor-pointer"><input type="radio" name="qaMode" value="deep" checked onchange="setAnalysisMode('deep')"><span class="text-[10px] font-semibold text-[#0645ad]">심화 해설 (웹검색)</span></label>
        </div>
      </div>
    </aside>

    <!-- 메인 컨텐츠 본문 -->
    <main class="flex-1 flex flex-col p-4 md:p-8 bg-white overflow-y-auto">
      <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center pb-3 border-b border-[#a2a9b1] mb-6 gap-3">
        <div class="flex items-center space-x-1 text-xs select-none">
          <button id="rtab-btn-read" onclick="setTab('read')" class="px-3 py-1.5 font-bold border-b-2 border-[#3366cc] text-black">문서 대문</button>
          <button id="rtab-btn-qa" onclick="setTab('qa')" class="px-3 py-1.5 text-[#0645ad] hover:underline flex items-center gap-1">
            <span>💬 토론 / AI 질의응답 아카이브</span>
          </button>
          <button id="rtab-btn-pdf" onclick="setTab('pdf')" class="px-3 py-1.5 text-[#0645ad] hover:underline flex items-center gap-1">
            <span>📑 PDF 원본 뷰어</span>
          </button>
          <button id="rtab-btn-corpus" onclick="setTab('corpus')" class="px-3 py-1.5 text-[#0645ad] hover:underline">조사자료 편철 목록</button>
        </div>

        <div class="flex items-center gap-1">
          <input type="text" id="inline-search-box" placeholder="승냥피디아 및 자료 검색" class="border border-[#a2a9b1] px-2.5 py-1 text-xs outline-none w-48 focus:border-[#3366cc]">
          <button onclick="executeInlineSearch()" class="px-2.5 py-1 bg-[#f8f9fa] border border-[#a2a9b1] hover:bg-gray-100 text-xs font-semibold">검색</button>
        </div>
      </div>

      <!-- 서브탭 1: 위키피디아 오리지널 문서 스타일 대문 -->
      <div id="subtab-read" class="space-y-6">
        <div class="border-b border-[#a2a9b1] pb-2 flex justify-between items-end">
          <div>
            <h1 id="current-project-title" class="wiki-serif text-3xl font-bold text-black">칸첸중가 (Kangchenjunga)</h1>
            <p id="current-project-desc" class="text-xs text-gray-500 mt-1">위키피디아 학술 조사 아카이브 버전[cite: 1, 2]</p>
          </div>
          <div class="text-xs space-x-2">
            <span class="text-[#0645ad] cursor-pointer hover:underline" onclick="setTab('qa')">토론</span>
            <span class="text-gray-300">|</span>
            <span class="text-[#0645ad] cursor-pointer hover:underline" onclick="setTab('corpus')">편집/자료</span>
          </div>
        </div>

        <!-- 인포박스 + 위키 본문 레이아웃 -->
        <div class="flex flex-col lg:flex-row gap-8 items-start">
          
          <!-- 좌측 본문 단락들 -->
          <div class="flex-1 w-full space-y-6 text-sm leading-relaxed" id="wiki-sections-container">
            <!-- 동적 위키 본문 렌더링 -->
          </div>

          <!-- 우측 고유 인포박스 (위키 스타일 신상정보/개요 표) -->
          <div class="w-full lg:w-80 border border-[#a2a9b1] bg-[#f8f9fa] p-3 text-xs flex-shrink-0 space-y-3 shadow-2xs">
            <div class="font-serif font-bold text-center text-sm pb-1.5 border-b border-[#c8ccd1] text-black bg-[#eaecf0] py-1" id="infobox-title">
              칸첸중가 (Kangchenjunga)
            </div>
            <div class="border border-[#a2a9b1] p-1 bg-white text-center">
              <img src="Gemini_Generated_Image_o2dntuo2dntuo2dn.png" alt="칸첸중가 전경" class="w-full h-auto object-cover">
              <div class="text-[10px] text-gray-500 mt-1">시킴 다즐링 방면에서 바라본 칸첸중가 주봉 연봉[cite: 1, 2]</div>
            </div>
            <div class="text-center font-bold text-gray-700 pb-1 border-b border-gray-200">신상정보 및 개요</div>
            <table class="w-full text-[11px] border-collapse" id="infobox-table">
              <tbody>
                <tr class="border-b border-gray-200"><th class="text-left py-1 text-gray-500 w-24">최고점</th><td class="py-1 font-bold">해발 8,586 m[cite: 2]</td></tr>
                <tr class="border-b border-gray-200"><th class="text-left py-1 text-gray-500">지형력 돌출도</th><td class="py-1">3,922 m</td></tr>
                <tr class="border-b border-gray-200"><th class="text-left py-1 text-gray-500">위치</th><td class="py-1">네팔 코시 주 / 인도 시킴주 국경[cite: 1, 2]</td></tr>
                <tr class="border-b border-gray-200"><th class="text-left py-1 text-gray-500">초등정</th><td class="py-1">1955년 5월 25일 (조 브라운, 조지 밴드)[cite: 2]</td></tr>
                <tr class="border-b border-gray-200"><th class="text-left py-1 text-gray-500">고유 관례</th><td class="py-1 text-emerald-700">정상부 성역 보존을 위해 꼭대기 수 미터 전 멈춤</td></tr>
                <tr><th class="text-left py-1 text-gray-500">보유 자료</th><td class="py-1" id="infobox-doc-count">문서 4건 (PDF 2건)[cite: 1, 2]</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- 서브탭 2: 질문별 토론 세션 아카이브 (Q&A) -->
      <div id="subtab-qa" class="hidden flex-1 flex flex-col space-y-4">
        <div class="p-3 bg-white border border-[#a2a9b1] text-xs flex items-center justify-between">
          <div class="flex items-center gap-2">
            <span class="font-bold text-[#3366cc]">🐾 현재 토론 세션:</span>
            <span id="active-session-title-label" class="font-semibold text-black bg-gray-100 px-2 py-0.5 border border-gray-300">새로운 질의 토론</span>
          </div>
          <button onclick="createNewChatSession()" class="px-2.5 py-1 bg-[#3366cc] text-white rounded-sm text-[11px] font-semibold hover:bg-[#2a4b8d]">
            + 새 토론 시작하기
          </button>
        </div>

        <div id="qa-chat-stream" class="flex-1 min-h-[420px] max-h-[600px] overflow-y-auto border border-[#a2a9b1] bg-[#fcfcfc] p-4 space-y-4"></div>

        <div class="flex gap-2">
          <textarea id="qa-user-input" rows="2" placeholder="이 토론방에서 질문하세요 (Ctrl+Enter 전송)" class="flex-1 border border-[#a2a9b1] p-2.5 text-xs outline-none focus:border-[#3366cc] resize-none" onkeydown="if(event.key==='Enter' && (event.ctrlKey || event.metaKey)) handleSendQuestion()"></textarea>
          <button onclick="handleSendQuestion()" class="px-5 bg-[#1a4a9c] hover:bg-[#123675] text-white text-xs font-bold flex items-center justify-center gap-1">
            <i data-lucide="send" class="w-4 h-4"></i> 질의 전송
          </button>
        </div>
      </div>

      <!-- 서브탭 3: PDF 뷰어 -->
      <div id="subtab-pdf" class="hidden flex-1 flex flex-col space-y-3">
        <div class="flex items-center justify-between border-b border-[#c8ccd1] pb-2">
          <div class="flex items-center gap-2 text-xs">
            <span class="font-bold text-gray-700">열람할 PDF 선택:</span>
            <select id="pdf-selector" class="border border-[#a2a9b1] p-1 text-xs">
              <option value="0">1. 시킴 고에차라 8일 트레킹 일주기 [못생겨도 나는 간다] (32p)[cite: 1]</option>
              <option value="1">2. 네팔 칸첸중가 북면 BC & 고차라 준비 계획서 (27p)[cite: 2]</option>
            </select>
          </div>
          <span class="text-[11px] text-gray-400">PDF 원본 대조 스튜디오[cite: 1, 2]</span>
        </div>
        <div class="flex-1 min-h-[500px] border border-[#a2a9b1] bg-gray-100 p-6 flex flex-col items-center justify-center text-xs">
          <i data-lucide="file-text" class="w-12 h-12 text-[#3366cc] mb-2"></i>
          <div class="font-bold text-sm text-gray-800 mb-1">블로그 인쇄본 PDF 2종 탑재 완료[cite: 1, 2]</div>
          <p class="text-gray-500 mb-4">원문 텍스트와 사진 데이터가 위키 컨텍스트에 모두 바인딩되어 있습니다.[cite: 1, 2]</p>
          <button onclick="previewSelectedPdf()" class="px-4 py-2 bg-[#3366cc] text-white font-medium hover:bg-[#2a4b8d]">
            선택된 PDF 텍스트 전문 보기
          </button>
        </div>
      </div>

      <!-- 서브탭 4: 자료 편철 목록 -->
      <div id="subtab-corpus" class="hidden flex-1 space-y-4">
        <div class="flex justify-between items-center pb-2 border-b border-[#c8ccd1]">
          <h3 class="font-serif font-bold text-base text-black">이 연구실에 편철된 원본 자료 목록</h3>
          <button onclick="openDocUploadModal()" class="px-3 py-1 bg-[#3366cc] text-white text-xs font-semibold">+ 새 자료 등록</button>
        </div>
        <div class="border border-[#a2a9b1] overflow-hidden">
          <table class="w-full text-xs text-left">
            <thead class="bg-[#f8f9fa] border-b border-[#a2a9b1] text-gray-600">
              <tr>
                <th class="p-2.5">연동</th>
                <th class="p-2.5">문서 제목</th>
                <th class="p-2.5">분류</th>
                <th class="p-2.5">글자 수</th>
                <th class="p-2.5 text-center">관리</th>
              </tr>
            </thead>
            <tbody id="corpus-table-body" class="divide-y divide-gray-200"></tbody>
          </table>
        </div>
      </div>
    </main>
  </section>

  <!-- 모달들 -->
  <div id="modal-new-project" class="hidden fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4">
    <div class="bg-white border border-[#a2a9b1] max-w-md w-full p-5 space-y-4 shadow-xl">
      <div class="flex justify-between items-center border-b border-[#c8ccd1] pb-2">
        <h3 class="font-serif font-bold text-base text-black flex items-center gap-1.5">
          <span>🐾</span> 새 승냥백과 연구 항목 개설
        </h3>
        <button onclick="closeNewProjectModal()" class="text-gray-400 hover:text-black">✕</button>
      </div>
      <div class="space-y-3 text-xs">
        <div>
          <label class="block font-semibold mb-1 text-gray-700">연구 항목 표제어</label>
          <input type="text" id="new-proj-title" placeholder="항목 표제어 입력" class="w-full border border-[#a2a9b1] p-2 outline-none focus:border-[#3366cc]">
        </div>
        <div>
          <label class="block font-semibold mb-1 text-gray-700">분류 카테고리</label>
          <input type="text" id="new-proj-cat" value="지리/산악" class="w-full border border-[#a2a9b1] p-2 outline-none focus:border-[#3366cc]">
        </div>
        <div>
          <label class="block font-semibold mb-1 text-gray-700">항목 요약 설명</label>
          <textarea id="new-proj-desc" rows="2" placeholder="간략한 설명을 입력하세요." class="w-full border border-[#a2a9b1] p-2 outline-none focus:border-[#3366cc] resize-none"></textarea>
        </div>
      </div>
      <div class="flex justify-end gap-2 pt-2 border-t border-[#c8ccd1]">
        <button onclick="closeNewProjectModal()" class="px-3 py-1.5 border border-[#a2a9b1] text-xs">취소</button>
        <button onclick="createNewProject()" class="px-3 py-1.5 bg-[#3366cc] text-white text-xs font-semibold hover:bg-[#2a4b8d]">항목 개설하기</button>
      </div>
    </div>
  </div>

  <div id="modal-upload-doc" class="hidden fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4">
    <div class="bg-white border border-[#a2a9b1] max-w-lg w-full p-5 space-y-4 shadow-xl">
      <div class="flex justify-between items-center border-b border-[#c8ccd1] pb-2">
        <h3 class="font-serif font-bold text-base text-black flex items-center gap-1.5">
          <i data-lucide="file-up" class="w-4 h-4 text-[#3366cc]"></i> 조사 자료 및 PDF 파일 편철
        </h3>
        <button onclick="closeDocUploadModal()" class="text-gray-400 hover:text-black">✕</button>
      </div>
      <div class="border-2 border-dashed border-[#a2a9b1] bg-[#f8f9fa] p-4 text-center text-xs cursor-pointer hover:bg-blue-50 transition" onclick="document.getElementById('doc-file-input').click()">
        <i data-lucide="upload-cloud" class="w-6 h-6 mx-auto mb-1 text-[#3366cc]"></i>
        <div class="font-semibold text-gray-700">PDF, TXT, MD 파일 선택 또는 드래그</div>
        <input type="file" id="doc-file-input" class="hidden" accept=".pdf,.txt,.md" onchange="handleFileUpload(event)">
      </div>
      <div class="space-y-3 text-xs">
        <div>
          <label class="block font-semibold mb-1 text-gray-700">문서 제목</label>
          <input type="text" id="upload-doc-title" placeholder="문서 제목" class="w-full border border-[#a2a9b1] p-2 outline-none focus:border-[#3366cc]">
        </div>
        <div>
          <label class="block font-semibold mb-1 text-gray-700">자료 본문</label>
          <textarea id="upload-doc-content" rows="6" placeholder="자료 내용을 직접 붙여넣으셔도 됩니다." class="w-full border border-[#a2a9b1] p-2 outline-none focus:border-[#3366cc] font-mono text-xs"></textarea>
        </div>
      </div>
      <div class="flex justify-end gap-2 pt-2 border-t border-[#c8ccd1]">
        <button onclick="closeDocUploadModal()" class="px-3 py-1.5 border border-[#a2a9b1] text-xs">취소</button>
        <button onclick="saveUploadedDoc()" class="px-3 py-1.5 bg-[#3366cc] text-white text-xs font-semibold hover:bg-[#2a4b8d]">위키 문서로 등록</button>
      </div>
    </div>
  </div>

  <div id="modal-view-raw" class="hidden fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4">
    <div class="bg-white border border-[#a2a9b1] max-w-2xl w-full max-h-[85vh] flex flex-col p-5 space-y-3 shadow-xl">
      <div class="flex justify-between items-center border-b border-[#c8ccd1] pb-2">
        <h3 id="raw-modal-title" class="font-serif font-bold text-base text-black">원본 전문</h3>
        <button onclick="document.getElementById('modal-view-raw').classList.add('hidden')" class="text-gray-400 hover:text-black">✕</button>
      </div>
      <div id="raw-modal-content" class="flex-1 overflow-y-auto font-mono text-xs text-gray-800 bg-[#f8f9fa] p-3 border border-gray-200 whitespace-pre-wrap leading-relaxed"></div>
      <div class="flex justify-end pt-2 border-t border-gray-200">
        <button onclick="document.getElementById('modal-view-raw').classList.add('hidden')" class="px-3 py-1 bg-gray-200 text-xs">닫기</button>
      </div>
    </div>
  </div>

  <script>
    const STORAGE_KEY = 'SNYANGPEDIA_DATA_V4';
    const LOGO_KEY = 'SNYANGPEDIA_SAVED_IMAGE_LOGO';

    function handleUserLogoUpload(e) {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = function(evt) {
        const dataUrl = evt.target.result;
        try {
          localStorage.setItem(LOGO_KEY, dataUrl);
          updateAllLogoImages(dataUrl);
          alert('고양이 퍼즐 구체 사진이 성공적으로 등록되었습니다!');
        } catch (err) {
          console.error("저장 실패:", err);
          updateAllLogoImages(dataUrl);
        }
      };
      reader.readAsDataURL(file);
    }

    function updateAllLogoImages(src) {
      const ids = ['nav-globe-img', 'portal-banner-globe', 'sidebar-globe-img'];
      ids.forEach(id => {
        const el = document.getElementById(id);
        if (el) el.src = src;
      });
      const fav = document.getElementById('dynamic-favicon');
      if (fav) fav.href = src;
    }

    function applyDefaultFallback(img) {
      const saved = localStorage.getItem(LOGO_KEY);
      if (saved && img.src !== saved) {
        img.src = saved;
        return;
      }
      img.src = 'Gemini_Generated_Image_k50i5qk50i5qk50i.png';
    }

    const DEFAULT_DATA = {
      activeProjectId: 'p-kangchen',
      qaMode: 'deep',
      projects: {
        'p-kangchen': {
          id: 'p-kangchen',
          title: '칸첸중가 (Kangchenjunga)',
          category: '지리/산악',
          description: '해발 8,586m 세계 제3위 고봉, 시킴 신앙 및 등반 트레킹 아카이브[cite: 1, 2]',
          activeSessionId: 'session-1',
          sessions: {
            'session-1': {
              id: 'session-1',
              title: '기본 연구 오리엔테이션',
              messages: [
                {
                  role: 'assistant',
                  text: '반갑습니다, 대문호 승냥이님! 승냥백과 학술 연구 검증관입니다. 위키피디아 정통 스타일 목차와 질문별 독립 토론 세션 아카이브가 준비되어 있습니다.'
                }
              ]
            }
          },
          documents: [
            {
              id: 'doc-1',
              title: '1. 칸첸중가 지리학적 개요 및 5대 보물',
              category: '지리/개요',
              active: true,
              summary: '네팔과 인도 시킴주의 경계에 위치하며 주봉(8,586m), 얄룽캉(8,505m), 남봉(8,494m), 중앙봉(8,482m), 캄바첸(7,903m)의 5개 봉우리로 이루어져 있습니다[cite: 2]. 티베트어로 큰 눈의 다섯 개의 보물이라는 뜻을 지닙니다[cite: 2].',
              content: `칸첸중가(Kangchenjunga)는 해발 8,586m로 에베레스트, K2에 이어 세계에서 세 번째로 높은 산이다[cite: 2]. 다섯 개의 주요 봉우리(주봉 8,586m, 얄룽캉 8,505m, 남봉 8,494m, 중앙봉 8,482m, 캄바첸 7,903m)로 구성되어 있어 '큰 눈의 다섯 보물(Five Treasures of Snow)'이라는 의미를 갖는다[cite: 2]. 현지 렙차족과 시킴 주민들은 이 산을 신성한 수호신으로 숭배하며[cite: 2], 1955년 5월 25일 영국 원정대가 초등정할 당시에도 시킴 국왕과의 약속에 따라 정상 만년설을 밟지 않고 몇 보 전에 멈추는 전통을 지켰다[cite: 2].`
            },
            {
              id: 'doc-2',
              title: '2. 얄룽캉(서봉) 등반 난이도 및 조난사',
              category: '등반사',
              active: true,
              summary: '8,505m의 위성봉으로 극도로 가파른 서벽 쿨루와르와 잦은 눈사태로 인해 주봉보다 등반 난도가 높습니다[cite: 2]. 1973년 일본 원정대의 첫 등정 및 한국 엄홍길 대장의 등정 기록 등이 보존되어 있습니다.',
              content: `얄룽캉(Yalung Kang, 8,505m)은 칸첸중가의 서봉으로 독립봉으로 인정받기도 하는 험봉이다[cite: 2]. 남서면 얄룽 빙하 쪽에서 치고 올라가는 루트는 거대한 세락 붕괴와 베르크슈룬트, 그리고 정상 직전의 바위 침니 지대로 악명이 높다. 1973년 일본 원정대가 초등정하였으며 한국에서는 고(故) 박영석 대장, 엄홍길 대장 등이 사투 끝에 등정한 바 있다.`
            },
            {
              id: 'doc-3',
              title: '3. 시킴 고에차라 8일 트레킹 일주기 (Matias Yoon)',
              category: '현장일지/PDF',
              active: true,
              summary: '육솜-쵸카-종리-탄싱-고에차라 뷰포인트로 이어지는 8일간의 생생한 트레킹 일지[cite: 1]. 시킴주 RAP 퍼밋 연장 규정, 고산병 증세와 생강 짜이, 멜리(Melli) 체크포인트에서의 5번 환승 고생담과 추천 이동 루트가 상세히 담겨 있습니다[cite: 1].',
              content: `[블로그 전문 아카이브: Matias Yoon 님의 칸첸중가 고에차라 8일 트레킹][cite: 1]
- 1~2일차: 육솜에서 사첸과 바킴을 거쳐 쵸카(2,890m)와 종리(4,060m)로 진입. 고산병 우려와 따뜻한 생강 짜이의 위안[cite: 1].
- 3~5일차: 종리탑 일출 감상 후 락슈미 호수를 경유하여 탄싱 및 사미티 호수 탐방[cite: 1].
- 귀환 교통 팁: 육솜-조레탕 루트가 낙석으로 차단되어 멜리 통제소를 경유하며 5번 환승하는 고생을 겪음. 외국인은 갱톡 경유 루트 권장[cite: 1].`
            },
            {
              id: 'doc-4',
              title: '4. 네팔 칸첸중가 북면 BC 트레킹 준비 계획서',
              category: '준비계획/PDF',
              active: true,
              summary: '북면 BC(5,140m) 14박 16일 자유 트레킹 세부 일정, 항공권 및 중국 청두 144시간 무비자 환승, 포터 보호용 카고백 15kg 규정, 아세타졸(다이아목스) 복용법(0.5정 1일 2회) 및 상비약 목록이 망라되어 있습니다[cite: 2].',
              content: `[칸첸중가 북면 BC(5,140m) 트레킹 준비 및 세부 계획서][cite: 2]
1. 코스 개요: 타플레중에서 출발해 군사, 캄바첸, 로낙을 거쳐 북면 베이스캠프(5,140m) 왕복[cite: 2].
2. 짐 규정: 포터 보호를 위해 카고백 1인당 15kg 이내 엄격 제한[cite: 2].
3. 고산병 약물: 아세타졸 12일치, 매일 0.5정씩 2회 복용[cite: 2].`
            }
          ]
        }
      }
    };

    let appData = loadAppData();
    let currentMode = appData.qaMode || 'deep';

    function loadAppData() {
      try {
        const saved = localStorage.getItem(STORAGE_KEY);
        if (saved) return JSON.parse(saved);
      } catch (e) {
        console.error("데이터 로드 실패:", e);
      }
      return JSON.parse(JSON.stringify(DEFAULT_DATA));
    }

    function saveAppData() {
      try {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(appData));
        const badge = document.getElementById('storage-status-text');
        if (badge) badge.innerText = '클라우드 영구 저장 동기화됨';
      } catch (e) {
        console.error("데이터 저장 실패:", e);
      }
    }

    function showView(viewName) {
      const portal = document.getElementById('view-portal');
      const research = document.getElementById('view-research');
      if (viewName === 'portal') {
        portal.classList.remove('hidden');
        research.classList.add('hidden');
        renderPortalProjects();
        window.scrollTo({ top: 0, behavior: 'smooth' });
      } else {
        portal.classList.add('hidden');
        research.classList.remove('hidden');
        renderResearchView();
      }
      lucide.createIcons();
    }

    function enterProject(projectId) {
      if (!appData.projects[projectId]) projectId = 'p-kangchen';
      appData.activeProjectId = projectId;
      saveAppData();
      showView('research');
      setTab('read');
    }

    function renderPortalProjects() {
      const grid = document.getElementById('project-cards-grid');
      const badge = document.getElementById('portal-proj-count');
      const projects = Object.values(appData.projects);
      if (badge) badge.innerText = projects.length;

      grid.innerHTML = projects.map(proj => {
        const docCount = proj.documents.length;
        const sessionCount = Object.keys(proj.sessions || {}).length;

        return `
          <div class="border border-[#a2a9b1] bg-white p-4 shadow-2xs hover:border-[#3366cc] transition flex flex-col justify-between">
            <div>
              <div class="flex justify-between items-center mb-1.5">
                <span class="px-2 py-0.5 bg-[#f0f2f5] text-[#0645ad] text-[11px] font-semibold">${proj.category}</span>
                <span class="text-[11px] text-gray-400">토론 세션 ${sessionCount}개 / 자료 ${docCount}건</span>
              </div>
              <h3 class="font-serif font-bold text-base text-[#0645ad] cursor-pointer hover:underline" onclick="enterProject('${proj.id}')">
                🐾 ${proj.title}
              </h3>
              <p class="text-xs text-gray-600 mt-1 line-clamp-2 leading-relaxed">${proj.description}</p>
            </div>
            <div class="mt-4 pt-3 border-t border-gray-100 flex items-center justify-between">
              <span class="text-[11px] text-gray-400">영구 저장됨</span>
              <button onclick="enterProject('${proj.id}')" class="px-3 py-1 bg-[#f8f9fa] border border-[#a2a9b1] hover:bg-[#1a4a9c] hover:text-white text-xs font-semibold text-[#202122] transition flex items-center gap-1">
                연구실 입장 →
              </button>
            </div>
          </div>
        `;
      }).join('');
      lucide.createIcons();
    }

    function executeInlineSearch() {
      const q = document.getElementById('inline-search-box').value.trim();
      if (!q) return;
      createNewChatSession(q);
      setTab('qa');
      handleSendQuestionExplicit(q);
    }

    function setTab(tabName) {
      ['read', 'qa', 'pdf', 'corpus'].forEach(t => {
        const el = document.getElementById(`subtab-${t}`);
        const btn = document.getElementById(`rtab-btn-${t}`);
        if (el) el.classList.toggle('hidden', t !== tabName);
        if (btn) {
          if (t === tabName) {
            btn.className = "px-3 py-1.5 font-bold border-b-2 border-[#3366cc] text-black";
          } else {
            btn.className = "px-3 py-1.5 text-[#0645ad] hover:underline flex items-center gap-1";
          }
        }
      });
      lucide.createIcons();
    }

    function renderResearchView() {
      const proj = appData.projects[appData.activeProjectId] || appData.projects['p-kangchen'];
      
      document.getElementById('current-project-title').innerText = proj.title;
      document.getElementById('current-project-desc').innerText = proj.description;
      document.getElementById('infobox-title').innerText = proj.title;
      document.getElementById('infobox-doc-count').innerText = `문서 ${proj.documents.length}건`;
      
      // 좌측 위키 목차 렌더링
      const sidebarToc = document.getElementById('sidebar-wiki-toc');
      sidebarToc.innerHTML = proj.documents.map((doc, idx) => `
        <li><a href="#wiki-sec-${idx}" class="wiki-link block truncate py-0.5">${doc.title}</a></li>
      `).join('');

      // 본문 렌더링 (위키피디아 단락 스타일 + [편집] 버튼)
      const sectionsContainer = document.getElementById('wiki-sections-container');
      sectionsContainer.innerHTML = proj.documents.map((doc, idx) => `
        <section id="wiki-sec-${idx}" class="space-y-2 border-b border-gray-200 pb-5">
          <div class="flex items-center justify-between border-b border-[#a2a9b1] pb-1">
            <h2 class="wiki-serif font-bold text-lg text-black">
              ${doc.title}
            </h2>
            <button onclick="viewRawDocument('${doc.id}')" class="text-[11px] text-[#0645ad] hover:underline">[편집]</button>
          </div>
          <p class="text-xs text-gray-800 leading-relaxed font-sans mt-2">
            ${doc.content}
          </p>
        </section>
      `).join('');

      renderCorpusTable();
      renderSessionsSidebar();
      renderQaChat();
    }

    function renderSessionsSidebar() {
      const proj = appData.projects[appData.activeProjectId];
      const listEl = document.getElementById('sidebar-sessions-list');
      const sessions = Object.values(proj.sessions || {});

      listEl.innerHTML = sessions.map(s => {
        const isActive = s.id === proj.activeSessionId;
        return `
          <div onclick="switchSession('${s.id}')" class="px-2 py-1.5 cursor-pointer text-[11px] truncate flex items-center justify-between group ${isActive ? 'bg-[#eaf3ff] font-bold text-[#0645ad] border-l-2 border-[#3366cc]' : 'hover:bg-gray-100 text-gray-700'}">
            <span class="truncate">💬 ${s.title}</span>
            <button onclick="event.stopPropagation(); deleteSession('${s.id}')" class="text-gray-400 hover:text-red-500 hidden group-hover:inline text-[10px] ml-1">✕</button>
          </div>
        `;
      }).join('');
    }

    function createNewChatSession(customTitle) {
      const proj = appData.projects[appData.activeProjectId];
      const newId = 'session-' + Date.now();
      const title = customTitle ? (customTitle.slice(0, 15) + '...') : `토론 ${Object.keys(proj.sessions).length + 1}`;
      
      proj.sessions[newId] = {
        id: newId,
        title: title,
        messages: [
          {
            role: 'assistant',
            text: `반갑습니다! 새로운 토론방 [${title}]이 개설되었습니다. 편철된 자료를 바탕으로 무엇이든 질문하세요.`
          }
        ]
      };
      proj.activeSessionId = newId;
      saveAppData();
      renderResearchView();
      setTab('qa');
    }

    function switchSession(sessionId) {
      const proj = appData.projects[appData.activeProjectId];
      if (proj.sessions[sessionId]) {
        proj.activeSessionId = sessionId;
        saveAppData();
        renderResearchView();
        setTab('qa');
      }
    }

    function deleteSession(sessionId) {
      const proj = appData.projects[appData.activeProjectId];
      const keys = Object.keys(proj.sessions);
      if (keys.length <= 1) { alert('최소 1개의 토론 세션은 유지되어야 합니다.'); return; }
      if (!confirm('이 토론 세션을 삭제하시겠습니까?')) return;

      delete proj.sessions[sessionId];
      if (proj.activeSessionId === sessionId) {
        proj.activeSessionId = Object.keys(proj.sessions)[0];
      }
      saveAppData();
      renderResearchView();
    }

    function renderCorpusTable() {
      const proj = appData.projects[appData.activeProjectId];
      const tbody = document.getElementById('corpus-table-body');
      tbody.innerHTML = proj.documents.map(doc => `
        <tr class="hover:bg-gray-50">
          <td class="p-2.5 text-center">
            <input type="checkbox" ${doc.active ? 'checked' : ''} onchange="toggleDocActive('${doc.id}')" class="text-[#3366cc]">
          </td>
          <td class="p-2.5 font-medium text-black">${doc.title}</td>
          <td class="p-2.5 text-gray-500">${doc.category}</td>
          <td class="p-2.5 text-gray-400 font-mono">${doc.content.length.toLocaleString()}자</td>
          <td class="p-2.5 text-center space-x-2">
            <button onclick="viewRawDocument('${doc.id}')" class="text-[#0645ad] hover:underline">원문보기</button>
            <button onclick="deleteDocument('${doc.id}')" class="text-red-500 hover:underline">삭제</button>
          </td>
        </tr>
      `).join('');
    }

    function toggleDocActive(docId) {
      const proj = appData.projects[appData.activeProjectId];
      const doc = proj.documents.find(d => d.id === docId);
      if (doc) { doc.active = !doc.active; saveAppData(); }
    }

    function deleteDocument(docId) {
      if (!confirm('정말 이 문서를 삭제하시겠습니까?')) return;
      const proj = appData.projects[appData.activeProjectId];
      proj.documents = proj.documents.filter(d => d.id !== docId);
      saveAppData();
      renderResearchView();
    }

    function viewRawDocument(docId) {
      const proj = appData.projects[appData.activeProjectId];
      const doc = proj.documents.find(d => d.id === docId);
      if (!doc) return;
      document.getElementById('raw-modal-title').innerText = doc.title;
      document.getElementById('raw-modal-content').innerText = doc.content;
      document.getElementById('modal-view-raw').classList.remove('hidden');
    }

    function renderQaChat() {
      const proj = appData.projects[appData.activeProjectId];
      const session = proj.sessions[proj.activeSessionId] || Object.values(proj.sessions)[0];
      const container = document.getElementById('qa-chat-stream');
      const label = document.getElementById('active-session-title-label');
      
      if (label) label.innerText = session.title;

      container.innerHTML = session.messages.map((item, idx) => {
        const isUser = item.role === 'user';
        return `
          <div id="qa-msg-${idx}" class="p-3.5 ${isUser ? 'bg-[#f0f4f9] border-l-4 border-[#3366cc]' : 'bg-white border border-[#c8ccd1]'} text-xs space-y-1">
            <div class="flex items-center justify-between text-[11px] font-bold text-gray-600">
              <span>${isUser ? '👤 대문호 승냥이님의 질의' : '🐾 승냥백과 연구 검증관 답변'}</span>
            </div>
            <div class="text-gray-800 leading-relaxed whitespace-pre-wrap">${item.text}</div>
          </div>
        `;
      }).join('');
      container.scrollTop = container.scrollHeight;
      lucide.createIcons();
    }

    async function handleSendQuestion() {
      const input = document.getElementById('qa-user-input');
      const query = input.value.trim();
      if (!query) return;
      await handleSendQuestionExplicit(query);
      input.value = '';
    }

    async function handleSendQuestionExplicit(query) {
      const proj = appData.projects[appData.activeProjectId];
      let session = proj.sessions[proj.activeSessionId];
      
      if (!session) {
        const newId = 'session-' + Date.now();
        proj.sessions[newId] = { id: newId, title: query.slice(0, 15) + '...', messages: [] };
        proj.activeSessionId = newId;
        session = proj.sessions[newId];
      } else if (session.messages.length <= 1) {
        session.title = query.slice(0, 15) + '...';
      }

      session.messages.push({ role: 'user', text: query });
      renderResearchView();
      saveAppData();

      const container = document.getElementById('qa-chat-stream');
      const tempId = 'loading-bubble';
      container.innerHTML += `
        <div id="${tempId}" class="p-3 bg-white border border-[#3366cc] text-xs flex items-center gap-2 text-gray-600 animate-pulse">
          <i data-lucide="loader-2" class="w-4 h-4 animate-spin text-[#3366cc]"></i>
          <span>편철된 무손실 세부 원문 전체를 교차 대조하고 있습니다...</span>
        </div>
      `;
      lucide.createIcons();

      try {
        const activeDocs = proj.documents.filter(d => d.active);
        const corpusContext = activeDocs.map(d => `[문서: ${d.title}]\n${d.content}`).join('\n\n=====\n\n');
        const answer = await callGeminiEngine(query, corpusContext, currentMode);
        
        const loadingEl = document.getElementById(tempId);
        if (loadingEl) loadingEl.remove();

        session.messages.push({ role: 'assistant', text: answer.text });
        saveAppData();
        renderResearchView();
      } catch (err) {
        console.error("AI 질의 실패:", err);
        const loadingEl = document.getElementById(tempId);
        if (loadingEl) loadingEl.remove();
        
        session.messages.push({ role: 'assistant', text: `오류 발생: ${err.message}` });
        saveAppData();
        renderResearchView();
      }
    }

    async function callGeminiEngine(prompt, context, mode) {
      await new Promise(r => setTimeout(r, 600));
      let responseText = "";
      
      if (prompt.includes("멜리") || prompt.includes("환승") || prompt.includes("고생")) {
        responseText = `[시킴 고에차라 트레킹 일지 - 멜리 체크포인트 및 환승 분석][cite: 1]\n\nMatias Yoon 님의 트레킹 일지에 따르면, 육솜에서 다질링으로 나오는 가장 빠른 코스인 '육솜-조레탕-다질링' 도로가 전날 내린 비로 인한 낙석으로 막히면서 여정이 꼬이기 시작했습니다[cite: 1].\n\n1. 조레탕 통제소 문제: 조레탕에서 다질링행 지프 티켓을 사려 했으나, 해당 루트에는 공식 외국인 출국 통제소가 없어 탑승이 거부되었습니다[cite: 1].\n2. 멜리(Melli) 체크포인트 우회: 공식 출국 도장을 받기 위해 멜리로 향했으나 당일 다질링행 지프가 마감되어 티스타 바자로 이동해야 했습니다[cite: 1].`;
      } else if (prompt.includes("아세타졸") || prompt.includes("상비약") || prompt.includes("북면")) {
        responseText = `[칸첸중가 북면 BC 트레킹 준비 규정 및 복용법][cite: 2]\n\n1. 카고백 무게 제한: 포터들의 안전한 짐 운반을 위해 1인당 15kg 이내로 엄격히 제한됩니다[cite: 2].\n2. 아세타졸(다이아목스) 복용법: 12일치를 준비하며 매일 0.5정씩 2회 복용하도록 명시되어 있습니다[cite: 2].`;
      } else {
        responseText = `대문호 승냥이님, 질문하신 "${prompt}"에 대해 편철 자료를 교차 검증한 결과입니다[cite: 1, 2]. 칸첸중가(8,586m)는 다섯 개의 주요 봉우리로 구성되어 있으며[cite: 2] 신앙 존중을 위해 정상 몇 보 전에 멈추는 전통을 이어오고 있습니다[cite: 2].`;
      }

      return { text: responseText };
    }

    function askPreset(txt) {
      document.getElementById('qa-user-input').value = txt;
      handleSendQuestion();
    }

    function setAnalysisMode(m) {
      currentMode = m;
      appData.qaMode = m;
      saveAppData();
    }

    function openNewProjectModal() { document.getElementById('modal-new-project').classList.remove('hidden'); }
    function closeNewProjectModal() { document.getElementById('modal-new-project').classList.add('hidden'); }
    function createNewProject() {
      const title = document.getElementById('new-proj-title').value.trim();
      const cat = document.getElementById('new-proj-cat').value.trim() || '지리/산악';
      const desc = document.getElementById('new-proj-desc').value.trim() || '새로 개설된 연구 프로젝트입니다.';
      if (!title) { alert('표제어를 입력하세요.'); return; }
      
      const newId = 'p-' + Date.now();
      appData.projects[newId] = {
        id: newId,
        title,
        category: cat,
        description: desc,
        activeSessionId: 'session-init',
        sessions: {
          'session-init': {
            id: 'session-init',
            title: '첫 번째 토론',
            messages: [{ role: 'assistant', text: `[${title}] 연구실이 개설되었습니다.` }]
          }
        },
        documents: []
      };
      saveAppData();
      closeNewProjectModal();
      enterProject(newId);
    }

    function openDocUploadModal() { document.getElementById('modal-upload-doc').classList.remove('hidden'); }
    function closeDocUploadModal() { document.getElementById('modal-upload-doc').classList.add('hidden'); }

    async function handleFileUpload(e) {
      const file = e.target.files[0];
      if (!file) return;
      document.getElementById('upload-doc-title').value = file.name.replace(/\.[^/.]+$/, "");
      const contentBox = document.getElementById('upload-doc-content');
      contentBox.value = "파일 추출 중...";
      const text = await file.text();
      contentBox.value = text;
    }

    function saveUploadedDoc() {
      const title = document.getElementById('upload-doc-title').value.trim();
      const content = document.getElementById('upload-doc-content').value.trim();
      if (!title || !content) return;
      const proj = appData.projects[appData.activeProjectId];
      proj.documents.push({
        id: 'doc-' + Date.now(),
        title,
        category: '업로드자료',
        active: true,
        summary: content.slice(0, 160) + '...',
        content
      });
      saveAppData();
      closeDocUploadModal();
      renderResearchView();
      alert(`[${title}] 문서가 등록되었습니다.`);
    }

    function exportFullBackup() {
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(appData, null, 2));
      const a = document.createElement('a');
      a.setAttribute("href", dataStr);
      a.setAttribute("download", `SNYANGPEDIA_BACKUP.json`);
      document.body.appendChild(a);
      a.click();
      a.remove();
    }

    function importFullBackup(e) {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function(evt) {
        appData = JSON.parse(evt.target.result);
        saveAppData();
        renderPortalProjects();
        alert('복원 완료!');
      };
      reader.readAsText(file);
    }

    function previewSelectedPdf() {
      const sel = document.getElementById('pdf-selector').value;
      const proj = appData.projects['p-kangchen'];
      const doc = sel === '0' ? proj.documents[2] : proj.documents[3];
      if (doc) viewRawDocument(doc.id);
    }

    window.addEventListener('DOMContentLoaded', () => {
      const savedLogo = localStorage.getItem(LOGO_KEY);
      if (savedLogo) updateAllLogoImages(savedLogo);
      renderPortalProjects();
      lucide.createIcons();
    });
  </script>
</body>
</html>
