<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>考試時程表</title>
  
  <!-- 引入 Google Fonts: ZCOOL KuaiLe (酷樂體，呈現手繪手刻質感) 與 Klee One -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Klee+One:wght@600&family=ZCOOL+KuaiLe&display=swap" rel="stylesheet">

  <!-- 引入 Tailwind CSS (CDN) -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- 引入 Phosphor Icons (圖示庫) -->
  <script src="https://unpkg.com/@phosphor-icons/web"></script>
  <style>
    :root {
      /* 依據附件一調整的核心配色 */
      --iweirdo-blue: #0b8ca3;      /* 湖藍/藍綠色背景 */
      --iweirdo-yellow: #f8e622;    /* 鮮明明黃色主色 */
      --iweirdo-yellow-dark: #d6c412;
      --iweirdo-white: #ffffff;
      --iweirdo-red: #ff5252;
    }
    
    body { 
      font-family: 'ZCOOL KuaiLe', 'Klee One', cursive, sans-serif; 
      background-color: var(--iweirdo-blue);
      color: var(--iweirdo-yellow);
    }
    
    ::-webkit-scrollbar { width: 8px; }
    ::-webkit-scrollbar-track { background: transparent; }
    ::-webkit-scrollbar-thumb { background: rgba(248, 230, 34, 0.4); border-radius: 10px; }
    ::-webkit-scrollbar-thumb:hover { background: rgba(248, 230, 34, 0.7); }
    
    /* 藍綠底色與微顆粒質感 */
    .iweirdo-bg {
      background-color: var(--iweirdo-blue);
      background-image: radial-gradient(circle, rgba(255,255,255,0.05) 1px, transparent 1px);
      background-size: 24px 24px;
    }

    .iweirdo-text-yellow { 
      color: var(--iweirdo-yellow); 
      text-shadow: 2px 2px 0px rgba(0, 0, 0, 0.2); 
    }
    .iweirdo-text-white { 
      color: var(--iweirdo-white); 
    }
    .iweirdo-text-red { 
      color: var(--iweirdo-red); 
    }

    /* 手繪粗線框樣式 */
    .iweirdo-box {
      border: 3px solid var(--iweirdo-yellow);
      border-radius: 255px 15px 225px 15px / 15px 225px 15px 255px;
      background: rgba(0, 0, 0, 0.15);
      color: var(--iweirdo-yellow);
      box-shadow: 4px 4px 0px rgba(0, 0, 0, 0.25);
    }
    .iweirdo-box:hover {
      background: rgba(0, 0, 0, 0.25);
      border-color: #ffffff;
      color: #ffffff;
    }
    .iweirdo-box-active {
      border: 4px solid var(--iweirdo-yellow);
      border-radius: 15px 255px 15px 225px / 225px 15px 255px 15px;
      background: var(--iweirdo-yellow);
      color: var(--iweirdo-blue) !important;
      box-shadow: 6px 6px 0px rgba(0,0,0,0.3);
    }

    .iweirdo-input {
      background: rgba(0, 0, 0, 0.2);
      border: none;
      border-bottom: 3px solid var(--iweirdo-yellow);
      color: var(--iweirdo-yellow);
      font-family: 'ZCOOL KuaiLe', cursive;
    }
    .iweirdo-input:focus {
      outline: none;
      border-bottom-color: #ffffff;
      background: rgba(0, 0, 0, 0.35);
    }
    ::-webkit-calendar-picker-indicator {
      filter: invert(0.9) sepia(1) saturate(5) hue-rotate(10deg);
      cursor: pointer;
    }
  </style>
</head>
<body class="antialiased overflow-hidden selection:bg-[var(--iweirdo-yellow)] selection:text-[#0b8ca3] iweirdo-bg h-screen flex flex-col relative">

  <!-- 頂部設定按鈕 -->
  <button onclick="openSettings()" class="absolute top-4 right-4 md:top-6 md:right-6 p-2 md:p-3 text-[var(--iweirdo-yellow)] hover:scale-110 transition-transform duration-300 z-20 flex items-center justify-center rounded-full" title="系統管理">
    <i class="ph ph-gear-six text-3xl md:text-5xl"></i>
  </button>

  <!-- 主容器 -->
  <div class="flex-1 w-full flex flex-col min-h-0 overflow-hidden">
    
    <!-- 上方：巨大時鐘與叮嚀區塊 -->
    <div class="w-full flex flex-col justify-center items-center pt-6 md:pt-10 pb-2 shrink-0 z-10">
      <div id="clock-display" class="font-bold tracking-[0.05em] tabular-nums text-center iweirdo-text-yellow" style="font-size: clamp(6rem, 22vh, 25rem); line-height: 1;">
        --:--:--
      </div>
      <div id="notes-display" class="text-2xl md:text-4xl mt-4 md:mt-6 tracking-widest text-white px-6 text-center max-w-5xl leading-relaxed">
      </div>
    </div>

    <!-- 下方/中央：考試科目清單區塊 -->
    <div class="w-full max-w-[1600px] mx-auto px-4 md:px-8 pb-12 md:pb-16 pt-2 flex-1 flex flex-col justify-center items-center min-h-0">
      <div id="schedule-container" class="flex flex-row flex-wrap justify-center items-center content-center gap-4 md:gap-6 w-full h-full overflow-hidden">
        <!-- 內容由 JavaScript 動態生成 -->
      </div>
    </div>
  </div>

  <!-- 右下方：倒數計時器 (浮動) -->
  <div id="countdown-container" class="fixed bottom-8 md:bottom-10 right-4 md:right-8 z-30 hidden transition-all duration-500 iweirdo-box p-4 md:p-5 bg-[#0b8ca3]/95 backdrop-blur-md transform translate-y-0">
    <div class="text-[var(--iweirdo-yellow)] text-xs md:text-sm font-bold tracking-[0.2em] mb-1 md:mb-2 flex items-center gap-2">
      <i class="ph-fill ph-timer animate-pulse"></i> 本節剩餘時間
    </div>
    <div id="countdown-display" class="text-5xl md:text-7xl font-mono text-white tabular-nums text-center font-bold tracking-widest leading-none">
      --:--
    </div>
  </div>

  <!-- 設定對話視窗 (Modal) -->
  <div id="settings-modal" class="fixed inset-0 bg-black/75 backdrop-blur-sm justify-center items-center z-50 p-4 hidden transition-opacity duration-300">
    <div class="w-full max-w-4xl flex flex-col overflow-hidden max-h-[90vh] shadow-2xl animate-[fadeIn_0.2s_ease-out] rounded-xl border-4 border-[var(--iweirdo-yellow)] relative iweirdo-bg">
        
        <div class="p-4 md:p-6 flex justify-between items-center shrink-0 border-b-2 border-[var(--iweirdo-yellow)] bg-black/20">
          <h3 class="text-xl md:text-3xl font-bold flex items-center gap-3 tracking-widest iweirdo-text-yellow">
            <i class="ph ph-sliders text-3xl"></i> 考程與系統設定
          </h3>
          <button onclick="closeSettings()" class="p-2 text-[var(--iweirdo-yellow)] hover:text-white transition-colors">
            <i class="ph ph-x text-3xl"></i>
          </button>
        </div>

        <div class="p-4 md:p-8 overflow-y-auto flex-1 flex flex-col md:flex-row gap-6 md:gap-8 min-h-0">
          
          <!-- 左欄：全局設定 -->
          <div class="w-full md:w-5/12 flex flex-col gap-6">
            <div class="iweirdo-box p-5 md:p-6 flex-1">
              <h4 class="text-base md:text-xl font-bold text-[var(--iweirdo-yellow)] tracking-[0.2em] mb-4 md:mb-6 flex items-center gap-2 border-b-2 border-[var(--iweirdo-yellow)] pb-3">
                <i class="ph ph-desktop"></i> 顯示設定
              </h4>
              
              <div class="space-y-4 md:space-y-6">
                <label class="flex items-center gap-3 cursor-pointer group">
                  <input type="checkbox" id="setting-countdown" onchange="autoSaveGlobalSettings()" class="w-5 h-5 rounded bg-black/20 border-white/50 text-[var(--iweirdo-yellow)] focus:ring-[var(--iweirdo-yellow)] cursor-pointer">
                  <span class="text-lg md:text-xl text-white tracking-widest group-hover:text-[var(--iweirdo-yellow)] transition-colors">啟用右下方倒數計時</span>
                </label>

                <div class="pt-2">
                  <label class="block text-lg md:text-xl font-bold text-[var(--iweirdo-yellow)] mb-2 md:mb-3 tracking-widest flex items-center gap-2">
                    <i class="ph ph-chat-text"></i> 提醒備忘錄
                  </label>
                  <textarea id="setting-notes" rows="3" onchange="autoSaveGlobalSettings()" placeholder="例如：保持專注，仔細檢查！" class="w-full p-3 md:p-4 iweirdo-input text-lg md:text-xl resize-none rounded"></textarea>
                </div>
              </div>
            </div>
          </div>

          <!-- 右欄：考程編輯 -->
          <div class="w-full md:w-7/12 flex flex-col">
            <h4 class="text-base md:text-xl font-bold text-white tracking-[0.2em] mb-3 md:mb-4 flex items-center gap-2 border-l-4 border-[var(--iweirdo-yellow)] pl-3">
              考試行程清單
            </h4>
            
            <div class="p-4 md:p-5 iweirdo-box mb-4 md:mb-6 grid grid-cols-2 md:flex md:flex-wrap gap-3 items-end bg-black/10">
              <div class="col-span-2 md:flex-1 md:min-w-[100px]">
                <label class="block text-sm font-bold text-white mb-1 md:mb-2 tracking-widest">科目名稱</label>
                <input type="text" id="new-subject" placeholder="例：國文" class="w-full p-2 iweirdo-input text-base md:text-lg" />
              </div>
              <div class="col-span-1 md:w-32 lg:w-40">
                <label class="block text-sm font-bold text-white mb-1 md:mb-2 tracking-widest">開始</label>
                <input type="time" id="new-start" class="w-full p-2 iweirdo-input text-base md:text-lg" />
              </div>
              <div class="col-span-1 md:w-32 lg:w-40">
                <label class="block text-sm font-bold text-white mb-1 md:mb-2 tracking-widest">結束</label>
                <input type="time" id="new-end" class="w-full p-2 iweirdo-input text-base md:text-lg" />
              </div>
              <button id="btn-submit-item" type="button" onclick="addEditingItem()" class="col-span-2 border-2 border-[var(--iweirdo-yellow)] bg-[var(--iweirdo-yellow)] text-[var(--iweirdo-blue)] hover:bg-white hover:text-black p-2 flex items-center gap-1 font-bold transition-all w-full md:w-auto justify-center tracking-widest rounded text-sm md:text-base">
                <i id="submit-icon" class="ph ph-plus text-lg"></i> <span id="submit-text">新增</span>
              </button>
            </div>

            <div id="editing-list" class="space-y-2 md:space-y-3 overflow-y-auto max-h-[30vh] pr-2">
            </div>
          </div>
        </div>

        <div class="p-4 md:p-5 flex justify-end gap-4 shrink-0 border-t-2 border-[var(--iweirdo-yellow)] bg-black/30">
          <button onclick="closeSettings()" class="px-6 py-2 md:py-3 font-bold bg-[var(--iweirdo-yellow)] text-[var(--iweirdo-blue)] hover:bg-white hover:text-black rounded transition-all tracking-widest text-base md:text-lg">
            關閉視窗 (自動儲存)
          </button>
        </div>

    </div>
  </div>

  <script>
    const defaultData = {
      schedule: [
        { id: '1', subject: '國文', startTime: '08:10', endTime: '09:30' },
        { id: '2', subject: '英文', startTime: '09:50', endTime: '11:10' },
        { id: '3', subject: '數學', startTime: '11:30', endTime: '12:50' },
      ],
      settings: { showCountdown: true, teacherNotes: '保持冷靜，認真作答！' }
    };
    
    let appData = JSON.parse(JSON.stringify(defaultData));
    
    try {
      const savedData = localStorage.getItem('boardAppData');
      if (savedData) appData = JSON.parse(savedData);
    } catch (e) {
      console.error('無法讀取本地儲存資料', e);
    }
    
    let editingSchedule = [];
    let activeExamId = null;
    let currentEditId = null; 

    const formatTime = (date) => {
      const h = String(date.getHours()).padStart(2, '0');
      const m = String(date.getMinutes()).padStart(2, '0');
      const s = String(date.getSeconds()).padStart(2, '0');
      return `${h}:${m}:${s}`;
    };

    const isCurrentExam = (start, end, timeObj) => {
      const currentMinutes = timeObj.getHours() * 60 + timeObj.getMinutes();
      const [startH, startM] = start.split(':').map(Number);
      const [endH, endM] = end.split(':').map(Number);
      const startTotal = startH * 60 + startM;
      const endTotal = endH * 60 + endM;
      return currentMinutes >= startTotal && currentMinutes <= endTotal;
    };

    function renderSchedule() {
      const container = document.getElementById('schedule-container');
      
      if (appData.schedule.length === 0) {
        container.innerHTML = `
          <div class="text-center text-white/70 mt-10 flex flex-col items-center w-full">
            <i class="ph ph-ghost text-5xl md:text-6xl mb-4 opacity-70"></i>
            <p class="tracking-widest text-lg md:text-2xl">目前沒有設定任何考程項目</p>
          </div>
        `;
        return;
      }

      container.innerHTML = appData.schedule.map(exam => {
        const isActive = exam.id === activeExamId;
        return `
          <div class="p-4 md:p-6 transition-all duration-300 relative ${isActive ? 'iweirdo-box-active scale-105 z-10' : 'iweirdo-box opacity-90 hover:opacity-100'} w-fit min-w-0 flex-shrink-0 group">
            
            ${isActive ? `<span class="absolute -top-3 -left-3 bg-[var(--iweirdo-blue)] text-[var(--iweirdo-yellow)] border-2 border-[var(--iweirdo-yellow)] text-xs font-bold px-3 py-1 tracking-[0.2em] rounded-full animate-bounce whitespace-nowrap shadow-lg z-30">進行中</span>` : ''}

            <div class="flex flex-col gap-2 h-full justify-center">
              
              <div class="flex items-center gap-3 mb-1">
                <span class="text-3xl md:text-5xl font-bold tracking-widest leading-none ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-[var(--iweirdo-yellow)]'}">
                  ${exam.subject}
                </span>
                
                <div class="flex gap-1 z-20 opacity-70 hover:opacity-100 transition-opacity">
                  <button onclick="editFromMain('${exam.id}')" class="hover:scale-125 transition-transform p-1" title="修改">
                    <i class="ph ph-pencil-simple text-xl md:text-2xl ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-white'}"></i>
                  </button>
                  <button onclick="deleteFromMain('${exam.id}')" class="hover:scale-125 transition-transform p-1" title="刪除">
                    <i class="ph ph-trash text-xl md:text-2xl ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-white'}"></i>
                  </button>
                </div>
              </div>
              
              <!-- 時間顯示區塊 -->
              <div class="flex items-center gap-3 mt-1">
                <i class="ph ${isActive ? 'ph-hourglass-high animate-spin' : 'ph-clock'} text-3xl md:text-4xl ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-white'}"></i>
                <div class="flex flex-col border-l-2 ${isActive ? 'border-[var(--iweirdo-blue)]' : 'border-[var(--iweirdo-yellow)]'} pl-3 py-0.5 gap-1">
                  <span class="tracking-[0.1em] font-mono text-2xl md:text-3xl leading-none font-bold ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-white'}">${exam.startTime}</span>
                  <span class="tracking-[0.1em] font-mono text-2xl md:text-3xl leading-none font-bold ${isActive ? 'text-[var(--iweirdo-blue)]' : 'text-white'}">${exam.endTime}</span>
                </div>
              </div>
              
            </div>
          </div>
        `;
      }).join('');
    }

    function renderEditingSchedule() {
      const listContainer = document.getElementById('editing-list');
      
      if (editingSchedule.length === 0) {
        listContainer.innerHTML = `<p class="text-center text-white/70 py-4 tracking-widest">無考程項目</p>`;
        return;
      }

      listContainer.innerHTML = editingSchedule.map(item => `
        <div class="flex justify-between items-center p-2 md:p-3 iweirdo-box bg-black/20">
          <div class="flex items-center gap-2 md:gap-3 flex-wrap flex-1">
            <span class="text-lg md:text-xl font-bold text-[var(--iweirdo-yellow)] tracking-widest">${item.subject}</span>
            <span class="text-white px-2 md:px-3 py-1 font-mono text-sm md:text-lg tracking-widest border-l-2 border-[var(--iweirdo-yellow)]">
              ${item.startTime} - ${item.endTime}
            </span>
          </div>
          <div class="flex items-center gap-1 shrink-0">
            <button onclick="editEditingItem('${item.id}')" class="text-white hover:text-[var(--iweirdo-yellow)] p-1.5 transition-colors">
              <i class="ph ph-pencil-simple text-xl md:text-2xl"></i>
            </button>
            <button onclick="removeEditingItem('${item.id}')" class="text-white hover:text-red-400 p-1.5 transition-colors">
              <i class="ph ph-trash text-xl md:text-2xl"></i>
            </button>
          </div>
        </div>
      `).join('');
    }

    function renderNotes() {
      const notes = appData.settings.teacherNotes;
      const display = document.getElementById('notes-display');
      display.textContent = notes;
      display.style.display = notes ? 'block' : 'none';
    }

    function updateClock() {
      const now = new Date();
      document.getElementById('clock-display').textContent = formatTime(now);

      let newActiveExam = null;
      for (const exam of appData.schedule) {
        if (isCurrentExam(exam.startTime, exam.endTime, now)) {
          newActiveExam = exam;
          break;
        }
      }

      if ((newActiveExam ? newActiveExam.id : null) !== activeExamId) {
        activeExamId = newActiveExam ? newActiveExam.id : null;
        renderSchedule();
      }

      const countdownContainer = document.getElementById('countdown-container');
      if (appData.settings.showCountdown && newActiveExam) {
        countdownContainer.classList.remove('translate-y-[150%]', 'opacity-0', 'hidden');
        countdownContainer.classList.add('translate-y-0', 'opacity-100');
        
        const [endH, endM] = newActiveExam.endTime.split(':').map(Number);
        const endDate = new Date();
        endDate.setHours(endH, endM, 0, 0);
        
        let diffSeconds = Math.floor((endDate - now) / 1000);
        if (diffSeconds < 0) diffSeconds = 0; 
        
        const m = String(Math.floor(diffSeconds / 60)).padStart(2, '0');
        const s = String(diffSeconds % 60).padStart(2, '0');
        document.getElementById('countdown-display').textContent = `${m}:${s}`;
      } else {
        countdownContainer.classList.remove('translate-y-0', 'opacity-100');
        countdownContainer.classList.add('translate-y-[150%]', 'opacity-0');
        setTimeout(() => {
          if (!appData.settings.showCountdown || !newActiveExam) countdownContainer.classList.add('hidden');
        }, 500); 
      }
    }

    function autoSaveGlobalSettings() {
      appData.settings.showCountdown = document.getElementById('setting-countdown').checked;
      appData.settings.teacherNotes = document.getElementById('setting-notes').value.trim();
      syncToMainBoard();
    }

    function syncToMainBoard() {
      try { localStorage.setItem('boardAppData', JSON.stringify(appData)); } catch (e) {}
      renderNotes();
      renderSchedule();
      updateClock();
    }

    function openSettings() {
      editingSchedule = JSON.parse(JSON.stringify(appData.schedule)); 
      document.getElementById('setting-countdown').checked = appData.settings.showCountdown;
      document.getElementById('setting-notes').value = appData.settings.teacherNotes;
      
      const modal = document.getElementById('settings-modal');
      modal.classList.remove('hidden');
      modal.classList.add('flex');
      renderEditingSchedule();
    }

    function closeSettings() {
      const modal = document.getElementById('settings-modal');
      modal.classList.add('hidden');
      modal.classList.remove('flex');
      resetInputFields();
    }

    function editFromMain(id) {
      openSettings();
      editEditingItem(id);
    }

    function deleteFromMain(id) {
      if (confirm('確定要刪除這個科目嗎？')) {
        appData.schedule = appData.schedule.filter(item => item.id !== id);
        activeExamId = null; 
        syncToMainBoard();
      }
    }

    function resetInputFields() {
      document.getElementById('new-subject').value = '';
      document.getElementById('new-start').value = '';
      document.getElementById('new-end').value = '';
      
      currentEditId = null;
      const btn = document.getElementById('btn-submit-item');
      document.getElementById('submit-text').textContent = '新增';
      document.getElementById('submit-icon').className = 'ph ph-plus text-lg md:text-xl';
    }

    function addEditingItem() {
      const subjectInput = document.getElementById('new-subject');
      const startInput = document.getElementById('new-start');
      const endInput = document.getElementById('new-end');

      const subject = subjectInput.value.trim();
      const startTime = startInput.value;
      const endTime = endInput.value;

      if (subject && startTime && endTime) {
        if(startTime >= endTime) {
          alert('結束時間必須晚於開始時間！');
          return;
        }

        if (currentEditId) {
          const index = editingSchedule.findIndex(item => item.id === currentEditId);
          if (index !== -1) {
            editingSchedule[index] = { ...editingSchedule[index], subject, startTime, endTime };
          }
        } else {
          editingSchedule.push({
            id: Date.now().toString(),
            subject,
            startTime,
            endTime
          });
        }

        editingSchedule.sort((a, b) => a.startTime.localeCompare(b.startTime));
        
        appData.schedule = [...editingSchedule];
        syncToMainBoard();
        
        renderEditingSchedule();
        resetInputFields(); 

        const btn = document.getElementById('btn-submit-item');
        document.getElementById('submit-text').textContent = '已儲存！';
        setTimeout(() => {
          document.getElementById('submit-text').textContent = '新增';
        }, 1200);

      } else {
        alert('請完整填寫所有欄位！');
      }
    }

    function editEditingItem(id) {
      const item = editingSchedule.find(i => i.id === id);
      if (item) {
        document.getElementById('new-subject').value = item.subject;
        document.getElementById('new-start').value = item.startTime;
        document.getElementById('new-end').value = item.endTime;
        currentEditId = id;
        
        const btn = document.getElementById('btn-submit-item');
        document.getElementById('submit-text').textContent = '更新';
        document.getElementById('submit-icon').className = 'ph ph-check text-lg md:text-xl';
      }
    }

    function removeEditingItem(id) {
      editingSchedule = editingSchedule.filter(item => item.id !== id);
      if (currentEditId === id) resetInputFields();
      
      appData.schedule = [...editingSchedule];
      syncToMainBoard();
      
      renderEditingSchedule();
    }

    function init() {
      renderNotes();
      renderSchedule(); 
      updateClock();    
      setInterval(updateClock, 1000);
    }

    window.addEventListener('DOMContentLoaded', init);

  </script>
</body>
</html># time
由 EZPage 建立的網站 - Deployed by EZPage
