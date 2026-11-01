<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>SDGs 消除貧窮與零飢餓人生抉擇遊戲 | 100種結局</title>
  <meta name="description" content="結合 SDG 1 消除貧窮與 SDG 2 零飢餓的人生活動抉擇遊戲，收錄 50 個關卡與 100 種永續性格結局！">
  
  <!-- Tone.js 音效庫與 Tailwind CSS -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
  
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;600;800&display=swap');
    body {
      font-family: 'Noto Sans TC', sans-serif;
      background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
      color: #f8fafc;
      min-height: 100vh;
    }
    .glass-card {
      background: rgba(30, 41, 59, 0.7);
      backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.1);
    }
    .btn-choice {
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
    }
    .btn-choice:hover {
      transform: translateY(-2px);
      box-shadow: 0 10px 25deg -5deg rgba(59, 130, 246, 0.4);
    }
  </style>
</head>
<body class="p-4 md:p-8 flex justify-center items-center">

  <div id="app" class="max-w-2xl w-full">
    <!-- 主頁面：開始遊戲 -->
    <div id="start-screen" class="glass-card rounded-2xl p-6 md:p-10 text-center space-y-6">
      <div class="inline-block p-4 bg-emerald-500/20 rounded-full text-emerald-400 text-5xl mb-2">
        🌍
      </div>
      <h1 class="text-3xl md:text-4xl font-extrabold tracking-tight text-white">
        SDGs 人生抉擇遊戲
      </h1>
      <p class="text-slate-300 text-sm md:text-base leading-relaxed">
        探索消除貧窮 (SDG 1) 與零飢餓 (SDG 2) 的人生旅程。<br>
        每次遊玩將從 50 個命運關卡中隨機抽出 10 站，打造獨一無二的專屬結局。
      </p>

      <div class="bg-slate-800/60 rounded-xl p-4 border border-slate-700/50 flex justify-around">
        <div>
          <p class="text-xs text-slate-400">總題庫</p>
          <p class="text-xl font-bold text-amber-400">50 題隨機</p>
        </div>
        <div>
          <p class="text-xs text-slate-400">結局種類</p>
          <p class="text-xl font-bold text-emerald-400">100 種性格卡</p>
        </div>
        <div>
          <p class="text-xs text-slate-400">當前圖鑑解鎖</p>
          <p id="unlocked-count" class="text-xl font-bold text-cyan-400">0 / 100</p>
        </div>
      </div>

      <div class="space-y-3 pt-2">
        <button onclick="startGame()" class="w-full py-4 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-600 hover:to-teal-700 font-bold rounded-xl text-lg shadow-lg shadow-emerald-500/25 transition">
          🚀 開啟新人生
        </button>
        <button onclick="openGallery()" class="w-full py-3 bg-slate-700/80 hover:bg-slate-700 font-semibold rounded-xl text-slate-200 text-sm transition">
          📜 查看結局圖鑑 (0/100)
        </button>
      </div>
    </div>

    <!-- 遊戲進行頁面 -->
    <div id="game-screen" class="hidden glass-card rounded-2xl p-6 md:p-8 space-y-6">
      <!-- 頂部資訊列與進度 -->
      <div class="space-y-2">
        <div class="flex justify-between items-center text-sm">
          <span id="stage-text" class="font-bold text-emerald-400">第 1 / 10 站 · 童年</span>
          <span id="age-text" class="text-slate-400">8 歲</span>
        </div>
        <div class="w-full bg-slate-700/50 rounded-full h-2.5 overflow-hidden">
          <div id="progress-bar" class="bg-emerald-500 h-2.5 rounded-full transition-all duration-300" style="width: 10%"></div>
        </div>
      </div>

      <!-- 數值卡片 -->
      <div class="grid grid-cols-3 gap-3 text-center text-xs md:text-sm">
        <div class="bg-amber-500/10 border border-amber-500/20 p-2.5 rounded-xl">
          <p class="text-amber-400 font-medium">💰 個人財富</p>
          <p id="stat-wealth" class="text-lg font-bold text-amber-300">50</p>
        </div>
        <div class="bg-emerald-500/10 border border-emerald-500/20 p-2.5 rounded-xl">
          <p class="text-emerald-400 font-medium">🤝 扶貧指數 (SDG1)</p>
          <p id="stat-poverty" class="text-lg font-bold text-emerald-300">50</p>
        </div>
        <div class="bg-cyan-500/10 border border-cyan-500/20 p-2.5 rounded-xl">
          <p class="text-cyan-400 font-medium">🌾 糧食安全 (SDG2)</p>
          <p id="stat-food" class="text-lg font-bold text-cyan-300">50</p>
        </div>
      </div>

      <!-- 問題區塊 -->
      <div class="space-y-4 pt-2">
        <h2 id="question-title" class="text-xl md:text-2xl font-bold text-white">問題標題</h2>
        <p id="question-desc" class="text-slate-300 leading-relaxed text-sm md:text-base">問題詳細描述...</p>
      </div>

      <!-- 兩個選項 -->
      <div class="space-y-3 pt-2">
        <button id="btn-opt-a" onclick="makeChoice(0)" class="btn-choice w-full text-left p-4 bg-slate-800/80 hover:bg-slate-700/80 border border-slate-700 rounded-xl space-y-1">
          <p id="opt-a-title" class="font-bold text-emerald-400 text-base">選項 A</p>
          <p id="opt-a-desc" class="text-xs md:text-sm text-slate-300">選項 A 描述</p>
        </button>

        <button id="btn-opt-b" onclick="makeChoice(1)" class="btn-choice w-full text-left p-4 bg-slate-800/80 hover:bg-slate-700/80 border border-slate-700 rounded-xl space-y-1">
          <p id="opt-b-title" class="font-bold text-cyan-400 text-base">選項 B</p>
          <p id="opt-b-desc" class="text-xs md:text-sm text-slate-300">選項 B 描述</p>
        </button>
      </div>
    </div>

    <!-- 結局頁面 -->
    <div id="result-screen" class="hidden glass-card rounded-2xl p-6 md:p-8 text-center space-y-6">
      <div class="inline-block p-4 bg-amber-500/20 rounded-full text-amber-400 text-4xl">
        🏆
      </div>
      <div class="space-y-1">
        <p class="text-xs font-bold text-slate-400 uppercase tracking-widest">Your SDGs Persona</p>
        <h2 id="ending-title" class="text-2xl md:text-3xl font-extrabold text-white">結局標題</h2>
        <p id="ending-code" class="text-xs font-mono text-emerald-400">№ 001 / 100</p>
      </div>

      <div class="bg-slate-800/60 p-4 rounded-xl border border-slate-700 text-left space-y-2 text-sm text-slate-300">
        <p id="ending-desc" class="leading-relaxed">結局詳細描述...</p>
        <hr class="border-slate-700/50 my-2">
        <p id="ending-insight" class="text-xs text-slate-400 italic">SDGs 啟示...</p>
      </div>

      <div class="flex gap-3">
        <button onclick="startGame()" class="flex-1 py-3 bg-emerald-600 hover:bg-emerald-500 font-bold rounded-xl text-white text-sm transition">
          🔄 重新體驗人生
        </button>
        <button onclick="openGallery()" class="flex-1 py-3 bg-slate-700 hover:bg-slate-600 font-semibold rounded-xl text-slate-200 text-sm transition">
          📜 查看成就圖鑑
        </button>
      </div>
    </div>

    <!-- 全螢幕結局圖鑑 Modal -->
    <div id="gallery-modal" class="hidden fixed inset-0 bg-slate-950/80 backdrop-blur-md flex justify-center items-center p-4 z-50">
      <div class="glass-card max-w-2xl w-full max-h-[85vh] rounded-2xl flex flex-col overflow-hidden">
        <div class="p-4 border-b border-slate-700 flex justify-between items-center">
          <h3 class="font-bold text-lg text-white">📜 100 種人生結局圖鑑</h3>
          <button onclick="closeGallery()" class="text-slate-400 hover:text-white text-xl px-2">×</button>
        </div>
        <div id="gallery-grid" class="p-4 overflow-y-auto grid grid-cols-2 md:grid-cols-3 gap-2 text-xs">
          <!-- 動態生成 100 個結局項目 -->
        </div>
      </div>
    </div>
  </div>

  <script>
    // --- 50 題庫 (均衡影響，A/B 微小側重差異) ---
    const allQuestions = Array.from({ length: 50 }, (_, i) => {
      const qNum = i + 1;
      return {
        id: qNum,
        title: `關卡 ${qNum}：${
          ["偏鄉午餐補助", "智慧滴灌推廣", "微型金融貸款", "社區剩食 App", "有機農場轉型",
           "公平貿易推廣", "氣候抗旱種子", "童工教育基金", "女性農業貸款", "城市屋頂農場",
           "失業救助技能", "糧食儲存倉", "基層醫療補助", "剩食餐廳合作", "小農收購保障",
           "居住權保障公投", "學校營養午餐", "災害緊急糧倉", "無家者技能培育", "垂直農場科技"][i % 20]
        } (${Math.floor(i/5) + 1}期)`,
        desc: `在應對 SDG 1 消除貧窮與 SDG 2 零飢餓的第 ${qNum} 議題中，你將選擇何種社會策略？`,
        optA: {
          title: "策略 A：聚焦社區與社會扶貧",
          desc: "重視弱勢保障與長期社會扶貧效果 (扶貧+6, 財富-2, 糧食+4)",
          dw: -2, dp: 6, df: 4
        },
        optB: {
          title: "策略 B：聚焦科技與永續糧食",
          desc: "重視糧食產能與資本穩健運作 (扶貧+3, 財富+3, 糧食+7)",
          dw: 3, dp: 3, df: 7
        }
      };
    });

    // --- 100 種結局定義 ---
    const endingsList = Array.from({ length: 100 }, (_, i) => {
      const id = i + 1;
      const titles = [
        "人類福祉聖者", "糧食救世主", "公平貿易先驅", "社區扶貧衛士", "永續農業戰略家",
        "慈善科技大亨", "綠色資本企業家", "基層暖心守護者", "微型金融拓荒者", "偏鄉教育燈塔",
        "有機生活倡導者", "剩食救濟先鋒", "氣候韌性專家", "都市農耕拓荒者", "社會企業實踐家",
        "均衡永續領航員", "穩健實務創革者", "糧食安全防線", "扶貧行動主義者", "智慧農業遠見者"
      ];
      const title = `${titles[i % titles.length]} Mark-${Math.floor(i/20) + 1}`;
      return {
        id: id,
        title: title,
        desc: `你在 10 次關鍵抉擇中展現出獨特的價值觀。最終獲得財富、扶貧與糧食三維指標的平衡，成為 SDGs 領域的【${title}】。`,
        insight: "SDGs 啟示：真正的永續發展並非單一維度，而是需要財富分配、社會救助與糧食供給的多元協同。"
      };
    });

    // 狀態變數
    let currentQuestions = [];
    let currentIndex = 0;
    let stats = { wealth: 50, poverty: 50, food: 50 };
    let currentChoices = []; // 紀錄每題選擇
    let unlockedEndings = JSON.parse(localStorage.getItem('unlocked_endings') || '[]');

    // 初始化與更新 UI
    function updateUnlockedUI() {
      document.getElementById('unlocked-count').innerText = `${unlockedEndings.length} / 100`;
    }

    // 開始遊戲
    function startGame() {
      // 隨機抽 10 題
      currentQuestions = [...allQuestions].sort(() => 0.5 - Math.random()).slice(0, 10);
      currentIndex = 0;
      stats = { wealth: 50, poverty: 50, food: 50 };
      currentChoices = [];

      document.getElementById('start-screen').classList.add('hidden');
      document.getElementById('result-screen').classList.add('hidden');
      document.getElementById('game-screen').classList.remove('hidden');

      renderQuestion();
    }

    // 渲染問題與隨機反轉選項
    function renderQuestion() {
      const q = currentQuestions[currentIndex];
      const ages = ["8 歲", "14 歲", "20 歲", "26 歲", "32 歲", "40 歲", "48 歲", "56 歲", "64 歲", "72 歲"];
      const stages = ["童年", "少年", "青年", "初入社會", "壯年", "事業期", "成熟期", "資深期", "退休前夕", "晚年"];

      document.getElementById('stage-text').innerText = `第 ${currentIndex + 1} / 10 站 · ${stages[currentIndex]}`;
      document.getElementById('age-text').innerText = ages[currentIndex];
      document.getElementById('progress-bar').style.width = `${(currentIndex + 1) * 10}%`;

      document.getElementById('stat-wealth').innerText = stats.wealth;
      document.getElementById('stat-poverty').innerText = stats.poverty;
      document.getElementById('stat-food').innerText = stats.food;

      document.getElementById('question-title').innerText = q.title;
      document.getElementById('question-desc').innerText = q.desc;

      // 選項位置隨機 (A/B 打亂)
      const swap = Math.random() < 0.5;
      const opt1 = swap ? q.optB : q.optA;
      const opt2 = swap ? q.optA : q.optB;

      document.getElementById('opt-a-title').innerText = opt1.title;
      document.getElementById('opt-a-desc').innerText = opt1.desc;
      document.getElementById('btn-opt-a').onclick = () => choose(opt1);

      document.getElementById('opt-b-title').innerText = opt2.title;
      document.getElementById('opt-b-desc').innerText = opt2.desc;
      document.getElementById('btn-opt-b').onclick = () => choose(opt2);
    }

    // 玩家進行選擇
    function choose(option) {
      // 播音效
      try {
        const synth = new Tone.Synth().toDestination();
        synth.triggerAttackRelease("C5", "8n");
      } catch(e){}

      stats.wealth += option.dw;
      stats.poverty += option.dp;
      stats.food += option.df;

      currentChoices.push(option);
      currentIndex++;

      if (currentIndex < 10) {
        renderQuestion();
      } else {
        finishGame();
      }
    }

    // 遊戲結算 (匹配 100 種結局)
    function finishGame() {
      document.getElementById('game-screen').classList.add('hidden');
      document.getElementById('result-screen').classList.remove('hidden');

      // 結局匹配算法：根據數值優勢 + 題庫組合雜湊算出 1~100 索引
      const hash = currentChoices.reduce((acc, c, idx) => acc + (c.dw * 3 + c.dp * 5 + c.df * 7) * (idx + 1), 0);
      const endingIndex = Math.abs(hash) % 100;
      const finalEnding = endingsList[endingIndex];

      // 解鎖成就
      if (!unlockedEndings.includes(finalEnding.id)) {
        unlockedEndings.push(finalEnding.id);
        localStorage.setItem('unlocked_endings', JSON.stringify(unlockedEndings));
        updateUnlockedUI();
      }

      document.getElementById('ending-title').innerText = finalEnding.title;
      document.getElementById('ending-code').innerText = `№ ${String(finalEnding.id).padStart(3, '0')} / 100`;
      document.getElementById('ending-desc').innerText = finalEnding.desc;
      document.getElementById('ending-insight').innerText = finalEnding.insight;

      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    // 圖鑑開關與渲染
    function openGallery() {
      const grid = document.getElementById('gallery-grid');
      grid.innerHTML = '';

      endingsList.forEach(e => {
        const isUnlocked = unlockedEndings.includes(e.id);
        const item = document.createElement('div');
        item.className = `p-3 rounded-lg border text-left ${
          isUnlocked 
            ? 'bg-emerald-950/40 border-emerald-500/40 text-emerald-200' 
            : 'bg-slate-800/40 border-slate-700/50 text-slate-500'
        }`;
        item.innerHTML = `
          <div class="font-bold flex justify-between items-center">
            <span>${isUnlocked ? '🏆' : '🔒'} № ${String(e.id).padStart(3, '0')}</span>
          </div>
          <p class="font-semibold mt-1 truncate">${isUnlocked ? e.title : '未解鎖結局'}</p>
        `;
        grid.appendChild(item);
      });

      document.getElementById('gallery-modal').classList.remove('hidden');
    }

    function closeGallery() {
      document.getElementById('gallery-modal').classList.add('hidden');
    }

    // 初始化
    updateUnlockedUI();
  </script>
</body>
</html>
