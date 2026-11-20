<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>拾光日記 Gleam Diary - 社群共照版</title>
    
    <!-- 引入 Tailwind CSS 與文青字體 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Serif+TC:wght@300;400;500;600&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        serif: ['"Noto Serif TC"', 'serif'],
                        sans: ['ui-sans-serif', 'system-ui', '-apple-system', 'BlinkMacSystemFont', 'sans-serif']
                    },
                    colors: {
                        paper: '#f4f1eb',
                        'paper-dark': '#e8e4db',
                        ink: '#2c2b29',
                        'ink-light': '#5a5854',
                        accent: '#7a8266',
                        'accent-warm': '#b38269',
                        'border-soft': '#d6d2c8'
                    },
                    animation: {
                        'fade-in': 'fadeIn 0.35s ease-out forwards',
                        'float': 'float 6s ease-in-out infinite',
                        'float-delayed': 'float 6s ease-in-out 2s infinite',
                        'float-fast': 'float 4.5s ease-in-out 1s infinite',
                        'ping-slow': 'ping 2s cubic-bezier(0, 0, 0.2, 1) infinite'
                    },
                    keyframes: {
                        fadeIn: {
                            '0%': { opacity: '0', transform: 'translateY(8px)' },
                            '100%': { opacity: '1', transform: 'translateY(0)' }
                        },
                        float: {
                            '0%, 100%': { transform: 'translateY(0)' },
                            '50%': { transform: 'translateY(-6px)' }
                        }
                    }
                }
            }
        }
    </script>

    <style>
        body { 
            background: linear-gradient(135deg, #ece8df 0%, #dfd9cc 100%); 
            color: #2c2b29; 
            overflow-x: hidden; 
            -webkit-tap-highlight-color: transparent;
        }
        .app-shell {
            background: linear-gradient(180deg, #fdfcfb 0%, #f4f1eb 100%);
            min-height: 100vh;
        }
        .hide-scroll::-webkit-scrollbar { display: none; }
        .hide-scroll { -ms-overflow-style: none; scrollbar-width: none; }
        textarea { resize: none; }
        textarea:focus, input:focus, select:focus { outline: none; border-color: #7a8266; box-shadow: 0 0 0 1px #7a8266; }
        
        .glass-panel {
            background: rgba(255, 255, 255, 0.78);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.9);
            box-shadow: 0 6px 24px -4px rgba(44, 43, 41, 0.05);
        }
        .break-words-safe {
            word-break: break-word;
            overflow-wrap: anywhere;
        }
    </style>
</head>
<body class="font-serif antialiased flex justify-center">

    <!-- Mobile App Shell Container -->
    <div class="app-shell w-full max-w-md shadow-2xl relative flex flex-col pb-20 border-x border-border-soft/50">

        <!-- Top Sticky Header & 3-Day Progress Bar -->
        <header id="app-header" class="hidden sticky top-0 bg-white/85 backdrop-blur-xl z-30 border-b border-border-soft/60 px-4 pt-3 pb-2.5 shadow-sm">
            <div class="flex justify-between items-center mb-2.5">
                <div class="flex items-center gap-2">
                    <div id="header-user-avatar" class="w-7 h-7 rounded-full bg-ink text-white text-xs font-sans font-bold flex items-center justify-center shadow-sm">A1</div>
                    <h1 class="text-base font-medium tracking-widest text-ink">拾光日記</h1>
                </div>
                <div class="flex items-center gap-2">
                    <span id="save-status" class="text-[10px] font-sans text-ink-light/80 flex items-center gap-1 px-2 py-0.5 rounded-full bg-paper border border-border-soft/60">
                        <span class="w-1.5 h-1.5 rounded-full bg-emerald-500"></span>
                        已同步
                    </span>
                    <button onclick="reselectRole()" class="text-[11px] font-sans tracking-wider px-2.5 py-1 rounded-full border border-border-soft hover:bg-paper transition text-ink-light bg-white/80">
                        切換帳號
                    </button>
                </div>
            </div>
            
            <!-- 3 Days Timeline (Always visible at top) -->
            <div class="w-full pt-0.5" id="timeline-container"></div>
        </header>

        <!-- Main Content Area (Supports Touch Swipe) -->
        <main id="touch-area" class="flex-1 w-full px-4 py-4 overflow-x-hidden">
            
            <!-- 0. Login / Role Selection View -->
            <div id="login-view" class="w-full py-6 animate-fade-in">
                <div class="text-center mb-8">
                    <div class="w-14 h-14 rounded-2xl bg-accent/15 border border-accent/30 flex items-center justify-center mx-auto mb-3 text-accent shadow-inner">
                        <svg class="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path fill="currentColor" fill-opacity="0.18" d="M12 6.253v13C10.832 18.477 9.246 18 7.5 18s-3.332.477-4.5 1.253v-13C4.168 5.477 5.754 5 7.5 5s3.332.477 4.5 1.253z"/>
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/>
                        </svg>
                    </div>
                    <h2 class="text-2xl tracking-widest mb-1.5 text-ink font-medium">拾光日記</h2>
                    <p class="text-ink-light font-light text-xs tracking-wider">每位夥伴皆可記錄微行動、探索匿名圈與給予星探回饋</p>
                </div>
                
                <div class="glass-panel p-6 rounded-3xl mb-6">
                    <h3 class="text-xs font-sans text-ink-light tracking-widest uppercase text-center mb-4">選擇你的匿名代號登入</h3>
                    <div class="grid grid-cols-3 gap-3 mb-3">
                        <button onclick="initLogin('A1')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent/15 text-accent group-hover:bg-accent group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A1</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A1</span>
                        </button>
                        <button onclick="initLogin('A2')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent/15 text-accent group-hover:bg-accent group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A2</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A2</span>
                        </button>
                        <button onclick="initLogin('A3')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent/15 text-accent group-hover:bg-accent group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A3</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A3</span>
                        </button>
                    </div>
                    <div class="grid grid-cols-3 gap-3">
                        <button onclick="initLogin('A4')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent-warm hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent-warm/15 text-accent-warm group-hover:bg-accent-warm group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A4</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A4</span>
                        </button>
                        <button onclick="initLogin('A5')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent-warm hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent-warm/15 text-accent-warm group-hover:bg-accent-warm group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A5</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A5</span>
                        </button>
                        <button onclick="initLogin('A6')" class="flex flex-col items-center p-3.5 rounded-2xl border border-border-soft bg-white/80 hover:border-accent-warm hover:bg-white transition shadow-sm group">
                            <div class="w-11 h-11 rounded-full bg-accent-warm/15 text-accent-warm group-hover:bg-accent-warm group-hover:text-white transition flex items-center justify-center font-sans font-bold text-sm mb-1.5">A6</div>
                            <span class="text-[11px] font-sans text-ink-light">使用者 A6</span>
                        </button>
                    </div>
                </div>

                <div class="text-center mt-8">
                    <button onclick="toggleTeamModal(true)" class="text-xs font-sans text-ink-light/70 hover:text-ink transition border-b border-ink-light/30 pb-0.5">
                        開發團隊的話
                    </button>
                </div>
            </div>

            <!-- TAB 1 (LEFT): 收件匣與特質存摺 -->
            <div id="tab-left" class="hidden animate-fade-in space-y-5">
                <!-- Sub-navigation Switch (純手繪感文青實心線條 SVG) -->
                <div class="flex bg-paper-dark/70 p-1 rounded-2xl border border-border-soft/60">
                    <button id="subtab-btn-traits" onclick="switchLeftSubTab('traits')" class="flex-1 py-2 rounded-xl text-xs font-sans font-medium transition bg-white text-ink shadow-sm flex items-center justify-center gap-1.5">
                        <svg class="w-3.5 h-3.5 text-accent-warm shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                            <path fill="currentColor" fill-opacity="0.25" stroke-linecap="round" stroke-linejoin="round" d="M12 2l2.4 7.2L22 12l-7.6 2.8L12 22l-2.4-7.2L2 12l7.6-2.8L12 2z"/>
                        </svg>
                        <span>特質存摺</span>
                    </button>
                    <button id="subtab-btn-inbox" onclick="switchLeftSubTab('inbox')" class="flex-1 py-2 rounded-xl text-xs font-sans font-medium transition text-ink-light hover:text-ink flex items-center justify-center gap-1.5">
                        <svg class="w-3.5 h-3.5 text-accent shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                            <rect x="3" y="5" width="18" height="14" rx="2" fill="currentColor" fill-opacity="0.15"/>
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 7l9 6 9-6"/>
                        </svg>
                        <span>收到的回饋信</span>
                        <span id="inbox-badge" class="px-1.5 py-0.2 bg-accent-warm text-white rounded-full text-[10px]">0</span>
                    </button>
                    <button id="subtab-btn-mydiary" onclick="switchLeftSubTab('mydiary')" class="flex-1 py-2 rounded-xl text-xs font-sans font-medium transition text-ink-light hover:text-ink flex items-center justify-center gap-1.5">
                        <svg class="w-3.5 h-3.5 text-ink-light shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                            <path fill="currentColor" fill-opacity="0.15" d="M6 4h11a2 2 0 012 2v12a2 2 0 01-2 2H6a2 2 0 01-2-2V6a2 2 0 012-2z"/>
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8 9h7M8 13h5"/>
                        </svg>
                        <span>我的日記</span>
                    </button>
                </div>

                <!-- Subview A: Trait Bank -->
                <div id="left-sub-traits" class="space-y-4">
                    <div class="glass-panel rounded-3xl p-5">
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="text-sm font-medium tracking-widest text-ink flex items-center gap-1.5">
                                <svg class="w-4 h-4 text-accent-warm shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <path fill="currentColor" fill-opacity="0.3" stroke-linecap="round" stroke-linejoin="round" d="M12 2l2.6 6.8L21 11l-6.4 2.2L12 20l-2.6-6.8L3 11l6.4-2.2L12 2z"/>
                                </svg>
                                <span>夥伴為你點亮的特質雲</span>
                            </h3>
                            <span id="total-traits-count" class="text-[11px] font-sans text-ink-light">共 0 顆星</span>
                        </div>
                        <div id="floating-tags-area" class="min-h-[190px] flex flex-wrap items-center justify-center content-center gap-2.5 p-4 mb-4 rounded-2xl bg-gradient-to-b from-white/70 to-paper/60 border border-border-soft/60"></div>
                        <div class="bg-white/95 p-4 rounded-2xl border border-border-soft min-h-[100px] shadow-inner">
                            <h4 class="text-[11px] text-ink-light mb-1.5 font-sans">點擊上方特質膠囊查看完整回饋：</h4>
                            <div id="trait-detail-text" class="text-ink leading-relaxed text-sm break-words-safe">尚無特質紀錄，快去廣場與夥伴互動吧！</div>
                        </div>
                    </div>
                </div>

                <!-- Subview B: Inbox Letters -->
                <div id="left-sub-inbox" class="hidden space-y-3">
                    <div id="inbox-list" class="flex flex-col gap-3"></div>
                </div>

                <!-- Subview C: My All Diaries -->
                <div id="left-sub-mydiary" class="hidden space-y-3">
                    <div id="mydiary-list" class="flex flex-col gap-3"></div>
                </div>
            </div>

            <!-- TAB 2 (CENTER / HOME): 今日提問、反思發文與公開動態牆 -->
            <div id="tab-center" class="hidden animate-fade-in space-y-5">

                <!-- Today's Prompt & Composer Card -->
                <div class="glass-panel rounded-3xl p-5">
                    <div class="flex justify-between items-center mb-3">
                        <div class="flex items-center gap-1.5 flex-wrap">
                            <span class="text-xs font-sans text-accent tracking-widest bg-accent/10 px-3 py-1 rounded-full font-medium">Day <span id="c-day-num">1</span> 提問</span>
                            <span id="c-prompt-type-badge" class="text-[10px] font-sans px-2.5 py-0.5 rounded-full bg-paper-dark text-ink-light border border-border-soft font-medium">預設問題</span>
                        </div>
                        <span class="text-xs text-ink-light font-sans bg-white/80 px-2.5 py-1 rounded-full border border-border-soft/50" id="c-date-str">11/15</span>
                    </div>
                    
                    <!-- Prompt with Dice Button -->
                    <div class="flex items-start justify-between gap-3 mb-4">
                        <h2 id="c-prompt-text" class="text-base sm:text-lg leading-relaxed text-ink font-medium break-words-safe flex-1"></h2>
                        <button type="button" onclick="rollPromptDice()" title="換一個問題" class="shrink-0 flex items-center gap-1 px-2.5 py-1.5 rounded-xl bg-paper border border-border-soft hover:border-accent hover:bg-white text-ink-light hover:text-ink transition shadow-sm active:scale-95">
                            <svg id="dice-icon" class="w-4 h-4 text-accent-warm transition-transform duration-300" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <rect x="3" y="3" width="18" height="18" rx="4" fill="currentColor" fill-opacity="0.15" stroke-linecap="round" stroke-linejoin="round"/>
                                <circle cx="8.5" cy="8.5" r="1.3" fill="currentColor"/>
                                <circle cx="15.5" cy="8.5" r="1.3" fill="currentColor"/>
                                <circle cx="12" cy="12" r="1.3" fill="currentColor"/>
                                <circle cx="8.5" cy="15.5" r="1.3" fill="currentColor"/>
                                <circle cx="15.5" cy="15.5" r="1.3" fill="currentColor"/>
                            </svg>
                            <span class="text-[11px] font-sans">換題</span>
                        </button>
                    </div>
                    
                    <!-- Input Mode -->
                    <div id="c-input-area">
                        <div class="relative mb-3.5">
                            <textarea id="reflection-input" class="w-full bg-white/95 rounded-2xl p-3.5 pb-8 text-ink placeholder:text-ink-light/45 border border-border-soft focus:border-accent h-28 leading-relaxed shadow-inner text-sm" placeholder="寫下你今天的微行動或想法...（送出後將匿名分享至廣場）" maxlength="150"></textarea>
                            <div class="absolute bottom-2.5 right-3.5 text-[11px] font-sans text-ink-light"><span id="char-count">0</span> / 150</div>
                        </div>

                        <button onclick="submitReflection()" class="w-full py-3 rounded-xl bg-ink text-white text-sm tracking-widest hover:bg-ink-light shadow-md transition flex items-center justify-center gap-2">
                            <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path fill="currentColor" fill-opacity="0.2" d="M21 3L3 10.5l7.5 3L14 21l7-18z"/>
                                <path stroke-linecap="round" stroke-linejoin="round" d="M21 3L3 10.5l7.5 3L14 21l7-18zM10.5 13.5L21 3"/>
                            </svg>
                            <span>送出反思</span>
                        </button>
                    </div>

                    <!-- Already Submitted Compact View -->
                    <div id="c-submitted-area" class="hidden bg-paper/70 rounded-2xl p-4 border border-border-soft/70">
                        <div class="flex justify-between items-center mb-2">
                            <span class="text-[11px] font-sans px-2.5 py-0.5 rounded-full bg-white border border-border-soft text-ink-light inline-flex items-center gap-1.5">
                                <svg class="w-3.5 h-3.5 text-accent" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <circle cx="12" cy="12" r="9" fill="currentColor" fill-opacity="0.15"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M3.6 9h16.8M3.6 15h16.8M12 3a14.5 14.5 0 000 18M12 3a14.5 14.5 0 010 18"/>
                                </svg>
                                <span>已匿名分享至廣場</span>
                            </span>
                            <button onclick="editMyReflection()" class="text-xs font-sans text-accent underline hover:text-ink">編輯內容</button>
                        </div>
                        <p id="c-submitted-preview" class="text-sm text-ink leading-relaxed break-words-safe"></p>
                    </div>
                </div>

                <!-- Community Feed Section -->
                <div class="space-y-3">
                    <div class="flex justify-between items-center px-1">
                        <h3 class="text-sm font-medium tracking-widest text-ink flex items-center gap-1.5">
                            <svg class="w-4 h-4 text-accent shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path fill="currentColor" fill-opacity="0.2" stroke-linecap="round" stroke-linejoin="round" d="M8 10h8M8 14h5m-9 6l1.5-4.5A8 8 0 1112 20a8.2 8.2 0 01-3.5-.8L4 20z"/>
                            </svg>
                            <span>Day <span id="feed-day-label">1</span> 社群共照動態牆</span>
                        </h3>
                        <span class="text-[11px] font-sans text-ink-light">點選夥伴反思即可給予星探回饋</span>
                    </div>
                    <div id="public-feed-list" class="space-y-3.5">
                        <!-- Rendered by JS -->
                    </div>
                </div>
            </div>

            <!-- TAB 3 (RIGHT): 更多功能敬請期待 -->
            <div id="tab-right" class="hidden animate-fade-in">
                <div class="glass-panel rounded-3xl p-10 text-center my-8 flex flex-col items-center justify-center min-h-[300px]">
                    <div class="w-14 h-14 rounded-full bg-accent/10 border border-accent/20 text-accent flex items-center justify-center mb-4">
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path fill="currentColor" fill-opacity="0.2" d="M13 3l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"/>
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"/>
                        </svg>
                    </div>
                    <h3 class="text-base font-medium tracking-widest text-ink mb-2">更多功能敬請期待</h3>
                    <p class="text-xs font-sans text-ink-light leading-relaxed max-w-[220px]">更多互動功能正在籌備中，敬請期待後續更新。</p>
                </div>
            </div>

        </main>

        <!-- IG-Style Bottom Navigation Bar -->
        <nav id="bottom-nav" class="hidden fixed bottom-0 w-full max-w-md bg-white/95 backdrop-blur-xl border-t border-border-soft/80 z-40 px-6 py-2 flex justify-between items-center shadow-lg">
            <button onclick="switchMainTab('left')" id="nav-btn-left" class="flex flex-col items-center gap-1 py-1 px-4 rounded-2xl transition text-ink-light relative">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path fill="currentColor" fill-opacity="0.15" d="M4 13h2.586a1 1 0 01.707.293l2.414 2.414a1 1 0 00.707.293h3.172a1 1 0 00.707-.293l2.414-2.414A1 1 0 0117.414 13H20v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5z"/>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"/>
                </svg>
                <span class="text-[10px] font-sans tracking-wider font-medium">收件＆存摺</span>
            </button>
            
            <button onclick="switchMainTab('center')" id="nav-btn-center" class="flex flex-col items-center gap-1 py-1 px-5 rounded-2xl transition text-ink font-semibold">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path fill="currentColor" fill-opacity="0.15" d="M5 10l7-7 7 7v10a1 1 0 01-1 1h-3v-5a1 1 0 00-1-1h-4a1 1 0 00-1 1v5H6a1 1 0 01-1-1V10z"/>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"/>
                </svg>
                <span class="text-[10px] font-sans tracking-wider font-medium">今日廣場</span>
            </button>

            <button onclick="switchMainTab('right')" id="nav-btn-right" class="flex flex-col items-center gap-1 py-1 px-4 rounded-2xl transition text-ink-light">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <circle cx="12" cy="7" r="3" fill="currentColor" fill-opacity="0.15"/>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"/>
                </svg>
                <span class="text-[10px] font-sans tracking-wider font-medium">朋友圈</span>
            </button>
        </nav>

    </div>

    <!-- Feedback Drawer / Modal (給予他人回饋的二選一模板視窗：形狀第一、行為特質第二) -->
    <div id="feedback-modal" class="hidden fixed inset-0 bg-ink/50 backdrop-blur-sm z-50 flex items-end sm:items-center justify-center p-0 sm:p-4 animate-fade-in">
        <div class="bg-paper rounded-t-3xl sm:rounded-3xl p-5 sm:p-6 w-full max-w-md max-h-[90vh] overflow-y-auto shadow-2xl border border-white relative">
            <div class="flex justify-between items-center mb-4 pb-2 border-b border-border-soft">
                <div class="flex items-center gap-2">
                    <div id="fb-modal-target-avatar" class="w-7 h-7 rounded-full bg-accent text-white font-sans font-bold text-xs flex items-center justify-center">A1</div>
                    <h3 class="text-sm font-medium tracking-widest text-ink">給予 <span id="fb-modal-target-name">A1</span> 星探回饋</h3>
                </div>
                <button onclick="closeFeedbackModal()" class="text-ink-light hover:text-ink p-1.5" aria-label="關閉">
                    <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                    </svg>
                </button>
            </div>

            <!-- Target Reflection Preview -->
            <div class="bg-white/90 p-3.5 rounded-2xl border border-border-soft/80 mb-4 shadow-inner">
                <div class="flex items-center justify-between gap-2 mb-1.5 pb-1.5 border-b border-border-soft/60">
                    <span class="text-[10px] font-sans text-ink-light font-medium" id="fb-modal-day-str">Day 1 反思題目：</span>
                    <span id="fb-modal-prompt-badge" class="text-[10px] font-sans px-2 py-0.5 rounded-full bg-paper-dark text-ink-light border border-border-soft font-medium">預設問題</span>
                </div>
                <p id="fb-modal-prompt-text" class="text-xs text-accent font-medium mb-2 leading-relaxed break-words-safe"></p>
                <div class="text-[10px] font-sans text-ink-light mb-1">夥伴的反思內容：</div>
                <p id="fb-modal-reflection-text" class="text-sm text-ink leading-relaxed break-words-safe"></p>
            </div>

            <!-- 1. Template Selection (二選一：形狀優先，行為特質第二) -->
            <div class="mb-4">
                <label class="block text-xs font-sans text-ink-light mb-2 font-medium">1. 選擇回饋句型模板（二選一）</label>
                <div class="flex flex-col gap-2">
                    <label class="flex items-center p-2.5 bg-white/80 border border-border-soft rounded-xl cursor-pointer hover:bg-white transition text-xs">
                        <input type="radio" name="fb-template" value="3" class="mr-2.5 accent-accent-warm" onchange="onTemplateRadioChange('3')" checked>
                        <span>用隱喻 (形狀) 來形容你...</span>
                    </label>
                    <label class="flex items-center p-2.5 bg-white/80 border border-border-soft rounded-xl cursor-pointer hover:bg-white transition text-xs">
                        <input type="radio" name="fb-template" value="1" class="mr-2.5 accent-accent-warm" onchange="onTemplateRadioChange('1')">
                        <span>從你的行為，看出你的特質...</span>
                    </label>
                </div>
            </div>

            <!-- Template Fill Inputs -->
            <div id="template-fields" class="bg-white p-4 rounded-2xl border border-border-soft mb-4 shadow-inner"></div>

            <!-- 2. Tags Selector (2025 vs 2030 技能地圖 6 大領域 + 自訂標籤) -->
            <div class="mb-5">
                <div class="flex justify-between items-center mb-2">
                    <label class="block text-xs font-sans text-ink-light font-medium">2. 點選特質標籤 (最多 3 個)</label>
                    <span id="selected-tag-count" class="text-xs font-sans text-accent-warm font-medium">已選 0 / 3</span>
                </div>
                <div class="flex flex-col gap-2.5 max-h-52 overflow-y-auto pr-1 mb-3" id="tags-container"></div>
                <div class="flex gap-2">
                    <input type="text" id="custom-tag" placeholder="輸入自訂特質標籤（如：溫暖細膩）" class="flex-1 text-xs p-2.5 rounded-xl border border-border-soft bg-white" onkeydown="handleCustomTag(event)">
                    <button type="button" onclick="addCustomTagBtn()" class="px-3.5 py-2 bg-paper-dark text-ink text-xs font-sans rounded-xl border border-border-soft hover:bg-white transition flex items-center gap-1 shrink-0">
                        <svg class="w-3.5 h-3.5 text-accent" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M12 5v14M5 12h14"/>
                        </svg>
                        <span>新增</span>
                    </button>
                </div>
            </div>

            <button onclick="submitModalFeedback()" class="w-full py-3.5 rounded-xl bg-ink text-white text-sm tracking-widest hover:bg-ink-light shadow-md transition flex items-center justify-center gap-2">
                <svg class="w-4 h-4 text-accent-warm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                    <path fill="currentColor" fill-opacity="0.35" stroke-linecap="round" stroke-linejoin="round" d="M12 2l2.4 7.2L22 12l-7.6 2.8L12 22l-2.4-7.2L2 12l7.6-2.8L12 2z"/>
                </svg>
                <span>送出溫暖回饋</span>
            </button>
        </div>
    </div>

    <!-- Password Modal -->
    <div id="pwd-modal" class="hidden fixed inset-0 bg-ink/40 backdrop-blur-sm z-50 flex items-center justify-center p-4 animate-fade-in">
        <div class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl relative border border-white/50">
            <button onclick="closePasswordModal()" class="absolute top-4 right-4 text-ink-light hover:text-ink p-1" aria-label="關閉">
                <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                </svg>
            </button>
            <div id="pwd-avatar-preview" class="w-12 h-12 bg-accent/15 text-accent rounded-full flex items-center justify-center mb-3 mx-auto font-sans font-bold text-base">A1</div>
            <h3 class="text-base font-medium text-center text-ink mb-1 tracking-widest">輸入 <span id="pwd-role-label">A1</span> 專屬密碼</h3>
            <p class="text-[11px] text-ink-light text-center mb-5 font-sans">驗證身分以開啟你的日記與廣場</p>
            <input type="password" id="pwd-input" class="w-full text-center tracking-widest text-lg p-3 border border-border-soft rounded-xl mb-3 focus:border-accent bg-paper/50" placeholder="•••">
            <p id="pwd-error" class="hidden text-xs text-red-500 text-center mb-3 font-sans">密碼錯誤，請重新輸入</p>
            <button onclick="confirmPassword()" class="w-full bg-ink text-white py-3 rounded-xl hover:bg-ink-light tracking-widest text-sm shadow-md transition">確認進入</button>
        </div>
    </div>

    <!-- Notice Modal -->
    <div id="notice-modal" class="hidden fixed inset-0 bg-ink/40 backdrop-blur-sm z-[60] flex items-center justify-center p-4 animate-fade-in">
        <div class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl text-center border border-white/50">
            <p id="notice-msg" class="text-ink text-sm mb-5 tracking-wide leading-relaxed font-medium break-words-safe"></p>
            <button onclick="closeNoticeModal()" class="bg-ink text-white px-8 py-2 rounded-full hover:bg-ink-light tracking-widest text-xs shadow-md transition">好</button>
        </div>
    </div>

    <!-- Team Modal -->
    <div id="team-modal" class="hidden fixed inset-0 bg-ink/40 backdrop-blur-sm z-[60] flex items-center justify-center p-4 animate-fade-in">
        <div class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl relative border border-white/50">
            <button onclick="toggleTeamModal(false)" class="absolute top-4 right-4 text-ink-light hover:text-ink p-1" aria-label="關閉">
                <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                </svg>
            </button>
            <h3 class="text-base font-medium text-center text-ink mb-3 tracking-widest border-b border-border-soft/60 pb-2.5">開發團隊的話</h3>
            <p class="text-xs text-ink-light leading-relaxed tracking-wide text-justify font-sans">
                此為清大 115-1 社會創業課程的小組專案 MVP 測試。<br><br>
                所有填寫的資料內容將僅提供給開發團隊進行專案分析與發表，並會進行嚴格的去識別化處理，絕不外洩。請放心書寫，享受互相照見的過程。
            </p>
            <div class="mt-5 flex justify-center">
                <button onclick="toggleTeamModal(false)" class="bg-paper border border-border-soft text-ink px-6 py-2 rounded-full hover:bg-white tracking-widest text-xs transition">了解</button>
            </div>
        </div>
    </div>

    <script>
        // Google Apps Script Web App URL (請替換為您新試算表部署的網址)
        const GAS_URL = "https://script.google.com/macros/s/AKfycbxW7dHyGRemNoEUYlXyAu8g8SOiUF16mDEbXnaiam3QFNclu3VuBSnG5jbR0jKSQ02W/exec"; 

        const ALL_USERS = ['A1', 'A2', 'A3', 'A4', 'A5', 'A6'];
        const USER_PASSWORDS = {
            'A1': '123',
            'A2': '168',
            'A3': '576',
            'A4': '246',
            'A5': '532',
            'A6': '321'
        };

        // 3 天預設題目與隨機題庫
        const defaultQuestions = [
            "今天哪一刻覺得自己好像做得不錯？",
            "今天什麼時候覺得自己最有能量？",
            "今天和別人相處時，有沒有哪個反應或回饋讓你印象深刻？"
        ];
        const questionPool = [
            "今天遇到問題時，你用了什麼方式處理？",
            "今天有什麼事情讓你產生好奇，想進一步了解？",
            "今天有沒有哪件事，你發現自己有一套習慣的做法？",
            "今天有沒有哪件事，讓你發現自己和原本想像的不太一樣？",
            "今天有沒有哪個他人的反應或一句話，讓你重新思考自己？"
        ];
        const dates = ["11/15", "11/16", "11/17"];
        const TOTAL_DAYS = 3;

        const SHAPE_OPTIONS = ["愛心", "星形", "正方形", "圓形", "三角形", "多邊形"];

        const skillCategories = [
            {
                name: "個人核心韌性",
                badgeClass: "bg-sky-50 text-sky-800 border-sky-200 hover:bg-sky-100",
                activeClass: "bg-sky-600 text-white border-sky-600 shadow-sm",
                skills: ["#自我學習", "#抗壓韌性", "#情緒管理"]
            },
            {
                name: "思考與表達力",
                badgeClass: "bg-indigo-50 text-indigo-800 border-indigo-200 hover:bg-indigo-100",
                activeClass: "bg-indigo-600 text-white border-indigo-600 shadow-sm",
                skills: ["#問題解決力", "#創新應用", "#系統性思辨"]
            },
            {
                name: "跨人際與合作能力",
                badgeClass: "bg-emerald-50 text-emerald-800 border-emerald-200 hover:bg-emerald-100",
                activeClass: "bg-emerald-600 text-white border-emerald-600 shadow-sm",
                skills: ["#執行力", "#團隊合作", "#人際溝通", "#同理心", "#多元傾聽", "#客戶思維", "#跨文化理解"]
            },
            {
                name: "專業與數位技能",
                badgeClass: "bg-purple-50 text-purple-800 border-purple-200 hover:bg-purple-100",
                activeClass: "bg-purple-600 text-white border-purple-600 shadow-sm",
                skills: ["#AI與數位技能", "#資安管理", "#品質控管", "#數據分析", "#外語能力"]
            },
            {
                name: "人文與社會責任",
                badgeClass: "bg-amber-50 text-amber-800 border-amber-200 hover:bg-amber-100",
                activeClass: "bg-amber-600 text-white border-amber-600 shadow-sm",
                skills: ["#永續素養", "#DEI素養", "#人文素養"]
            },
            {
                name: "領導與組織管理",
                badgeClass: "bg-orange-50 text-orange-800 border-orange-200 hover:bg-orange-100",
                activeClass: "bg-orange-600 text-white border-orange-600 shadow-sm",
                skills: ["#領導力", "#人才管理", "#組織設計"]
            }
        ];

        window.AppState = {
            currentUser: null,        // 'A1' ~ 'A6'
            currentDay: 1,            // 1 ~ 3
            activeTab: 'center',      // 'left', 'center', 'right'
            leftSubTab: 'traits',     // 'traits', 'inbox', 'mydiary'
            isEditingMyRef: false,
            activeFeedbackTarget: null,
            expandedFeedPosts: {},    // key: `${user}_${day}` -> boolean (紀錄動態牆展開狀態)
            traitListCache: [],
            customPrompts: {},        // key: `${user}_${day}` -> prompt string
            diceRollCounts: {},       // key: `${user}_${day}` -> number (0: 預設問題, 1~3: 自選問題)
            reflections: {},          // key: `${user}_${day}` -> { user, day, content, visibility: 'public', timestamp }
            feedbacks: [],            // array of { id, day, fromUser, toUser, content, tags:[], timestamp }
            drafts: {}                // preserves typing & custom tags without interruption
        };

        function loadLocalBackup() {
            try {
                const sRef = localStorage.getItem('gleam_social_reflections_v7');
                if (sRef) window.AppState.reflections = JSON.parse(sRef);
                const sFb = localStorage.getItem('gleam_social_feedbacks_v7');
                if (sFb) window.AppState.feedbacks = JSON.parse(sFb);
                const sDr = localStorage.getItem('gleam_social_drafts_v7');
                if (sDr) window.AppState.drafts = JSON.parse(sDr);
                const sPr = localStorage.getItem('gleam_social_prompts_v7');
                if (sPr) window.AppState.customPrompts = JSON.parse(sPr);
                const sDc = localStorage.getItem('gleam_social_dice_counts_v7');
                if (sDc) window.AppState.diceRollCounts = JSON.parse(sDc);
            } catch (e) {}
        }

        function saveLocalBackup() {
            try {
                localStorage.setItem('gleam_social_reflections_v7', JSON.stringify(window.AppState.reflections));
                localStorage.setItem('gleam_social_feedbacks_v7', JSON.stringify(window.AppState.feedbacks));
                localStorage.setItem('gleam_social_drafts_v7', JSON.stringify(window.AppState.drafts));
                localStorage.setItem('gleam_social_prompts_v7', JSON.stringify(window.AppState.customPrompts));
                localStorage.setItem('gleam_social_dice_counts_v7', JSON.stringify(window.AppState.diceRollCounts));
            } catch (e) {}
        }

        function getPromptForUserDay(u, d) {
            const key = `${u}_${d}`;
            const savedRef = window.AppState.reflections[key];
            if (savedRef && savedRef.prompt && !window.AppState.customPrompts[key]) {
                return savedRef.prompt;
            }
            return window.AppState.customPrompts[key] || defaultQuestions[d - 1] || defaultQuestions[0];
        }

        function isDefaultPromptForDay(promptText, dayNum) {
            const defQ = defaultQuestions[dayNum - 1] || defaultQuestions[0];
            return !promptText || String(promptText).trim() === defQ;
        }

        function updateCenterPromptBadge(u, d) {
            const badgeEl = document.getElementById('c-prompt-type-badge');
            if (!badgeEl) return;
            const currentPrompt = getPromptForUserDay(u, d);
            const isDef = isDefaultPromptForDay(currentPrompt, d);
            const rollCount = window.AppState.diceRollCounts[`${u}_${d}`] || 0;

            if (isDef) {
                badgeEl.innerText = '預設問題';
                badgeEl.className = 'text-[10px] font-sans px-2.5 py-0.5 rounded-full bg-paper-dark text-ink-light border border-border-soft font-medium';
            } else {
                const countHint = (rollCount >= 1 && rollCount <= 3) ? ` (${rollCount}/3)` : '';
                badgeEl.innerText = `自選問題${countHint}`;
                badgeEl.className = 'text-[10px] font-sans px-2.5 py-0.5 rounded-full bg-accent-warm/15 text-accent-warm border border-accent-warm/30 font-medium';
            }
        }

        window.rollPromptDice = function() {
            const u = window.AppState.currentUser;
            const d = window.AppState.currentDay;
            const key = `${u}_${d}`;
            const currentPrompt = getPromptForUserDay(u, d);
            let rollCount = window.AppState.diceRollCounts[key] || 0;

            // 最多骰三次為「自選問題」，第四次點擊則自動回到當日「預設問題」
            let nextPrompt = '';
            if (rollCount >= 3) {
                rollCount = 0;
                nextPrompt = defaultQuestions[d - 1] || defaultQuestions[0];
            } else {
                rollCount += 1;
                const candidates = questionPool.filter(q => q !== currentPrompt);
                const poolToUse = candidates.length > 0 ? candidates : questionPool;
                nextPrompt = poolToUse[Math.floor(Math.random() * poolToUse.length)];
            }

            window.AppState.diceRollCounts[key] = rollCount;
            window.AppState.customPrompts[key] = nextPrompt;
            saveLocalBackup();

            const diceIcon = document.getElementById('dice-icon');
            if (diceIcon) {
                diceIcon.style.transform = 'rotate(360deg)';
                setTimeout(() => { diceIcon.style.transform = 'rotate(0deg)'; }, 300);
            }
            const promptEl = document.getElementById('c-prompt-text');
            if (promptEl) promptEl.innerText = nextPrompt;
            updateCenterPromptBadge(u, d);
        };

        // 偵測回饋文字中是否提及預設形狀，並回傳對應的文青實心結合線條信封＋形狀視覺化圖示
        function detectMentionedShape(text) {
            if (!text) return null;
            const s = String(text);
            if (s.includes('愛心') || s.includes('心形') || s.includes('心型')) return 'heart';
            if (s.includes('星形') || s.includes('星型')) return 'star';
            if (s.includes('正方形')) return 'square';
            if (s.includes('圓形')) return 'circle';
            if (s.includes('三角形')) return 'triangle';
            if (s.includes('多邊形')) return 'polygon';
            return null;
        }

        function getEnvelopeWithShapeVisualHtml(contentText) {
            const shape = detectMentionedShape(contentText);
            let shapeBadgeSvg = '';
            let shapeLabel = '';

            if (shape === 'heart') {
                shapeLabel = '愛心';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-rose-500" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M12 20.5l-1.35-1.22C5.6 14.7 2.5 11.88 2.5 8.4 2.5 5.6 4.7 3.4 7.5 3.4c1.6 0 3.14.74 4.5 2.05 1.36-1.31 2.9-2.05 4.5-2.05 2.8 0 5 2.2 5 5 0 3.48-3.1 6.3-8.15 10.88L12 20.5z"/></svg>`;
            } else if (shape === 'star') {
                shapeLabel = '星形';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-amber-500" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M12 2.5l2.85 5.78 6.38.93-4.61 4.5 1.09 6.35L12 17.06l-5.71 3 1.09-6.35-4.61-4.5 6.38-.93L12 2.5z"/></svg>`;
            } else if (shape === 'square') {
                shapeLabel = '正方形';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-sky-600" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><rect x="4.5" y="4.5" width="15" height="15" rx="2.5"/></svg>`;
            } else if (shape === 'circle') {
                shapeLabel = '圓形';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-emerald-600" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><circle cx="12" cy="12" r="8"/></svg>`;
            } else if (shape === 'triangle') {
                shapeLabel = '三角形';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-purple-600" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M12 3.5L3 19.5h18L12 3.5z"/></svg>`;
            } else if (shape === 'polygon') {
                shapeLabel = '多邊形';
                shapeBadgeSvg = `<svg class="w-4 h-4 text-indigo-600" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="1.5"><polygon stroke-linecap="round" stroke-linejoin="round" points="12,2.5 20.5,8.5 17.2,19.5 6.8,19.5 3.5,8.5"/></svg>`;
            }

            return `
                <div class="shrink-0 flex flex-col items-center justify-center">
                    <div class="relative w-12 h-12 rounded-2xl bg-paper border border-border-soft flex items-center justify-center shadow-sm">
                        <svg class="w-6 h-6 text-ink-light" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <rect x="3" y="5" width="18" height="14" rx="2" fill="currentColor" fill-opacity="0.12"/>
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/>
                        </svg>
                        ${shapeBadgeSvg ? `
                            <div class="absolute -bottom-1.5 -right-1.5 w-6 h-6 rounded-full bg-white border border-border-soft shadow flex items-center justify-center" title="形狀隱喻：${shapeLabel}">
                                ${shapeBadgeSvg}
                            </div>
                        ` : ''}
                    </div>
                    ${shapeLabel ? `<span class="text-[9px] font-sans text-ink-light mt-1 font-medium">${shapeLabel}</span>` : ''}
                </div>
            `;
        }

        function updateSaveIndicator(text, isSyncing = false) {
            const el = document.getElementById('save-status');
            if (!el) return;
            const dot = isSyncing ? 'bg-amber-400 animate-ping-slow' : 'bg-emerald-500';
            el.innerHTML = `<span class="w-1.5 h-1.5 rounded-full ${dot}"></span>${text}`;
        }

        function formatFeedbackSentence(str) {
            if (!str) return '';
            return String(str).replace(/\*\*(.*?)\*\*/g, '<strong class="text-ink font-semibold underline decoration-accent-warm/50 underline-offset-4">$1</strong>');
        }

        function getAvatarColorClass(userId) {
            const num = parseInt(String(userId).replace('A', ''), 10);
            return (num <= 3) 
                ? 'bg-accent text-white' 
                : 'bg-accent-warm text-white';
        }

        // --- 登入與密碼驗證 ---
        let pendingLoginUser = null;

        window.initLogin = function(userId) {
            pendingLoginUser = userId;
            document.getElementById('pwd-role-label').innerText = userId;
            const avatarEl = document.getElementById('pwd-avatar-preview');
            avatarEl.innerText = userId;
            avatarEl.className = `w-12 h-12 rounded-full flex items-center justify-center mb-3 mx-auto font-sans font-bold text-base shadow-sm ${getAvatarColorClass(userId)}`;
            
            const modal = document.getElementById('pwd-modal');
            const input = document.getElementById('pwd-input');
            document.getElementById('pwd-error').classList.add('hidden');
            modal.classList.remove('hidden');
            input.value = '';
            setTimeout(() => input.focus(), 100);
        };

        window.closePasswordModal = function() {
            document.getElementById('pwd-modal').classList.add('hidden');
            pendingLoginUser = null;
        };

        window.confirmPassword = function() {
            const val = document.getElementById('pwd-input').value.trim();
            const expected = USER_PASSWORDS[pendingLoginUser];
            if (val === expected || val === '0000') {
                finalizeLogin(pendingLoginUser);
            } else {
                document.getElementById('pwd-error').classList.remove('hidden');
            }
        };

        function finalizeLogin(userId) {
            window.AppState.currentUser = userId;
            window.AppState.isEditingMyRef = false;

            const headerAvatar = document.getElementById('header-user-avatar');
            headerAvatar.innerText = userId;
            headerAvatar.className = `w-7 h-7 rounded-full text-xs font-sans font-bold flex items-center justify-center shadow-sm ${getAvatarColorClass(userId)}`;

            document.getElementById('login-view').classList.add('hidden');
            document.getElementById('app-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            closePasswordModal();

            switchMainTab('center', true);
            fetchSheetData();
        }

        window.reselectRole = function() {
            window.AppState.currentUser = null;
            document.getElementById('app-header').classList.add('hidden');
            document.getElementById('bottom-nav').classList.add('hidden');
            ['tab-left', 'tab-center', 'tab-right'].forEach(id => document.getElementById(id).classList.add('hidden'));
            document.getElementById('login-view').classList.remove('hidden');
        };

        // --- 底部三欄分頁切換 ---
        window.switchMainTab = function(tabName, forceRebuild = false) {
            window.AppState.activeTab = tabName;
            ['left', 'center', 'right'].forEach(t => {
                const view = document.getElementById('tab-' + t);
                const btn = document.getElementById('nav-btn-' + t);
                if (t === tabName) {
                    view.classList.remove('hidden');
                    btn.classList.remove('text-ink-light');
                    btn.classList.add('text-ink', 'font-semibold', 'bg-paper/80');
                } else {
                    view.classList.add('hidden');
                    btn.classList.add('text-ink-light');
                    btn.classList.remove('text-ink', 'font-semibold', 'bg-paper/80');
                }
            });
            refreshAllViews(forceRebuild);
            window.scrollTo({ top: 0, behavior: 'smooth' });
        };

        window.switchLeftSubTab = function(subName) {
            window.AppState.leftSubTab = subName;
            ['traits', 'inbox', 'mydiary'].forEach(s => {
                const el = document.getElementById('left-sub-' + s);
                const btn = document.getElementById('subtab-btn-' + s);
                if (s === subName) {
                    el.classList.remove('hidden');
                    btn.className = "flex-1 py-2 rounded-xl text-xs font-sans font-medium transition bg-white text-ink shadow-sm flex items-center justify-center gap-1.5";
                } else {
                    el.classList.add('hidden');
                    btn.className = "flex-1 py-2 rounded-xl text-xs font-sans font-medium transition text-ink-light hover:text-ink flex items-center justify-center gap-1.5";
                }
            });
            renderLeftTab();
        };

        // --- 頂部 3 日行事曆進度條 ---
        window.setDay = function(d) {
            window.AppState.currentDay = d;
            window.AppState.isEditingMyRef = false;
            refreshAllViews(true);
        };

        function renderTimeline() {
            const container = document.getElementById('timeline-container');
            const u = window.AppState.currentUser;
            let html = '<div class="grid grid-cols-3 w-full py-0.5 max-w-xs mx-auto">';

            for (let i = 1; i <= TOTAL_DAYS; i++) {
                const myRef = window.AppState.reflections[`${u}_${i}`];
                const hasData = !!(myRef && myRef.content);
                const isCurrent = i === window.AppState.currentDay;
                
                let dotClass = 'w-3.5 h-3.5 rounded-full border-[2px] transition-all duration-300 relative z-10 box-border ';
                let textClass = 'text-[10px] font-sans transition-all duration-300 mt-0.5 ';
                let dayClass = 'text-[9px] font-sans font-medium tracking-wider mb-0.5 transition-all duration-300 ';
                
                if (hasData) {
                    dotClass += 'border-white bg-ink shadow scale-105';
                    textClass += 'text-ink font-medium';
                    dayClass += 'text-ink font-medium';
                } else if (isCurrent) {
                    dotClass += 'border-accent bg-paper shadow-sm scale-110 ring-2 ring-accent/25';
                    textClass += 'text-accent font-semibold';
                    dayClass += 'text-accent font-semibold';
                } else {
                    dotClass += 'border-border-soft bg-paper';
                    textClass += 'text-ink-light/50';
                    dayClass += 'text-ink-light/50';
                }
                
                html += `
                    <div class="flex flex-col items-center cursor-pointer group select-none" onclick="window.setDay(${i})">
                        <span class="${dayClass}">Day ${i}</span>
                        <div class="relative flex items-center justify-center w-full h-4">
                            ${i > 1 ? '<div class="absolute left-0 right-1/2 top-1/2 -translate-y-1/2 h-[2px] bg-border-soft z-0"></div>' : ''}
                            ${i < TOTAL_DAYS ? '<div class="absolute left-1/2 right-0 top-1/2 -translate-y-1/2 h-[2px] bg-border-soft z-0"></div>' : ''}
                            <div class="${dotClass}"></div>
                        </div>
                        <span class="${textClass}">${dates[i-1]}</span>
                    </div>`;
            }
            html += '</div>';
            container.innerHTML = html;
        }

        function refreshAllViews(forceRebuildInput = false) {
            if (!window.AppState.currentUser) return;
            renderTimeline();
            updateInboxBadge();
            if (window.AppState.activeTab === 'center') renderCenterTab(forceRebuildInput);
            if (window.AppState.activeTab === 'left') renderLeftTab();
        }

        // --- 中間分頁：今日廣場與發文 ---
        function getMyRefDraft(u, d) {
            const key = `ref_${u}_${d}`;
            if (!window.AppState.drafts[key]) {
                const existing = window.AppState.reflections[`${u}_${d}`];
                window.AppState.drafts[key] = {
                    content: existing ? existing.content : '',
                    visibility: 'public'
                };
            }
            return window.AppState.drafts[key];
        }

        function renderCenterTab(forceRebuildInput = false) {
            const u = window.AppState.currentUser;
            const d = window.AppState.currentDay;

            document.getElementById('c-day-num').innerText = d;
            document.getElementById('feed-day-label').innerText = d;
            document.getElementById('c-date-str').innerText = dates[d-1];
            document.getElementById('c-prompt-text').innerText = getPromptForUserDay(u, d);
            updateCenterPromptBadge(u, d);

            const myRef = window.AppState.reflections[`${u}_${d}`];
            const inputArea = document.getElementById('c-input-area');
            const submittedArea = document.getElementById('c-submitted-area');
            const draft = getMyRefDraft(u, d);

            if (myRef && myRef.content && !window.AppState.isEditingMyRef) {
                inputArea.classList.add('hidden');
                submittedArea.classList.remove('hidden');
                document.getElementById('c-submitted-preview').innerText = myRef.content;
            } else {
                inputArea.classList.remove('hidden');
                submittedArea.classList.add('hidden');
                if (forceRebuildInput) {
                    const inputEl = document.getElementById('reflection-input');
                    inputEl.value = draft.content || '';
                    document.getElementById('char-count').innerText = inputEl.value.length;
                }
            }

            renderCommunityFeed();
        }

        window.editMyReflection = function() {
            const u = window.AppState.currentUser;
            const d = window.AppState.currentDay;
            const myRef = window.AppState.reflections[`${u}_${d}`];
            if (myRef) {
                window.AppState.drafts[`ref_${u}_${d}`] = {
                    content: myRef.content,
                    visibility: 'public'
                };
            }
            window.AppState.isEditingMyRef = true;
            renderCenterTab(true);
        };

        window.submitReflection = function() {
            const u = window.AppState.currentUser;
            const d = window.AppState.currentDay;
            const inputEl = document.getElementById('reflection-input');
            const content = inputEl.value.trim();
            const draft = getMyRefDraft(u, d);
            const visibility = 'public';
            const key = `${u}_${d}`;

            if (!content && !window.AppState.reflections[key]) {
                window.showNotice("請先寫下一點今日的微行動或想法喔！");
                return;
            }

            const nowStr = new Date().toLocaleString('zh-TW');
            const currentPrompt = getPromptForUserDay(u, d);
            if (content) {
                window.AppState.reflections[key] = {
                    user: u,
                    day: d,
                    prompt: currentPrompt,
                    content: content,
                    visibility: visibility,
                    timestamp: nowStr
                };
            } else {
                delete window.AppState.reflections[key];
            }

            draft.content = content;
            window.AppState.isEditingMyRef = false;
            saveLocalBackup();
            refreshAllViews(true);

            sendDataToCloud({
                action: 'saveReflection',
                user: u,
                day: d,
                prompt: currentPrompt,
                content: content,
                visibility: visibility,
                timestamp: nowStr
            });
        };

        function renderCommunityFeed() {
            const container = document.getElementById('public-feed-list');
            const u = window.AppState.currentUser;
            const d = window.AppState.currentDay;
            let html = '';
            let visibleCount = 0;

            ALL_USERS.forEach(uid => {
                const post = window.AppState.reflections[`${uid}_${d}`];
                if (post && post.content) {
                    visibleCount++;
                    html += buildPostCardHtml(post, u);
                }
            });

            if (visibleCount === 0) {
                html = `
                    <div class="glass-panel rounded-3xl p-8 text-center text-ink-light text-xs leading-relaxed">
                        目前 Day ${d} 廣場還沒有反思貼文。<br>成為今天第一個分享微行動的人吧！
                    </div>
                `;
            }
            container.innerHTML = html;
        }

        window.toggleFeedFeedback = function(postKey) {
            window.AppState.expandedFeedPosts[postKey] = !window.AppState.expandedFeedPosts[postKey];
            renderCommunityFeed();
        };

        // 廣場貼文：顯示匿名夥伴反思，並提供「已有 ? 人回饋，點擊展開」查看其他匿名者給他的回饋內容
        function buildPostCardHtml(post, viewerId) {
            const isMe = post.user === viewerId;
            const postKey = `${post.user}_${post.day}`;
            const postFeedbacks = window.AppState.feedbacks.filter(f => Number(f.day) === Number(post.day) && f.toUser === post.user);
            const myExistingFb = postFeedbacks.find(f => f.fromUser === viewerId);
            const fbCount = postFeedbacks.length;
            const isExpanded = !!window.AppState.expandedFeedPosts[postKey];
            const postPrompt = post.prompt || getPromptForUserDay(post.user, post.day);
            const isDefPrompt = isDefaultPromptForDay(postPrompt, post.day);
            const promptTypeBadgeHtml = isDefPrompt
                ? `<span class="text-[10px] font-sans px-2 py-0.5 rounded-full bg-paper-dark text-ink-light border border-border-soft font-medium shrink-0">預設問題</span>`
                : `<span class="text-[10px] font-sans px-2 py-0.5 rounded-full bg-accent-warm/15 text-accent-warm border border-accent-warm/30 font-medium shrink-0">自選問題</span>`;

            let expandableFeedbackHtml = '';
            if (fbCount > 0) {
                const fbItemsHtml = postFeedbacks.map(fb => `
                    <div class="bg-white/90 p-3 rounded-2xl border border-border-soft/70 flex items-start gap-3">
                        ${getEnvelopeWithShapeVisualHtml(fb.content)}
                        <div class="flex-1 min-w-0">
                            <div class="flex items-center justify-between mb-1">
                                <span class="text-[11px] font-sans font-medium text-ink flex items-center gap-1.5">
                                    <span class="w-4 h-4 rounded-full text-[9px] flex items-center justify-center font-bold ${getAvatarColorClass(fb.fromUser)}">${fb.fromUser}</span>
                                    <span>匿名夥伴 ${fb.fromUser}</span>
                                </span>
                            </div>
                            <p class="text-xs text-ink leading-relaxed mb-2 break-words-safe">${formatFeedbackSentence(fb.content)}</p>
                            <div class="flex flex-wrap gap-1">
                                ${(fb.tags || []).map(t => `<span class="text-[10px] font-sans bg-paper px-2 py-0.5 rounded-full border border-border-soft text-ink-light">${t}</span>`).join('')}
                            </div>
                        </div>
                    </div>
                `).join('');

                expandableFeedbackHtml = `
                    <div class="mt-3.5 pt-2.5 border-t border-border-soft/60">
                        <button type="button" onclick="toggleFeedFeedback('${postKey}')" class="w-full flex items-center justify-between text-xs font-sans text-ink-light hover:text-ink py-1 px-1 rounded-lg transition">
                            <span class="flex items-center gap-1.5 font-medium">
                                <svg class="w-3.5 h-3.5 text-accent-warm shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <path fill="currentColor" fill-opacity="0.2" stroke-linecap="round" stroke-linejoin="round" d="M21 11.5a8.38 8.38 0 01-.9 3.8 8.5 8.5 0 01-7.6 4.7 8.38 8.38 0 01-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 01-.9-3.8 8.5 8.5 0 014.7-7.6 8.38 8.38 0 013.8-.9h.5a8.48 8.48 0 018 8v.5z"/>
                                </svg>
                                <span>已有 ${fbCount} 人回饋（點擊${isExpanded ? '收起' : '展開'}）</span>
                            </span>
                            <svg class="w-4 h-4 transition-transform duration-200 ${isExpanded ? 'rotate-180' : ''}" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/>
                            </svg>
                        </button>
                        <div class="${isExpanded ? 'mt-2.5 space-y-2 animate-fade-in' : 'hidden'}">
                            ${fbItemsHtml}
                        </div>
                    </div>
                `;
            } else {
                expandableFeedbackHtml = `
                    <div class="mt-3.5 pt-2.5 border-t border-border-soft/50 flex items-center gap-1.5 text-[11px] font-sans text-ink-light/65">
                        <svg class="w-3.5 h-3.5 text-ink-light/50 shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"/>
                        </svg>
                        <span>尚無夥伴回饋，成為第一個點亮他特質的人吧</span>
                    </div>
                `;
            }

            return `
                <div id="post-card-${post.user}" class="glass-panel rounded-3xl p-4 sm:p-5 transition duration-300">
                    <div class="flex justify-between items-center mb-3">
                        <div class="flex items-center gap-2.5">
                            <div class="w-9 h-9 rounded-full flex items-center justify-center font-sans font-bold text-xs shadow-sm ${getAvatarColorClass(post.user)}">
                                ${post.user}
                            </div>
                            <div>
                                <div class="text-xs font-sans font-semibold text-ink flex items-center gap-1.5">
                                    <span>匿名夥伴 ${post.user}</span>
                                    ${isMe ? '<span class="text-[10px] bg-paper-dark px-2 py-0.2 rounded-full text-ink-light font-normal">我自己</span>' : ''}
                                </div>
                                <div class="text-[10px] font-sans text-ink-light/70 flex items-center gap-1.5 mt-0.5 flex-wrap">
                                    <span class="inline-flex items-center gap-1">
                                        <svg class="w-3 h-3 text-accent" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                            <circle cx="12" cy="12" r="9" fill="currentColor" fill-opacity="0.15"/>
                                            <path stroke-linecap="round" stroke-linejoin="round" d="M3.6 9h16.8M3.6 15h16.8M12 3a14.5 14.5 0 000 18M12 3a14.5 14.5 0 010 18"/>
                                        </svg>
                                        <span>匿名公開</span>
                                    </span>
                                    ${promptTypeBadgeHtml}
                                </div>
                            </div>
                        </div>
                        <button onclick="openFeedbackModal('${post.user}', ${post.day})" class="px-3 py-1.5 rounded-full text-xs font-sans font-medium transition flex items-center gap-1.5 shadow-sm ${myExistingFb ? 'bg-accent-warm/15 text-accent-warm border border-accent-warm/30' : 'bg-ink text-white hover:bg-ink-light'}">
                            <svg class="w-3.5 h-3.5 shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path fill="currentColor" fill-opacity="0.3" stroke-linecap="round" stroke-linejoin="round" d="M12 2l2.4 7.2L22 12l-7.6 2.8L12 22l-2.4-7.2L2 12l7.6-2.8L12 2z"/>
                            </svg>
                            <span>${myExistingFb ? '已回饋 (可修改)' : '給予回饋'}</span>
                        </button>
                    </div>

                    <div class="mb-2.5 px-3 py-2 rounded-xl bg-paper/75 border border-border-soft/60 text-xs text-ink-light leading-relaxed break-words-safe">
                        <span class="font-sans text-[10px] text-accent font-medium mr-1">反思題目：</span>${postPrompt}
                    </div>

                    <p class="text-sm sm:text-base text-ink leading-relaxed pl-1 break-words-safe">${post.content}</p>
                    ${expandableFeedbackHtml}
                </div>
            `;
        }

        // --- 回饋模板 Modal (二選一：預設第一個為形狀 '3'，第二個為行為特質 '1') ---
        function getAllPresetSkills() {
            const list = [];
            skillCategories.forEach(c => c.skills.forEach(s => list.push(s)));
            return list;
        }

        function getModalFbDraft(fromUser, toUser, day) {
            const key = `fb_${fromUser}_${toUser}_${day}`;
            const allPreset = getAllPresetSkills();

            if (!window.AppState.drafts[key]) {
                const existing = window.AppState.feedbacks.find(f => f.fromUser === fromUser && f.toUser === toUser && Number(f.day) === Number(day));
                if (existing && existing.rawDraft) {
                    window.AppState.drafts[key] = JSON.parse(JSON.stringify(existing.rawDraft));
                    if (window.AppState.drafts[key].tpl !== '1' && window.AppState.drafts[key].tpl !== '3') {
                        window.AppState.drafts[key].tpl = '3';
                    }
                } else {
                    const initTags = existing && Array.isArray(existing.tags) ? [...existing.tags] : [];
                    const initCustomPool = initTags.filter(t => !allPreset.includes(t));
                    window.AppState.drafts[key] = {
                        tpl: '3',
                        fields: { '3': ['愛心', ''], '1': ['', '', ''] },
                        tags: initTags,
                        customPool: initCustomPool
                    };
                }
            }
            if (!window.AppState.drafts[key].fields['3'] || !SHAPE_OPTIONS.includes(window.AppState.drafts[key].fields['3'][0])) {
                window.AppState.drafts[key].fields['3'] = ['愛心', (window.AppState.drafts[key].fields['3'] && window.AppState.drafts[key].fields['3'][1]) || ''];
            }
            if (!window.AppState.drafts[key].fields['1']) {
                window.AppState.drafts[key].fields['1'] = ['', '', ''];
            }
            if (!Array.isArray(window.AppState.drafts[key].customPool)) {
                window.AppState.drafts[key].customPool = (window.AppState.drafts[key].tags || []).filter(t => !allPreset.includes(t));
            }
            return window.AppState.drafts[key];
        }

        window.openFeedbackModal = function(targetUser, dayNum) {
            window.AppState.activeFeedbackTarget = { user: targetUser, day: dayNum };
            const post = window.AppState.reflections[`${targetUser}_${dayNum}`];
            const postPrompt = (post && post.prompt) ? post.prompt : getPromptForUserDay(targetUser, dayNum);
            const isDefPrompt = isDefaultPromptForDay(postPrompt, dayNum);
            
            const av = document.getElementById('fb-modal-target-avatar');
            av.innerText = targetUser;
            av.className = `w-7 h-7 rounded-full font-sans font-bold text-xs flex items-center justify-center ${getAvatarColorClass(targetUser)}`;
            
            document.getElementById('fb-modal-target-name').innerText = targetUser;
            document.getElementById('fb-modal-day-str').innerText = `Day ${dayNum} (${dates[dayNum-1]}) 反思題目：`;
            document.getElementById('fb-modal-prompt-text').innerText = postPrompt;

            const promptBadgeEl = document.getElementById('fb-modal-prompt-badge');
            if (promptBadgeEl) {
                if (isDefPrompt) {
                    promptBadgeEl.innerText = '預設問題';
                    promptBadgeEl.className = 'text-[10px] font-sans px-2 py-0.5 rounded-full bg-paper-dark text-ink-light border border-border-soft font-medium';
                } else {
                    promptBadgeEl.innerText = '自選問題';
                    promptBadgeEl.className = 'text-[10px] font-sans px-2 py-0.5 rounded-full bg-accent-warm/15 text-accent-warm border border-accent-warm/30 font-medium';
                }
            }

            document.getElementById('fb-modal-reflection-text').innerText = post ? post.content : '';

            const draft = getModalFbDraft(window.AppState.currentUser, targetUser, dayNum);
            document.querySelectorAll('input[name="fb-template"]').forEach(r => {
                r.checked = (r.value === draft.tpl);
            });

            updateModalFeedbackForm();
            renderModalTagsSelector();
            document.getElementById('feedback-modal').classList.remove('hidden');
        };

        window.closeFeedbackModal = function() {
            document.getElementById('feedback-modal').classList.add('hidden');
            window.AppState.activeFeedbackTarget = null;
        };

        window.onTemplateRadioChange = function(tplVal) {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            draft.tpl = String(tplVal);
            saveLocalBackup();
            updateModalFeedbackForm();
        };

        window.onDraftFieldInput = function(tpl, idx, val) {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            if (!draft.fields[tpl]) draft.fields[tpl] = ['', '', ''];
            draft.fields[tpl][idx] = val;
            saveLocalBackup();
        };

        function updateModalFeedbackForm() {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            const tpl = (draft.tpl === '1') ? '1' : '3';
            const vals = draft.fields[tpl] || (tpl === '3' ? ['愛心', ''] : ['', '', '']);
            const container = document.getElementById('template-fields');
            const esc = (s) => String(s || '').replace(/"/g, '&quot;');

            let html = '';
            if (tpl === '3') {
                const selectedShape = SHAPE_OPTIONS.includes(vals[0]) ? vals[0] : '愛心';
                const optionsHtml = SHAPE_OPTIONS.map(opt => `<option value="${opt}" ${opt === selectedShape ? 'selected' : ''}>${opt}</option>`).join('');
                html = `
                    <div class="flex flex-wrap items-center gap-x-1.5 gap-y-2.5 text-xs sm:text-sm leading-relaxed">
                        <span>如果用一種形狀形容你，我會選</span>
                        <select onchange="onDraftFieldInput('3', 0, this.value)" class="border-b-2 border-accent/50 bg-paper/80 rounded-lg px-2.5 py-1.5 font-sans font-bold text-ink cursor-pointer">
                            ${optionsHtml}
                        </select>
                        <span>，因為</span>
                        <input type="text" value="${esc(vals[1])}" oninput="onDraftFieldInput('3', 1, this.value)" class="border-b-2 border-accent/40 bg-paper/50 rounded-t px-2 py-1 flex-1 min-w-[130px] text-center font-bold text-ink" placeholder="具體原因">
                        <span>。</span>
                    </div>`;
            } else {
                html = `
                    <div class="flex flex-wrap items-baseline gap-x-1.5 gap-y-2.5 text-xs sm:text-sm leading-relaxed">
                        <span>從你</span>
                        <input type="text" value="${esc(vals[0])}" oninput="onDraftFieldInput('1', 0, this.value)" class="border-b-2 border-accent/40 bg-paper/50 rounded-t px-2 py-1 flex-1 min-w-[100px] text-center font-bold text-ink" placeholder="具體行為">
                        <span>，我注意到你可能很</span>
                        <input type="text" value="${esc(vals[1])}" oninput="onDraftFieldInput('1', 1, this.value)" class="border-b-2 border-accent/40 bg-paper/50 rounded-t px-2 py-1 flex-1 min-w-[85px] text-center font-bold text-ink" placeholder="某種特質">
                        <span>，因為</span>
                        <input type="text" value="${esc(vals[2])}" oninput="onDraftFieldInput('1', 2, this.value)" class="border-b-2 border-accent/40 bg-paper/50 rounded-t px-2 py-1 flex-1 min-w-[120px] text-center font-bold text-ink" placeholder="具體原因">
                        <span>。</span>
                    </div>`;
            }
            container.innerHTML = html;
        }

        // 標籤渲染：將「自訂特質標籤」置於最上方明顯處，並使用 dataset 安全綁定點擊事件，防止特殊字元破壞
        function renderModalTagsSelector() {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            const selected = draft.tags || [];
            document.getElementById('selected-tag-count').innerText = `已選 ${selected.length} / 3`;

            let html = '';
            const allPreset = getAllPresetSkills();

            // 確保所有非預設的已選標籤也存在於 customPool 中
            selected.forEach(t => {
                if (!allPreset.includes(t) && !draft.customPool.includes(t)) {
                    draft.customPool.push(t);
                }
            });

            if (draft.customPool && draft.customPool.length > 0) {
                html += `
                    <div class="bg-amber-50/70 p-2.5 rounded-xl border border-accent-warm/40">
                        <div class="text-[10px] font-sans text-accent-warm mb-1.5 font-medium flex items-center gap-1">
                            <svg class="w-3 h-3" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                <path fill="currentColor" fill-opacity="0.25" d="M12 2l2.4 7.2L22 12l-7.6 2.8L12 22l-2.4-7.2L2 12l7.6-2.8L12 2z"/>
                            </svg>
                            <span>自訂特質標籤（點擊可選取或取消）</span>
                        </div>
                        <div class="flex flex-wrap gap-1.5">
                            ${draft.customPool.map(tag => {
                                const isSel = selected.includes(tag);
                                const cls = isSel 
                                    ? 'bg-accent-warm text-white border-accent-warm shadow-sm' 
                                    : 'bg-white text-ink-light border-border-soft hover:bg-paper';
                                const enc = encodeURIComponent(tag);
                                return `<button type="button" data-tag="${enc}" onclick="window.toggleModalTagDecoded(this.dataset.tag)" class="px-2.5 py-1 rounded-full text-[11px] font-sans transition border flex items-center gap-1 ${cls}">
                                    <span>${tag}</span>
                                    ${isSel ? '<span class="text-[9px] opacity-80">✓</span>' : ''}
                                </button>`;
                            }).join('')}
                        </div>
                    </div>
                `;
            }

            skillCategories.forEach(cat => {
                html += `
                    <div class="bg-white/80 p-2.5 rounded-xl border border-border-soft/60">
                        <div class="text-[10px] font-sans text-ink-light mb-1.5 font-medium">${cat.name}</div>
                        <div class="flex flex-wrap gap-1.5">
                `;
                cat.skills.forEach(tag => {
                    const isSel = selected.includes(tag);
                    const cls = isSel ? cat.activeClass : cat.badgeClass;
                    const enc = encodeURIComponent(tag);
                    html += `<button type="button" data-tag="${enc}" class="px-2.5 py-1 rounded-full text-[11px] font-sans transition border ${cls}" onclick="window.toggleModalTagDecoded(this.dataset.tag)">${tag}</button>`;
                });
                html += `</div></div>`;
            });

            document.getElementById('tags-container').innerHTML = html;
        }

        window.toggleModalTagDecoded = function(encodedTag) {
            if (!encodedTag) return;
            window.toggleModalTag(decodeURIComponent(encodedTag));
        };

        window.toggleModalTag = function(tag) {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            if (draft.tags.includes(tag)) {
                draft.tags = draft.tags.filter(t => t !== tag);
            } else {
                if (draft.tags.length >= 3) draft.tags.shift();
                draft.tags.push(tag);
            }
            saveLocalBackup();
            renderModalTagsSelector();
        };

        window.handleCustomTag = function(e) {
            if (e.key === 'Enter') {
                e.preventDefault();
                window.addCustomTagBtn();
            }
        };

        window.addCustomTagBtn = function() {
            const input = document.getElementById('custom-tag');
            if (!input) return;
            const rawVal = input.value.trim().replace(/^#+/, '');
            if (!rawVal) {
                window.showNotice("請先輸入自訂標籤文字喔！");
                return;
            }
            const tagStr = '#' + rawVal;
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const draft = getModalFbDraft(window.AppState.currentUser, target.user, target.day);
            const allPreset = getAllPresetSkills();

            if (!allPreset.includes(tagStr) && !draft.customPool.includes(tagStr)) {
                draft.customPool.push(tagStr);
            }
            if (!draft.tags.includes(tagStr)) {
                if (draft.tags.length >= 3) draft.tags.shift();
                draft.tags.push(tagStr);
            }

            input.value = '';
            saveLocalBackup();
            renderModalTagsSelector();

            const tc = document.getElementById('tags-container');
            if (tc) tc.scrollTo({ top: 0, behavior: 'smooth' });
        };

        window.submitModalFeedback = function() {
            const target = window.AppState.activeFeedbackTarget;
            if (!target) return;
            const fromUser = window.AppState.currentUser;
            const toUser = target.user;
            const day = target.day;
            const draft = getModalFbDraft(fromUser, toUser, day);
            const tpl = (draft.tpl === '1') ? '1' : '3';
            const vals = draft.fields[tpl] || (tpl === '3' ? ['愛心', ''] : ['', '', '']);

            let sentence = '';
            if (tpl === '3') {
                const v1 = SHAPE_OPTIONS.includes(vals[0]) ? vals[0] : '愛心';
                const v2 = (vals[1] || '').trim();
                if (!v2) { window.showNotice("請填寫選擇這個形狀的原因喔！"); return; }
                sentence = `如果用一種形狀形容你，我會選 **${v1}**，因為 **${v2}**。`;
            } else {
                const v1 = vals[0].trim(), v2 = vals[1].trim(), v3 = vals[2].trim();
                if (!v1 && !v2 && !v3) { window.showNotice("請填寫具體的觀察詞彙喔！"); return; }
                sentence = `從你 **${v1 || '日常的行動'}**，我注意到你可能很 **${v2 || '用心'}**，因為 **${v3 || '你願意花心力完成它'}**。`;
            }

            const finalTags = draft.tags.length > 0 ? [...draft.tags] : ['#自我學習'];
            const nowStr = new Date().toLocaleString('zh-TW');
            const fbId = `${day}_${fromUser}_${toUser}`;

            const existingIdx = window.AppState.feedbacks.findIndex(f => f.fromUser === fromUser && f.toUser === toUser && Number(f.day) === Number(day));
            const fbObj = {
                id: fbId,
                day: day,
                fromUser: fromUser,
                toUser: toUser,
                content: sentence,
                tags: finalTags,
                timestamp: nowStr,
                rawDraft: JSON.parse(JSON.stringify(draft))
            };

            if (existingIdx >= 0) {
                window.AppState.feedbacks[existingIdx] = fbObj;
            } else {
                window.AppState.feedbacks.push(fbObj);
            }

            // 自動展開該貼文的回饋列表，方便立即確認
            window.AppState.expandedFeedPosts[`${toUser}_${day}`] = true;

            saveLocalBackup();
            closeFeedbackModal();
            refreshAllViews(false);
            window.showNotice(`已將溫暖回饋寄送至 ${toUser} 的收件匣！`);

            sendDataToCloud({
                action: 'saveFeedback',
                day: day,
                fromUser: fromUser,
                toUser: toUser,
                content: sentence,
                tags: finalTags.join(', '),
                rawDraft: draft,
                timestamp: nowStr
            });
        };

        // --- 左側分頁：收件匣、特質存摺與我的日記 ---
        function updateInboxBadge() {
            const u = window.AppState.currentUser;
            const myFbs = window.AppState.feedbacks.filter(f => f.toUser === u);
            const badge = document.getElementById('inbox-badge');
            if (badge) badge.innerText = myFbs.length;
        }

        function renderLeftTab() {
            const u = window.AppState.currentUser;
            const myFbs = window.AppState.feedbacks.filter(f => f.toUser === u);

            // 1. 特質存摺
            const area = document.getElementById('floating-tags-area');
            let allTags = [];
            myFbs.forEach(fb => {
                const tagsArr = (fb.tags && fb.tags.length > 0) ? fb.tags : ['#日常閃光點'];
                tagsArr.forEach(t => {
                    allTags.push({
                        tag: t,
                        day: fb.day,
                        fromUser: fb.fromUser,
                        dateStr: dates[fb.day - 1] || '',
                        htmlText: formatFeedbackSentence(fb.content)
                    });
                });
            });

            window.AppState.traitListCache = allTags;
            document.getElementById('total-traits-count').innerText = `共 ${allTags.length} 顆星`;

            if (allTags.length === 0) {
                area.innerHTML = `<div class="text-ink-light/50 text-xs tracking-widest text-center py-10">尚無特質標籤，期待星探回饋的降臨。</div>`;
            } else {
                const anims = ['animate-float', 'animate-float-delayed', 'animate-float-fast'];
                area.innerHTML = allTags.map((item, idx) => `
                    <button type="button" id="trait-pill-${idx}" onclick="showTraitDetailByIndex(${idx})" class="trait-pill px-3.5 py-2 bg-accent text-white rounded-full text-xs font-sans shadow cursor-pointer hover:scale-105 transition flex items-center gap-1.5 ${anims[idx % 3]}">
                        <span>${item.tag}</span>
                        <span class="text-[9px] opacity-85 bg-black/20 px-1.5 py-0.2 rounded-full">${item.fromUser}</span>
                    </button>
                `).join('');
                showTraitDetailByIndex(allTags.length - 1);
            }

            // 2. 收件匣列表 (左側附上信封造型與形狀視覺化呈現)
            const inboxList = document.getElementById('inbox-list');
            if (myFbs.length === 0) {
                inboxList.innerHTML = `<div class="glass-panel rounded-3xl p-8 text-center text-xs text-ink-light">目前還沒有收到夥伴的回饋信喔！</div>`;
            } else {
                inboxList.innerHTML = [...myFbs].reverse().map(fb => `
                    <div class="glass-panel p-4 rounded-3xl flex items-start gap-3.5">
                        ${getEnvelopeWithShapeVisualHtml(fb.content)}
                        <div class="flex-1 min-w-0">
                            <div class="flex justify-between items-center mb-1.5 pb-1.5 border-b border-border-soft/50">
                                <span class="text-xs font-sans font-semibold text-accent-warm flex items-center gap-1.5">
                                    <span class="w-5 h-5 rounded-full bg-accent-warm text-white text-[10px] flex items-center justify-center">${fb.fromUser}</span>
                                    <span>來自 ${fb.fromUser} 的星探回饋</span>
                                </span>
                                <span class="text-[11px] font-sans text-ink-light">Day ${fb.day} (${dates[fb.day-1] || ''})</span>
                            </div>
                            <p class="text-sm text-ink leading-relaxed mb-2.5 break-words-safe">${formatFeedbackSentence(fb.content)}</p>
                            <div class="flex flex-wrap gap-1.5">
                                ${(fb.tags || []).map(t => `<span class="text-[10px] font-sans bg-accent-warm/10 text-accent-warm px-2.5 py-0.5 rounded-full border border-accent-warm/20">${t}</span>`).join('')}
                            </div>
                        </div>
                    </div>
                `).join('');
            }

            // 3. 我的所有日記
            const diaryList = document.getElementById('mydiary-list');
            let diaryHtml = '';
            for (let d = TOTAL_DAYS; d >= 1; d--) {
                const r = window.AppState.reflections[`${u}_${d}`];
                if (r && r.content) {
                    diaryHtml += `
                        <div class="glass-panel p-4 rounded-3xl">
                            <div class="flex justify-between items-center mb-2">
                                <span class="text-xs font-sans font-medium text-accent bg-accent/10 px-2.5 py-0.5 rounded-full">Day ${d} (${dates[d-1]})</span>
                                <span class="text-[11px] font-sans text-ink-light inline-flex items-center gap-1">
                                    <svg class="w-3.5 h-3.5 text-accent" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                        <circle cx="12" cy="12" r="9" fill="currentColor" fill-opacity="0.15"/>
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M3.6 9h16.8M3.6 15h16.8M12 3a14.5 14.5 0 000 18M12 3a14.5 14.5 0 010 18"/>
                                    </svg>
                                    <span>匿名公開</span>
                                </span>
                            </div>
                            <p class="text-sm text-ink leading-relaxed break-words-safe">${r.content}</p>
                        </div>
                    `;
                }
            }
            diaryList.innerHTML = diaryHtml || `<div class="glass-panel rounded-3xl p-8 text-center text-xs text-ink-light">尚未寫下任何日記，回到今日廣場開始記錄吧！</div>`;
        }

        window.showTraitDetailByIndex = function(idx) {
            const list = window.AppState.traitListCache || [];
            const item = list[idx];
            if (!item) return;

            document.querySelectorAll('.trait-pill').forEach((el, i) => {
                if (i === idx) {
                    el.classList.remove('bg-accent');
                    el.classList.add('bg-accent-warm', 'ring-4', 'ring-accent-warm/30', 'scale-105');
                } else {
                    el.classList.add('bg-accent');
                    el.classList.remove('bg-accent-warm', 'ring-4', 'ring-accent-warm/30', 'scale-105');
                }
            });

            document.getElementById('trait-detail-text').innerHTML = `
                <div class="flex justify-between items-center mb-1.5 pb-1 border-b border-border-soft/50">
                    <span class="text-xs font-sans text-accent-warm font-bold">${item.tag}</span>
                    <span class="text-[11px] font-sans text-ink-light">來自 ${item.fromUser} · Day ${item.day} (${item.dateStr})</span>
                </div>
                <div class="text-ink leading-relaxed text-sm break-words-safe">${item.htmlText}</div>
            `;
        };

        // --- 雲端 Google Sheets 同步 (支援 Google Sites 穿透與即時回傳) ---
        function applyCloudDataToState(json) {
            if (!json || json.status !== "success") return;
            if (Array.isArray(json.reflections)) {
                const newRefs = {};
                json.reflections.forEach(r => {
                    if (r.prompt) {
                        window.AppState.customPrompts[`${r.user}_${r.day}`] = r.prompt;
                    }
                    if (r.content && String(r.content).trim() !== "") {
                        newRefs[`${r.user}_${r.day}`] = {
                            user: r.user,
                            day: Number(r.day),
                            prompt: r.prompt || '',
                            content: r.content,
                            visibility: 'public',
                            timestamp: r.timestamp
                        };
                    }
                });
                window.AppState.reflections = newRefs;
            }
            if (Array.isArray(json.feedbacks)) {
                window.AppState.feedbacks = json.feedbacks;
            }
            saveLocalBackup();
            updateSaveIndicator("已同步");
            if (window.AppState.currentUser) {
                refreshAllViews(false);
            }
        }

        async function sendDataToCloud(payloadObj) {
            if (!GAS_URL || GAS_URL.includes("YOUR_SCRIPT_ID") || !GAS_URL.startsWith("https://script.google.com/")) {
                updateSaveIndicator("尚未綁定試算表網址");
                window.showNotice("提醒：您尚未在 index.html 中將 GAS_URL 替換為您部署的 Google Apps Script 網址，因此目前僅儲存於本機喔！");
                return;
            }
            updateSaveIndicator("同步至雲端中...", true);
            try {
                // 優先使用 GET payload 通道，100% 避免 Google Sites iframe 對 POST 302 轉址的丟包問題
                const encodedPayload = encodeURIComponent(JSON.stringify(payloadObj));
                const url = `${GAS_URL}?payload=${encodedPayload}&t=${Date.now()}`;
                const res = await fetch(url, { method: 'GET', redirect: 'follow' });
                const json = await res.json();
                applyCloudDataToState(json);
            } catch (err) {
                // 備援 POST 通道
                try {
                    await fetch(GAS_URL, {
                        method: 'POST',
                        mode: 'no-cors',
                        headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                        body: JSON.stringify(payloadObj)
                    });
                    setTimeout(() => fetchSheetData(), 1500);
                } catch (e2) {
                    updateSaveIndicator("離線暫存");
                }
            }
        }

        async function fetchSheetData() {
            loadLocalBackup();
            if (!GAS_URL || GAS_URL.includes("YOUR_SCRIPT_ID") || !GAS_URL.startsWith("https://script.google.com/")) {
                updateSaveIndicator("本機模式");
                return;
            }
            updateSaveIndicator("同步中...", true);
            try {
                const res = await fetch(`${GAS_URL}?action=readAll&t=${Date.now()}`, { method: 'GET', redirect: 'follow' });
                const json = await res.json();
                applyCloudDataToState(json);
            } catch (e) {
                updateSaveIndicator("離線模式");
            }
        }

        // --- Modals & 事件綁定 ---
        window.toggleTeamModal = function(show) {
            document.getElementById('team-modal').classList.toggle('hidden', !show);
        };
        window.showNotice = function(msg) {
            document.getElementById('notice-msg').innerText = msg;
            document.getElementById('notice-modal').classList.remove('hidden');
        };
        window.closeNoticeModal = function() {
            document.getElementById('notice-modal').classList.add('hidden');
        };

        window.addEventListener('DOMContentLoaded', () => {
            loadLocalBackup();

            const refInput = document.getElementById('reflection-input');
            if (refInput) {
                refInput.addEventListener('input', (e) => {
                    const val = e.target.value;
                    document.getElementById('char-count').innerText = val.length;
                    if (window.AppState.currentUser) {
                        const draft = getMyRefDraft(window.AppState.currentUser, window.AppState.currentDay);
                        draft.content = val;
                        saveLocalBackup();
                    }
                });
            }

            const pwdInput = document.getElementById('pwd-input');
            if (pwdInput) {
                pwdInput.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') window.confirmPassword();
                });
            }

            // 手機左右滑動切換分頁支援
            let touchStartX = 0;
            let touchStartY = 0;
            const touchArea = document.getElementById('touch-area');
            touchArea.addEventListener('touchstart', (e) => {
                touchStartX = e.changedTouches[0].screenX;
                touchStartY = e.changedTouches[0].screenY;
            }, { passive: true });

            touchArea.addEventListener('touchend', (e) => {
                if (!window.AppState.currentUser) return;
                const activeTag = document.activeElement ? document.activeElement.tagName : '';
                if (activeTag === 'INPUT' || activeTag === 'TEXTAREA' || activeTag === 'SELECT') return;

                const dx = e.changedTouches[0].screenX - touchStartX;
                const dy = e.changedTouches[0].screenY - touchStartY;
                if (Math.abs(dx) > 75 && Math.abs(dx) > Math.abs(dy) * 1.8) {
                    const order = ['left', 'center', 'right'];
                    const curIdx = order.indexOf(window.AppState.activeTab);
                    if (dx < 0 && curIdx < 2) {
                        switchMainTab(order[curIdx + 1]);
                    } else if (dx > 0 && curIdx > 0) {
                        switchMainTab(order[curIdx - 1]);
                    }
                }
            }, { passive: true });

            document.addEventListener('visibilitychange', () => {
                if (document.visibilityState === 'visible' && window.AppState.currentUser) {
                    fetchSheetData();
                }
            });
        });
    </script>
</body>
</html># Glimmer-Diary