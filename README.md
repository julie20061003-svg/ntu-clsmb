<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>國立臺灣大學 醫技系 畢業學分追蹤系統</title>
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- QR Code 生成元件庫 -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen py-8 px-4">
  <div class="max-w-5xl mx-auto space-y-6">
    
    <!-- 頁頭與代碼/QR Code 控制區 -->
    <header class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
      <div>
        <div class="inline-block px-2.5 py-0.5 rounded-full text-xs font-semibold bg-blue-50 text-blue-700 border border-blue-200 mb-1">
          114 學年度入學學生適用
        </div>
        <h1 class="text-2xl font-bold text-slate-900">國立臺灣大學 醫學檢驗暨生物技術學學系</h1>
        <p class="text-slate-500 text-sm mt-0.5">畢業學分門檻、系必修勾選與體育/服務課追蹤</p>
      </div>

      <!-- 同步按鈕區 -->
      <div class="flex items-center gap-2">
        <button onclick="openExportModal()" class="flex items-center gap-1.5 px-3.5 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-xl shadow-sm text-xs sm:text-sm font-medium transition">
          📱 產生同步 QR / 代碼
        </button>
        <button onclick="openImportModal()" class="flex items-center gap-1.5 px-3.5 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 border border-slate-300 rounded-xl shadow-sm text-xs sm:text-sm font-medium transition">
          📥 輸入代碼匯入
        </button>
      </div>
    </header>

    <!-- 修課規定特別註明 Banner -->
    <div class="bg-amber-50 border border-amber-200 text-amber-900 p-4 rounded-xl text-xs sm:text-sm space-y-1.5">
      <div class="font-bold text-amber-950 flex items-center gap-1">
        <span>📌</span> 臺大醫技系特別修課規定：
      </div>
      <ul class="list-disc list-inside space-y-1 text-amber-900 pl-1 font-medium">
        <li><b>選修學分規定：</b>最少需修滿 <b>5 學分</b>。超修之通識課程（A1~A8 領域，無 * 兼充者）自動併入選修學分計算。</li>
        <li><b>服務課（甲）（乙）：</b>應必修，但 0 學分。</li>
        <li><b>體育課程：</b>體育應必修 4 學分，但不計入畢業總學分數內。</li>
        <li><b>醫技系指定通識領域：</b>A1 (文學藝術)、A2 (歷史思維)、A3 (哲學道德)、A4 (公民意識)、A5 (量化分析)。</li>
      </ul>
    </div>

    <!-- 總覽進度看板 -->
    <section class="space-y-4">
      <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
        <div class="flex flex-col md:flex-row justify-between items-start md:items-end mb-3 gap-2">
          <div>
            <span class="text-xs font-semibold uppercase tracking-wider text-slate-400">Graduation Progress</span>
            <div class="text-3xl font-extrabold text-blue-600 mt-0.5">
              <span id="stat-total-earned">0</span> / <span id="stat-total-target">128</span> <span class="text-base font-normal text-slate-500">畢業總學分</span>
            </div>
          </div>
          <div class="text-right">
            <span class="text-sm text-slate-500">距離畢業還差 </span>
            <span id="stat-total-remaining" class="text-2xl font-bold text-blue-600">128</span>
            <span class="text-sm text-slate-500"> 學分</span>
          </div>
        </div>
        <div class="w-full bg-slate-100 rounded-full h-3.5 overflow-hidden mb-3">
          <div id="stat-main-bar" class="bg-blue-600 h-full transition-all duration-300 rounded-full" style="width: 0%"></div>
        </div>
        
        <div class="bg-slate-50 p-2.5 rounded-lg border border-slate-200 text-xs text-slate-600 flex flex-wrap items-center gap-1">
          <span class="font-bold text-slate-700">🎓 總學分採計拆解：</span>
          <span>系必修 (<b id="calc-req" class="text-blue-600">0</b>)</span> +
          <span>共同通識基本採計 (<b id="calc-common" class="text-blue-600">0</b>)</span> +
          <span class="bg-slate-200 text-slate-800 px-1.5 py-0.5 rounded font-bold">選修含通識超修 (<span id="calc-ele-total">0</span>)</span> =
          <span class="font-extrabold text-slate-900" id="calc-total">0</span> 學分
        </div>
      </div>

      <!-- 四大分類卡片 -->
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4">
        <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-2 flex flex-col justify-between">
          <div class="text-xs font-bold text-slate-500">系訂必修 (已勾選)</div>
          <div class="text-2xl font-extrabold text-slate-800"><span id="cat-earned-req">0</span> / 99 <span class="text-xs font-normal text-slate-400">學分</span></div>
          <div class="text-xs text-slate-500">尚需 <span id="cat-rem-req" class="font-semibold text-slate-700">99</span> 學分</div>
        </div>

        <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-2 flex flex-col justify-between">
          <div class="text-xs font-bold text-slate-500">共同必修 + 通識</div>
          <div class="text-2xl font-extrabold text-slate-800"><span id="cat-earned-common">0</span> / 24 <span class="text-xs font-normal text-slate-400">學分</span></div>
          <div class="text-xs text-slate-500">尚需 <span id="cat-rem-common" class="font-semibold text-slate-700">24</span> 學分</div>
        </div>

        <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-2 flex flex-col justify-between">
          <div class="text-xs font-bold text-slate-500 flex justify-between items-center">
            <span>選修 (含通識超修)</span>
            <span id="ele-badge" class="hidden text-[10px] font-bold px-1.5 py-0.5 rounded bg-emerald-100 text-emerald-700">✅ 已達標</span>
          </div>
          <div class="text-2xl font-extrabold text-slate-800"><span id="cat-earned-ele-total">0</span> / 5 <span class="text-xs font-normal text-slate-400">學分</span></div>
          <div class="text-xs text-slate-500">自由選修 <b id="cat-earned-ele" class="text-slate-700">0</b> + 通識超修 <b id="cat-earned-excess" class="text-slate-700">0</b></div>
        </div>
        
        <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-2 flex flex-col justify-between">
          <div class="text-xs font-bold text-slate-500 flex justify-between items-center">
            <span>體育 & 服務學習</span>
            <span class="text-[10px] text-slate-400">不計畢業128</span>
          </div>
          <div class="space-y-1">
            <div class="text-xs font-semibold text-slate-700 flex justify-between items-center">
              <span>體育：<b id="cat-earned-pe" class="text-blue-600 text-sm">0</b> / 4 學分</span>
              <span id="pe-badge" class="hidden text-[10px] font-bold px-1.5 py-0.5 rounded bg-emerald-100 text-emerald-700">✅ 已達標</span>
            </div>
            <div class="text-xs font-semibold text-slate-700 flex justify-between items-center">
              <span>服務：<b id="cat-earned-srv" class="text-emerald-600 text-sm">0</b> / 2 門</span>
              <span id="srv-badge" class="hidden text-[10px] font-bold px-1.5 py-0.5 rounded bg-emerald-100 text-emerald-700">✅ 已達標</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 共同必修 + 通識：雙修法對照卡片 -->
    <section class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 space-y-5">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 border-b pb-3">
        <div>
          <h2 class="font-bold text-slate-900 text-lg">🏛️ 共同必修與通識學分 (24 學分) 雙修法對照</h2>
          <p class="text-xs text-slate-500 mt-0.5">點選選擇套用修法，無 * 號之通識超修學分自動轉算入畢業選修學分</p>
        </div>
        <div class="text-xs bg-slate-100 p-2 rounded-lg text-slate-700 font-medium space-y-0.5">
          <div>國文：<span id="raw-cn" class="font-bold text-blue-600">0</span> 學分 | 外文：<span id="raw-en" class="font-bold text-blue-600">0</span> 學分 | 通識：<span id="raw-ge" class="font-bold text-blue-600">0</span> 學分</div>
          <div>已修指定領域 (A1~A5)：<span id="raw-domains" class="font-bold text-emerald-600">無</span></div>
        </div>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <!-- 修法 1 -->
        <label id="card-path-1" class="relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-slate-50 border-slate-200 hover:border-blue-300">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <input type="radio" name="adopted_path" value="1" onchange="changePath('1')" id="radio-path-1" class="w-4 h-4 accent-blue-600">
              <span class="font-bold text-slate-800 text-sm">修法 (1)：6國文 + 6外文 + 12通識 (2指定領域)</span>
            </div>
            <span id="badge-path-1" class="text-xs font-semibold px-2 py-0.5 rounded bg-slate-200 text-slate-600">未採用</span>
          </div>
          <div class="flex justify-between items-center text-xs border-y py-1.5 border-slate-200/60">
            <span class="text-slate-600">指定領域需求：<b class="text-slate-800">至少 2 個領域</b></span>
            <span id="domain-status-p1" class="font-semibold text-slate-500">未達 2 領域</span>
          </div>
          <div class="space-y-1">
            <div class="flex justify-between text-xs font-medium text-slate-600">
              <span>基本採計進度</span>
              <span><span id="path1-valid">0</span> / 24 學分</span>
            </div>
            <div class="w-full bg-slate-200 rounded-full h-2.5 overflow-hidden">
              <div id="path1-bar" class="bg-blue-600 h-full transition-all duration-300" style="width: 0%"></div>
            </div>
          </div>
          <div class="grid grid-cols-3 gap-2 pt-1 text-xs text-slate-600 text-center">
            <div class="bg-white p-2 rounded border">國文: <span id="p1-cn" class="font-bold">0</span>/6</div>
            <div class="bg-white p-2 rounded border">外文: <span id="p1-en" class="font-bold">0</span>/6</div>
            <div class="bg-white p-2 rounded border">通識: <span id="p1-ge" class="font-bold">0</span>/12</div>
          </div>
          <div class="text-xs font-semibold text-slate-700 bg-slate-100 p-2 rounded flex justify-between items-center">
            <span>✨ 此修法併入選修之超修學分 (無*)：</span>
            <b class="text-sm">+<span id="p1-excess">0</span> 學分</b>
          </div>
        </label>

        <!-- 修法 2 -->
        <label id="card-path-2" class="relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-slate-50 border-slate-200 hover:border-blue-300">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <input type="radio" name="adopted_path" value="2" onchange="changePath('2')" id="radio-path-2" class="w-4 h-4 accent-blue-600">
              <span class="font-bold text-slate-800 text-sm">修法 (2)：3國文 + 6外文 + 15通識 (3指定領域)</span>
            </div>
            <span id="badge-path-2" class="text-xs font-semibold px-2 py-0.5 rounded bg-slate-200 text-slate-600">未採用</span>
          </div>
          <div class="flex justify-between items-center text-xs border-y py-1.5 border-slate-200/60">
            <span class="text-slate-600">指定領域需求：<b class="text-slate-800">至少 3 個領域</b></span>
            <span id="domain-status-p2" class="font-semibold text-slate-500">未達 3 領域</span>
          </div>
          <div class="space-y-1">
            <div class="flex justify-between text-xs font-medium text-slate-600">
              <span>基本採計進度</span>
              <span><span id="path2-valid">0</span> / 24 學分</span>
            </div>
            <div class="w-full bg-slate-200 rounded-full h-2.5 overflow-hidden">
              <div id="path2-bar" class="bg-blue-600 h-full transition-all duration-300" style="width: 0%"></div>
            </div>
          </div>
          <div class="grid grid-cols-3 gap-2 pt-1 text-xs text-slate-600 text-center">
            <div class="bg-white p-2 rounded border">國文: <span id="p2-cn" class="font-bold">0</span>/3</div>
            <div class="bg-white p-2 rounded border">外文: <span id="p2-en" class="font-bold">0</span>/6</div>
            <div class="bg-white p-2 rounded border">通識: <span id="p2-ge" class="font-bold">0</span>/15</div>
          </div>
          <div class="text-xs font-semibold text-slate-700 bg-slate-100 p-2 rounded flex justify-between items-center">
            <span>✨ 此修法併入選修之超修學分 (無*)：</span>
            <b class="text-sm">+<span id="p2-excess">0</span> 學分</b>
          </div>
        </label>
      </div>
    </section>

    <!-- 主區塊 -->
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
      <!-- 左側：必修 / 體育 / 服務學習 勾選區 -->
      <div class="lg:col-span-6 space-y-4">
        <section class="bg-white p-5 rounded-2xl shadow-sm border border-slate-200 space-y-3">
          <div class="flex justify-between items-center border-b pb-2">
            <h2 class="font-bold text-slate-800 text-base">☑️ 必修與體育服務點選完成</h2>
            <span class="text-xs font-medium text-slate-600" id="req-completion-count">系必修: 0/49 | 體育/服務: 0/6</span>
          </div>
          <div class="flex gap-1 bg-slate-100 p-1 rounded-lg text-xs font-medium">
            <button onclick="filterGrade('all')" id="tab-all" class="flex-1 py-1 rounded-md bg-white shadow-sm text-slate-800 font-semibold">全部</button>
            <button onclick="filterGrade('大一')" id="tab-大一" class="flex-1 py-1 rounded-md text-slate-500 hover:text-slate-800">大一</button>
            <button onclick="filterGrade('大二')" id="tab-大二" class="flex-1 py-1 rounded-md text-slate-500 hover:text-slate-800">大二</button>
            <button onclick="filterGrade('大三')" id="tab-大三" class="flex-1 py-1 rounded-md text-slate-500 hover:text-slate-800">大三</button>
            <button onclick="filterGrade('大四')" id="tab-大四" class="flex-1 py-1 rounded-md text-slate-500 hover:text-slate-800">大四</button>
          </div>
          <div class="space-y-1.5 max-h-[520px] overflow-y-auto pr-1 text-sm" id="required-courses-list"></div>
        </section>
      </div>

      <!-- 右側：共同 / 通識 / 選修紀錄區 -->
      <div class="lg:col-span-6 space-y-6">
        <section class="bg-white p-5 rounded-2xl shadow-sm border border-slate-200 space-y-4">
          <h2 class="font-bold text-slate-800 text-base border-b pb-2">➕ 新增共同 / 通識 / 選修紀錄</h2>
          <form onsubmit="addSemesterCourse(event)" class="grid grid-cols-12 gap-3 text-sm">
            <div class="col-span-6 sm:col-span-3">
              <label class="block text-xs text-slate-500 mb-1">修課學期</label>
              <input type="text" id="sem-term" placeholder="例: 114-1" required class="w-full px-3 py-1.5 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none">
            </div>
            <div class="col-span-6 sm:col-span-4">
              <label class="block text-xs text-slate-500 mb-1">科目名稱</label>
              <input type="text" id="sem-course-name" placeholder="科目名稱" required class="w-full px-3 py-1.5 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none">
            </div>
            
            <div class="col-span-4 sm:col-span-2">
              <label class="block text-xs text-slate-500 mb-1">學分數</label>
              <select id="sem-credit" class="w-full px-2 py-1.5 border rounded-lg bg-white focus:ring-2 focus:ring-blue-500 outline-none text-xs">
                <option value="1">1 學分</option>
                <option value="2">2 學分</option>
                <option value="3" selected>3 學分</option>
                <option value="4">4 學分</option>
                <option value="5">5 學分</option>
                <option value="6">6 學分</option>
              </select>
            </div>

            <div class="col-span-8 sm:col-span-3">
              <label class="block text-xs text-slate-500 mb-1">類別</label>
              <select id="sem-category" onchange="handleCategoryChange()" class="w-full px-2 py-1.5 border rounded-lg bg-white focus:ring-2 focus:ring-blue-500 outline-none text-xs">
                <option value="chinese">大學國文</option>
                <option value="english">外文領域</option>
                <option value="genEdu">通識課程</option>
                <option value="ele">選修課程</option>
                <option value="pe">體育/服務</option>
              </select>
            </div>

            <div id="domain-wrapper" class="col-span-12 hidden bg-purple-50/80 p-3.5 rounded-xl border border-purple-200 space-y-2">
              <div class="flex justify-between items-center">
                <label class="block text-xs font-bold text-purple-900">🎯 請選擇通識領域分類（可複選，雙領域兼充）：</label>
                <label class="inline-flex items-center gap-1 cursor-pointer text-xs text-purple-900 font-medium">
                  <input type="checkbox" id="sem-has-star" class="w-3.5 h-3.5 accent-purple-700 rounded">
                  <span>此課程帶有星號 (*)</span>
                </label>
              </div>

              <div class="relative">
                <button type="button" onclick="toggleDomainDropdown()" id="domain-dropdown-btn" class="w-full px-3 py-2 bg-white border border-purple-300 rounded-lg text-xs text-left text-slate-700 flex justify-between items-center focus:ring-2 focus:ring-purple-400">
                  <span id="domain-selected-text">請點擊下拉勾選領域 (可多選)...</span>
                  <span class="text-xs text-slate-400">▼</span>
                </button>

                <div id="domain-dropdown-menu" class="hidden absolute z-20 top-full left-0 right-0 mt-1 bg-white border border-purple-200 rounded-xl shadow-lg p-2 space-y-1 max-h-56 overflow-y-auto text-xs">
                  <div class="px-2 py-1 font-bold text-emerald-700 bg-emerald-50 rounded text-[11px]">醫技系指定領域 (A1~A5)</div>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A1" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A1：文學與藝術（指定領域）</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A2" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A2：歷史思維（指定領域）</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A3" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A3：哲學與道德思考（指定領域）</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A4" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A4：公民意識與社會分析（指定領域）</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A5" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A5：量化分析與數學素養（指定領域）</span>
                  </label>

                  <div class="px-2 py-1 font-bold text-slate-600 bg-slate-100 rounded text-[11px] mt-1">其他通識領域 (A6~A8)</div>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A6" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A6：物質科學</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A7" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A7：生命科學</span>
                  </label>
                  <label class="flex items-center gap-2 p-1.5 hover:bg-purple-50 rounded cursor-pointer">
                    <input type="checkbox" name="domain-opt" value="A8" onchange="updateDomainBtnText()" class="accent-purple-600 rounded">
                    <span>A8：應用科學</span>
                  </label>
                </div>
              </div>
            </div>

            <div class="col-span-12 flex justify-end">
              <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded-lg font-medium text-sm hover:bg-blue-700 transition">
                + 加入修課紀錄
              </button>
            </div>
          </form>
        </section>

        <section class="bg-white p-5 rounded-2xl shadow-sm border border-slate-200 space-y-4">
          <h2 class="font-bold text-slate-800 text-base border-b pb-2">📚 歷年明細紀錄</h2>
          <div id="semesters-container" class="space-y-4 max-h-[350px] overflow-y-auto pr-1"></div>
        </section>
      </div>
    </div>
  </div>

  <!-- 匯出 Modal (QR Code & 代碼) -->
  <div id="export-modal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 space-y-4 shadow-xl">
      <div class="flex justify-between items-center border-b pb-3">
        <h3 class="font-bold text-slate-800 text-base">📱 手機掃描或複製代碼</h3>
        <button onclick="closeExportModal()" class="text-slate-400 hover:text-slate-600 text-lg">✕</button>
      </div>
      <p class="text-xs text-slate-500 leading-relaxed">
        用手機相機直接掃描下方 QR Code 即可開啓並匯入資料；或點擊「複製代碼」在另一台裝置輸入。
      </p>
      <div class="flex justify-center p-4 bg-slate-50 rounded-xl border border-slate-200">
        <div id="qrcode"></div>
      </div>
      <div class="space-y-1.5">
        <label class="block text-xs font-semibold text-slate-700">專屬同步代碼：</label>
        <textarea id="export-code-text" readonly class="w-full h-20 p-2.5 text-xs bg-slate-50 border rounded-xl font-mono text-slate-600 select-all focus:outline-none resize-none"></textarea>
        <button onclick="copyExportCode()" class="w-full py-2 bg-slate-800 hover:bg-slate-900 text-white rounded-xl text-xs font-medium transition">
          📋 一鍵複製同步代碼
        </button>
      </div>
    </div>
  </div>

  <!-- 匯入 Modal -->
  <div id="import-modal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 space-y-4 shadow-xl">
      <div class="flex justify-between items-center border-b pb-3">
        <h3 class="font-bold text-slate-800 text-base">📥 輸入代碼匯入紀錄</h3>
        <button onclick="closeImportModal()" class="text-slate-400 hover:text-slate-600 text-lg">✕</button>
      </div>
      <p class="text-xs text-slate-500 leading-relaxed">
        請貼上從其他裝置複製的同步代碼，匯入後將替換目前的修課紀錄：
      </p>
      <textarea id="import-code-input" placeholder="請在此貼上代碼..." class="w-full h-28 p-2.5 text-xs border rounded-xl font-mono focus:ring-2 focus:ring-blue-500 outline-none resize-none"></textarea>
      <div class="flex justify-end gap-2 pt-2">
        <button onclick="closeImportModal()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-xl text-xs font-medium">取消</button>
        <button onclick="submitImportCode()" class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-xl text-xs font-medium">確認匯入</button>
      </div>
    </div>
  </div>

  <script>
    const requiredCoursesList = [
      { id: 'req-srv1', name: '服務學習甲', credit: 0, grade: '大一', isService: true },
      { id: 'req-pe1', name: '體育一', credit: 1, grade: '大一', isPE: true },
      { id: 'req-pe2', name: '體育二', credit: 1, grade: '大一', isPE: true },
      { id: 'req-1', name: '普通化學丙', credit: 3, grade: '大一' },
      { id: 'req-2', name: '普通化學實驗', credit: 1, grade: '大一' },
      { id: 'req-3', name: '普通生物學', credit: 3, grade: '大一' },
      { id: 'req-4', name: '普通生物學實驗', credit: 1, grade: '大一' },
      { id: 'req-5', name: '微積分1', credit: 2, grade: '大一' },
      { id: 'req-6', name: '醫技導論', credit: 2, grade: '大一' },
      { id: 'req-7', name: '分析化學', credit: 3, grade: '大一' },
      { id: 'req-8', name: '分析化學實驗', credit: 1, grade: '大一' },
      { id: 'req-9', name: '有機化學', credit: 3, grade: '大一' },
      { id: 'req-10', name: '有機化學實驗', credit: 1, grade: '大一' },
      { id: 'req-srv2', name: '服務學習乙', credit: 0, grade: '大二', isService: true },
      { id: 'req-pe3', name: '體育三', credit: 1, grade: '大二', isPE: true },
      { id: 'req-pe4', name: '體育四', credit: 1, grade: '大二', isPE: true },
      { id: 'req-11', name: '生物化學', credit: 4, grade: '大二' },
      { id: 'req-12', name: '解剖學', credit: 3, grade: '大二' },
      { id: 'req-13', name: '生理學', credit: 4, grade: '大二' },
      { id: 'req-14', name: '實驗室安全衛生', credit: 1, grade: '大二' },
      { id: 'req-15', name: '生物化學實驗', credit: 2, grade: '大二' },
      { id: 'req-16', name: '寄生蟲學丙', credit: 3, grade: '大二' },
      { id: 'req-17', name: '微生物學免疫學及實驗', credit: 5, grade: '大二' },
      { id: 'req-18', name: '病理學', credit: 2, grade: '大三' },
      { id: 'req-19', name: '生物統計學', credit: 3, grade: '大三' },
      { id: 'req-20', name: '儀器分析', credit: 1, grade: '大三' },
      { id: 'req-21', name: '臨床生化及分子檢驗實驗', credit: 2, grade: '大三' },
      { id: 'req-22', name: '臨床生化學', credit: 2, grade: '大三' },
      { id: 'req-23', name: '臨床生理學', credit: 2, grade: '大三' },
      { id: 'req-24', name: '血液學與臨床血液學(上)', credit: 2, grade: '大三' },
      { id: 'req-25', name: '血液學實驗(上)', credit: 1, grade: '大三' },
      { id: 'req-26', name: '分子生物學', credit: 3, grade: '大三' },
      { id: 'req-27', name: '血庫學', credit: 1, grade: '大三' },
      { id: 'req-28', name: '臨床鏡檢學', credit: 1, grade: '大三' },
      { id: 'req-29', name: '臨床鏡檢學實驗', credit: 1, grade: '大三' },
      { id: 'req-30', name: '臨床微生物檢驗及生物技術實驗', credit: 2, grade: '大三' },
      { id: 'req-31', name: '臨床細菌及黴菌學', credit: 2, grade: '大三' },
      { id: 'req-32', name: '臨床病毒學', credit: 1, grade: '大三' },
      { id: 'req-33', name: '臨床血清免疫學', credit: 1, grade: '大三' },
      { id: 'req-34', name: '血液學與臨床血液學(下)', credit: 3, grade: '大三' },
      { id: 'req-35', name: '血液學實驗(下)', credit: 1, grade: '大三' },
      { id: 'req-36', name: '組織及病理切片技術實驗', credit: 2, grade: '大三' },
      { id: 'req-37', name: '醫學分子檢驗學上', credit: 1, grade: '大三' },
      { id: 'req-38', name: '臨床鏡檢學實習', credit: 3, grade: '大四' },
      { id: 'req-39', name: '臨床生理學實習', credit: 2, grade: '大四' },
      { id: 'req-40', name: '臨床生化學實習', credit: 3, grade: '大四' },
      { id: 'req-41', name: '臨床細菌及黴菌學實習', credit: 3, grade: '大四' },
      { id: 'req-42', name: '臨床血清免疫學實習', credit: 2, grade: '大四' },
      { id: 'req-43', name: '病毒學基礎及生物技術實驗', credit: 1, grade: '大四' },
      { id: 'req-44', name: '臨床血液學實習', credit: 3, grade: '大四' },
      { id: 'req-45', name: '血庫實習', credit: 1, grade: '大四' },
      { id: 'req-46', name: '臨床病毒學實習', credit: 2, grade: '大四' },
      { id: 'req-47', name: '病理學實習', credit: 1, grade: '大四' },
      { id: 'req-48', name: '醫學分子檢驗學實習', credit: 1, grade: '大四' },
      { id: 'req-49', name: '醫學分子檢驗學下', credit: 1, grade: '大四' }
    ];

    const designatedDomains = ['A1', 'A2', 'A3', 'A4', 'A5'];

    let state = {
      adoptedPath: '1',
      completedReqIds: [],
      semesterLogs: [],
      currentFilter: 'all'
    };

    const categoryLabels = {
      chinese: '大學國文',
      english: '外文領域',
      genEdu: '通識課程',
      ele: '選修課程',
      pe: '體育/服務'
    };

    // 編碼與解碼工具函數
    function encodeStateData(data) {
      const exportObj = {
        adoptedPath: data.adoptedPath,
        completedReqIds: data.completedReqIds,
        semesterLogs: data.semesterLogs
      };
      return btoa(encodeURIComponent(JSON.stringify(exportObj)));
    }

    function decodeStateData(codeStr) {
      const jsonStr = decodeURIComponent(atob(codeStr.trim()));
      return JSON.parse(jsonStr);
    }

    function init() {
      // 1. 載入本地記憶
      const localData = localStorage.getItem('ntu_mlsb_v13_data');
      if (localData) {
        try { state = JSON.parse(localData); } catch(e) {}
      }

      // 2. 檢測 URL Hash（手機掃描 QR Code 開啟時自動匯入）
      if (window.location.hash.startsWith('#data=')) {
        const codeFromUrl = window.location.hash.replace('#data=', '');
        try {
          const importedState = decodeStateData(codeFromUrl);
          if (importedState && Array.isArray(importedState.completedReqIds)) {
            state.adoptedPath = importedState.adoptedPath || '1';
            state.completedReqIds = importedState.completedReqIds;
            state.semesterLogs = importedState.semesterLogs || [];
            localStorage.setItem('ntu_mlsb_v13_data', JSON.stringify(state));
            
            // 清理網址列，防止重複載入
            window.history.replaceState(null, null, window.location.pathname);
            alert('🎉 已成功由 QR Code 匯入最新修課紀錄！');
          }
        } catch(e) {
          console.error('URL Hash 解密失敗', e);
        }
      }

      document.addEventListener('click', function(e) {
        const btn = document.getElementById('domain-dropdown-btn');
        const menu = document.getElementById('domain-dropdown-menu');
        if (btn && menu && !btn.contains(e.target) && !menu.contains(e.target)) {
          menu.classList.add('hidden');
        }
      });

      handleCategoryChange();
      renderAll();
    }

    function saveData() {
      localStorage.setItem('ntu_mlsb_v13_data', JSON.stringify(state));
      renderAll();
    }

    // Modal 與 QR Code 控制函數
    function openExportModal() {
      const code = encodeStateData(state);
      document.getElementById('export-code-text').value = code;

      // 建立包含完整資料的網址供手機掃描
      const currentUrl = window.location.origin + window.location.pathname;
      const shareUrl = `${currentUrl}#data=${code}`;

      const qrContainer = document.getElementById('qrcode');
      qrContainer.innerHTML = '';
      new QRCode(qrContainer, {
        text: shareUrl,
        width: 170,
        height: 170,
        colorDark: "#0f172a",
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.M
      });

      document.getElementById('export-modal').classList.remove('hidden');
    }

    function closeExportModal() {
      document.getElementById('export-modal').classList.add('hidden');
    }

    function copyExportCode() {
      const textarea = document.getElementById('export-code-text');
      textarea.select();
      navigator.clipboard.writeText(textarea.value).then(() => {
        alert('✅ 同步代碼已複製到剪貼簿！');
      }).catch(() => {
        document.execCommand('copy');
        alert('✅ 同步代碼已複製到剪貼簿！');
      });
    }

    function openImportModal() {
      document.getElementById('import-code-input').value = '';
      document.getElementById('import-modal').classList.remove('hidden');
    }

    function closeImportModal() {
      document.getElementById('import-modal').classList.add('hidden');
    }

    function submitImportCode() {
      const rawCode = document.getElementById('import-code-input').value.trim();
      if (!rawCode) {
        alert('請輸入代碼！');
        return;
      }
      try {
        const importedState = decodeStateData(rawCode);
        if (importedState && Array.isArray(importedState.completedReqIds)) {
          state.adoptedPath = importedState.adoptedPath || '1';
          state.completedReqIds = importedState.completedReqIds;
          state.semesterLogs = importedState.semesterLogs || [];
          saveData();
          closeImportModal();
          alert('✅ 修課紀錄匯入成功！');
        } else {
          throw new Error('資料格式不正確');
        }
      } catch(e) {
        alert('❌ 代碼格式無效，請確認代碼是否完整複製！');
      }
    }

    function handleCategoryChange() {
      const cat = document.getElementById('sem-category').value;
      const domainWrapper = document.getElementById('domain-wrapper');
      if (cat === 'genEdu') {
        domainWrapper.classList.remove('hidden');
      } else {
        domainWrapper.classList.add('hidden');
      }
    }

    function toggleDomainDropdown() {
      const menu = document.getElementById('domain-dropdown-menu');
      menu.classList.toggle('hidden');
    }

    function updateDomainBtnText() {
      const checkboxes = document.querySelectorAll('input[name="domain-opt"]:checked');
      const selected = Array.from(checkboxes).map(cb => cb.value);
      const textSpan = document.getElementById('domain-selected-text');
      
      if (selected.length === 0) {
        textSpan.textContent = "請點擊下拉勾選領域 (可多選)...";
        textSpan.className = "text-slate-400";
      } else {
        textSpan.textContent = `已選擇 ${selected.length} 個領域: ${selected.join(', ')}`;
        textSpan.className = "text-purple-900 font-semibold";
      }
    }

    function changePath(path) {
      state.adoptedPath = path;
      saveData();
    }

    function toggleRequiredCourse(id) {
      const index = state.completedReqIds.indexOf(id);
      if (index > -1) {
        state.completedReqIds.splice(index, 1);
      } else {
        state.completedReqIds.push(id);
      }
      saveData();
    }

    function filterGrade(grade) {
      state.currentFilter = grade;
      ['all', '大一', '大二', '大三', '大四'].forEach(g => {
        const btn = document.getElementById(`tab-${g}`);
        if (g === grade) {
          btn.className = "flex-1 py-1 rounded-md bg-white shadow-sm text-slate-800 font-semibold";
        } else {
          btn.className = "flex-1 py-1 rounded-md text-slate-500 hover:text-slate-800";
        }
      });
      renderRequiredList();
    }

    function renderAll() {
      const earnedReqCredits = requiredCoursesList
        .filter(c => state.completedReqIds.includes(c.id) && !c.isPE && !c.isService)
        .reduce((sum, c) => sum + Number(c.credit), 0);

      const checkedPECredits = requiredCoursesList
        .filter(c => state.completedReqIds.includes(c.id) && c.isPE)
        .reduce((sum, c) => sum + Number(c.credit), 0);

      const checkedServiceCount = requiredCoursesList
        .filter(c => state.completedReqIds.includes(c.id) && c.isService)
        .length;

      const raw = { chinese: 0, english: 0, genEdu: 0, genEduNoStarExcessable: 0, ele: 0, loggedPE: 0 };
      const takenDomainsSet = new Set();

      state.semesterLogs.forEach(sem => {
        sem.courses.forEach(c => {
          const cred = Number(c.credit);
          if (c.category === 'pe') {
            raw.loggedPE += cred;
          } else {
            raw[c.category] = (raw[c.category] || 0) + cred;
          }
          
          if (c.category === 'genEdu') {
            if (c.domains && Array.isArray(c.domains)) {
              c.domains.forEach(d => takenDomainsSet.add(d));
            }
            if (!c.hasStar) {
              raw.genEduNoStarExcessable += cred;
            }
          }
        });
      });

      const takenDesignatedDomains = Array.from(takenDomainsSet).filter(d => designatedDomains.includes(d));

      document.getElementById('raw-cn').textContent = raw.chinese;
      document.getElementById('raw-en').textContent = raw.english;
      document.getElementById('raw-ge').textContent = raw.genEdu;
      document.getElementById('raw-domains').textContent = takenDesignatedDomains.length > 0 ? takenDesignatedDomains.join(', ') : '無';

      const p1_cn = Math.min(raw.chinese, 6);
      const p1_en = Math.min(raw.english, 6);
      const p1_ge = Math.min(raw.genEdu, 12);
      const p1_valid = p1_cn + p1_en + p1_ge;
      
      const cn_excess1 = Math.max(0, raw.chinese - 6);
      const en_excess1 = Math.max(0, raw.english - 6);
      const ge_raw_excess1 = Math.max(0, raw.genEdu - 12);
      const ge_valid_excess1 = Math.min(ge_raw_excess1, raw.genEduNoStarExcessable);
      const p1_excess = cn_excess1 + en_excess1 + ge_valid_excess1;
      const p1_domain_ok = takenDesignatedDomains.length >= 2;

      const p2_cn = Math.min(raw.chinese, 3);
      const p2_en = Math.min(raw.english, 6);
      const p2_ge = Math.min(raw.genEdu, 15);
      const p2_valid = p2_cn + p2_en + p2_ge;

      const cn_excess2 = Math.max(0, raw.chinese - 3);
      const en_excess2 = Math.max(0, raw.english - 6);
      const ge_raw_excess2 = Math.max(0, raw.genEdu - 15);
      const ge_valid_excess2 = Math.min(ge_raw_excess2, raw.genEduNoStarExcessable);
      const p2_excess = cn_excess2 + en_excess2 + ge_valid_excess2;
      const p2_domain_ok = takenDesignatedDomains.length >= 3;

      document.getElementById('path1-valid').textContent = p1_valid;
      document.getElementById('path1-bar').style.width = `${Math.min(100, Math.round((p1_valid / 24) * 100))}%`;
      document.getElementById('p1-cn').textContent = raw.chinese;
      document.getElementById('p1-en').textContent = raw.english;
      document.getElementById('p1-ge').textContent = raw.genEdu;
      document.getElementById('p1-excess').textContent = p1_excess;
      document.getElementById('domain-status-p1').className = p1_domain_ok ? "font-semibold text-emerald-600" : "font-semibold text-slate-500";
      document.getElementById('domain-status-p1').textContent = p1_domain_ok ? `✅ 已達標 (${takenDesignatedDomains.length}/2)` : `未達 2 領域 (${takenDesignatedDomains.length}/2)`;

      document.getElementById('path2-valid').textContent = p2_valid;
      document.getElementById('path2-bar').style.width = `${Math.min(100, Math.round((p2_valid / 24) * 100))}%`;
      document.getElementById('p2-cn').textContent = raw.chinese;
      document.getElementById('p2-en').textContent = raw.english;
      document.getElementById('p2-ge').textContent = raw.genEdu;
      document.getElementById('p2-excess').textContent = p2_excess;
      document.getElementById('domain-status-p2').className = p2_domain_ok ? "font-semibold text-emerald-600" : "font-semibold text-slate-500";
      document.getElementById('domain-status-p2').textContent = p2_domain_ok ? `✅ 已達標 (${takenDesignatedDomains.length}/3)` : `未達 3 領域 (${takenDesignatedDomains.length}/3)`;

      const isPath1 = state.adoptedPath === '1';
      document.getElementById('radio-path-1').checked = isPath1;
      document.getElementById('radio-path-2').checked = !isPath1;

      document.getElementById('card-path-1').className = isPath1 
        ? "relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-blue-50/40 border-blue-500 shadow-sm" 
        : "relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-slate-50 border-slate-200 opacity-70 hover:opacity-100";
      document.getElementById('badge-path-1').className = isPath1 ? "text-xs font-semibold px-2 py-0.5 rounded bg-blue-600 text-white" : "text-xs font-semibold px-2 py-0.5 rounded bg-slate-200 text-slate-600";
      document.getElementById('badge-path-1').textContent = isPath1 ? "採用中" : "未採用";

      document.getElementById('card-path-2').className = !isPath1 
        ? "relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-blue-50/40 border-blue-500 shadow-sm" 
        : "relative p-4 rounded-xl border-2 cursor-pointer transition space-y-3 block bg-slate-50 border-slate-200 opacity-70 hover:opacity-100";
      document.getElementById('badge-path-2').className = !isPath1 ? "text-xs font-semibold px-2 py-0.5 rounded bg-blue-600 text-white" : "text-xs font-semibold px-2 py-0.5 rounded bg-slate-200 text-slate-600";
      document.getElementById('badge-path-2').textContent = !isPath1 ? "採用中" : "未採用";

      const activeCommonEarned = isPath1 ? p1_valid : p2_valid;
      const activeExcess = isPath1 ? p1_excess : p2_excess;

      const totalElective = raw.ele + activeExcess;
      const totalEarned = earnedReqCredits + activeCommonEarned + totalElective;
      const totalTarget = 128;

      document.getElementById('stat-total-earned').textContent = totalEarned;
      document.getElementById('stat-total-target').textContent = totalTarget;
      document.getElementById('stat-total-remaining').textContent = Math.max(0, totalTarget - totalEarned);
      document.getElementById('stat-main-bar').style.width = `${Math.min(100, Math.round((totalEarned / totalTarget) * 100))}%`;

      document.getElementById('calc-req').textContent = earnedReqCredits;
      document.getElementById('calc-common').textContent = activeCommonEarned;
      document.getElementById('calc-ele-total').textContent = totalElective;
      document.getElementById('calc-total').textContent = totalEarned;

      document.getElementById('cat-earned-req').textContent = earnedReqCredits;
      document.getElementById('cat-rem-req').textContent = Math.max(0, 99 - earnedReqCredits);

      document.getElementById('cat-earned-common').textContent = activeCommonEarned;
      document.getElementById('cat-rem-common').textContent = Math.max(0, 24 - activeCommonEarned);

      document.getElementById('cat-earned-ele-total').textContent = totalElective;
      document.getElementById('cat-earned-ele').textContent = raw.ele;
      document.getElementById('cat-earned-excess').textContent = activeExcess;

      const eleBadge = document.getElementById('ele-badge');
      if (totalElective >= 5) {
        eleBadge.classList.remove('hidden');
      } else {
        eleBadge.classList.add('hidden');
      }

      const totalPE = checkedPECredits + raw.loggedPE;
      document.getElementById('cat-earned-pe').textContent = totalPE;
      document.getElementById('cat-earned-srv').textContent = checkedServiceCount;

      const peBadge = document.getElementById('pe-badge');
      if (totalPE >= 4) {
        peBadge.classList.remove('hidden');
      } else {
        peBadge.classList.add('hidden');
      }

      const srvBadge = document.getElementById('srv-badge');
      if (checkedServiceCount >= 2) {
        srvBadge.classList.remove('hidden');
      } else {
        srvBadge.classList.add('hidden');
      }

      const checkedReqCount = requiredCoursesList.filter(c => state.completedReqIds.includes(c.id) && !c.isPE && !c.isService).length;
      const checkedOtherCount = state.completedReqIds.length - checkedReqCount;
      document.getElementById('req-completion-count').textContent = `系必修: ${checkedReqCount}/49 | 體育/服務: ${checkedOtherCount}/6`;

      renderRequiredList();
      renderSemesterLogs();
    }

    function renderRequiredList() {
      const reqListEl = document.getElementById('required-courses-list');
      const filtered = requiredCoursesList.filter(item => 
        state.currentFilter === 'all' || item.grade === state.currentFilter
      );

      reqListEl.innerHTML = filtered.map(item => {
        const isChecked = state.completedReqIds.includes(item.id);
        
        let badgeStyle = 'bg-slate-200 text-slate-600';
        let badgeText = item.grade;

        if (item.isPE) {
          badgeStyle = 'bg-blue-100 text-blue-700 font-semibold';
          badgeText = '體育 (不計學分)';
        } else if (item.isService) {
          badgeStyle = 'bg-emerald-100 text-emerald-700 font-semibold';
          badgeText = '服務 (0學分)';
        } else if (isChecked) {
          badgeStyle = 'bg-emerald-100 text-emerald-700 font-semibold';
          badgeText = item.grade;
        }

        return `
          <label class="flex justify-between items-center p-2.5 rounded-lg border text-xs sm:text-sm cursor-pointer transition select-none ${isChecked ? 'bg-emerald-50/80 border-emerald-200' : 'bg-slate-50 border-slate-200 hover:bg-slate-100'}">
            <div class="flex items-center gap-2.5">
              <input type="checkbox" ${isChecked ? 'checked' : ''} onchange="toggleRequiredCourse('${item.id}')" class="w-4 h-4 accent-emerald-600 rounded">
              <span class="font-medium ${isChecked ? 'line-through text-slate-500' : 'text-slate-800'}">${item.name}</span>
              <span class="text-xs text-slate-400">(${item.credit}學分)</span>
            </div>
            <span class="text-xs px-2 py-0.5 rounded ${badgeStyle}">
              ${badgeText}
            </span>
          </label>
        `;
      }).join('');
    }

    function renderSemesterLogs() {
      const semContainer = document.getElementById('semesters-container');
      if (state.semesterLogs.length === 0) {
        semContainer.innerHTML = `<div class="text-center py-8 text-slate-400 text-sm">尚無紀錄，請在上方新增！</div>`;
        return;
      }

      semContainer.innerHTML = state.semesterLogs.map(sem => {
        const semCredits = sem.courses.reduce((sum, c) => c.category !== 'pe' ? sum + Number(c.credit) : sum, 0);
        return `
          <div class="border border-slate-200 rounded-xl overflow-hidden shadow-sm">
            <div class="bg-slate-50 px-4 py-2 border-b border-slate-200 flex justify-between items-center">
              <div class="font-bold text-slate-700 text-xs sm:text-sm">${sem.term} 學期 <span class="text-xs font-normal text-slate-500 ml-1">（採計學分：${semCredits}）</span></div>
              <button onclick="removeSemester('${sem.id}')" class="text-xs text-rose-500 hover:underline">刪除學期</button>
            </div>
            <div class="p-2.5 divide-y divide-slate-100">
              ${sem.courses.map(c => `
                <div class="py-1.5 flex justify-between items-center text-xs sm:text-sm">
                  <div class="flex items-center gap-2">
                    <span class="font-medium text-slate-800">${c.name}${c.hasStar ? ' *' : ''}</span>
                    <span class="text-xs px-2 py-0.5 rounded ${c.category === 'chinese' ? 'bg-rose-50 text-rose-600' : c.category === 'english' ? 'bg-indigo-50 text-indigo-600' : c.category === 'genEdu' ? 'bg-purple-100 text-purple-700 font-medium' : c.category === 'ele' ? 'bg-amber-50 text-amber-600' : 'bg-slate-100 text-slate-600'}">
                      ${categoryLabels[c.category]}${c.domains && c.domains.length > 0 ? ` (${c.domains.join(', ')})` : ''}
                    </span>
                  </div>
                  <div class="flex items-center gap-2.5">
                    <span class="font-semibold text-slate-700">${c.credit} 學分</span>
                    <button onclick="removeCourse('${sem.id}', '${c.id}')" class="text-slate-400 hover:text-rose-500 text-xs">×</button>
                  </div>
                </div>
              `).join('')}
            </div>
          </div>
        `;
      }).join('');
    }

    function addSemesterCourse(e) {
      e.preventDefault();
      const term = document.getElementById('sem-term').value.trim();
      const name = document.getElementById('sem-course-name').value.trim();
      const credit = Number(document.getElementById('sem-credit').value) || 0;
      const category = document.getElementById('sem-category').value;

      let domains = [];
      let hasStar = false;

      if (category === 'genEdu') {
        const checkboxes = document.querySelectorAll('input[name="domain-opt"]:checked');
        domains = Array.from(checkboxes).map(cb => cb.value);
        hasStar = document.getElementById('sem-has-star').checked;
      }

      if (!term || !name) return;

      let sem = state.semesterLogs.find(s => s.term === term);
      if (!sem) {
        sem = { id: 'sem-' + Date.now(), term, courses: [] };
        state.semesterLogs.push(sem);
      }

      sem.courses.push({
        id: 'c-' + Date.now(),
        name,
        credit,
        category,
        domains,
        hasStar
      });

      document.getElementById('sem-course-name').value = '';
      document.querySelectorAll('input[name="domain-opt"]').forEach(cb => cb.checked = false);
      document.getElementById('sem-has-star').checked = false;
      updateDomainBtnText();
      saveData();
    }

    function removeCourse(semId, courseId) {
      const sem = state.semesterLogs.find(s => s.id === semId);
      if (sem) {
        sem.courses = sem.courses.filter(c => c.id !== courseId);
        if (sem.courses.length === 0) {
          state.semesterLogs = state.semesterLogs.filter(s => s.id !== semId);
        }
        saveData();
      }
    }

    function removeSemester(semId) {
      if (confirm('確定要刪除整個學期的紀錄嗎？')) {
        state.semesterLogs = state.semesterLogs.filter(s => s.id !== semId);
        saveData();
      }
    }

    init();
  </script>
</body>
</html>
