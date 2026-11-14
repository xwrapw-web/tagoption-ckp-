# tagoption-ckp-
<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>NovaX Pro | Institutional Multi-Asset Trading Terminal</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Inter & JetBrains Mono -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
  <!-- Font Awesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" />

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          },
          colors: {
            obsidian: '#0b0e14',
            panel: '#131722',
            panelHover: '#1a1f2c',
            borderDark: '#1e222d',
            textPrimary: '#eaecef',
            textSecondary: '#848e9c',
            buy: '#0ecb81',
            buyHover: '#0bb572',
            buyDim: 'rgba(14, 203, 129, 0.14)',
            sell: '#f6465d',
            sellHover: '#e03a50',
            sellDim: 'rgba(246, 70, 93, 0.14)',
            accent: '#3b82f6',
            gold: '#f0b90b'
          }
        }
      }
    };
  </script>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      background-color: #0b0e14;
      color: #eaecef;
      font-family: 'Inter', sans-serif;
      margin: 0;
      padding: 0;
      overflow-x: hidden;
    }

    /* Enforce tabular numbers across all prices, volumes, and balances */
    .tabular {
      font-variant-numeric: tabular-nums;
      font-family: 'JetBrains Mono', 'Inter', monospace;
    }

    /* Custom sleek scrollbars for order book & tables */
    ::-webkit-scrollbar {
      width: 5px;
      height: 5px;
    }
    ::-webkit-scrollbar-track {
      background: #0b0e14;
    }
    ::-webkit-scrollbar-thumb {
      background: #2b313e;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #474d57;
    }

    /* Custom Range Slider Styling */
    input[type="range"] {
      -webkit-appearance: none;
      appearance: none;
      background: transparent;
      cursor: pointer;
    }
    input[type="range"]::-webkit-slider-runnable-track {
      height: 4px;
      background: #2b313e;
      border-radius: 4px;
    }
    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 14px;
      height: 14px;
      border-radius: 50%;
      background: #eaecef;
      border: 2px solid #3b82f6;
      margin-top: -5px;
      transition: transform 0.12s ease;
    }
    input[type="range"]::-webkit-slider-thumb:hover {
      transform: scale(1.15);
    }

    /* Hide number input arrows */
    input[type=number]::-webkit-inner-spin-button,
    input[type=number]::-webkit-outer-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }
    input[type=number] {
      -moz-appearance: textfield;
    }

    /* Flash animations for live price updates */
    @keyframes flashGreen {
      0% { background-color: rgba(14, 203, 129, 0.25); }
      100% { background-color: transparent; }
    }
    @keyframes flashRed {
      0% { background-color: rgba(246, 70, 93, 0.25); }
      100% { background-color: transparent; }
    }
    .flash-up {
      animation: flashGreen 0.5s ease-out;
    }
    .flash-down {
      animation: flashRed 0.5s ease-out;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col bg-obsidian text-textPrimary select-none">

  <!-- =====================================================================
       1. TOP NAVIGATION BAR
  ====================================================================== -->
  <header class="h-14 bg-panel border-b border-borderDark px-4 flex items-center justify-between sticky top-0 z-40">
    <!-- Left: Brand & Nav Links -->
    <div class="flex items-center gap-6">
      <a href="#" class="flex items-center gap-2.5 group">
        <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-blue-500 to-emerald-400 flex items-center justify-center shadow-lg shadow-blue-500/20">
          <i class="fa-solid fa-bolt text-obsidian text-sm font-bold"></i>
        </div>
        <div class="flex items-baseline gap-1.5">
          <span class="font-bold text-lg tracking-tight text-white">NovaX</span>
          <span class="text-[10px] font-semibold uppercase tracking-wider px-1.5 py-0.5 rounded bg-blue-500/15 text-blue-400 border border-blue-500/30">PRO</span>
        </div>
      </a>

      <nav class="hidden lg:flex items-center gap-1 text-sm font-medium">
        <button onclick="showToast('Markets overview loaded', 'info')" class="px-3 py-1.5 rounded-lg text-textSecondary hover:text-white hover:bg-panelHover transition">Markets</button>
        <button class="px-3 py-1.5 rounded-lg text-white bg-panelHover border border-borderDark">Spot & Perps</button>
        <button onclick="showToast('Derivatives margin mode active', 'info')" class="px-3 py-1.5 rounded-lg text-textSecondary hover:text-white hover:bg-panelHover transition">Derivatives</button>
        <button onclick="showToast('NovaX Earn: Up to 8.4% APY on USDT', 'info')" class="px-3 py-1.5 rounded-lg text-textSecondary hover:text-white hover:bg-panelHover transition">Earn</button>
        <button onclick="switchBottomTab('assets')" class="px-3 py-1.5 rounded-lg text-textSecondary hover:text-white hover:bg-panelHover transition">Portfolio</button>
      </nav>
    </div>

    <!-- Right: Account Equity, Live Status & Deposit Modal Trigger -->
    <div class="flex items-center gap-3">
      <!-- Live Feed Status Pill -->
      <div class="hidden sm:flex items-center gap-2 px-2.5 py-1 rounded-full bg-obsidian border border-borderDark text-xs text-textSecondary">
        <span class="w-2 h-2 rounded-full bg-buy animate-pulse"></span>
        <span>WSS Connected</span>
        <span class="text-[11px] text-gray-500 tabular">14ms</span>
      </div>

      <!-- Total Equity Pill -->
      <div class="flex items-center gap-2.5 bg-obsidian border border-borderDark rounded-lg px-3 py-1.5">
        <div class="text-right">
          <div class="text-[10px] uppercase tracking-wider text-textSecondary leading-none">Est. Equity</div>
          <div id="header-total-equity" class="text-xs sm:text-sm font-semibold text-white tabular mt-0.5">$50,000.00</div>
        </div>
        <div class="h-6 w-[1px] bg-borderDark"></div>
        <button onclick="openDepositModal()" class="bg-blue-600 hover:bg-blue-500 text-white text-xs font-semibold px-3 py-1.5 rounded-md transition shadow-sm shadow-blue-600/30 flex items-center gap-1.5">
          <i class="fa-solid fa-wallet text-[11px]"></i>
          <span>Deposit</span>
        </button>
      </div>
    </div>
  </header>

  <!-- =====================================================================
       2. MARKET TICKER & ASSET SELECTOR BAR
  ====================================================================== -->
  <section class="bg-panel border-b border-borderDark px-4 py-2 flex flex-wrap items-center justify-between gap-4 relative z-30">
    <!-- Asset Pair Selector Dropdown -->
    <div class="relative" id="pair-selector-wrapper">
      <button id="pair-selector-btn" onclick="togglePairDropdown()" class="flex items-center gap-3 px-2.5 py-1.5 rounded-lg hover:bg-panelHover border border-transparent hover:border-borderDark transition">
        <div id="active-pair-icon" class="w-8 h-8 rounded-full bg-amber-500/20 text-amber-400 flex items-center justify-center font-bold text-sm border border-amber-500/30">
          <i class="fa-brands fa-bitcoin"></i>
        </div>
        <div class="text-left">
          <div class="flex items-center gap-1.5">
            <span id="active-pair-symbol" class="font-bold text-base text-white tracking-tight">BTC/USDT</span>
            <span id="active-pair-badge" class="text-[10px] font-semibold px-1.5 py-0.5 rounded bg-gold/15 text-gold">10x</span>
            <i class="fa-solid fa-chevron-down text-xs text-textSecondary ml-1"></i>
          </div>
          <div id="active-pair-name" class="text-xs text-textSecondary">Bitcoin Perpetual / Spot</div>
        </div>
      </button>

      <!-- Dropdown Menu -->
      <div id="pair-dropdown" class="hidden absolute left-0 mt-2 w-80 sm:w-96 bg-panel border border-borderDark rounded-xl shadow-2xl shadow-black/80 overflow-hidden z-50">
        <div class="p-3 border-b border-borderDark">
          <div class="relative">
            <i class="fa-solid fa-magnifying-glass absolute left-3 top-2.5 text-xs text-textSecondary"></i>
            <input type="text" id="pair-search-input" onkeyup="filterPairs()" placeholder="Search symbol (BTC, ETH, SOL, AAPL)..." class="w-full bg-obsidian border border-borderDark rounded-lg pl-8 pr-3 py-1.5 text-xs text-white focus:outline-none focus:border-blue-500" />
          </div>
        </div>
        <div class="grid grid-cols-3 px-3 py-2 text-[11px] font-medium text-textSecondary border-b border-borderDark bg-obsidian/50">
          <span>Pair</span>
          <span class="text-right">Last Price</span>
          <span class="text-right">24h Change</span>
        </div>
        <div id="pair-dropdown-list" class="max-h-64 overflow-y-auto divide-y divide-borderDark/50">
          <!-- Populated dynamically via JS -->
        </div>
      </div>
    </div>

    <!-- Live Market Metrics Strip -->
    <div class="flex items-center gap-5 sm:gap-7 overflow-x-auto py-1 flex-1 justify-start lg:justify-end">
      <!-- Last / Mark Price -->
      <div class="min-w-fit">
        <div id="ticker-price" class="text-base sm:text-lg font-bold text-buy tabular leading-tight">94,285.50</div>
        <div id="ticker-usd" class="text-[11px] text-textSecondary tabular">$94,285.50</div>
      </div>

      <!-- 24h Change -->
      <div class="min-w-fit">
        <div class="text-[11px] text-textSecondary">24h Change</div>
        <div id="ticker-change" class="text-xs font-semibold text-buy tabular mt-0.5">+2,840.20 (+3.11%)</div>
      </div>

      <!-- 24h High -->
      <div class="min-w-fit">
        <div class="text-[11px] text-textSecondary">24h High</div>
        <div id="ticker-high" class="text-xs font-medium text-textPrimary tabular mt-0.5">95,120.00</div>
      </div>

      <!-- 24h Low -->
      <div class="min-w-fit">
        <div class="text-[11px] text-textSecondary">24h Low</div>
        <div id="ticker-low" class="text-xs font-medium text-textPrimary tabular mt-0.5">91,105.80</div>
      </div>

      <!-- 24h Volume -->
      <div class="min-w-fit">
        <div class="text-[11px] text-textSecondary">24h Volume (USDT)</div>
        <div id="ticker-vol" class="text-xs font-medium text-textPrimary tabular mt-0.5">1,482,910,420.00</div>
      </div>

      <!-- Funding / Countdown -->
      <div class="hidden xl:block min-w-fit">
        <div class="text-[11px] text-textSecondary">Funding / Countdown</div>
        <div class="text-xs font-medium tabular mt-0.5 flex items-center gap-1.5">
          <span class="text-gold">+0.0100%</span>
          <span class="text-textSecondary">/</span>
          <span id="funding-countdown" class="text-textPrimary">03:42:18</span>
        </div>
      </div>
    </div>
  </section>

  <!-- =====================================================================
       3. MAIN TRADING WORKSPACE GRID
       Desktop: 12-Col Grid -> [Chart: 7 cols] [OrderBook/Trades: 2.5 cols] [OrderForm: 2.5 cols]
  ====================================================================== -->
  <main class="flex-1 grid grid-cols-1 lg:grid-cols-12 gap-[1px] bg-borderDark">

    <!-- ===================================================================
         LEFT / CENTER: INTERACTIVE CANDLESTICK CHART + BOTTOM PORTFOLIO
    ==================================================================== -->
    <div class="lg:col-span-7 xl:col-span-8 flex flex-col gap-[1px] bg-borderDark">

      <!-- CHART MODULE -->
      <div class="bg-panel flex flex-col h-[430px] sm:h-[490px] relative">
        <!-- Chart Toolbar -->
        <div class="px-3 py-2 border-b border-borderDark flex flex-wrap items-center justify-between gap-2 text-xs">
          <!-- Timeframe Selector -->
          <div class="flex items-center gap-1" id="timeframe-buttons">
            <span class="text-textSecondary mr-1 hidden sm:inline">Time</span>
            <button data-tf="1m" onclick="setTimeframe('1m')" class="tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition">1m</button>
            <button data-tf="5m" onclick="setTimeframe('5m')" class="tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition">5m</button>
            <button data-tf="15m" onclick="setTimeframe('15m')" class="tf-btn px-2 py-1 rounded bg-panelHover text-blue-400 font-semibold transition">15m</button>
            <button data-tf="1H" onclick="setTimeframe('1H')" class="tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition">1H</button>
            <button data-tf="4H" onclick="setTimeframe('4H')" class="tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition">4H</button>
            <button data-tf="1D" onclick="setTimeframe('1D')" class="tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition">1D</button>
          </div>

          <!-- Chart Type & Indicators -->
          <div class="flex items-center gap-2">
            <!-- Candle vs Line Toggle -->
            <div class="flex items-center bg-obsidian rounded-md p-0.5 border border-borderDark">
              <button id="btn-chart-candle" onclick="setChartType('candle')" class="px-2 py-1 rounded text-[11px] font-medium bg-panelHover text-white transition" title="Candlesticks">
                <i class="fa-solid fa-chart-column mr-1"></i>Candles
              </button>
              <button id="btn-chart-line" onclick="setChartType('line')" class="px-2 py-1 rounded text-[11px] font-medium text-textSecondary hover:text-white transition" title="Area Line">
                <i class="fa-solid fa-chart-line mr-1"></i>Line
              </button>
            </div>

            <!-- Indicator Toggles -->
            <button id="toggle-ma" onclick="toggleIndicator('ma')" class="px-2 py-1 rounded border border-blue-500/40 bg-blue-500/10 text-blue-400 text-[11px] font-medium transition">
              MA(7,25)
            </button>
            <button id="toggle-vol" onclick="toggleIndicator('vol')" class="px-2 py-1 rounded border border-emerald-500/40 bg-emerald-500/10 text-emerald-400 text-[11px] font-medium transition">
              VOL
            </button>
          </div>
        </div>

        <!-- Live OHLCV Legend Overlay inside Chart -->
        <div id="chart-ohlcv-bar" class="px-3 py-1.5 text-[11px] flex flex-wrap items-center gap-x-4 gap-y-1 bg-panel/90 border-b border-borderDark/50 z-10 pointer-events-none">
          <span class="font-semibold text-white" id="ohlc-symbol">BTC/USDT · 15m</span>
          <span class="text-textSecondary">O: <span id="ohlc-open" class="text-buy tabular">94,120.00</span></span>
          <span class="text-textSecondary">H: <span id="ohlc-high" class="text-buy tabular">94,350.00</span></span>
          <span class="text-textSecondary">L: <span id="ohlc-low" class="text-buy tabular">94,080.50</span></span>
          <span class="text-textSecondary">C: <span id="ohlc-close" class="text-buy tabular">94,285.50</span></span>
          <span class="text-textSecondary">Vol: <span id="ohlc-vol" class="text-textPrimary tabular">412.84</span></span>
          <span class="hidden sm:inline text-amber-400 tabular">MA(7): <span id="ohlc-ma7">94,190.2</span></span>
          <span class="hidden sm:inline text-purple-400 tabular">MA(25): <span id="ohlc-ma25">93,880.4</span></span>
        </div>

        <!-- HTML5 Canvas Container -->
        <div id="chart-container" class="flex-1 w-full relative overflow-hidden cursor-crosshair">
          <canvas id="trading-canvas" class="block w-full h-full"></canvas>
        </div>
      </div>

      <!-- =================================================================
           6. BOTTOM PORTFOLIO & ORDERS MANAGEMENT DRAWER
      ================================================================== -->
      <div class="bg-panel flex-1 flex flex-col min-h-[260px]">
        <!-- Drawer Tabs -->
        <div class="px-4 border-b border-borderDark flex items-center justify-between overflow-x-auto">
          <div class="flex items-center gap-6 text-xs font-semibold">
            <button onclick="switchBottomTab('positions')" id="tab-btn-positions" class="bottom-tab-btn py-3 border-b-2 border-blue-500 text-white whitespace-nowrap transition">
              Positions (<span id="count-positions">1</span>)
            </button>
            <button onclick="switchBottomTab('open-orders')" id="tab-btn-open-orders" class="bottom-tab-btn py-3 border-b-2 border-transparent text-textSecondary hover:text-white whitespace-nowrap transition">
              Open Orders (<span id="count-open-orders">2</span>)
            </button>
            <button onclick="switchBottomTab('history')" id="tab-btn-history" class="bottom-tab-btn py-3 border-b-2 border-transparent text-textSecondary hover:text-white whitespace-nowrap transition">
              Order History
            </button>
            <button onclick="switchBottomTab('assets')" id="tab-btn-assets" class="bottom-tab-btn py-3 border-b-2 border-transparent text-textSecondary hover:text-white whitespace-nowrap transition">
              Assets & Balances
            </button>
          </div>

          <button onclick="cancelAllOrders()" id="cancel-all-btn" class="hidden sm:inline-flex items-center gap-1 text-[11px] text-sell hover:underline py-1">
            <i class="fa-solid fa-xmark"></i> Cancel All
          </button>
        </div>

        <!-- Tab Contents -->
        <div class="flex-1 overflow-x-auto">
          <!-- TAB 1: POSITIONS -->
          <div id="bottom-panel-positions" class="bottom-panel block">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="text-[11px] text-textSecondary border-b border-borderDark bg-obsidian/40">
                  <th class="py-2.5 px-4 font-medium">Symbol</th>
                  <th class="py-2.5 px-3 font-medium">Side / Lev</th>
                  <th class="py-2.5 px-3 font-medium text-right">Size</th>
                  <th class="py-2.5 px-3 font-medium text-right">Entry Price</th>
                  <th class="py-2.5 px-3 font-medium text-right">Mark Price</th>
                  <th class="py-2.5 px-3 font-medium text-right">Unrealized PnL (ROE%)</th>
                  <th class="py-2.5 px-4 font-medium text-right">Action</th>
                </tr>
              </thead>
              <tbody id="positions-tbody" class="divide-y divide-borderDark/60 tabular">
                <!-- Populated dynamically -->
              </tbody>
            </table>
          </div>

          <!-- TAB 2: OPEN ORDERS -->
          <div id="bottom-panel-open-orders" class="bottom-panel hidden">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="text-[11px] text-textSecondary border-b border-borderDark bg-obsidian/40">
                  <th class="py-2.5 px-4 font-medium">Time</th>
                  <th class="py-2.5 px-3 font-medium">Pair</th>
                  <th class="py-2.5 px-3 font-medium">Type</th>
                  <th class="py-2.5 px-3 font-medium">Side</th>
                  <th class="py-2.5 px-3 font-medium text-right">Price</th>
                  <th class="py-2.5 px-3 font-medium text-right">Amount</th>
                  <th class="py-2.5 px-3 font-medium text-right">Total (USDT)</th>
                  <th class="py-2.5 px-4 font-medium text-right">Cancel</th>
                </tr>
              </thead>
              <tbody id="open-orders-tbody" class="divide-y divide-borderDark/60 tabular">
                <!-- Populated dynamically -->
              </tbody>
            </table>
          </div>

          <!-- TAB 3: ORDER HISTORY -->
          <div id="bottom-panel-history" class="bottom-panel hidden">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="text-[11px] text-textSecondary border-b border-borderDark bg-obsidian/40">
                  <th class="py-2.5 px-4 font-medium">Time</th>
                  <th class="py-2.5 px-3 font-medium">Pair</th>
                  <th class="py-2.5 px-3 font-medium">Type</th>
                  <th class="py-2.5 px-3 font-medium">Side</th>
                  <th class="py-2.5 px-3 font-medium text-right">Avg. Price</th>
                  <th class="py-2.5 px-3 font-medium text-right">Executed</th>
                  <th class="py-2.5 px-4 font-medium text-right">Status</th>
                </tr>
              </thead>
              <tbody id="history-tbody" class="divide-y divide-borderDark/60 tabular">
                <!-- Populated dynamically -->
              </tbody>
            </table>
          </div>

          <!-- TAB 4: ASSETS / BALANCES -->
          <div id="bottom-panel-assets" class="bottom-panel hidden">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="text-[11px] text-textSecondary border-b border-borderDark bg-obsidian/40">
                  <th class="py-2.5 px-4 font-medium">Asset</th>
                  <th class="py-2.5 px-3 font-medium text-right">Total Balance</th>
                  <th class="py-2.5 px-3 font-medium text-right">Available</th>
                  <th class="py-2.5 px-3 font-medium text-right">In Order</th>
                  <th class="py-2.5 px-4 font-medium text-right">USD Value</th>
                </tr>
              </thead>
              <tbody id="assets-tbody" class="divide-y divide-borderDark/60 tabular">
                <!-- Populated dynamically -->
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- ===================================================================
         4. MIDDLE COLUMN: LIVE ORDER BOOK & RECENT TRADES
    ==================================================================== -->
    <div class="lg:col-span-2 xl:col-span-2 bg-panel flex flex-col h-[560px] lg:h-auto">
      <!-- Top Tabs: Order Book vs Recent Trades (or split view on tall screens) -->
      <div class="px-3 py-2 border-b border-borderDark flex items-center justify-between">
        <div class="flex items-center gap-3 text-xs font-semibold">
          <button id="ob-tab-book" onclick="switchRightMidTab('book')" class="text-white border-b-2 border-blue-500 pb-1 transition">Order Book</button>
          <button id="ob-tab-trades" onclick="switchRightMidTab('trades')" class="text-textSecondary hover:text-white pb-1 transition">Trades</button>
        </div>

        <!-- Book Layout Filter Icons (Both / Bids / Asks) -->
        <div id="ob-layout-controls" class="flex items-center gap-1">
          <button onclick="setBookMode('both')" id="ob-mode-both" class="p-1 rounded bg-panelHover text-white" title="Both Bids & Asks">
            <svg class="w-3.5 h-3.5" viewBox="0 0 16 16" fill="none">
              <rect x="2" y="2" width="5" height="5" fill="#f6465d"/>
              <rect x="2" y="9" width="5" height="5" fill="#0ecb81"/>
              <rect x="9" y="2" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="6" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="10" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="14" width="5" height="2" fill="#848e9c"/>
            </svg>
          </button>
          <button onclick="setBookMode('bids')" id="ob-mode-bids" class="p-1 rounded text-textSecondary hover:text-white" title="Bids Only">
            <svg class="w-3.5 h-3.5" viewBox="0 0 16 16" fill="none">
              <rect x="2" y="2" width="5" height="12" fill="#0ecb81"/>
              <rect x="9" y="2" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="6" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="10" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="14" width="5" height="2" fill="#848e9c"/>
            </svg>
          </button>
          <button onclick="setBookMode('asks')" id="ob-mode-asks" class="p-1 rounded text-textSecondary hover:text-white" title="Asks Only">
            <svg class="w-3.5 h-3.5" viewBox="0 0 16 16" fill="none">
              <rect x="2" y="2" width="5" height="12" fill="#f6465d"/>
              <rect x="9" y="2" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="6" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="10" width="5" height="2" fill="#848e9c"/>
              <rect x="9" y="14" width="5" height="2" fill="#848e9c"/>
            </svg>
          </button>
        </div>
      </div>

      <!-- ORDER BOOK VIEW -->
      <div id="view-order-book" class="flex-1 flex flex-col overflow-hidden">
        <!-- Column Headers -->
        <div class="grid grid-cols-3 px-3 py-1.5 text-[10px] font-medium text-textSecondary border-b border-borderDark/50">
          <span>Price (USDT)</span>
          <span class="text-right" id="ob-size-header">Size (BTC)</span>
          <span class="text-right">Total</span>
        </div>

        <!-- Asks (Sell Orders - Red) -->
        <div id="orderbook-asks" class="flex-1 flex flex-col justify-end overflow-hidden py-1">
          <!-- Populated dynamically -->
        </div>

        <!-- Spread / Mid Price Bar -->
        <div class="px-3 py-2 bg-obsidian/80 border-y border-borderDark flex items-center justify-between">
          <div class="flex items-center gap-1.5">
            <span id="ob-mid-price" class="text-sm font-bold text-buy tabular">94,285.50</span>
            <i id="ob-mid-arrow" class="fa-solid fa-arrow-up text-[10px] text-buy"></i>
          </div>
          <span id="ob-spread-text" class="text-[10px] text-textSecondary tabular">Spread: 0.50 (0.01%)</span>
        </div>

        <!-- Bids (Buy Orders - Green) -->
        <div id="orderbook-bids" class="flex-1 flex flex-col justify-start overflow-hidden py-1">
          <!-- Populated dynamically -->
        </div>
      </div>

      <!-- RECENT TRADES VIEW -->
      <div id="view-recent-trades" class="hidden flex-1 flex flex-col overflow-hidden">
        <div class="grid grid-cols-3 px-3 py-1.5 text-[10px] font-medium text-textSecondary border-b border-borderDark/50">
          <span>Price (USDT)</span>
          <span class="text-right">Amount</span>
          <span class="text-right">Time</span>
        </div>
        <div id="recent-trades-list" class="flex-1 overflow-y-auto divide-y divide-borderDark/30 text-[11px] tabular">
          <!-- Populated dynamically -->
        </div>
      </div>

      <!-- Bottom Mini Recent Trades Feed (Always visible on Desktop inside Order Book column) -->
      <div id="mini-trades-section" class="hidden lg:flex flex-col h-48 border-t border-borderDark">
        <div class="px-3 py-1.5 bg-obsidian/50 border-b border-borderDark flex items-center justify-between text-[11px] font-semibold text-textSecondary">
          <span>Market Trades</span>
          <span class="text-[10px] font-normal text-buy">Live</span>
        </div>
        <div id="mini-trades-list" class="flex-1 overflow-y-auto px-3 py-1 space-y-1 text-[11px] tabular">
          <!-- Populated dynamically -->
        </div>
      </div>
    </div>

    <!-- ===================================================================
         5. RIGHT COLUMN: ORDER EXECUTION TERMINAL
    ==================================================================== -->
    <div class="lg:col-span-3 xl:col-span-2 bg-panel p-3.5 flex flex-col justify-between">
      <div>
        <!-- Margin / Leverage Header -->
        <div class="flex items-center justify-between gap-2 mb-3">
          <div class="flex items-center gap-1.5 bg-obsidian border border-borderDark rounded-md p-0.5 text-[11px] font-semibold w-full">
            <button id="margin-cross-btn" onclick="setMarginMode('Cross')" class="flex-1 py-1 rounded bg-panelHover text-white transition">Cross</button>
            <button id="margin-iso-btn" onclick="setMarginMode('Isolated')" class="flex-1 py-1 rounded text-textSecondary hover:text-white transition">Isolated</button>
          </div>
          <button onclick="cycleLeverage()" id="leverage-badge-btn" class="px-2.5 py-1.5 rounded-md bg-obsidian border border-borderDark hover:border-blue-500 text-xs font-bold text-gold tabular transition whitespace-nowrap" title="Click to adjust leverage">
            10x
          </button>
        </div>

        <!-- Buy / Long vs Sell / Short Tabs -->
        <div class="grid grid-cols-2 gap-1.5 p-1 bg-obsidian rounded-lg border border-borderDark mb-3">
          <button id="side-buy-btn" onclick="setOrderSide('buy')" class="py-2 rounded-md text-xs font-bold bg-buy text-obsidian shadow-md shadow-buy/20 transition">
            Buy / Long
          </button>
          <button id="side-sell-btn" onclick="setOrderSide('sell')" class="py-2 rounded-md text-xs font-bold text-textSecondary hover:text-white transition">
            Sell / Short
          </button>
        </div>

        <!-- Order Type Tabs (Limit, Market, Stop-Limit) -->
        <div class="flex items-center gap-3 border-b border-borderDark pb-2 mb-3 text-xs font-semibold">
          <button onclick="setOrderType('Limit')" id="otype-Limit" class="otype-btn text-white border-b-2 border-blue-500 pb-1.5 -mb-2 transition">Limit</button>
          <button onclick="setOrderType('Market')" id="otype-Market" class="otype-btn text-textSecondary hover:text-white pb-1.5 -mb-2 transition">Market</button>
          <button onclick="setOrderType('Stop-Limit')" id="otype-Stop-Limit" class="otype-btn text-textSecondary hover:text-white pb-1.5 -mb-2 transition">Stop-Limit</button>
        </div>

        <!-- Available Balance Row -->
        <div class="flex items-center justify-between text-[11px] mb-3">
          <span class="text-textSecondary">Available</span>
          <span id="order-avail-balance" class="text-white font-semibold tabular">50,000.00 USDT</span>
        </div>

        <!-- Order Form Inputs -->
        <div class="space-y-2.5">
          <!-- Stop Price Input (Visible only for Stop-Limit) -->
          <div id="stop-price-group" class="hidden">
            <label class="block text-[11px] text-textSecondary mb-1">Stop Trigger Price</label>
            <div class="flex items-center bg-obsidian border border-borderDark focus-within:border-blue-500 rounded-lg px-3 py-2 transition">
              <input type="number" step="any" id="input-stop-price" placeholder="0.00" class="w-full bg-transparent text-xs text-white font-semibold tabular focus:outline-none" />
              <span class="text-[11px] text-textSecondary ml-2">USDT</span>
            </div>
          </div>

          <!-- Order Price Input -->
          <div id="limit-price-group">
            <div class="flex items-center justify-between text-[11px] text-textSecondary mb-1">
              <span>Order Price</span>
              <button onclick="fillLastPrice()" class="text-blue-400 hover:underline">Last</button>
            </div>
            <div class="flex items-center bg-obsidian border border-borderDark focus-within:border-blue-500 rounded-lg px-3 py-2 transition">
              <input type="number" step="any" id="input-order-price" oninput="handlePriceOrAmountChange('price')" class="w-full bg-transparent text-xs text-white font-semibold tabular focus:outline-none" />
              <span class="text-[11px] text-textSecondary ml-2">USDT</span>
            </div>
          </div>

          <!-- Market Price Banner (Visible only for Market orders) -->
          <div id="market-price-banner" class="hidden bg-obsidian/70 border border-borderDark rounded-lg px-3 py-2.5 text-xs text-textSecondary flex items-center justify-between">
            <span>Execution Price</span>
            <span class="text-white font-semibold">Best Market Price</span>
          </div>

          <!-- Amount / Size Input -->
          <div>
            <label class="block text-[11px] text-textSecondary mb-1">Amount (<span id="form-base-asset">BTC</span>)</label>
            <div class="flex items-center bg-obsidian border border-borderDark focus-within:border-blue-500 rounded-lg px-3 py-2 transition">
              <input type="number" step="any" id="input-order-amount" placeholder="0.0000" oninput="handlePriceOrAmountChange('amount')" class="w-full bg-transparent text-xs text-white font-semibold tabular focus:outline-none" />
              <span id="form-base-unit" class="text-[11px] text-textSecondary ml-2">BTC</span>
            </div>
          </div>

          <!-- Percentage Slider & Quick Buttons -->
          <div class="pt-1">
            <input type="range" id="order-percent-slider" min="0" max="100" value="0" oninput="applyOrderPercentage(this.value)" class="w-full" />
            <div class="grid grid-cols-4 gap-1.5 mt-2">
              <button type="button" onclick="applyOrderPercentage(25)" class="py-1 rounded bg-obsidian hover:bg-panelHover border border-borderDark text-[10px] font-semibold text-textSecondary hover:text-white transition">25%</button>
              <button type="button" onclick="applyOrderPercentage(50)" class="py-1 rounded bg-obsidian hover:bg-panelHover border border-borderDark text-[10px] font-semibold text-textSecondary hover:text-white transition">50%</button>
              <button type="button" onclick="applyOrderPercentage(75)" class="py-1 rounded bg-obsidian hover:bg-panelHover border border-borderDark text-[10px] font-semibold text-textSecondary hover:text-white transition">75%</button>
              <button type="button" onclick="applyOrderPercentage(100)" class="py-1 rounded bg-obsidian hover:bg-panelHover border border-borderDark text-[10px] font-semibold text-textSecondary hover:text-white transition">100%</button>
            </div>
          </div>

          <!-- Total Order Value Input -->
          <div>
            <label class="block text-[11px] text-textSecondary mb-1">Total Order Value</label>
            <div class="flex items-center bg-obsidian border border-borderDark focus-within:border-blue-500 rounded-lg px-3 py-2 transition">
              <input type="number" step="any" id="input-order-total" placeholder="0.00" oninput="handlePriceOrAmountChange('total')" class="w-full bg-transparent text-xs text-white font-semibold tabular focus:outline-none" />
              <span class="text-[11px] text-textSecondary ml-2">USDT</span>
            </div>
          </div>

          <!-- Leverage Slider -->
          <div class="bg-obsidian/60 border border-borderDark rounded-lg p-2.5">
            <div class="flex items-center justify-between text-[11px] mb-1">
              <span class="text-textSecondary">Leverage</span>
              <span id="leverage-slider-val" class="font-bold text-gold tabular">10x</span>
            </div>
            <input type="range" id="leverage-slider" min="1" max="20" value="10" oninput="setLeverage(this.value)" class="w-full" />
            <div class="flex justify-between text-[9px] text-textSecondary mt-1 tabular">
              <span>1x</span>
              <span>5x</span>
              <span>10x</span>
              <span>15x</span>
              <span>20x</span>
            </div>
          </div>

          <!-- Advanced Order Flags -->
          <div class="flex items-center justify-between text-[11px] text-textSecondary pt-1">
            <label class="flex items-center gap-1.5 cursor-pointer hover:text-white">
              <input type="checkbox" id="chk-post-only" class="rounded bg-obsidian border-borderDark text-blue-500 focus:ring-0" />
              <span>Post-Only</span>
            </label>
            <label class="flex items-center gap-1.5 cursor-pointer hover:text-white">
              <input type="checkbox" id="chk-reduce-only" class="rounded bg-obsidian border-borderDark text-blue-500 focus:ring-0" />
              <span>Reduce-Only</span>
            </label>
          </div>
        </div>
      </div>

      <!-- Submit Button & Margin Summary -->
      <div class="mt-4 pt-3 border-t border-borderDark space-y-2.5">
        <div class="space-y-1 text-[11px]">
          <div class="flex justify-between">
            <span class="text-textSecondary">Required Margin</span>
            <span id="summary-req-margin" class="text-white font-medium tabular">0.00 USDT</span>
          </div>
          <div class="flex justify-between">
            <span class="text-textSecondary">Est. Fee (0.04%)</span>
            <span id="summary-est-fee" class="text-textSecondary tabular">0.00 USDT</span>
          </div>
        </div>

        <button id="submit-order-btn" onclick="executeOrder()" class="w-full py-3 rounded-lg font-bold text-xs sm:text-sm bg-buy hover:bg-buyHover text-obsidian shadow-lg shadow-buy/20 transition transform active:scale-[0.99]">
          Buy / Long BTC
        </button>
      </div>
    </div>
  </main>

  <!-- =====================================================================
       DEPOSIT MODAL (Simulated Institutional Funding)
  ====================================================================== -->
  <div id="deposit-modal" class="hidden fixed inset-0 bg-black/75 backdrop-blur-sm z-50 flex items-center justify-center p-4">
    <div class="bg-panel border border-borderDark rounded-2xl w-full max-w-md p-5 shadow-2xl">
      <div class="flex items-center justify-between pb-3 border-b border-borderDark">
        <h3 class="font-bold text-base text-white flex items-center gap-2">
          <i class="fa-solid fa-building-columns text-blue-400"></i>
          Instant Treasury Deposit
        </h3>
        <button onclick="closeDepositModal()" class="text-textSecondary hover:text-white p-1">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>
      <div class="py-4 space-y-4">
        <p class="text-xs text-textSecondary leading-relaxed">
          Add simulated USDT liquidity to your NovaX Pro margin account to test high-frequency limit and market executions.
        </p>
        <div>
          <label class="block text-xs text-textSecondary mb-1.5">Deposit Amount (USDT)</label>
          <div class="grid grid-cols-3 gap-2 mb-2">
            <button onclick="document.getElementById('deposit-amount-input').value = 5000" class="py-1.5 rounded bg-obsidian border border-borderDark hover:border-blue-500 text-xs font-semibold tabular">+$5,000</button>
            <button onclick="document.getElementById('deposit-amount-input').value = 25000" class="py-1.5 rounded bg-obsidian border border-borderDark hover:border-blue-500 text-xs font-semibold tabular">+$25,000</button>
            <button onclick="document.getElementById('deposit-amount-input').value = 100000" class="py-1.5 rounded bg-obsidian border border-borderDark hover:border-blue-500 text-xs font-semibold tabular">+$100,000</button>
          </div>
          <input type="number" id="deposit-amount-input" value="10000" class="w-full bg-obsidian border border-borderDark rounded-lg px-3 py-2 text-sm font-bold text-white tabular focus:outline-none focus:border-blue-500" />
        </div>
      </div>
      <div class="flex justify-end gap-2 pt-2">
        <button onclick="closeDepositModal()" class="px-4 py-2 rounded-lg text-xs font-semibold text-textSecondary hover:text-white bg-obsidian border border-borderDark">Cancel</button>
        <button onclick="confirmDeposit()" class="px-4 py-2 rounded-lg text-xs font-semibold text-white bg-blue-600 hover:bg-blue-500 shadow-lg shadow-blue-600/20">Confirm Deposit</button>
      </div>
    </div>
  </div>

  <!-- =====================================================================
       TOAST NOTIFICATION CONTAINER
  ====================================================================== -->
  <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 max-w-sm w-full pointer-events-none"></div>

  <!-- =====================================================================
       JAVASCRIPT APPLICATION ENGINE
  ====================================================================== -->
  <script>
    /**
     * =========================================================================
     * 1. MARKET DATA & STATE CONFIGURATION
     * =========================================================================
     */
    const MARKETS = {
      'BTC/USDT': {
        symbol: 'BTC/USDT',
        base: 'BTC',
        quote: 'USDT',
        name: 'Bitcoin Perpetual / Spot',
        price: 94285.50,
        open24h: 91445.30,
        high24h: 95120.00,
        low24h: 91105.80,
        vol24h: 1482910420,
        decimals: 2,
        sizeDecimals: 4,
        tickSize: 0.50,
        volatility: 0.0018,
        iconHtml: '<i class="fa-brands fa-bitcoin"></i>',
        iconBg: 'bg-amber-500/20 text-amber-400 border-amber-500/30',
        maxLev: '20x'
      },
      'ETH/USDT': {
        symbol: 'ETH/USDT',
        base: 'ETH',
        quote: 'USDT',
        name: 'Ethereum Perpetual / Spot',
        price: 3412.80,
        open24h: 3310.15,
        high24h: 3488.00,
        low24h: 3290.40,
        vol24h: 842190310,
        decimals: 2,
        sizeDecimals: 3,
        tickSize: 0.10,
        volatility: 0.0022,
        iconHtml: '<i class="fa-brands fa-ethereum"></i>',
        iconBg: 'bg-indigo-500/20 text-indigo-400 border-indigo-500/30',
        maxLev: '20x'
      },
      'SOL/USDT': {
        symbol: 'SOL/USDT',
        base: 'SOL',
        quote: 'USDT',
        name: 'Solana Perpetual / Spot',
        price: 218.45,
        open24h: 223.90,
        high24h: 226.80,
        low24h: 214.10,
        vol24h: 519402100,
        decimals: 2,
        sizeDecimals: 2,
        tickSize: 0.05,
        volatility: 0.0030,
        iconHtml: '<i class="fa-solid fa-layer-group"></i>',
        iconBg: 'bg-emerald-500/20 text-emerald-400 border-emerald-500/30',
        maxLev: '20x'
      },
      'AAPL/USD': {
        symbol: 'AAPL/USD',
        base: 'AAPL',
        quote: 'USDT',
        name: 'Apple Inc. Tokenized Equity',
        price: 234.62,
        open24h: 231.10,
        high24h: 235.90,
        low24h: 230.50,
        vol24h: 194820500,
        decimals: 2,
        sizeDecimals: 2,
        tickSize: 0.01,
        volatility: 0.0009,
        iconHtml: '<i class="fa-brands fa-apple"></i>',
        iconBg: 'bg-gray-500/20 text-gray-200 border-gray-500/30',
        maxLev: '10x'
      }
    };

    // Global Application State
    const state = {
      activeSymbol: 'BTC/USDT',
      timeframe: '15m',
      chartType: 'candle', // 'candle' | 'line'
      showMA: true,
      showVol: true,
      bookMode: 'both',    // 'both' | 'bids' | 'asks'
      orderSide: 'buy',    // 'buy' | 'sell'
      orderType: 'Limit',  // 'Limit' | 'Market' | 'Stop-Limit'
      marginMode: 'Cross',
      leverage: 10,
      candlesBySymbol: {},
      recentTrades: [],
      mousePos: null,      // { x, y } on canvas
      // Portfolio & Account State
      balances: {
        USDT: { total: 50000.00, locked: 4700.00 },
        BTC:  { total: 0.4500,   locked: 0.0000 },
        ETH:  { total: 4.200,    locked: 0.000 },
        SOL:  { total: 35.00,    locked: 0.00 },
        AAPL: { total: 15.00,    locked: 0.00 }
      },
      positions: [
        {
          id: 'POS-101',
          symbol: 'BTC/USDT',
          side: 'Long',
          leverage: 10,
          size: 0.2500,
          entryPrice: 92840.00
        }
      ],
      openOrders: [
        {
          id: 'ORD-8821',
          time: '10:42:15',
          symbol: 'BTC/USDT',
          type: 'Limit',
          side: 'Buy',
          price: 93200.00,
          amount: 0.0500,
          total: 4660.00
        },
        {
          id: 'ORD-8822',
          time: '10:51:04',
          symbol: 'ETH/USDT',
          type: 'Limit',
          side: 'Sell',
          price: 3520.00,
          amount: 1.000,
          total: 3520.00
        }
      ],
      orderHistory: [
        {
          id: 'ORD-8810',
          time: '09:15:32',
          symbol: 'BTC/USDT',
          type: 'Market',
          side: 'Buy',
          avgPrice: 92840.00,
          executed: '0.2500 BTC',
          status: 'Filled'
        },
        {
          id: 'ORD-8804',
          time: '08:03:11',
          symbol: 'SOL/USDT',
          type: 'Limit',
          side: 'Sell',
          avgPrice: 221.50,
          executed: '12.00 SOL',
          status: 'Filled'
        }
      ]
    };

    /**
     * =========================================================================
     * 2. SYNTHETIC HISTORICAL CANDLES & INDICATOR CALCULATIONS
     * =========================================================================
     */
    function generateInitialCandles(symbol, count = 95) {
      const m = MARKETS[symbol];
      const candles = [];
      let currentPrice = m.price * (1 - (Math.random() - 0.45) * 0.03);
      const now = Date.now();
      const tfMs = getTimeframeMs(state.timeframe);

      for (let i = count; i > 0; i--) {
        const time = now - i * tfMs;
        const open = currentPrice;
        const changePct = (Math.random() - 0.485) * m.volatility * 2.5;
        const close = i === 1 ? m.price : open * (1 + changePct);
        const high = Math.max(open, close) * (1 + Math.random() * m.volatility);
        const low = Math.min(open, close) * (1 - Math.random() * m.volatility);
        const volume = (Math.random() * 450 + 80) * (10000 / m.price);

        candles.push({
          time,
          open: Number(open.toFixed(m.decimals)),
          high: Number(high.toFixed(m.decimals)),
          low: Number(low.toFixed(m.decimals)),
          close: Number(close.toFixed(m.decimals)),
          volume: Number(volume.toFixed(2))
        });

        currentPrice = close;
      }
      return candles;
    }

    function getTimeframeMs(tf) {
      const map = {
        '1m': 60 * 1000,
        '5m': 5 * 60 * 1000,
        '15m': 15 * 60 * 1000,
        '1H': 60 * 60 * 1000,
        '4H': 4 * 60 * 60 * 1000,
        '1D': 24 * 60 * 60 * 1000
      };
      return map[tf] || 15 * 60 * 1000;
    }

    function calculateSMA(candles, period) {
      const sma = [];
      for (let i = 0; i < candles.length; i++) {
        if (i < period - 1) {
          sma.push(null);
          continue;
        }
        let sum = 0;
        for (let j = 0; j < period; j++) {
          sum += candles[i - j].close;
        }
        sma.push(sum / period);
      }
      return sma;
    }

    /**
     * =========================================================================
     * 3. HTML5 CANVAS CANDLESTICK & VOLUME RENDERER
     * =========================================================================
     */
    const canvas = document.getElementById('trading-canvas');
    const ctx = canvas.getContext('2d');

    function resizeCanvas() {
      const container = document.getElementById('chart-container');
      if (!container) return;
      const dpr = window.devicePixelRatio || 1;
      const rect = container.getBoundingClientRect();
      canvas.width = rect.width * dpr;
      canvas.height = rect.height * dpr;
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      drawChart();
    }

    function drawChart() {
      const container = document.getElementById('chart-container');
      if (!container) return;
      const width = container.clientWidth;
      const height = container.clientHeight;
      if (width === 0 || height === 0) return;

      const market = MARKETS[state.activeSymbol];
      const candles = state.candlesBySymbol[state.activeSymbol] || [];
      if (candles.length === 0) return;

      // Clear background
      ctx.fillStyle = '#131722';
      ctx.fillRect(0, 0, width, height);

      // Chart Layout Margins
      const rightAxisWidth = 72;
      const bottomAxisHeight = 24;
      const chartWidth = width - rightAxisWidth;
      const chartHeight = height - bottomAxisHeight;
      const volHeight = state.showVol ? chartHeight * 0.22 : 0;
      const priceAreaHeight = chartHeight - volHeight - 12;

      // Determine visible candles based on available width
      const candleStep = Math.max(6, Math.min(14, chartWidth / 75));
      const maxVisible = Math.floor(chartWidth / candleStep);
      const visibleCandles = candles.slice(-maxVisible);

      // Price Min / Max
      let minPrice = Infinity;
      let maxPrice = -Infinity;
      let maxVol = 0;

      visibleCandles.forEach(c => {
        if (c.low < minPrice) minPrice = c.low;
        if (c.high > maxPrice) maxPrice = c.high;
        if (c.volume > maxVol) maxVol = c.volume;
      });

      const pricePadding = (maxPrice - minPrice) * 0.08 || market.tickSize * 10;
      minPrice -= pricePadding;
      maxPrice += pricePadding;
      const priceRange = maxPrice - minPrice;

      const priceToY = (p) => 10 + ((maxPrice - p) / priceRange) * priceAreaHeight;
      const yToPrice = (y) => maxPrice - ((y - 10) / priceAreaHeight) * priceRange;

      // 1. Draw Horizontal & Vertical Grid Lines
      ctx.strokeStyle = '#1e222d';
      ctx.lineWidth = 1;
      const gridRows = 6;
      ctx.font = '10px "JetBrains Mono", monospace';
      ctx.fillStyle = '#848e9c';
      ctx.textAlign = 'left';

      for (let i = 0; i <= gridRows; i++) {
        const y = 10 + (priceAreaHeight / gridRows) * i;
        ctx.beginPath();
        ctx.moveTo(0, y);
        ctx.lineTo(chartWidth, y);
        ctx.stroke();

        const labelPrice = yToPrice(y);
        ctx.fillText(formatPrice(labelPrice, market.decimals), chartWidth + 8, y + 3);
      }

      // Vertical time gridlines
      const timeStep = Math.max(1, Math.floor(visibleCandles.length / 6));
      for (let i = 0; i < visibleCandles.length; i += timeStep) {
        const x = i * candleStep + candleStep / 2;
        ctx.beginPath();
        ctx.moveTo(x, 0);
        ctx.lineTo(x, chartHeight);
        ctx.stroke();

        const d = new Date(visibleCandles[i].time);
        const timeStr = `${String(d.getHours()).padStart(2, '0')}:${String(d.getMinutes()).padStart(2, '0')}`;
        ctx.textAlign = 'center';
        ctx.fillText(timeStr, x, height - 8);
      }

      // 2. Draw Volume Bars (if enabled)
      if (state.showVol && maxVol > 0) {
        visibleCandles.forEach((c, idx) => {
          const x = idx * candleStep;
          const barW = Math.max(2, candleStep * 0.65);
          const barH = (c.volume / maxVol) * volHeight;
          const isUp = c.close >= c.open;
          ctx.fillStyle = isUp ? 'rgba(14, 203, 129, 0.28)' : 'rgba(246, 70, 93, 0.28)';
          ctx.fillRect(x + (candleStep - barW) / 2, chartHeight - barH, barW, barH);
        });
      }

      // 3. Draw Candlesticks OR Area Line
      if (state.chartType === 'candle') {
        visibleCandles.forEach((c, idx) => {
          const xCenter = idx * candleStep + candleStep / 2;
          const isUp = c.close >= c.open;
          const color = isUp ? '#0ecb81' : '#f6465d';

          const yOpen = priceToY(c.open);
          const yClose = priceToY(c.close);
          const yHigh = priceToY(c.high);
          const yLow = priceToY(c.low);

          // Wick
          ctx.strokeStyle = color;
          ctx.lineWidth = 1.2;
          ctx.beginPath();
          ctx.moveTo(xCenter, yHigh);
          ctx.lineTo(xCenter, yLow);
          ctx.stroke();

          // Body
          const bodyW = Math.max(2, candleStep * 0.66);
          const bodyTop = Math.min(yOpen, yClose);
          const bodyH = Math.max(1.5, Math.abs(yClose - yOpen));
          ctx.fillStyle = color;
          ctx.fillRect(xCenter - bodyW / 2, bodyTop, bodyW, bodyH);
        });
      } else {
        // Area Line Chart
        ctx.beginPath();
        visibleCandles.forEach((c, idx) => {
          const x = idx * candleStep + candleStep / 2;
          const y = priceToY(c.close);
          if (idx === 0) ctx.moveTo(x, y);
          else ctx.lineTo(x, y);
        });
        ctx.strokeStyle = '#3b82f6';
        ctx.lineWidth = 2;
        ctx.stroke();

        // Gradient Fill under line
        const lastX = (visibleCandles.length - 1) * candleStep + candleStep / 2;
        ctx.lineTo(lastX, priceAreaHeight + 10);
        ctx.lineTo(candleStep / 2, priceAreaHeight + 10);
        ctx.closePath();
        const grad = ctx.createLinearGradient(0, 10, 0, priceAreaHeight + 10);
        grad.addColorStop(0, 'rgba(59, 130, 246, 0.28)');
        grad.addColorStop(1, 'rgba(59, 130, 246, 0.0)');
        ctx.fillStyle = grad;
        ctx.fill();
      }

      // 4. Draw Moving Averages (MA7 & MA25)
      const fullMA7 = calculateSMA(candles, 7);
      const fullMA25 = calculateSMA(candles, 25);
      const visMA7 = fullMA7.slice(-visibleCandles.length);
      const visMA25 = fullMA25.slice(-visibleCandles.length);

      if (state.showMA) {
        drawMALine(visMA7, '#f59e0b', candleStep, priceToY);
        drawMALine(visMA25, '#a855f7', candleStep, priceToY);
      }

      // 5. Draw Current Live Price Line & Tag on Right Axis
      const lastCandle = visibleCandles[visibleCandles.length - 1];
      const lastY = priceToY(lastCandle.close);
      const isLastUp = lastCandle.close >= lastCandle.open;
      const liveColor = isLastUp ? '#0ecb81' : '#f6465d';

      ctx.setLineDash([4, 4]);
      ctx.strokeStyle = liveColor;
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(0, lastY);
      ctx.lineTo(chartWidth, lastY);
      ctx.stroke();
      ctx.setLineDash([]);

      // Price Badge on Right Axis
      ctx.fillStyle = liveColor;
      ctx.fillRect(chartWidth + 2, lastY - 9, rightAxisWidth - 4, 18);
      ctx.fillStyle = '#0b0e14';
      ctx.font = 'bold 10px "JetBrains Mono", monospace';
      ctx.textAlign = 'left';
      ctx.fillText(formatPrice(lastCandle.close, market.decimals), chartWidth + 6, lastY + 3.5);

      // Right Axis Divider Line
      ctx.strokeStyle = '#1e222d';
      ctx.beginPath();
      ctx.moveTo(chartWidth, 0);
      ctx.lineTo(chartWidth, height);
      ctx.stroke();

      // 6. Interactive Crosshair & OHLCV Hover Update
      let activeCandle = lastCandle;
      let activeMA7 = visMA7[visMA7.length - 1];
      let activeMA25 = visMA25[visMA25.length - 1];

      if (state.mousePos && state.mousePos.x < chartWidth && state.mousePos.y < chartHeight) {
        const { x, y } = state.mousePos;
        const hoveredIdx = Math.min(
          visibleCandles.length - 1,
          Math.max(0, Math.floor(x / candleStep))
        );
        activeCandle = visibleCandles[hoveredIdx];
        activeMA7 = visMA7[hoveredIdx];
        activeMA25 = visMA25[hoveredIdx];

        const snapX = hoveredIdx * candleStep + candleStep / 2;

        // Crosshair lines
        ctx.setLineDash([3, 3]);
        ctx.strokeStyle = '#848e9c';
        ctx.lineWidth = 1;

        ctx.beginPath();
        ctx.moveTo(snapX, 0);
        ctx.lineTo(snapX, chartHeight);
        ctx.moveTo(0, y);
        ctx.lineTo(chartWidth, y);
        ctx.stroke();
        ctx.setLineDash([]);

        // Crosshair Y Price Pill
        const hoverPrice = yToPrice(y);
        ctx.fillStyle = '#2b313e';
        ctx.fillRect(chartWidth + 2, y - 9, rightAxisWidth - 4, 18);
        ctx.fillStyle = '#eaecef';
        ctx.font = '10px "JetBrains Mono", monospace';
        ctx.fillText(formatPrice(hoverPrice, market.decimals), chartWidth + 6, y + 3.5);
      }

      updateOHLCBar(activeCandle, activeMA7, activeMA25);
    }

    function drawMALine(maArray, color, candleStep, priceToY) {
      ctx.beginPath();
      let started = false;
      maArray.forEach((val, idx) => {
        if (val === null) return;
        const x = idx * candleStep + candleStep / 2;
        const y = priceToY(val);
        if (!started) {
          ctx.moveTo(x, y);
          started = true;
        } else {
          ctx.lineTo(x, y);
        }
      });
      ctx.strokeStyle = color;
      ctx.lineWidth = 1.4;
      ctx.stroke();
    }

    function updateOHLCBar(candle, ma7, ma25) {
      const m = MARKETS[state.activeSymbol];
      const isUp = candle.close >= candle.open;
      const cls = isUp ? 'text-buy tabular' : 'text-sell tabular';

      document.getElementById('ohlc-symbol').textContent = `${m.symbol} · ${state.timeframe}`;
      const elOpen = document.getElementById('ohlc-open');
      const elHigh = document.getElementById('ohlc-high');
      const elLow = document.getElementById('ohlc-low');
      const elClose = document.getElementById('ohlc-close');

      elOpen.textContent = formatPrice(candle.open, m.decimals);
      elHigh.textContent = formatPrice(candle.high, m.decimals);
      elLow.textContent = formatPrice(candle.low, m.decimals);
      elClose.textContent = formatPrice(candle.close, m.decimals);

      elOpen.className = cls;
      elHigh.className = cls;
      elLow.className = cls;
      elClose.className = cls;

      document.getElementById('ohlc-vol').textContent = candle.volume.toFixed(2);
      document.getElementById('ohlc-ma7').textContent = ma7 ? formatPrice(ma7, m.decimals) : '--';
      document.getElementById('ohlc-ma25').textContent = ma25 ? formatPrice(ma25, m.decimals) : '--';
    }

    // Canvas Mouse & Touch Listeners
    canvas.addEventListener('mousemove', (e) => {
      const rect = canvas.getBoundingClientRect();
      state.mousePos = { x: e.clientX - rect.left, y: e.clientY - rect.top };
      drawChart();
    });
    canvas.addEventListener('mouseleave', () => {
      state.mousePos = null;
      drawChart();
    });
    canvas.addEventListener('touchmove', (e) => {
      if (e.touches.length > 0) {
        const rect = canvas.getBoundingClientRect();
        state.mousePos = { x: e.touches[0].clientX - rect.left, y: e.touches[0].clientY - rect.top };
        drawChart();
      }
    }, { passive: true });
    canvas.addEventListener('touchend', () => {
      state.mousePos = null;
      drawChart();
    });

    /**
     * =========================================================================
     * 4. ORDER BOOK & RECENT TRADES ENGINE
     * =========================================================================
     */
    function renderOrderBook() {
      const m = MARKETS[state.activeSymbol];
      const midPrice = m.price;
      const tick = m.tickSize;
      const rowCount = state.bookMode === 'both' ? 9 : 18;

      const asksContainer = document.getElementById('orderbook-asks');
      const bidsContainer = document.getElementById('orderbook-bids');

      asksContainer.style.display = state.bookMode === 'bids' ? 'none' : 'flex';
      bidsContainer.style.display = state.bookMode === 'asks' ? 'none' : 'flex';

      document.getElementById('ob-size-header').textContent = `Size (${m.base})`;

      // Generate realistic depth levels
      let asks = [];
      let cumAsk = 0;
      for (let i = 1; i <= rowCount; i++) {
        const price = midPrice + i * tick * (1 + Math.floor(i / 5));
        const size = Number(((Math.random() * 1.8 + 0.05) * (10000 / midPrice)).toFixed(m.sizeDecimals));
        cumAsk += size;
        asks.push({ price, size, total: cumAsk });
      }
      const maxAskTotal = cumAsk || 1;
      asks.reverse(); // Highest ask on top, lowest ask near spread

      let bids = [];
      let cumBid = 0;
      for (let i = 1; i <= rowCount; i++) {
        const price = midPrice - i * tick * (1 + Math.floor(i / 5));
        const size = Number(((Math.random() * 1.8 + 0.05) * (10000 / midPrice)).toFixed(m.sizeDecimals));
        cumBid += size;
        bids.push({ price, size, total: cumBid });
      }
      const maxBidTotal = cumBid || 1;

      // Render Asks
      asksContainer.innerHTML = asks.map(row => {
        const depthPct = Math.min(100, (row.total / maxAskTotal) * 100);
        return `
          <div onclick="selectOrderBookPrice(${row.price.toFixed(m.decimals)})" class="relative grid grid-cols-3 px-3 py-[3px] text-[11px] tabular cursor-pointer hover:bg-panelHover transition">
            <div class="absolute inset-y-0 right-0 bg-sellDim pointer-events-none" style="width: ${depthPct}%"></div>
            <span class="text-sell font-medium relative z-10">${formatPrice(row.price, m.decimals)}</span>
            <span class="text-right text-textPrimary relative z-10">${row.size.toFixed(m.sizeDecimals)}</span>
            <span class="text-right text-textSecondary relative z-10">${row.total.toFixed(2)}</span>
          </div>
        `;
      }).join('');

      // Render Bids
      bidsContainer.innerHTML = bids.map(row => {
        const depthPct = Math.min(100, (row.total / maxBidTotal) * 100);
        return `
          <div onclick="selectOrderBookPrice(${row.price.toFixed(m.decimals)})" class="relative grid grid-cols-3 px-3 py-[3px] text-[11px] tabular cursor-pointer hover:bg-panelHover transition">
            <div class="absolute inset-y-0 right-0 bg-buyDim pointer-events-none" style="width: ${depthPct}%"></div>
            <span class="text-buy font-medium relative z-10">${formatPrice(row.price, m.decimals)}</span>
            <span class="text-right text-textPrimary relative z-10">${row.size.toFixed(m.sizeDecimals)}</span>
            <span class="text-right text-textSecondary relative z-10">${row.total.toFixed(2)}</span>
          </div>
        `;
      }).join('');

      // Update Spread
      const bestAsk = asks[asks.length - 1]?.price || midPrice + tick;
      const bestBid = bids[0]?.price || midPrice - tick;
      const spread = Math.max(tick, bestAsk - bestBid);
      const spreadPct = ((spread / midPrice) * 100).toFixed(3);
      document.getElementById('ob-spread-text').textContent = `Spread: ${spread.toFixed(m.decimals)} (${spreadPct}%)`;
    }

    function selectOrderBookPrice(price) {
      if (state.orderType === 'Market') {
        setOrderType('Limit');
      }
      document.getElementById('input-order-price').value = price;
      handlePriceOrAmountChange('price');
      showToast(`Limit price set to $${formatPrice(price, MARKETS[state.activeSymbol].decimals)}`, 'info');
    }

    function pushNewMarketTrade(price, isBuy) {
      const m = MARKETS[state.activeSymbol];
      const now = new Date();
      const timeStr = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
      const size = Number(((Math.random() * 0.9 + 0.01) * (15000 / price)).toFixed(m.sizeDecimals));

      state.recentTrades.unshift({
        price,
        size,
        time: timeStr,
        isBuy
      });

      if (state.recentTrades.length > 35) state.recentTrades.pop();
      renderRecentTrades();
    }

    function renderRecentTrades() {
      const m = MARKETS[state.activeSymbol];
      const fullList = document.getElementById('recent-trades-list');
      const miniList = document.getElementById('mini-trades-list');

      const rowsHtml = state.recentTrades.map(t => `
        <div class="grid grid-cols-3 px-3 py-1 hover:bg-panelHover">
          <span class="${t.isBuy ? 'text-buy' : 'text-sell'} font-medium">${formatPrice(t.price, m.decimals)}</span>
          <span class="text-right text-textPrimary">${t.size.toFixed(m.sizeDecimals)}</span>
          <span class="text-right text-textSecondary">${t.time}</span>
        </div>
      `).join('');

      const miniHtml = state.recentTrades.slice(0, 8).map(t => `
        <div class="grid grid-cols-3">
          <span class="${t.isBuy ? 'text-buy' : 'text-sell'}">${formatPrice(t.price, m.decimals)}</span>
          <span class="text-right text-textPrimary">${t.size.toFixed(m.sizeDecimals)}</span>
          <span class="text-right text-textSecondary">${t.time}</span>
        </div>
      `).join('');

      if (fullList) fullList.innerHTML = rowsHtml;
      if (miniList) miniList.innerHTML = miniHtml;
    }

    /**
     * =========================================================================
     * 5. ORDER EXECUTION TERMINAL & PORTFOLIO LOGIC
     * =========================================================================
     */
    function setOrderSide(side) {
      state.orderSide = side;
      const buyBtn = document.getElementById('side-buy-btn');
      const sellBtn = document.getElementById('side-sell-btn');
      const submitBtn = document.getElementById('submit-order-btn');
      const m = MARKETS[state.activeSymbol];

      if (side === 'buy') {
        buyBtn.className = 'py-2 rounded-md text-xs font-bold bg-buy text-obsidian shadow-md shadow-buy/20 transition';
        sellBtn.className = 'py-2 rounded-md text-xs font-bold text-textSecondary hover:text-white transition';
        submitBtn.className = 'w-full py-3 rounded-lg font-bold text-xs sm:text-sm bg-buy hover:bg-buyHover text-obsidian shadow-lg shadow-buy/20 transition transform active:scale-[0.99]';
        submitBtn.textContent = `Buy / Long ${m.base}`;
      } else {
        sellBtn.className = 'py-2 rounded-md text-xs font-bold bg-sell text-white shadow-md shadow-sell/20 transition';
        buyBtn.className = 'py-2 rounded-md text-xs font-bold text-textSecondary hover:text-white transition';
        submitBtn.className = 'w-full py-3 rounded-lg font-bold text-xs sm:text-sm bg-sell hover:bg-sellHover text-white shadow-lg shadow-sell/20 transition transform active:scale-[0.99]';
        submitBtn.textContent = `Sell / Short ${m.base}`;
      }
      updateAvailableBalanceDisplay();
    }

    function setOrderType(type) {
      state.orderType = type;
      document.querySelectorAll('.otype-btn').forEach(btn => {
        btn.className = 'otype-btn text-textSecondary hover:text-white pb-1.5 -mb-2 transition';
      });
      const activeBtn = document.getElementById(`otype-${type}`);
      if (activeBtn) {
        activeBtn.className = 'otype-btn text-white border-b-2 border-blue-500 pb-1.5 -mb-2 transition';
      }

      const stopGroup = document.getElementById('stop-price-group');
      const limitGroup = document.getElementById('limit-price-group');
      const marketBanner = document.getElementById('market-price-banner');

      if (type === 'Market') {
        stopGroup.classList.add('hidden');
        limitGroup.classList.add('hidden');
        marketBanner.classList.remove('hidden');
      } else if (type === 'Stop-Limit') {
        stopGroup.classList.remove('hidden');
        limitGroup.classList.remove('hidden');
        marketBanner.classList.add('hidden');
      } else {
        stopGroup.classList.add('hidden');
        limitGroup.classList.remove('hidden');
        marketBanner.classList.add('hidden');
      }
      recalculateOrderSummary();
    }

    function setMarginMode(mode) {
      state.marginMode = mode;
      const crossBtn = document.getElementById('margin-cross-btn');
      const isoBtn = document.getElementById('margin-iso-btn');
      if (mode === 'Cross') {
        crossBtn.className = 'flex-1 py-1 rounded bg-panelHover text-white transition';
        isoBtn.className = 'flex-1 py-1 rounded text-textSecondary hover:text-white transition';
      } else {
        isoBtn.className = 'flex-1 py-1 rounded bg-panelHover text-white transition';
        crossBtn.className = 'flex-1 py-1 rounded text-textSecondary hover:text-white transition';
      }
    }

    function setLeverage(val) {
      state.leverage = parseInt(val, 10);
      document.getElementById('leverage-slider').value = state.leverage;
      document.getElementById('leverage-slider-val').textContent = `${state.leverage}x`;
      document.getElementById('leverage-badge-btn').textContent = `${state.leverage}x`;
      recalculateOrderSummary();
    }

    function cycleLeverage() {
      const steps = [1, 3, 5, 10, 20];
      const next = steps.find(s => s > state.leverage) || 1;
      setLeverage(next);
    }

    function fillLastPrice() {
      const m = MARKETS[state.activeSymbol];
      document.getElementById('input-order-price').value = m.price.toFixed(m.decimals);
      handlePriceOrAmountChange('price');
    }

    function handlePriceOrAmountChange(source) {
      const m = MARKETS[state.activeSymbol];
      const priceInput = document.getElementById('input-order-price');
      const amountInput = document.getElementById('input-order-amount');
      const totalInput = document.getElementById('input-order-total');

      const execPrice = state.orderType === 'Market' ? m.price : (parseFloat(priceInput.value) || m.price);
      const amount = parseFloat(amountInput.value) || 0;
      const total = parseFloat(totalInput.value) || 0;

      if (source === 'price' || source === 'amount') {
        const calcTotal = amount * execPrice;
        totalInput.value = calcTotal > 0 ? calcTotal.toFixed(2) : '';
      } else if (source === 'total') {
        const calcAmount = execPrice > 0 ? total / execPrice : 0;
        amountInput.value = calcAmount > 0 ? calcAmount.toFixed(m.sizeDecimals) : '';
      }
      recalculateOrderSummary();
    }

    function applyOrderPercentage(pct) {
      const m = MARKETS[state.activeSymbol];
      document.getElementById('order-percent-slider').value = pct;
      const availUsdt = Math.max(0, state.balances.USDT.total - state.balances.USDT.locked);
      const execPrice = state.orderType === 'Market'
        ? m.price
        : (parseFloat(document.getElementById('input-order-price').value) || m.price);

      // Buying power scales with leverage
      const maxBuyingPower = availUsdt * state.leverage * (pct / 100);
      const amount = maxBuyingPower / execPrice;

      document.getElementById('input-order-amount').value = amount > 0 ? amount.toFixed(m.sizeDecimals) : '';
      document.getElementById('input-order-total').value = maxBuyingPower > 0 ? maxBuyingPower.toFixed(2) : '';
      recalculateOrderSummary();
    }

    function recalculateOrderSummary() {
      const total = parseFloat(document.getElementById('input-order-total').value) || 0;
      const reqMargin = total / state.leverage;
      const estFee = total * 0.0004;

      document.getElementById('summary-req-margin').textContent = `${reqMargin.toFixed(2)} USDT`;
      document.getElementById('summary-est-fee').textContent = `${estFee.toFixed(2)} USDT`;
    }

    function executeOrder() {
      const m = MARKETS[state.activeSymbol];
      const amount = parseFloat(document.getElementById('input-order-amount').value);
      const inputPrice = parseFloat(document.getElementById('input-order-price').value);
      const execPrice = state.orderType === 'Market' ? m.price : inputPrice;

      if (!amount || amount <= 0) {
        showToast('Please enter a valid order amount.', 'error');
        return;
      }
      if (state.orderType !== 'Market' && (!execPrice || execPrice <= 0)) {
        showToast('Please enter a valid limit price.', 'error');
        return;
      }

      const notionalValue = amount * execPrice;
      const requiredMargin = notionalValue / state.leverage;
      const availUsdt = state.balances.USDT.total - state.balances.USDT.locked;

      if (requiredMargin > availUsdt) {
        showToast(`Insufficient available USDT margin (Need $${requiredMargin.toFixed(2)}).`, 'error');
        return;
      }

      const now = new Date();
      const timeStr = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
      const sideLabel = state.orderSide === 'buy' ? 'Buy' : 'Sell';

      // Determine immediate fill vs resting limit order
      const isImmediateMarket = state.orderType === 'Market';
      const isMarketableLimit = state.orderType === 'Limit' && (
        (state.orderSide === 'buy' && execPrice >= m.price) ||
        (state.orderSide === 'sell' && execPrice <= m.price)
      );

      if (isImmediateMarket || isMarketableLimit) {
        // Deduct fee & add/update position
        const fee = notionalValue * 0.0004;
        state.balances.USDT.total -= fee;

        const posSide = state.orderSide === 'buy' ? 'Long' : 'Short';
        const existingPos = state.positions.find(p => p.symbol === m.symbol && p.side === posSide);

        if (existingPos) {
          const totalSize = existingPos.size + amount;
          existingPos.entryPrice = ((existingPos.entryPrice * existingPos.size) + (execPrice * amount)) / totalSize;
          existingPos.size = totalSize;
          existingPos.leverage = state.leverage;
        } else {
          state.positions.unshift({
            id: `POS-${Math.floor(100 + Math.random() * 900)}`,
            symbol: m.symbol,
            side: posSide,
            leverage: state.leverage,
            size: amount,
            entryPrice: execPrice
          });
        }

        // Record in Order History
        state.orderHistory.unshift({
          id: `ORD-${Math.floor(1000 + Math.random() * 9000)}`,
          time: timeStr,
          symbol: m.symbol,
          type: state.orderType,
          side: sideLabel,
          avgPrice: execPrice,
          executed: `${amount.toFixed(m.sizeDecimals)} ${m.base}`,
          status: 'Filled'
        });

        pushNewMarketTrade(execPrice, state.orderSide === 'buy');
        switchBottomTab('positions');
        showToast(`${sideLabel} ${amount.toFixed(m.sizeDecimals)} ${m.base} filled @ $${formatPrice(execPrice, m.decimals)}`, 'success');
      } else {
        // Resting Limit / Stop-Limit Order
        state.balances.USDT.locked += requiredMargin;
        state.openOrders.unshift({
          id: `ORD-${Math.floor(1000 + Math.random() * 9000)}`,
          time: timeStr,
          symbol: m.symbol,
          type: state.orderType,
          side: sideLabel,
          price: execPrice,
          amount: amount,
          total: notionalValue,
          lockedMargin: requiredMargin
        });

        switchBottomTab('open-orders');
        showToast(`${state.orderType} ${sideLabel} order placed @ $${formatPrice(execPrice, m.decimals)}`, 'info');
      }

      // Reset inputs
      document.getElementById('input-order-amount').value = '';
      document.getElementById('input-order-total').value = '';
      document.getElementById('order-percent-slider').value = 0;
      recalculateOrderSummary();
      renderAllPortfolioTables();
    }

    function cancelOrder(orderId) {
      const idx = state.openOrders.findIndex(o => o.id === orderId);
      if (idx === -1) return;
      const ord = state.openOrders[idx];
      const releasedMargin = ord.lockedMargin || (ord.total / 10);
      state.balances.USDT.locked = Math.max(0, state.balances.USDT.locked - releasedMargin);
      state.openOrders.splice(idx, 1);

      renderAllPortfolioTables();
      showToast(`Order ${orderId} canceled.`, 'info');
    }

    function cancelAllOrders() {
      if (state.openOrders.length === 0) return;
      state.openOrders = [];
      state.balances.USDT.locked = 0;
      renderAllPortfolioTables();
      showToast('All open orders canceled.', 'info');
    }

    function closePosition(posId) {
      const idx = state.positions.findIndex(p => p.id === posId);
      if (idx === -1) return;
      const pos = state.positions[idx];
      const markPrice = MARKETS[pos.symbol].price;
      const pnl = pos.side === 'Long'
        ? (markPrice - pos.entryPrice) * pos.size
        : (pos.entryPrice - markPrice) * pos.size;

      state.balances.USDT.total += pnl;
      state.positions.splice(idx, 1);

      const now = new Date();
      const timeStr = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
      state.orderHistory.unshift({
        id: `ORD-${Math.floor(1000 + Math.random() * 9000)}`,
        time: timeStr,
        symbol: pos.symbol,
        type: 'Market Close',
        side: pos.side === 'Long' ? 'Sell' : 'Buy',
        avgPrice: markPrice,
        executed: `${pos.size.toFixed(MARKETS[pos.symbol].sizeDecimals)} ${MARKETS[pos.symbol].base}`,
        status: 'Filled'
      });

      renderAllPortfolioTables();
      const sign = pnl >= 0 ? '+' : '';
      showToast(`Position closed. Realized PnL: ${sign}$${pnl.toFixed(2)}`, pnl >= 0 ? 'success' : 'error');
    }

    /**
     * =========================================================================
     * 6. RENDER PORTFOLIO TABLES & EQUITY
     * =========================================================================
     */
    function renderAllPortfolioTables() {
      document.getElementById('count-positions').textContent = state.positions.length;
      document.getElementById('count-open-orders').textContent = state.openOrders.length;

      // 1. Positions Table
      let totalUnrealizedPnl = 0;
      const posTbody = document.getElementById('positions-tbody');
      if (state.positions.length === 0) {
        posTbody.innerHTML = `<tr><td colspan="7" class="py-8 text-center text-textSecondary">No active positions</td></tr>`;
      } else {
        posTbody.innerHTML = state.positions.map(pos => {
          const m = MARKETS[pos.symbol];
          const mark = m.price;
          const pnl = pos.side === 'Long'
            ? (mark - pos.entryPrice) * pos.size
            : (pos.entryPrice - mark) * pos.size;
          totalUnrealizedPnl += pnl;

          const initMargin = (pos.entryPrice * pos.size) / pos.leverage;
          const roe = initMargin > 0 ? (pnl / initMargin) * 100 : 0;
          const pnlCls = pnl >= 0 ? 'text-buy' : 'text-sell';
          const sideBadge = pos.side === 'Long'
            ? `<span class="px-1.5 py-0.5 rounded bg-buyDim text-buy font-semibold">Long ${pos.leverage}x</span>`
            : `<span class="px-1.5 py-0.5 rounded bg-sellDim text-sell font-semibold">Short ${pos.leverage}x</span>`;

          return `
            <tr class="hover:bg-panelHover/60 transition">
              <td class="py-2.5 px-4 font-bold text-white">${pos.symbol}</td>
              <td class="py-2.5 px-3">${sideBadge}</td>
              <td class="py-2.5 px-3 text-right text-white">${pos.size.toFixed(m.sizeDecimals)} ${m.base}</td>
              <td class="py-2.5 px-3 text-right text-textSecondary">${formatPrice(pos.entryPrice, m.decimals)}</td>
              <td class="py-2.5 px-3 text-right text-white">${formatPrice(mark, m.decimals)}</td>
              <td class="py-2.5 px-3 text-right font-semibold ${pnlCls}">
                ${pnl >= 0 ? '+' : ''}${pnl.toFixed(2)} USDT (${roe >= 0 ? '+' : ''}${roe.toFixed(2)}%)
              </td>
              <td class="py-2.5 px-4 text-right">
                <button onclick="closePosition('${pos.id}')" class="px-2.5 py-1 rounded bg-obsidian hover:bg-sell hover:text-white border border-borderDark text-[11px] font-semibold text-textPrimary transition">
                  Market Close
                </button>
              </td>
            </tr>
          `;
        }).join('');
      }

      // 2. Open Orders Table
      const ooTbody = document.getElementById('open-orders-tbody');
      if (state.openOrders.length === 0) {
        ooTbody.innerHTML = `<tr><td colspan="8" class="py-8 text-center text-textSecondary">No open orders</td></tr>`;
      } else {
        ooTbody.innerHTML = state.openOrders.map(ord => {
          const m = MARKETS[ord.symbol];
          const sideCls = ord.side === 'Buy' ? 'text-buy' : 'text-sell';
          return `
            <tr class="hover:bg-panelHover/60 transition">
              <td class="py-2.5 px-4 text-textSecondary">${ord.time}</td>
              <td class="py-2.5 px-3 font-bold text-white">${ord.symbol}</td>
              <td class="py-2.5 px-3 text-textSecondary">${ord.type}</td>
              <td class="py-2.5 px-3 font-semibold ${sideCls}">${ord.side}</td>
              <td class="py-2.5 px-3 text-right text-white">${formatPrice(ord.price, m.decimals)}</td>
              <td class="py-2.5 px-3 text-right text-white">${ord.amount.toFixed(m.sizeDecimals)}</td>
              <td class="py-2.5 px-3 text-right text-textSecondary">${ord.total.toFixed(2)}</td>
              <td class="py-2.5 px-4 text-right">
                <button onclick="cancelOrder('${ord.id}')" class="text-sell hover:underline font-semibold text-[11px]">Cancel</button>
              </td>
            </tr>
          `;
        }).join('');
      }

      // 3. Order History Table
      const histTbody = document.getElementById('history-tbody');
      histTbody.innerHTML = state.orderHistory.map(h => {
        const m = MARKETS[h.symbol];
        const sideCls = h.side === 'Buy' ? 'text-buy' : 'text-sell';
        return `
          <tr class="hover:bg-panelHover/60 transition">
            <td class="py-2.5 px-4 text-textSecondary">${h.time}</td>
            <td class="py-2.5 px-3 font-bold text-white">${h.symbol}</td>
            <td class="py-2.5 px-3 text-textSecondary">${h.type}</td>
            <td class="py-2.5 px-3 font-semibold ${sideCls}">${h.side}</td>
            <td class="py-2.5 px-3 text-right text-white">${formatPrice(h.avgPrice, m.decimals)}</td>
            <td class="py-2.5 px-3 text-right text-white">${h.executed}</td>
            <td class="py-2.5 px-4 text-right"><span class="px-2 py-0.5 rounded bg-buyDim text-buy text-[10px] font-semibold">${h.status}</span></td>
          </tr>
        `;
      }).join('');

      // 4. Assets Table & Total Equity Calculation
      let totalSpotUsd = 0;
      const assetsTbody = document.getElementById('assets-tbody');
      assetsTbody.innerHTML = Object.entries(state.balances).map(([asset, bal]) => {
        const priceUsd = asset === 'USDT' ? 1 : (MARKETS[`${asset}/USDT`]?.price || 1);
        const usdVal = bal.total * priceUsd;
        totalSpotUsd += usdVal;
        const avail = Math.max(0, bal.total - bal.locked);

        return `
          <tr class="hover:bg-panelHover/60 transition">
            <td class="py-2.5 px-4 font-bold text-white">${asset}</td>
            <td class="py-2.5 px-3 text-right text-white">${bal.total.toFixed(asset === 'USDT' ? 2 : 4)}</td>
            <td class="py-2.5 px-3 text-right text-textPrimary">${avail.toFixed(asset === 'USDT' ? 2 : 4)}</td>
            <td class="py-2.5 px-3 text-right text-textSecondary">${bal.locked.toFixed(asset === 'USDT' ? 2 : 4)}</td>
            <td class="py-2.5 px-4 text-right font-semibold text-white">$${usdVal.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}</td>
          </tr>
        `;
      }).join('');

      const grandEquity = totalSpotUsd + totalUnrealizedPnl;
      document.getElementById('header-total-equity').textContent = `$${grandEquity.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`;
      updateAvailableBalanceDisplay();
    }

    function updateAvailableBalanceDisplay() {
      const availUsdt = Math.max(0, state.balances.USDT.total - state.balances.USDT.locked);
      document.getElementById('order-avail-balance').textContent = `${availUsdt.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })} USDT`;
    }

    /**
     * =========================================================================
     * 7. PAIR SWITCHING, UI TABS & LIVE TICK ENGINE
     * =========================================================================
     */
    function switchMarket(symbol) {
      if (!MARKETS[symbol]) return;
      state.activeSymbol = symbol;
      const m = MARKETS[symbol];

      if (!state.candlesBySymbol[symbol]) {
        state.candlesBySymbol[symbol] = generateInitialCandles(symbol);
      }

      // Update Ticker Header Info
      document.getElementById('active-pair-symbol').textContent = m.symbol;
      document.getElementById('active-pair-name').textContent = m.name;
      document.getElementById('active-pair-badge').textContent = m.maxLev;
      const iconEl = document.getElementById('active-pair-icon');
      iconEl.className = `w-8 h-8 rounded-full flex items-center justify-center font-bold text-sm border ${m.iconBg}`;
      iconEl.innerHTML = m.iconHtml;

      // Update Order Form labels & default price
      document.getElementById('form-base-asset').textContent = m.base;
      document.getElementById('form-base-unit').textContent = m.base;
      document.getElementById('input-order-price').value = m.price.toFixed(m.decimals);
      document.getElementById('input-order-amount').value = '';
      document.getElementById('input-order-total').value = '';
      setOrderSide(state.orderSide);

      // Seed fresh trades for this pair
      state.recentTrades = [];
      for (let i = 0; i < 18; i++) {
        const jitter = (Math.random() - 0.5) * m.tickSize * 6;
        pushNewMarketTrade(Number((m.price + jitter).toFixed(m.decimals)), Math.random() > 0.48);
      }

      updateTickerStatsUI(true);
      renderOrderBook();
      drawChart();
      closePairDropdown();
    }

    function updateTickerStatsUI(isUp) {
      const m = MARKETS[state.activeSymbol];
      const change = m.price - m.open24h;
      const changePct = (change / m.open24h) * 100;
      const isPositive24h = change >= 0;

      const priceEl = document.getElementById('ticker-price');
      priceEl.textContent = formatPrice(m.price, m.decimals);
      priceEl.className = `text-base sm:text-lg font-bold tabular leading-tight ${isUp ? 'text-buy' : 'text-sell'}`;

      document.getElementById('ticker-usd').textContent = `$${formatPrice(m.price, m.decimals)}`;

      const changeEl = document.getElementById('ticker-change');
      const sign = isPositive24h ? '+' : '';
      changeEl.textContent = `${sign}${formatPrice(change, m.decimals)} (${sign}${changePct.toFixed(2)}%)`;
      changeEl.className = `text-xs font-semibold tabular mt-0.5 ${isPositive24h ? 'text-buy' : 'text-sell'}`;

      document.getElementById('ticker-high').textContent = formatPrice(m.high24h, m.decimals);
      document.getElementById('ticker-low').textContent = formatPrice(m.low24h, m.decimals);
      document.getElementById('ticker-vol').textContent = m.vol24h.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 });

      // Update Order Book Mid Price
      const obMid = document.getElementById('ob-mid-price');
      const obArrow = document.getElementById('ob-mid-arrow');
      obMid.textContent = formatPrice(m.price, m.decimals);
      obMid.className = `text-sm font-bold tabular ${isUp ? 'text-buy' : 'text-sell'}`;
      obArrow.className = `fa-solid ${isUp ? 'fa-arrow-up text-buy' : 'fa-arrow-down text-sell'} text-[10px]`;
    }

    function renderPairDropdownList(filter = '') {
      const listEl = document.getElementById('pair-dropdown-list');
      const query = filter.toLowerCase();

      listEl.innerHTML = Object.values(MARKETS)
        .filter(m => m.symbol.toLowerCase().includes(query) || m.name.toLowerCase().includes(query))
        .map(m => {
          const chgPct = ((m.price - m.open24h) / m.open24h) * 100;
          const isPos = chgPct >= 0;
          return `
            <div onclick="switchMarket('${m.symbol}')" class="grid grid-cols-3 items-center px-3 py-2.5 hover:bg-panelHover cursor-pointer transition text-xs">
              <div>
                <div class="font-bold text-white">${m.symbol}</div>
                <div class="text-[10px] text-textSecondary">${m.base} Perp</div>
              </div>
              <div class="text-right font-semibold text-white tabular">${formatPrice(m.price, m.decimals)}</div>
              <div class="text-right font-semibold tabular ${isPos ? 'text-buy' : 'text-sell'}">
                ${isPos ? '+' : ''}${chgPct.toFixed(2)}%
              </div>
            </div>
          `;
        }).join('');
    }

    function togglePairDropdown() {
      const dd = document.getElementById('pair-dropdown');
      dd.classList.toggle('hidden');
      if (!dd.classList.contains('hidden')) {
        renderPairDropdownList();
        document.getElementById('pair-search-input').focus();
      }
    }

    function closePairDropdown() {
      document.getElementById('pair-dropdown').classList.add('hidden');
    }

    function filterPairs() {
      const val = document.getElementById('pair-search-input').value;
      renderPairDropdownList(val);
    }

    // Close dropdown when clicking outside
    document.addEventListener('click', (e) => {
      const wrapper = document.getElementById('pair-selector-wrapper');
      if (wrapper && !wrapper.contains(e.target)) {
        closePairDropdown();
      }
    });

    // Chart Controls
    function setTimeframe(tf) {
      state.timeframe = tf;
      document.querySelectorAll('.tf-btn').forEach(btn => {
        if (btn.dataset.tf === tf) {
          btn.className = 'tf-btn px-2 py-1 rounded bg-panelHover text-blue-400 font-semibold transition';
        } else {
          btn.className = 'tf-btn px-2 py-1 rounded text-textSecondary hover:text-white hover:bg-panelHover transition';
        }
      });
      state.candlesBySymbol[state.activeSymbol] = generateInitialCandles(state.activeSymbol);
      drawChart();
    }

    function setChartType(type) {
      state.chartType = type;
      const cBtn = document.getElementById('btn-chart-candle');
      const lBtn = document.getElementById('btn-chart-line');
      if (type === 'candle') {
        cBtn.className = 'px-2 py-1 rounded text-[11px] font-medium bg-panelHover text-white transition';
        lBtn.className = 'px-2 py-1 rounded text-[11px] font-medium text-textSecondary hover:text-white transition';
      } else {
        lBtn.className = 'px-2 py-1 rounded text-[11px] font-medium bg-panelHover text-white transition';
        cBtn.className = 'px-2 py-1 rounded text-[11px] font-medium text-textSecondary hover:text-white transition';
      }
      drawChart();
    }

    function toggleIndicator(ind) {
      if (ind === 'ma') {
        state.showMA = !state.showMA;
        const btn = document.getElementById('toggle-ma');
        btn.className = state.showMA
          ? 'px-2 py-1 rounded border border-blue-500/40 bg-blue-500/10 text-blue-400 text-[11px] font-medium transition'
          : 'px-2 py-1 rounded border border-borderDark text-textSecondary text-[11px] font-medium transition';
      } else if (ind === 'vol') {
        state.showVol = !state.showVol;
        const btn = document.getElementById('toggle-vol');
        btn.className = state.showVol
          ? 'px-2 py-1 rounded border border-emerald-500/40 bg-emerald-500/10 text-emerald-400 text-[11px] font-medium transition'
          : 'px-2 py-1 rounded border border-borderDark text-textSecondary text-[11px] font-medium transition';
      }
      drawChart();
    }

    // Order Book & Drawer Tabs
    function setBookMode(mode) {
      state.bookMode = mode;
      ['both', 'bids', 'asks'].forEach(m => {
        const el = document.getElementById(`ob-mode-${m}`);
        el.className = m === mode ? 'p-1 rounded bg-panelHover text-white' : 'p-1 rounded text-textSecondary hover:text-white';
      });
      renderOrderBook();
    }

    function switchRightMidTab(tab) {
      const bookView = document.getElementById('view-order-book');
      const tradesView = document.getElementById('view-recent-trades');
      const controls = document.getElementById('ob-layout-controls');
      const btnBook = document.getElementById('ob-tab-book');
      const btnTrades = document.getElementById('ob-tab-trades');

      if (tab === 'book') {
        bookView.classList.remove('hidden');
        tradesView.classList.add('hidden');
        controls.classList.remove('hidden');
        btnBook.className = 'text-white border-b-2 border-blue-500 pb-1 transition';
        btnTrades.className = 'text-textSecondary hover:text-white pb-1 transition';
      } else {
        bookView.classList.add('hidden');
        tradesView.classList.remove('hidden');
        controls.classList.add('hidden');
        btnTrades.className = 'text-white border-b-2 border-blue-500 pb-1 transition';
        btnBook.className = 'text-textSecondary hover:text-white pb-1 transition';
      }
    }

    function switchBottomTab(tabName) {
      document.querySelectorAll('.bottom-panel').forEach(p => p.classList.add('hidden'));
      document.getElementById(`bottom-panel-${tabName}`).classList.remove('hidden');

      document.querySelectorAll('.bottom-tab-btn').forEach(btn => {
        btn.className = 'bottom-tab-btn py-3 border-b-2 border-transparent text-textSecondary hover:text-white whitespace-nowrap transition';
      });
      const activeBtn = document.getElementById(`tab-btn-${tabName}`);
      if (activeBtn) {
        activeBtn.className = 'bottom-tab-btn py-3 border-b-2 border-blue-500 text-white whitespace-nowrap transition';
      }
    }

    // Deposit Modal Handlers
    function openDepositModal() {
      document.getElementById('deposit-modal').classList.remove('hidden');
    }
    function closeDepositModal() {
      document.getElementById('deposit-modal').classList.add('hidden');
    }
    function confirmDeposit() {
      const amt = parseFloat(document.getElementById('deposit-amount-input').value);
      if (!amt || amt <= 0) {
        showToast('Enter a valid deposit amount.', 'error');
        return;
      }
      state.balances.USDT.total += amt;
      renderAllPortfolioTables();
      closeDepositModal();
      showToast(`+$${amt.toLocaleString('en-US', { minimumFractionDigits: 2 })} USDT credited to account.`, 'success');
    }

    // Toast Notification Utility
    function showToast(message, type = 'info') {
      const container = document.getElementById('toast-container');
      const toast = document.createElement('div');
      const borderMap = {
        success: 'border-buy/50 bg-panel text-white',
        error: 'border-sell/50 bg-panel text-white',
        info: 'border-blue-500/50 bg-panel text-white'
      };
      const iconMap = {
        success: '<i class="fa-solid fa-circle-check text-buy"></i>',
        error: '<i class="fa-solid fa-circle-exclamation text-sell"></i>',
        info: '<i class="fa-solid fa-circle-info text-blue-400"></i>'
      };

      toast.className = `pointer-events-auto flex items-center gap-2.5 px-4 py-3 rounded-xl border shadow-2xl text-xs font-medium transition-all duration-300 transform translate-y-2 opacity-0 ${borderMap[type]}`;
      toast.innerHTML = `${iconMap[type]} <span>${message}</span>`;
      container.appendChild(toast);

      requestAnimationFrame(() => {
        toast.classList.remove('translate-y-2', 'opacity-0');
      });

      setTimeout(() => {
        toast.classList.add('opacity-0', 'translate-y-2');
        setTimeout(() => toast.remove(), 300);
      }, 3200);
    }

    function formatPrice(val, decimals = 2) {
      return Number(val).toLocaleString('en-US', {
        minimumFractionDigits: decimals,
        maximumFractionDigits: decimals
      });
    }

    /**
     * =========================================================================
     * 8. REAL-TIME WEBSOCKET SIMULATION LOOP (EVERY 1.5s)
     * =========================================================================
     */
    let tickCount = 0;
    function startLiveMarketFeed() {
      setInterval(() => {
        tickCount++;
        // Simulate subtle price ticks across all markets
        Object.keys(MARKETS).forEach(sym => {
          const m = MARKETS[sym];
          const prevPrice = m.price;
          const delta = (Math.random() - 0.492) * m.volatility * m.price;
          const newPrice = Math.max(m.tickSize, Number((prevPrice + delta).toFixed(m.decimals)));

          m.price = newPrice;
          if (newPrice > m.high24h) m.high24h = newPrice;
          if (newPrice < m.low24h) m.low24h = newPrice;
          m.vol24h += Math.abs(delta) * 125;

          // Update active candle for this market
          const candles = state.candlesBySymbol[sym];
          if (candles && candles.length > 0) {
            const last = candles[candles.length - 1];
            last.close = newPrice;
            if (newPrice > last.high) last.high = newPrice;
            if (newPrice < last.low) last.low = newPrice;
            last.volume = Number((last.volume + Math.random() * 4.5).toFixed(2));

            // Every 12 ticks, roll into a new candle
            if (tickCount % 12 === 0) {
              candles.push({
                time: Date.now(),
                open: newPrice,
                high: newPrice,
                low: newPrice,
                close: newPrice,
                volume: 1.2
              });
              if (candles.length > 140) candles.shift();
            }
          }

          if (sym === state.activeSymbol) {
            const isUp = newPrice >= prevPrice;
            updateTickerStatsUI(isUp);
            pushNewMarketTrade(newPrice, isUp);
          }
        });

        renderOrderBook();
        renderAllPortfolioTables();
        drawChart();
      }, 1500);

      // Funding Countdown Timer (1s interval)
      let secondsLeft = 3 * 3600 + 42 * 60 + 18;
      setInterval(() => {
        secondsLeft = secondsLeft > 0 ? secondsLeft - 1 : 8 * 3600;
        const hrs = String(Math.floor(secondsLeft / 3600)).padStart(2, '0');
        const mins = String(Math.floor((secondsLeft % 3600) / 60)).padStart(2, '0');
        const secs = String(secondsLeft % 60).padStart(2, '0');
        const el = document.getElementById('funding-countdown');
        if (el) el.textContent = `${hrs}:${mins}:${secs}`;
      }, 1000);
    }

    /**
     * =========================================================================
     * 9. INITIALIZATION ON WINDOW LOAD
     * =========================================================================
     */
    window.addEventListener('resize', resizeCanvas);
    window.onload = function () {
      // Pre-generate candle datasets for all pairs
      Object.keys(MARKETS).forEach(sym => {
        state.candlesBySymbol[sym] = generateInitialCandles(sym);
      });

      switchMarket('BTC/USDT');
      renderAllPortfolioTables();
      resizeCanvas();
      startLiveMarketFeed();
    };
  </script>
</body>
</html>