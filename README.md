<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>리더 코칭 챗봇: Leader's Conflict Coach</title>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* 시스템 폰트 스택 */
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
            margin: 0;
            background-color: #f4f5f7; /* 배경색 */
            color: #333;
            line-height: 1.6;
        }

        /* 색상 팔레트 */
        :root {
            --color-primary: #1f2a44;
            --color-accent: #3b82f6;
            --color-support: #6b7280;
            --color-border: #e6e8ef;
            --color-danger: #b91c1c;
            --color-warn: #d97706;
            --color-info: #0ea5e9;
            --color-white: #ffffff;
            --color-text-dark: #1f2a44;
            --color-text-light: #6b7280;
        }

        #app {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            max-width: 1200px; /* 최대 너비 설정 */
            margin: 0 auto; /* 중앙 정렬 */
            padding: 16px; /* 전체적인 패딩 */
            gap: 16px;
        }

        /* 헤더 */
        header {
            background-color: var(--color-primary);
            color: var(--color-white);
            padding: 16px 24px;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        header h1 {
            margin: 0;
            font-size: 1.8rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .title-chip {
            background-color: var(--color-accent);
            color: var(--color-white);
            padding: 8px 16px; /* 크기 키움 */
            border-radius: 24px; /* 크기에 맞춰 라운드 증가 */
            font-size: 1.2rem; /* 글씨 크기 키움 */
            font-weight: bold;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .title-chip svg {
            width: 20px; /* 아이콘 크기 키움 */
            height: 20px; /* 아이콘 크기 키움 */
            fill: currentColor;
        }

        /* 메인 콘텐츠 영역 (실습 영역 + 사이드 영역) */
        main {
            display: flex;
            flex: 1; /* 남은 공간 모두 차지 */
            gap: 16px;
            flex-wrap: wrap; /* 모바일에서 컬럼 전환 */
        }

        #practice-area {
            flex: 2; /* 실습 영역이 더 넓게 */
            display: flex;
            flex-direction: column;
            gap: 16px;
            min-width: 300px; /* 최소 너비 */
        }

        #side-area {
            flex: 1; /* 사이드 영역 */
            display: flex;
            flex-direction: column;
            gap: 16px;
            min-width: 280px; /* 최소 너비 */
        }

        /* 학습 대시보드 (하단 고정) */
        #dashboard-area {
            flex-shrink: 0; /* 줄어들지 않도록 */
            width: 100%;
            margin-top: auto; /* 푸터 위, 메인 콘텐츠 아래에 배치 */
        }

        #learning-dashboard {
            display: flex;
            gap: 16px;
            flex-wrap: wrap; /* 반응형 */
        }
        #learning-dashboard > div {
            flex: 1;
            min-width: 300px; /* 차트 컨테이너 최소 너비 */
        }

        /* 푸터 */
        footer {
            background-color: var(--color-primary);
            color: var(--color-white);
            padding: 16px 24px;
            text-align: center;
            border-radius: 8px;
            margin-top: 16px;
            box-shadow: 0 -4px 6px rgba(0, 0, 0, 0.05);
            font-size: 0.9rem;
        }

        /* 공통 스타일: 카드 (.surface) */
        .surface {
            background-color: var(--color-white);
            border: 1px solid var(--color-border);
            border-radius: 8px;
            padding: 24px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
        }

        .surface h2 {
            margin-top: 0;
            margin-bottom: 16px;
            color: var(--color-primary);
            font-size: 1.3rem;
        }

        /* 버튼 */
        .btn {
            padding: 10px 18px;
            border-radius: 6px;
            border: none;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.2s, box-shadow 0.2s;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .btn-primary {
            background-color: var(--color-accent);
            color: var(--color-white);
        }

        .btn-primary:hover {
            background-color: #2563eb;
            box-shadow: 0 4px 8px rgba(59, 130, 246, 0.3);
        }

        .btn-secondary {
            background-color: var(--color-support);
            color: var(--color-white);
        }

        .btn-secondary:hover {
            background-color: #4b5563;
            box-shadow: 0 4px 8px rgba(107, 114, 128, 0.3);
        }

        .btn-outline {
            background-color: transparent;
            color: var(--color-accent);
            border: 1px solid var(--color-accent);
        }
        .btn-outline:hover {
            background-color: rgba(59, 130, 246, 0.1);
        }


        /* 입력 필드 */
        input[type="text"],
        textarea,
        select {
            width: 100%;
            padding: 12px;
            border: 1px solid var(--color-border);
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 1rem;
            color: var(--color-text-dark);
            transition: border-color 0.2s, box-shadow 0.2s;
        }

        input[type="text"]:focus,
        textarea:focus,
        select:focus {
            outline: none;
            border-color: var(--color-accent);
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
        }

        textarea {
            min-height: 120px;
            resize: vertical;
        }

        /* 케이스 선택 */
        #case-categories {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 16px;
        }

        .case-chip {
            padding: 10px 16px;
            background-color: #f0f2f5;
            border: 1px solid var(--color-border);
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.2s;
            font-weight: 500;
            color: var(--color-text-dark);
        }

        .case-chip:hover {
            background-color: #e0e2e5;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
        }

        .case-chip.case--active {
            background-color: var(--color-accent);
            color: var(--color-white);
            border-color: var(--color-accent);
            box-shadow: 0 4px 8px rgba(59, 130, 246, 0.3);
        }

        /* 선택된 케이스 배지 숨김 (요청 사항 반영) */
        #selected-case-display {
            display: none;
        }

        /* 시나리오 */
        #scenario-content {
            background-color: #f9fafb;
            border: 1px solid var(--color-border);
            border-left: 4px solid var(--color-accent);
            padding: 16px;
            border-radius: 6px;
            margin-top: 16px;
            white-space: pre-wrap; /* 대화문 줄바꿈 유지 */
            font-size: 0.95rem;
            color: var(--color-text-dark);
            min-height: 80px; /* 시나리오 컨테이너 최소 높이 */
            display: flex;
            align-items: center; /* 텍스트 중앙 정렬 */
        }

        /* 코칭 피드백 */
        .feedback-block {
            margin-bottom: 20px;
        }

        .feedback-block h3 {
            font-size: 1.1rem;
            color: var(--color-primary);
            margin-top: 0;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .feedback-block p {
            margin: 0;
            color: var(--color-text-dark);
            font-size: 0.95rem;
        }

        .risk-badge {
            display: inline-block;
            padding: 4px 10px;
            border-radius: 16px;
            font-size: 0.85rem;
            font-weight: bold;
            color: var(--color-white);
            margin-left: 8px;
        }

        .risk-badge.HIGH { background-color: var(--color-danger); }
        .risk-badge.MEDIUM { background-color: var(--color-warn); }
        .risk-badge.LOW { background-color: var(--color-info); }
        .risk-badge.NONE { background-color: var(--color-support); }

        #feedback-actions {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 20px;
        }

        /* 학습 대시보드 차트 컨테이너 */
        .chart-container {
            height: 160px; /* 고정 높이 */
            width: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* 히스토리 */
        #history-list {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        #history-list li {
            background-color: #f9fafb;
            border: 1px solid var(--color-border);
            border-radius: 6px;
            padding: 12px 16px;
            margin-bottom: 8px;
            font-size: 0.9rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        #history-list li span:first-child {
            color: var(--color-text-dark);
            font-weight: 500;
        }
        #history-list li span:last-child {
            color: var(--color-text-light);
            font-size: 0.8rem;
        }

        #history-actions {
            display: flex;
            justify-content: space-between;
            margin-top: 16px;
        }

        /* HR 퀵가이드 */
        #hr-guide-content {
            background-color: #f9fafb;
            border: 1px solid var(--color-border);
            border-left: 4px solid var(--color-warn);
            padding: 16px;
            border-radius: 6px;
            font-size: 0.95rem;
            color: var(--color-text-dark);
            min-height: 100px;
        }

        /* 모달 */
        .modal {
            display: none; /* Hidden by default */
            position: fixed; /* Stay in place */
            z-index: 1000; /* Sit on top */
            left: 0;
            top: 0;
            width: 100%; /* Full width */
            height: 100%; /* Full height */
            overflow: auto; /* Enable scroll if needed */
            background-color: rgba(0,0,0,0.5); /* Black w/ opacity */
            justify-content: center;
            align-items: center;
            padding: 20px;
            box-sizing: border-box;
        }

        .modal-content {
            background-color: #fefefe;
            margin: auto;
            padding: 20px 30px;
            border: 1px solid #888;
            border-radius: 8px;
            width: 90%;
            max-width: 600px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
            position: relative;
        }

        .modal-close-btn {
            color: var(--color-support);
            font-size: 28px;
            font-weight: bold;
            position: absolute;
            top: 10px;
            right: 15px;
            cursor: pointer;
            transition: color 0.2s;
        }

        .modal-close-btn:hover,
        .modal-close-btn:focus {
            color: var(--color-primary);
            text-decoration: none;
            cursor: pointer;
        }

        .modal h3 {
            color: var(--color-primary);
            margin-top: 0;
            margin-bottom: 15px;
            font-size: 1.4rem;
        }

        .modal p {
            margin-bottom: 10px;
            color: var(--color-text-dark);
        }

        .modal ul {
            padding-left: 20px;
            color: var(--color-text-dark);
        }
        .modal li {
            margin-bottom: 5px;
        }

        /* Utility classes */
        .mb-4 { margin-bottom: 16px; }
        .mt-4 { margin-top: 16px; }
        .flex-row-end { display: flex; justify-content: flex-end; gap: 10px; }
        .flex-col { display: flex; flex-direction: column; }
        .gap-2 { gap: 8px; }
        .hidden { display: none !important; }

        /* 반응형 디자인 */
        @media (max-width: 768px) {
            #app {
                padding: 10px;
                gap: 10px;
            }
            header {
                padding: 12px 16px;
                flex-direction: column;
                align-items: flex-start;
                gap: 10px;
            }
            header h1 {
                font-size: 1.5rem;
            }
            .title-chip {
                font-size: 1rem; /* 모바일 크기 조정 */
                padding: 6px 14px;
            }

            main {
                flex-direction: column;
            }
            #practice-area, #side-area {
                min-width: unset;
                width: 100%;
            }

            #learning-dashboard {
                flex-direction: column; /* 모바일에서 1열 */
            }
            #learning-dashboard > div {
                min-width: unset;
                width: 100%;
            }

            .surface {
                padding: 16px;
            }
            .surface h2 {
                font-size: 1.1rem;
                margin-bottom: 12px;
            }

            .btn {
                padding: 8px 14px;
                font-size: 0.9rem;
            }
            input[type="text"],
            textarea,
            select {
                padding: 10px;
                font-size: 0.9rem;
            }
            textarea {
                min-height: 100px;
            }
            .case-chip {
                padding: 8px 14px;
                font-size: 0.9rem;
            }
            /* #selected-case-display는 이미 display: none; */
            #scenario-content {
                font-size: 0.85rem;
                min-height: 60px;
            }
            .feedback-block h3 {
                font-size: 1rem;
            }
            .feedback-block p {
                font-size: 0.85rem;
            }
            .risk-badge {
                font-size: 0.75rem;
                padding: 3px 8px;
            }
            #history-list li {
                font-size: 0.8rem;
                padding: 10px 14px;
            }
            #history-list li span:last-child {
                font-size: 0.7rem;
            }
            #hr-guide-content {
                font-size: 0.85rem;
                min-height: 80px;
            }
        }
    </style>
</head>
<body>
    <div id="app">
        <!-- 헤더 -->
        <header>
            <h1>
                <span class="title-chip">
                    <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM17 13H13V17H11V13H7V11H11V7H13V11H17V13Z" fill="currentColor"/>
                    </svg>
                    Leader's Conflict Coach
                </span>
                리더 코칭 챗봇
            </h1>
        </header>

        <!-- 메인 콘텐츠 영역 -->
        <main>
            <!-- 실습 영역 -->
            <div id="practice-area">
                <!-- 케이스 선택 -->
                <section id="case-selection" class="surface">
                    <h2>케이스 선택</h2>
                    <div id="case-categories">
                        <div class="case-chip" data-case="평가">평가</div>
                        <div class="case-chip" data-case="보상">보상</div>
                        <div class="case-chip" data-case="승진">승진</div>
                        <div class="case-chip" data-case="갈등">갈등</div>
                        <div class="case-chip" data-case="노무">노무</div>
                    </div>
                    <!-- 선택된 케이스 배지 표시 영역 (이제 사용하지 않음) -->
                    <div id="selected-case-display" class="hidden">
                        <span class="selected-case-badge" id="current-case-badge"></span>
                    </div>
                </section>

                <!-- 시나리오 -->
                <section id="scenario-section" class="surface">
                    <h2>시나리오 선택 및 대화</h2>
                    <select id="scenario-dropdown" class="mb-4" disabled>
                        <option value="">케이스를 먼저 선택해주세요</option>
                    </select>
                    <div id="scenario-content">시나리오를 선택하면 대화문이 여기에 표시됩니다.</div>
                </section>

                <!-- 리더 답변 입력 -->
                <section id="answer-input-section" class="surface">
                    <h2>리더 답변 입력</h2>
                    <textarea id="leader-response-input" placeholder="여기에 리더의 답변을 입력해주세요." class="mb-4"></textarea>
                    <div class="flex-row-end">
                        <button id="copy-best-practice-btn" class="btn btn-secondary" disabled>
                            <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor">
                                <path d="M16 1H4C2.9 1 2 1.9 2 3V17H4V3H16V1ZM19 5H8C6.9 5 6 5.9 6 7V21C6 22.1 6.9 23 8 23H19C20.1 23 21 22.1 21 21V7C21 5.9 20.1 5 19 5ZM19 21H8V7H19V21Z"/>
                            </svg>
                            모범 대화 복사
                        </button>
                        <button id="get-coaching-btn" class="btn btn-primary" disabled>
                            <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor">
                                <path d="M9 21.035V17.035H5V7.035H19V17.035H13L9 21.035ZM12 2C6.48 2 2 6.48 2 12C2 17.52 6.48 22 12 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 12 2ZM12 19.035C7.58 19.035 4 15.455 4 11.035C4 6.615 7.58 3.035 12 3.035C16.42 3.035 20 6.615 20 11.035C20 15.455 16.42 19.035 12 19.035Z"/>
                            </svg>
                            코칭 받기
                        </button>
                    </div>
                </section>

                <!-- 코칭 피드백 -->
                <section id="coaching-feedback-section" class="surface hidden">
                    <h2>코칭 피드백 <span id="feedback-risk-badge" class="risk-badge"></span></h2>
                    <div id="feedback-blocks">
                        <div class="feedback-block">
                            <h3><svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor"><path d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM17.387 7.766L10.02 15.132L6.613 11.726L5.2 13.139L10.02 17.959L18.799 9.18L17.387 7.766Z"/></svg> 잘한 점</h3>
                            <p id="feedback-well-done"></p>
                        </div>
                        <div class="feedback-block">
                            <h3><svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor"><path d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM13 17H11V15H13V17ZM13 13H11V7H13V13Z"/></svg> 보완할 점</h3>
                            <p id="feedback-suggestions"></p>
                        </div>
                        <div class="feedback-block">
                            <h3><svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor"><path d="M16 1H4C2.9 1 2 1.9 2 3V17H4V3H16V1ZM19 5H8C6.9 5 6 5.9 6 7V21C6 22.1 6.9 23 8 23H19C20.1 23 21 22.1 21 21V7C21 5.9 20.1 5 19 5ZM19 21H8V7H19V21Z"/></svg> 모범 대화</h3>
                            <p id="feedback-best-practice"></p>
                        </div>
                        <div class="feedback-block">
                            <h3><svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor"><path d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM12 20C7.58862 20 4 16.4114 4 12C4 7.58862 7.58862 4 12 4C16.4114 4 20 7.58862 20 12C20 16.4114 16.4114 20 12 20ZM11 11V7H13V11H17V13H13V17H11V13H7V11H11Z"/></svg> 다음 학습 제안</h3>
                            <p id="feedback-next-learning"></p>
                        </div>
                    </div>
                    <div id="feedback-hr-button-container" class="flex-row-end hidden">
                        <button id="hr-guide-trigger-btn" class="btn btn-outline">
                            <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor">
                                <path d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM13 16V18H11V16H13ZM13 6V14H11V6H13Z"/>
                            </svg>
                            HR 연결 가이드 보기
                        </button>
                    </div>
                </section>
            </div>

            <!-- 히스토리 & HR 퀵가이드 -->
            <div id="side-area">
                <!-- 히스토리 -->
                <section id="history-section" class="surface">
                    <h2>최근 코칭 히스토리 (최근 5건)</h2>
                    <ul id="history-list">
                        <!-- History items will be loaded here -->
                        <li id="no-history-message">아직 코칭 기록이 없습니다.</li>
                    </ul>
                    <div id="history-actions">
                        <button id="reset-history-btn" class="btn btn-secondary">
                            <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor">
                                <path d="M12 4V1L8 5L12 9V6C16.42 6 20 9.58 20 14C20 18.42 16.42 22 12 22C7.58 22 4 18.42 4 14H6C6 17.31 8.69 20 12 20C15.31 20 18 17.31 18 14C18 10.69 15.31 8 12 8V11L8 7L12 4Z"/>
                            </svg>
                            초기화
                        </button>
                        <button id="export-history-btn" class="btn btn-secondary">
                            <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor">
                                <path d="M19 9H15V3H9V9H5L12 17L19 9ZM4 19V21H20V19H4Z"/>
                            </svg>
                            기록 내보내기 (JSON)
                        </button>
                    </div>
                </section>

                <!-- HR 퀵가이드 요약 -->
                <section id="hr-quick-guide-section" class="surface">
                    <h2>HR 퀵가이드 요약</h2>
                    <div id="hr-guide-content">
                        <p>HR 관련 갈등 발생 시, 다음 절차를 따르는 것이 중요합니다:</p>
                        <ol>
                            <li>객관적인 사실 관계 파악</li>
                            <li>개인 면담을 통한 의견 청취</li>
                            <li>사내 규정 및 가이드라인 확인</li>
                            <li>필요시 HR 부서에 전문가 도움 요청</li>
                            <li>중립적인 입장에서 해결책 모색</li>
                            <li>재발 방지 및 후속 조치 계획 수립</li>
                        </ol>
                        <p>자세한 내용은 HR 연결 가이드를 참고하세요.</p>
                    </div>
                </section>
            </div>
        </main>

        <!-- 학습 대시보드 -->
        <div id="dashboard-area">
            <section id="learning-dashboard" class="surface">
                <h2>학습 대시보드</h2>
                <div class="chart-wrapper">
                    <h3 style="color: var(--color-primary); margin-bottom: 10px; font-size: 1.1rem;">리스크 분포</h3>
                    <div class="chart-container">
                        <canvas id="riskDistributionChart"></canvas>
                    </div>
                </div>
                <div class="chart-wrapper">
                    <h3 style="color: var(--color-primary); margin-bottom: 10px; font-size: 1.1rem;">케이스별 시도 횟수</h3>
                    <div class="chart-container">
                        <canvas id="caseAttemptsChart"></canvas>
                    </div>
                </div>
            </section>
        </div>

        <!-- 푸터 -->
        <footer>
            &copy; 2025 리더 코칭 챗봇. All rights reserved.
        </footer>

        <!-- HR Guide Modal -->
        <div id="hr-guide-modal" class="modal">
            <div class="modal-content">
                <span class="modal-close-btn">&times;</span>
                <h3>HR 연결 가이드</h3>
                <p>HR 관련 사안 발생 시 리더는 다음과 같은 절차와 원칙을 준수해야 합니다:</p>
                <ul>
                    <li>**사건 초기 인지 및 조치:** 문제 발생 시 신속하게 인지하고, 추가적인 확산이나 피해를 막기 위한 초기 조치를 취합니다.</li>
                    <li>**사실 관계 확인:** 관련자들과 개별 면담을 통해 객관적인 사실 관계를 파악하고, 증거를 확보합니다. (예: 진술서, 메일, 메신저 기록 등)</li>
                    <li>**비밀 유지 및 중립성 확보:** 사건 조사 및 해결 과정에서 모든 정보의 비밀을 유지하고, 특정 개인에게 편향되지 않는 중립적인 태도를 유지합니다.</li>
                    <li>**피해자 보호:** 피해자가 2차 피해를 입지 않도록 보호 조치를 취하고, 심리 상담 등 필요한 지원을 안내합니다.</li>
                    <li>**HR 부서 협의:** 복잡하거나 법적 문제가 발생할 소지가 있는 경우, 반드시 HR 부서 또는 법무팀과 협의하여 전문가의 조언을 구합니다.</li>
                    <li>**공식적인 절차 진행:** 사내 규정(취업규칙, 징계 규정 등)에 따라 공식적인 절차를 진행하며, 이 과정에서 관련자들에게 충분한 소명 기회를 제공합니다.</li>
                    <li>**재발 방지 대책 수립:** 사건 해결 후, 유사 사례의 재발을 막기 위한 교육, 제도 개선 등의 대책을 수립하고 실행합니다.</li>
                    <li>**기록 관리:** 모든 과정과 조치 내용을 상세하게 기록하고 관리하여, 추후 발생할 수 있는 문제에 대비합니다.</li>
                </ul>
                <p>이 가이드는 일반적인 원칙이며, 구체적인 상황에 따라 HR 부서의 지침을 따르는 것이 가장 중요합니다.</p>
            </div>
        </div>

        <!-- Alert Modal (for copy/export success) -->
        <div id="alert-modal" class="modal">
            <div class="modal-content">
                <span class="modal-close-btn" id="alert-close-btn">&times;</span>
                <h3 id="alert-title">알림</h3>
                <p id="alert-message"></p>
                <div class="flex-row-end mt-4">
                    <button id="alert-ok-btn" class="btn btn-primary">확인</button>
                </div>
            </div>
        </div>

    </div>

    <script defer>
        window.onload = function() {
            // DOM 요소 참조
            const caseChips = document.querySelectorAll('.case-chip');
            const currentCaseBadge = document.getElementById('current-case-badge');
            const scenarioDropdown = document.getElementById('scenario-dropdown');
            const scenarioContent = document.getElementById('scenario-content');
            const leaderResponseInput = document.getElementById('leader-response-input');
            const getCoachingBtn = document.getElementById('get-coaching-btn');
            const copyBestPracticeBtn = document.getElementById('copy-best-practice-btn');
            const coachingFeedbackSection = document.getElementById('coaching-feedback-section');
            const feedbackWellDone = document.getElementById('feedback-well-done');
            const feedbackSuggestions = document.getElementById('feedback-suggestions');
            const feedbackBestPractice = document.getElementById('feedback-best-practice');
            const feedbackNextLearning = document.getElementById('feedback-next-learning');
            const feedbackRiskBadge = document.getElementById('feedback-risk-badge');
            const feedbackHrButtonContainer = document.getElementById('feedback-hr-button-container');
            const hrGuideTriggerBtn = document.getElementById('hr-guide-trigger-btn');
            const historyList = document.getElementById('history-list');
            const noHistoryMessage = document.getElementById('no-history-message');
            const resetHistoryBtn = document.getElementById('reset-history-btn');
            const exportHistoryBtn = document.getElementById('export-history-btn');
            const hrGuideModal = document.getElementById('hr-guide-modal');
            const alertModal = document.getElementById('alert-modal');
            const alertTitle = document.getElementById('alert-title');
            const alertMessage = document.getElementById('alert-message');
            const alertOkBtn = document.getElementById('alert-ok-btn');
            const modalCloseBtns = document.querySelectorAll('.modal-close-btn');

            let selectedCase = null;
            let selectedScenarioId = null;
            let coachingHistory = []; // localStorage에 저장될 데이터

            // Chart.js 인스턴스
            let riskDistributionChart;
            let caseAttemptsChart;

            // Mock 데이터: 시나리오 정보 - 각 케이스별 5개 시나리오로 업데이트
            const scenarios = {
                "평가": [
                    { id: "eval-1", title: "성과 저조 직원 코칭", conversation: "리더: 김대리, 최근 성과가 목표에 미달하고 있습니다. 어떤 점이 어렵습니까?\n김대리:..." },
                    { id: "eval-2", title: "다면 평가 결과 공유", conversation: "리더: 박대리, 다면 평가 결과에 대해 이야기 나누고 싶습니다. 몇 가지 개선 사항이 있었는데요...\n박대리:..." },
                    { id: "eval-3", title: "성과 상향 조정 목표 설정", conversation: "리더: 이팀장님, 다음 분기 목표를 상향 조정해야 할 것 같습니다. 어떻게 생각하십니까?\n이팀장:..." },
                    { id: "eval-4", title: "새로운 평가 시스템 도입 설명", conversation: "리더: 다음 달부터 새로운 평가 시스템이 도입됩니다. 궁금한 점이 있으시면 질문해주세요.\n직원:..." },
                    { id: "eval-5", title: "직원 역량 개발 계획 수립", conversation: "리더: 최과장님, 장기적인 역량 개발을 위해 함께 계획을 세워보고 싶습니다. 어떤 분야에 관심이 있으신가요?\n최과장:..." }
                ],
                "보상": [
                    { id: "comp-1", title: "연봉 불만 직원 면담", conversation: "리더: 이과장님, 최근 연봉 인상에 대해 불만이 있으시다고 들었습니다. 어떤 점이 불만족스러우신가요?\n이과장:..." },
                    { id: "comp-2", title: "인센티브 제도 설명", conversation: "리더: 새로 도입된 인센티브 제도에 대해 설명드리겠습니다. 궁금한 점이 있으시면 언제든 질문해주세요.\n직원:..." },
                    { id: "comp-3", title: "승진 누락 직원의 보상 불만", conversation: "리더: 김대리, 이번 승진에서 누락되어 실망이 크리라 생각합니다. 보상 관련하여 할 이야기가 있습니까?\n김대리:..." },
                    { id: "comp-4", title: "성과에 따른 특별 보상 제안", conversation: "리더: 박팀장님, 지난 프로젝트에서 탁월한 성과를 보여주셔서 특별 보상을 제안하고 싶습니다.\n박팀장:..." },
                    { id: "comp-5", title: "직원 복리후생 제도 안내", conversation: "리더: 저희 회사의 새로운 복리후생 제도에 대해 안내드리겠습니다. 궁금한 점은 HR에 문의해주세요.\n직원:..." }
                ],
                "승진": [
                    { id: "promo-1", title: "승진 누락 직원 격려", conversation: "리더: 최주임, 이번 승진에서 아쉽게도 누락되었습니다. 실망이 크시리라 생각합니다만...\n최주임:..." },
                    { id: "promo-2", title: "승진 후보자 역량 강화 코칭", conversation: "리더: 김대리, 다음 승진 기회를 위해 몇 가지 역량 강화를 함께 계획해 봅시다.\n김대리:..." },
                    { id: "promo-3", title: "리더십 포지션 승진 프로세스 설명", conversation: "리더: 새로운 리더십 포지션 승진 프로세스에 대해 설명드리겠습니다. 주요 변경 사항은...\n직원:..." },
                    { id: "promo-4", title: "조직 개편에 따른 직위 변경 설명", conversation: "리더: 조직 개편으로 인해 박팀장님의 직위가 변경될 예정입니다. 이에 대해 설명드리겠습니다.\n박팀장:..." },
                    { id: "promo-5", title: "승진 기준 변경 안내", conversation: "리더: 내년부터 승진 기준에 일부 변경이 있습니다. 미리 안내드리니 참고해주세요.\n직원:..." }
                ],
                "갈등": [
                    { id: "conflict-1", title: "부서 간 업무 갈등 중재", conversation: "리더: 김팀장님, 박팀장님, 두 분 부서 간 업무 협력에 어려움이 있다고 들었습니다. 어떤 문제가 있습니까?\n김팀장:..." },
                    { id: "conflict-2", title: "팀원 간 개인적인 갈등 해결", conversation: "리더: 이대리, 최주임, 두 분 사이에 갈등이 있는 것으로 보입니다. 각자의 입장을 들어보고 싶습니다.\n이대리:..." },
                    { id: "conflict-3", title: "리더와 팀원 간 의사소통 불화", conversation: "리더: 김팀장, 저와의 의사소통 방식에 불만이 있다고 들었습니다. 어떤 점이 개선되어야 할까요?\n김팀장:..." },
                    { id: "conflict-4", title: "고객 불만으로 인한 내부 갈등", conversation: "리더: 최근 고객 불만이 접수되었고, 이로 인해 팀 내 갈등이 발생하고 있습니다. 어떻게 해결할까요?\n직원:..." },
                    { id: "conflict-5", title: "원격 근무 환경에서의 협업 갈등", conversation: "리더: 원격 근무가 지속되면서 협업에 어려움을 겪는 팀원들이 있습니다. 어떤 지원이 필요할까요?\n직원:..." }
                ],
                "노무": [
                    { id: "labor-1", title: "직장 내 괴롭힘 초기 대응", conversation: "리더: 홍대리, 최근 직장 내 괴롭힘으로 힘들어하는 직원이 있다고 들었습니다. 어떤 상황인지 말씀해주시겠습니까?\n홍대리:..." },
                    { id: "labor-2", title: "근무 시간 외 연락에 대한 불만", conversation: "리더: 박주임, 근무 시간 외 연락에 대해 부담을 느낀다고 들었습니다. 어떤 어려움이 있나요?\n박주임:..." },
                    { id: "labor-3", title: "퇴사 통보 직원 면담", conversation: "리더: 김과장님, 퇴사 의사를 밝히셨군요. 혹시 퇴사를 결정하게 된 구체적인 이유를 들을 수 있을까요?\n김과장:..." },
                    { id: "labor-4", title: "육아휴직 신청 직원에 대한 안내", conversation: "리더: 이대리, 육아휴직을 신청해주셔서 감사합니다. 관련 절차와 지원 제도에 대해 안내해 드리겠습니다.\n이대리:..." },
                    { id: "labor-5", title: "징계 절차 관련 문의 응대", conversation: "리더: 최팀장님, 징계 절차에 대해 문의하셨는데, 어떤 부분에 대해 궁금하신가요? 사내 규정을 설명드리겠습니다.\n최팀장:..." }
                ]
            };

            // HR 키워드 (가상의 키워드)
            const hrKeywords = ["직장 내 괴롭힘", "징계", "해고", "성희롱", "노사", "부당", "고충"];

            /**
             * Alert Modal을 표시합니다.
             * @param {string} title - 알림 제목
             * @param {string} message - 알림 메시지
             */
            function showAlert(title, message) {
                alertTitle.textContent = title;
                alertMessage.textContent = message;
                alertModal.style.display = 'flex';
            }

            // 모달 닫기
            modalCloseBtns.forEach(btn => {
                btn.onclick = function() {
                    btn.closest('.modal').style.display = 'none';
                }
            });

            alertOkBtn.onclick = function() {
                alertModal.style.display = 'none';
            }

            window.onclick = function(event) {
                if (event.target === hrGuideModal) {
                    hrGuideModal.style.display = 'none';
                }
                if (event.target === alertModal) {
                    alertModal.style.display = 'none';
                }
            }

            /**
             * 로컬 스토리지에서 데이터를 로드합니다.
             * @returns {Array} 로드된 코칭 기록 배열
             */
            function loadCoachingHistory() {
                const historyJson = localStorage.getItem('coachingHistory');
                return historyJson ? JSON.parse(historyJson) : [];
            }

            /**
             * 로컬 스토리지에 데이터를 저장합니다. 최대 30건만 유지합니다.
             */
            function saveCoachingHistory() {
                localStorage.setItem('coachingHistory', JSON.stringify(coachingHistory.slice(0, 30))); // 최근 30건만 저장
            }

            /**
             * Chart.js 도넛 차트를 업데이트합니다.
             */
            function updateRiskDistributionChart() {
                const ctx = document.getElementById('riskDistributionChart').getContext('2d');
                const riskCounts = { 'HIGH': 0, 'MEDIUM': 0, 'LOW': 0, 'NONE': 0 };
                coachingHistory.forEach(record => {
                    if (record.feedback && riskCounts.hasOwnProperty(record.feedback.riskLevel)) {
                        riskCounts[record.feedback.riskLevel]++;
                    }
                });

                const data = {
                    labels: ['HIGH', 'MEDIUM', 'LOW', 'NONE'],
                    datasets: [{
                        data: [riskCounts.HIGH, riskCounts.MEDIUM, riskCounts.LOW, riskCounts.NONE],
                        backgroundColor: [
                            'var(--color-danger)',
                            'var(--color-warn)',
                            'var(--color-info)',
                            'var(--color-support)'
                        ],
                        hoverOffset: 4
                    }]
                };

                const options = {
                    responsive: true,
                    maintainAspectRatio: false, // 컨테이너 높이에 맞춤
                    plugins: {
                        legend: {
                            position: 'right',
                            labels: {
                                color: 'var(--color-text-dark)'
                            }
                        },
                        title: {
                            display: false,
                        }
                    }
                };

                if (riskDistributionChart) {
                    riskDistributionChart.data = data;
                    riskDistributionChart.options = options;
                    riskDistributionChart.update();
                } else {
                    riskDistributionChart = new Chart(ctx, {
                        type: 'doughnut',
                        data: data,
                        options: options
                    });
                }
            }

            /**
             * Chart.js 막대 차트를 업데이트합니다.
             */
            function updateCaseAttemptsChart() {
                const ctx = document.getElementById('caseAttemptsChart').getContext('2d');
                const caseCounts = {};
                Object.keys(scenarios).forEach(caseType => caseCounts[caseType] = 0); // 모든 케이스 초기화
                coachingHistory.forEach(record => {
                    if (caseCounts.hasOwnProperty(record.caseType)) {
                        caseCounts[record.caseType]++;
                    }
                });

                const labels = Object.keys(caseCounts);
                const dataValues = Object.values(caseCounts);

                const data = {
                    labels: labels,
                    datasets: [{
                        label: '시도 횟수',
                        data: dataValues,
                        backgroundColor: 'var(--color-accent)',
                        borderColor: 'var(--color-primary)',
                        borderWidth: 1
                    }]
                };

                const options = {
                    responsive: true,
                    maintainAspectRatio: false, // 컨테이너 높이에 맞춤
                    plugins: {
                        legend: {
                            display: false
                        },
                        title: {
                            display: false,
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                precision: 0, // 정수만 표시
                                color: 'var(--color-text-light)'
                            },
                            grid: {
                                color: 'var(--color-border)'
                            }
                        },
                        x: {
                            ticks: {
                                color: 'var(--color-text-light)'
                            },
                            grid: {
                                display: false
                            }
                        }
                    }
                };

                if (caseAttemptsChart) {
                    caseAttemptsChart.data = data;
                    caseAttemptsChart.options = options;
                    caseAttemptsChart.update();
                } else {
                    caseAttemptsChart = new Chart(ctx, {
                        type: 'bar',
                        data: data,
                        options: options
                    });
                }
            }

            /**
             * 히스토리 목록을 UI에 렌더링합니다. (최근 5건)
             */
            function renderHistoryList() {
                historyList.innerHTML = '';
                if (coachingHistory.length === 0) {
                    historyList.appendChild(noHistoryMessage);
                    noHistoryMessage.classList.remove('hidden');
                } else {
                    noHistoryMessage.classList.add('hidden');
                    coachingHistory.slice(0, 5).forEach(record => {
                        const li = document.createElement('li');
                        li.innerHTML = `
                            <span>${record.caseType} - ${record.scenarioTitle}</span>
                            <span>${new Date(record.timestamp).toLocaleDateString('ko-KR')}</span>
                        `;
                        historyList.appendChild(li);
                    });
                }
            }

            /**
             * 모든 차트와 히스토리 목록을 업데이트합니다.
             */
            function updateAllDashboards() {
                updateRiskDistributionChart();
                updateCaseAttemptsChart();
                renderHistoryList();
            }

            /**
             * 케이스 선택 핸들러
             */
            caseChips.forEach(chip => {
                chip.addEventListener('click', () => {
                    // 모든 칩의 active 클래스 제거
                    caseChips.forEach(c => c.classList.remove('case--active'));
                    // 클릭한 칩에 active 클래스 추가
                    chip.classList.add('case--active');

                    selectedCase = chip.dataset.case;
                    // currentCaseBadge.textContent = selectedCase; // 이 줄을 제거하여 선택된 케이스 배지 표시를 없앰
                    // currentCaseBadge.classList.remove('hidden'); // 이 줄을 제거하여 선택된 케이스 배지 표시를 없앰

                    // 시나리오 드롭다운 업데이트
                    updateScenarioDropdown(selectedCase);
                    // 시나리오 선택 활성화
                    scenarioDropdown.disabled = false;
                    // 코칭 받기 버튼 비활성화 (새 케이스 선택 시 시나리오 다시 선택해야 함)
                    getCoachingBtn.disabled = true;
                });
            });

            /**
             * 시나리오 드롭다운을 업데이트합니다.
             * @param {string} caseType - 선택된 케이스 유형
             */
            function updateScenarioDropdown(caseType) {
                scenarioDropdown.innerHTML = `<option value="">시나리오를 선택해주세요</option>`;
                if (scenarios[caseType]) {
                    scenarios[caseType].forEach(scenario => {
                        const option = document.createElement('option');
                        option.value = scenario.id;
                        option.textContent = scenario.title;
                        scenarioDropdown.appendChild(option);
                    });
                }
                scenarioContent.textContent = "시나리오를 선택하면 대화문이 여기에 표시됩니다.";
                selectedScenarioId = null;
            }

            /**
             * 시나리오 드롭다운 변경 핸들러
             */
            scenarioDropdown.addEventListener('change', () => {
                selectedScenarioId = scenarioDropdown.value;
                const selectedScenario = scenarios[selectedCase].find(s => s.id === selectedScenarioId);
                if (selectedScenario) {
                    scenarioContent.textContent = selectedScenario.conversation;
                    getCoachingBtn.disabled = false; // 시나리오 선택 시 코칭 받기 버튼 활성화
                } else {
                    scenarioContent.textContent = "시나리오를 선택하면 대화문이 여기에 표시됩니다.";
                    getCoachingBtn.disabled = true;
                }
                toggleCoachingButton(); // 버튼 상태 업데이트
            });

            /**
             * 가상 코칭 엔진 (로컬 피드백 생성)
             * @param {string} caseType - 케이스 유형
             * @param {string} scenarioTitle - 시나리오 제목
             * @param {string} userInput - 리더의 답변
             * @returns {object} 피드백 객체
             */
            function localCoach(caseType, scenarioTitle, userInput) {
                let wellDone = "긍정적인 경청 태도가 돋보입니다.";
                let suggestions = "문제 해결에 대한 구체적인 제안이 부족합니다. '그래서 무엇을 할 것인가?'에 대한 답을 명확히 제시해 보세요.";
                let bestPractice = "직원과 함께 해결책을 모색하고, 명확한 행동 계획을 수립하는 것이 중요합니다.";
                let nextLearning = "효과적인 문제 해결을 위한 코칭 스킬 강의를 수강해 보세요.";
                let riskLevel = "LOW";
                let hrKeywordsDetected = false;

                // HR 키워드 감지
                const allKeywords = ([...hrKeywords, scenarioTitle, userInput].join(' ')).toLowerCase(); // 모든 키워드를 소문자로 변환하여 비교
                if (hrKeywords.some(keyword => allKeywords.includes(keyword.toLowerCase()))) {
                    hrKeywordsDetected = true;
                    riskLevel = "HIGH"; // HR 관련 키워드 감지 시 리스크 상향
                    suggestions = "HR 관련 키워드가 감지되었습니다. 정확한 절차적 대응을 위해 HR 부서와 즉시 연결하는 것이 중요합니다.";
                }

                // 기본적인 피드백 로직 (가상)
                if (userInput.length < 20) {
                    suggestions += " 답변이 너무 짧습니다. 좀 더 구체적으로 설명하고, 리더의 의도를 명확히 전달해 주세요.";
                    if (riskLevel === "LOW") riskLevel = "MEDIUM";
                }
                if (userInput.includes("화를 내") || userInput.includes("책임을 져") || userInput.includes("무조건")) {
                    suggestions += " 감정적인 표현은 갈등을 심화시킬 수 있습니다. 중립적이고 객관적인 언어를 사용해 보세요.";
                    if (riskLevel === "LOW" || riskLevel === "MEDIUM") riskLevel = "HIGH";
                }
                if (userInput.includes("칭찬") || userInput.includes("격려")) {
                    wellDone += " 직원을 칭찬하고 격려하는 것은 매우 긍정적입니다.";
                }
                if (userInput.includes("해결책") && userInput.includes("함께")) {
                    wellDone += " 직원과 함께 해결책을 모색하려는 접근이 좋습니다.";
                }

                // 특정 케이스/시나리오에 대한 좀 더 구체적인 피드백 (예시)
                if (caseType === "평가") {
                    if (scenarioTitle === "성과 저조 직원 코칭") {
                        if (!userInput.includes("목표") && !userInput.includes("개선")) {
                            suggestions += " 성과 개선을 위한 구체적인 목표 설정 및 행동 계획 수립에 대한 논의가 필요합니다.";
                        }
                        bestPractice = "리더: 김대리, 최근 성과가 목표에 미달하고 있습니다. 어떤 점이 어렵습니까? 제가 도울 부분이 있다면 기꺼이 돕겠습니다. 우리 함께 개선 목표와 계획을 세워볼까요?";
                    } else if (scenarioTitle === "다면 평가 결과 공유") {
                         bestPractice = "리더: 박대리, 다면 평가 결과에 대해 이야기 나누고 싶습니다. 몇 가지 개선 사항이 있었는데요, 이를 어떻게 받아들이시고 다음 단계로 나아갈지 함께 고민해보고 싶습니다.";
                    }
                } else if (caseType === "보상") {
                    if (scenarioTitle === "연봉 불만 직원 면담") {
                        bestPractice = "리더: 이과장님, 최근 연봉 인상에 대해 불만이 있으시다고 들었습니다. 어떤 점이 불만족스러우신가요? 제게 솔직하게 이야기해주시면, 제가 어떤 방식으로 도울 수 있을지 함께 찾아보겠습니다.";
                    } else if (scenarioTitle === "인센티브 제도 설명") {
                        bestPractice = "리더: 새로 도입된 인센티브 제도에 대해 설명드리겠습니다. 제도의 취지와 목표에 대해 설명하고, 궁금한 점이 있으시면 언제든 질문해주세요.";
                    }
                } else if (caseType === "갈등") {
                    if (scenarioTitle === "부서 간 업무 갈등 중재") {
                        bestPractice = "리더: 김팀장님, 박팀장님, 두 분 부서 간 업무 협력에 어려움이 있다고 들었습니다. 어떤 문제가 있습니까? 각자의 입장을 충분히 듣고, 상호 이해를 바탕으로 합리적인 해결책을 찾아봅시다.";
                    } else if (scenarioTitle === "팀원 간 개인적인 갈등 해결") {
                        bestPractice = "리더: 이대리, 최주임, 두 분 사이에 갈등이 있는 것으로 보입니다. 각자의 입장을 들어보고 싶습니다. 서로 이해하고 해결책을 찾을 수 있도록 제가 돕겠습니다.";
                    }
                } else if (caseType === "승진") {
                    if (scenarioTitle === "승진 누락 직원 격려") {
                        bestPractice = "리더: 최주임, 이번 승진에서 아쉽게도 누락되었습니다. 실망이 크시리라 생각합니다만, 당신의 노력을 충분히 알고 있습니다. 다음 기회를 위해 어떤 부분을 함께 준비하면 좋을지 이야기해봅시다.";
                    }
                } else if (caseType === "노무") {
                    if (scenarioTitle === "직장 내 괴롭힘 초기 대응") {
                        bestPractice = "리더: 홍대리, 최근 직장 내 괴롭힘으로 힘들어하는 직원이 있다고 들었습니다. 어떤 상황인지 말씀해주시겠습니까? 제가 도울 수 있는 부분은 무엇인지, 그리고 HR 절차를 함께 알아봅시다.";
                    }
                }


                return { wellDone, suggestions, bestPractice, nextLearning, riskLevel, hrKeywordsDetected };
            }

            /**
             * "코칭 받기" 버튼 클릭 핸들러
             */
            getCoachingBtn.addEventListener('click', () => {
                const userInput = leaderResponseInput.value.trim();
                if (!selectedCase || !selectedScenarioId || !userInput) {
                    showAlert('입력 오류', '케이스와 시나리오를 선택하고 리더의 답변을 입력해주세요.');
                    return;
                }

                const selectedScenario = scenarios[selectedCase].find(s => s.id === selectedScenarioId);
                const scenarioTitle = selectedScenario ? selectedScenario.title : "알 수 없는 시나리오";
                const coachingResult = localCoach(selectedCase, scenarioTitle, userInput);

                // 피드백 UI 업데이트
                feedbackWellDone.textContent = coachingResult.wellDone;
                feedbackSuggestions.textContent = coachingResult.suggestions;
                feedbackBestPractice.textContent = coachingResult.bestPractice;
                feedbackNextLearning.textContent = coachingResult.nextLearning;

                feedbackRiskBadge.textContent = coachingResult.riskLevel;
                feedbackRiskBadge.className = `risk-badge ${coachingResult.riskLevel}`; // 클래스 변경으로 색상 업데이트

                if (coachingResult.hrKeywordsDetected) {
                    feedbackHrButtonContainer.classList.remove('hidden');
                } else {
                    feedbackHrButtonContainer.classList.add('hidden');
                }
                coachingFeedbackSection.classList.remove('hidden');

                // 히스토리 추가
                coachingHistory.unshift({ // 최신 기록이 맨 위로 오도록 unshift 사용
                    caseType: selectedCase,
                    scenarioId: selectedScenarioId,
                    scenarioTitle: scenarioTitle,
                    userInput: userInput,
                    feedback: coachingResult,
                    timestamp: new Date().toISOString()
                });
                saveCoachingHistory();
                updateAllDashboards(); // 대시보드 및 히스토리 업데이트
            });

            /**
             * "모범 대화 복사" 버튼 클릭 핸들러
             */
            copyBestPracticeBtn.addEventListener('click', () => {
                const bestPracticeText = feedbackBestPractice.textContent;
                if (bestPracticeText) {
                    // document.execCommand('copy')는 iframe 환경에서 navigator.clipboard.writeText()보다 안정적일 수 있음
                    const textarea = document.createElement('textarea');
                    textarea.value = bestPracticeText;
                    textarea.style.position = 'fixed'; // 화면 밖으로 이동
                    textarea.style.opacity = 0;
                    document.body.appendChild(textarea);
                    textarea.select();
                    try {
                        document.execCommand('copy');
                        showAlert('복사 완료', '모범 대화가 클립보드에 복사되었습니다.');
                    } catch (err) {
                        console.error('클립보드 복사 실패:', err);
                        showAlert('복사 실패', '모범 대화 복사에 실패했습니다.');
                    } finally {
                        document.body.removeChild(textarea);
                    }
                } else {
                    showAlert('복사 실패', '복사할 모범 대화 내용이 없습니다.');
                }
            });

            // 초기화 버튼 핸들러
            resetHistoryBtn.addEventListener('click', () => {
                coachingHistory = [];
                localStorage.removeItem('coachingHistory');
                updateAllDashboards();
                showAlert('초기화 완료', '모든 코칭 기록이 초기화되었습니다.');
            });

            // 기록 내보내기 버튼 핸들러
            exportHistoryBtn.addEventListener('click', () => {
                if (coachingHistory.length === 0) {
                    showAlert('내보내기 실패', '내보낼 코칭 기록이 없습니다.');
                    return;
                }
                const dataStr = JSON.stringify(coachingHistory, null, 2);
                const blob = new Blob([dataStr], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `coaching_history_${new Date().toISOString().slice(0, 10)}.json`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                showAlert('내보내기 완료', '코칭 기록이 JSON 파일로 다운로드되었습니다.');
            });

            // HR 가이드 모달 트리거
            hrGuideTriggerBtn.addEventListener('click', () => {
                hrGuideModal.style.display = 'flex';
            });

            // 초기 로드 시 데이터 및 대시보드 업데이트
            coachingHistory = loadCoachingHistory();
            updateAllDashboards();

            // 케이스와 시나리오가 모두 선택되어야 코칭 받기 버튼이 활성화되도록 리스너 추가
            function toggleCoachingButton() {
                if (selectedCase && selectedScenarioId && leaderResponseInput.value.trim() !== '') {
                    getCoachingBtn.disabled = false;
                } else {
                    getCoachingBtn.disabled = true;
                }
                // 모범 대화 복사 버튼 활성화/비활성화
                if (feedbackBestPractice.textContent.trim() !== '' && !coachingFeedbackSection.classList.contains('hidden')) {
                    copyBestPracticeBtn.disabled = false;
                } else {
                    copyBestPracticeBtn.disabled = true;
                }
            }
            leaderResponseInput.addEventListener('input', toggleCoachingButton);
            // scenarioDropdown.addEventListener('change', toggleCoachingButton); // 이미 등록됨, 다시 확인 차 추가 (위에 있음)

            // 페이지 로드 시 초기 버튼 상태 설정 (피드백이 없을 경우 복사 버튼 비활성화)
            if (feedbackBestPractice.textContent.trim() === '') {
                copyBestPracticeBtn.disabled = true;
            }
            toggleCoachingButton(); // 초기 상태 설정
        };
    </script>
</body>
</html>
