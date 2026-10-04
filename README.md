<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ApexTrade Journal - Platform Jurnal Trading & Manajemen Risiko Profesional</title>
  
  <!-- Fondasi SEO & Open Graph -->
  <meta name="description" content="Aplikasi jurnal trading profesional dengan dukungan akun Cent (USC), akun standar, manajemen risiko, visualisasi grafik ekuitas, dan kontrol admin.">
  <meta name="keywords" content="jurnal trading, trading journal, forex cent account, usc, risk management, equity curve">
  <meta name="author" content="ApexTrade System">
  <meta property="og:title" content="ApexTrade Journal - Professional Trading Journal">
  <meta property="og:description" content="Catat, analisis, dan tingkatkan konsistensi trading forex, crypto, dan saham Anda dengan akun Cent & Standard.">
  <meta property="og:type" content="website">

  <!-- Tailwind CSS & Chart.js -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            finDark: '#080d1a',
            finCard: '#0f172a',
            finBorder: '#1e293b',
            finAccent: '#38bdf8',
            finGreen: '#10b981',
            finRed: '#f43f5e',
            finAdmin: '#f59e0b',
          }
        }
      }
    }
  </script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600;700&family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background-color: #080d1a;
      color: #f1f5f9;
    }
    .mono { font-family: 'JetBrains Mono', monospace; }
    /* Scrollbar minimalis */
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #080d1a; }
    ::-webkit-scrollbar-thumb { background: #1e293b; border-radius: 3px; }
  </style>
</head>
<body class="min-h-screen flex flex-col bg-finDark antialiased text-slate-100 selection:bg-sky-500 selection:text-white">

  <!-- ================= LAYAR LOGIN / AUTH OVERLAY ================= -->
  <div id="authScreen" class="fixed inset-0 z-50 flex items-center justify-center bg-black/90 backdrop-blur-md p-4 transition-all">
    <div class="w-full max-w-md bg-finCard border border-finBorder rounded-2xl shadow-2xl p-6 sm:p-8">
      <div class="text-center mb-6">
        <div class="inline-flex items-center justify-center w-14 h-14 rounded-2xl bg-sky-500/10 border border-sky-500/30 text-sky-400 mb-3 text-2xl font-bold mono">
          ▲
        </div>
        <h1 class="text-2xl font-bold tracking-tight text-white">ApexTrade Journal</h1>
        <p class="text-sm text-slate-400 mt-1">Pilih peran akun untuk melanjutkan</p>
      </div>

      <div class="space-y-4">
        <!-- Opsi Trader Biasa -->
        <div class="p-4 rounded-xl border border-finBorder hover:border-sky-500/50 bg-slate-900/60 transition cursor-pointer" onclick="selectRole('trader')">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-3">
              <span class="text-2xl">📈</span>
              <div>
                <h2 class="font-semibold text-white">Akun Trader</h2>
                <p class="text-xs text-slate-400">Jurnal pribadi, akun Cent & Standard, analitik</p>
              </div>
            </div>
            <input type="radio" name="authRoleRadio" id="roleTraderRadio" checked class="text-sky-500 focus:ring-sky-500">
          </div>
        </div>

        <!-- Opsi Administrator -->
        <div class="p-4 rounded-xl border border-finBorder hover:border-amber-500/50 bg-slate-900/60 transition cursor-pointer" onclick="selectRole('admin')">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-3">
              <span class="text-2xl">👑</span>
              <div>
                <h2 class="font-semibold text-amber-400">Akun Super Admin</h2>
                <p class="text-xs text-slate-400">Kontrol risiko global, audit log & database raw</p>
              </div>
            </div>
            <input type="radio" name="authRoleRadio" id="roleAdminRadio" class="text-amber-500 focus:ring-amber-500">
          </div>
        </div>

        <!-- Kolom PIN Khusus Admin -->
        <div id="adminPinField" class="hidden pt-2">
          <label class="block text-xs font-semibold uppercase text-amber-400 mb-1">PIN Keamanan Admin</label>
          <input type="password" id="adminPinInput" placeholder="Masukkan PIN (Default: admin123)" class="w-full bg-slate-950 border border-amber-500/40 rounded-xl px-4 py-2.5 text-sm focus:outline-none focus:border-amber-400 mono">
          <p class="text-[11px] text-slate-500 mt-1">*PIN bawaan: <code class="text-amber-400">admin123</code></p>
        </div>

        <button onclick="handleLogin()" class="w-full mt-4 bg-sky-500 hover:bg-sky-400 text-slate-950 font-bold py-3 rounded-xl transition shadow-lg shadow-sky-500/20 flex items-center justify-center gap-2">
          <span>Masuk ke Workspace</span>
          <span>→</span>
        </button>
      </div>

      <div class="mt-6 pt-4 border-t border-finBorder text-center">
        <p class="text-xs text-slate-500">Kredensial tersimpan secara aman di peramban lokal perangkat.</p>
      </div>
    </div>
  </div>

  <!-- ================= TOP HEADER (NAVIGASI UTAMA) ================= -->
  <header class="sticky top-0 z-40 bg-finCard/90 backdrop-blur-md border-b border-finBorder px-4 sm:px-8 py-3">
    <div class="max-w-7xl mx-auto flex items-center justify-between gap-4">
      
      <!-- Brand & Mobile Toggle -->
      <div class="flex items-center gap-3">
        <button onclick="toggleMobileMenu()" class="sm:hidden p-2 text-slate-400 hover:text-white rounded-lg bg-slate-900 border border-finBorder">
          ☰
        </button>
        <div class="flex items-center gap-2.5 cursor-pointer" onclick="switchTab('dashboard')">
          <span class="w-8 h-8 rounded-lg bg-sky-500/20 border border-sky-400/40 text-sky-400 flex items-center justify-center font-bold text-sm mono">▲</span>
          <div>
            <div class="font-bold text-white tracking-wide leading-tight flex items-center gap-2">
              <span>ApexTrade</span>
              <span id="roleBadgeHeader" class="text-[10px] px-2 py-0.5 rounded-full font-mono bg-sky-500/10 text-sky-400 border border-sky-500/20">TRADER</span>
            </div>
            <p class="text-[10px] text-slate-400">Risk & Performance Journal</p>
          </div>
        </div>
      </div>

      <!-- Currency Unit Switcher (USD vs Cent vs IDR) -->
      <div class="flex items-center gap-2">
        <div class="bg-slate-950/80 border border-finBorder p-1 rounded-xl flex items-center text-xs">
          <button id="currBtnUSD" onclick="setCurrency('USD')" class="px-2.5 py-1 rounded-lg text-slate-400 font-medium transition hover:text-white">USD ($)</button>
          <button id="currBtnUSC" onclick="setCurrency('USC')" class="px-2.5 py-1 rounded-lg bg-sky-500/20 text-sky-400 border border-sky-500/30 font-semibold transition">CENT (¢)</button>
          <button id="currBtnIDR" onclick="setCurrency('IDR')" class="px-2.5 py-1 rounded-lg text-slate-400 font-medium transition hover:text-white">IDR (Rp)</button>
        </div>

        <!-- Tombol User & Logout -->
        <div class="relative">
          <button onclick="handleLogout()" title="Keluar / Ganti Akun" class="px-3 py-1.5 rounded-xl bg-slate-900 border border-finBorder text-xs text-rose-400 hover:bg-rose-500/10 hover:border-rose-500/40 transition flex items-center gap-1.5">
            <span>🚪</span>
            <span class="hidden md:inline">Keluar</span>
          </button>
        </div>
      </div>

    </div>
  </header>

  <!-- ================= LAYOUT UTAMA (SIDEBAR + KONTEN) ================= -->
  <div class="max-w-7xl mx-auto w-full flex-1 flex flex-col md:flex-row p-4 sm:p-6 gap-6">
    
    <!-- SIDEBAR NAVIGASI -->
    <aside id="sidebarNav" class="w-full md:w-60 flex-shrink-0 space-y-1.5 bg-finCard/50 p-3 rounded-2xl border border-finBorder self-start">
      <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500 px-3 py-1">Workspace</p>
      
      <button onclick="switchTab('dashboard')" id="nav-dashboard" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-sky-400 bg-sky-500/10 border border-sky-500/20 transition">
        <span>📊</span> Dashboard
      </button>

      <button onclick="switchTab('newTrade')" id="nav-newTrade" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-300 hover:bg-slate-900 border border-transparent transition">
        <span>✍️</span> Catat Trade Baru
      </button>

      <button onclick="switchTab('journal')" id="nav-journal" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-300 hover:bg-slate-900 border border-transparent transition">
        <span>📋</span> Riwayat Jurnal
      </button>

      <button onclick="switchTab('analytics')" id="nav-analytics" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-300 hover:bg-slate-900 border border-transparent transition">
        <span>📈</span> Analisis Performa
      </button>

      <!-- Menu Khusus Admin (Tersembunyi jika login sebagai Trader) -->
      <div id="adminMenuSection" class="hidden pt-2 border-t border-finBorder/60">
        <p class="text-[10px] font-bold uppercase tracking-wider text-amber-500/80 px-3 py-1 flex items-center gap-1">
          <span>👑</span> Hak Akses Khusus
        </p>
        <button onclick="switchTab('adminPanel')" id="nav-adminPanel" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-semibold text-amber-400 hover:bg-amber-500/10 border border-amber-500/30 transition">
          <span>🛡️</span> Admin Console
        </button>
      </div>

      <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500 px-3 pt-3 py-1">Konfigurasi</p>
      <button onclick="switchTab('settings')" id="nav-settings" class="nav-item w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-300 hover:bg-slate-900 border border-transparent transition">
        <span>⚙️</span> Pengaturan & Akun
      </button>
    </aside>

    <!-- AREA KONTEN UTAMA -->
    <main class="flex-1 min-w-0 space-y-6">

      <!-- ================= 1. TAB DASHBOARD ================= -->
      <section id="tab-dashboard" class="tab-content space-y-6">
        <!-- Banner Peringatan Risiko jika diaktifkan Admin -->
        <div id="riskWarningBanner" class="hidden p-4 rounded-2xl bg-amber-500/10 border border-amber-500/30 text-amber-300 flex items-center justify-between text-sm">
          <div class="flex items-center gap-3">
            <span class="text-xl">⚠️</span>
            <div>
              <p class="font-bold">Batas Risiko Harian Aktif!</p>
              <p class="text-xs text-amber-400/80">Admin menerapkan batasan max loss harian. Tetap patuhi aturan trading.</p>
            </div>
          </div>
        </div>

        <!-- Baris Metrik Finansial -->
        <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
          <div class="bg-finCard border border-finBorder p-4 rounded-2xl">
            <p class="text-xs text-slate-400">Total Net P&L</p>
            <h3 id="statNetPnl" class="text-xl sm:text-2xl font-bold mono mt-1 text-slate-100">+0.00</h3>
            <p id="statNetPnlCentSub" class="text-[11px] text-slate-500 mono mt-0.5">≈ $0.00 USD</p>
          </div>

          <div class="bg-finCard border border-finBorder p-4 rounded-2xl">
            <p class="text-xs text-slate-400">Win Rate</p>
            <h3 id="statWinRate" class="text-xl sm:text-2xl font-bold mono mt-1 text-sky-400">0.0%</h3>
            <p id="statWinLossCount" class="text-[11px] text-slate-500 mono mt-0.5">0 Win / 0 Loss</p>
          </div>

          <div class="bg-finCard border border-finBorder p-4 rounded-2xl">
            <p class="text-xs text-slate-400">Profit Factor</p>
            <h3 id="statProfitFactor" class="text-xl sm:text-2xl font-bold mono mt-1 text-slate-100">0.00</h3>
            <p class="text-[11px] text-slate-500 mono mt-0.5">Gross Win / Gross Loss</p>
          </div>

          <div class="bg-finCard border border-finBorder p-4 rounded-2xl">
            <p class="text-xs text-slate-400">Rasio R:R Rata-rata</p>
            <h3 id="statAvgRR" class="text-xl sm:text-2xl font-bold mono mt-1 text-emerald-400">1:0.00</h3>
            <p class="text-[11px] text-slate-500 mono mt-0.5">Risk-to-Reward Realized</p>
          </div>
        </div>

        <!-- Grafik Pertumbuhan Ekuitas -->
        <div class="bg-finCard border border-finBorder p-5 rounded-2xl">
          <div class="flex items-center justify-between mb-4">
            <div>
              <h2 class="font-bold text-white text-base">Kurva Ekuitas (Equity Growth)</h2>
              <p class="text-xs text-slate-400">Pertumbuhan modal kumulatif per transaksi</p>
            </div>
            <span class="text-xs px-2.5 py-1 rounded-lg bg-slate-900 border border-finBorder mono text-sky-400">Chart.js Live</span>
          </div>
          <div class="h-64 sm:h-72 w-full">
            <canvas id="equityChartCanvas"></canvas>
          </div>
        </div>

        <!-- 5 Transaksi Terakhir -->
        <div class="bg-finCard border border-finBorder p-5 rounded-2xl">
          <div class="flex items-center justify-between mb-4">
            <h2 class="font-bold text-white text-base">Aktivitas Terkini</h2>
            <button onclick="switchTab('journal')" class="text-xs text-sky-400 hover:underline">Lihat Semua →</button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left text-xs">
              <thead class="text-slate-500 border-b border-finBorder">
                <tr>
                  <th class="pb-2">Tanggal</th>
                  <th class="pb-2">Pair/Aset</th>
                  <th class="pb-2">Tipe Akun</th>
                  <th class="pb-2">Posisi</th>
                  <th class="pb-2 text-right">Hasil P&L</th>
                </tr>
              </thead>
              <tbody id="recentTradesTbody" class="divide-y divide-finBorder/60 mono">
                <!-- Diisi JavaScript -->
              </tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ================= 2. TAB CATAT TRADE BARU ================= -->
      <section id="tab-newTrade" class="tab-content hidden space-y-6">
        <div class="bg-finCard border border-finBorder p-5 sm:p-7 rounded-2xl">
          <div class="flex items-center justify-between border-b border-finBorder pb-4 mb-6">
            <div>
              <h2 class="text-lg font-bold text-white">Catat Transaksi Baru</h2>
              <p class="text-xs text-slate-400">Input parameter trading dan evaluasi psikologi Anda</p>
            </div>
            <span class="text-xs bg-sky-500/10 text-sky-400 border border-sky-500/20 px-3 py-1 rounded-xl">Form Eksekusi</span>
          </div>

          <form id="tradeForm" onsubmit="saveNewTrade(event)" class="space-y-5">
            <!-- Pilihan Tipe Akun -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Tipe Akun Trading</label>
                <select id="f_accountType" onchange="calculateFormEstimates()" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm focus:border-sky-500 focus:outline-none">
                  <option value="CENT">Akun Cent (USC / ¢)</option>
                  <option value="STANDARD">Akun Standar ($ USD)</option>
                </select>
                <p class="text-[11px] text-slate-500 mt-1">*Akun Cent: 100 USC senilai $1.00 USD.</p>
              </div>

              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Pair / Aset Finansial</label>
                <input type="text" id="f_symbol" required placeholder="Contoh: XAUUSD, EURUSD, BTC" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm uppercase mono focus:border-sky-500 focus:outline-none">
              </div>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Arah Posisi</label>
                <select id="f_direction" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm focus:border-sky-500 focus:outline-none">
                  <option value="BUY">BUY / LONG</option>
                  <option value="SELL">SELL / SHORT</option>
                </select>
              </div>

              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Ukuran Lot</label>
                <input type="number" step="0.01" id="f_lotSize" required placeholder="0.10" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm mono focus:border-sky-500 focus:outline-none">
              </div>

              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Setup / Strategi</label>
                <input type="text" id="f_strategy" placeholder="SMC, Breakout, FVG, EMA Cross" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm focus:border-sky-500 focus:outline-none">
              </div>
            </div>

            <!-- Harga & Risk:Reward -->
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Harga Entry</label>
                <input type="number" step="any" id="f_entryPrice" oninput="calculateFormEstimates()" required placeholder="0.00" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm mono focus:border-sky-500 focus:outline-none">
              </div>
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Stop Loss (SL)</label>
                <input type="number" step="any" id="f_stopLoss" oninput="calculateFormEstimates()" required placeholder="0.00" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm mono focus:border-sky-500 focus:outline-none">
              </div>
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Take Profit (TP)</label>
                <input type="number" step="any" id="f_takeProfit" oninput="calculateFormEstimates()" required placeholder="0.00" class="w-full bg-slate-950 border border-finBorder rounded-xl px-4 py-2.5 text-sm mono focus:border-sky-500 focus:outline-none">
              </div>
            </div>

            <!-- Hasil Nyata (Close Trade) -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 p-4 rounded-xl bg-slate-950/60 border border-finBorder">
              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Harga Exit (Penutupan)</label>
                <input type="number" step="any" id="f_exitPrice" placeholder="Kosongkan jika masih berjalan" class="w-full bg-slate-900 border border-finBorder rounded-xl px-4 py-2.5 text-sm mono focus:border-sky-500 focus:outline-none">
              </div>

              <div>
                <label class="block text-xs font-semibold uppercase text-slate-400 mb-1.5">Realisasi Profit/Loss (P
                # jurnal-trading-
