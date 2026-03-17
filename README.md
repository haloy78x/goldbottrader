# Gold AI Trading Bot (XAU/USD)

An autonomous algorithmic trading bot that uses **Artificial Intelligence** (Claude by Anthropic or GPT by OpenAI) to analyze the Gold (XAU/USD) market and automatically place trades through **MetaTrader 5**.

The bot fetches real-time market data, reads macro-economic news headlines, sends everything to an AI model for analysis, and executes the AI's trading decision on a bar-aligned schedule. It includes **5 selectable trading strategies**, a **web dashboard** with live charts and memory insights, **Telegram remote control** (7 interactive commands), **news API caching**, **hardened market-unavailable detection**, an optional **economic calendar window**, a **centralized risk gate manager**, a **rule-based trade memory layer with reviewed HOLD outcomes**, **confidence calibration**, **execution analytics**, a **fast execution watchdog**, **backtesting**, and comprehensive safety controls.

> **Disclaimer:** This software is for educational and experimental purposes. Trading involves substantial risk of loss. Use at your own risk. This is NOT financial advice.

---

## Table of Contents

1. [What You Need Before Starting](#what-you-need-before-starting)
2. [Step 1 — Install Python](#step-1--install-python)
3. [Step 2 — Install MetaTrader 5](#step-2--install-metatrader-5)
4. [Step 3 — Set Up a Demo Account (Recommended for Testing)](#step-3--set-up-a-demo-account-recommended-for-testing)
5. [Step 4 — Get Your API Keys](#step-4--get-your-api-keys)
6. [Step 5 — Configure the Bot](#step-5--configure-the-bot)
7. [Step 6 — Install Python Dependencies](#step-6--install-python-dependencies)
8. [Step 7 — Run the Bot](#step-7--run-the-bot)
9. [Step 8 — Set Up Telegram Notifications (Optional)](#step-8--set-up-telegram-notifications-optional)
10. [Trading Strategies](#trading-strategies)
11. [Using the Web Dashboard](#using-the-web-dashboard)
12. [Understanding the Dashboard](#understanding-the-dashboard)
13. [How the Bot Works](#how-the-bot-works)
14. [Backtesting and Regression Tests](#backtesting-and-regression-tests)
15. [Live Trading vs Demo Trading](#live-trading-vs-demo-trading)
16. [Risk Controls](#risk-controls)
17. [Troubleshooting](#troubleshooting)
18. [Project Structure](#project-structure)

---

## What You Need Before Starting

Before you can run the bot, you need **four things**. A fifth key is optional if you want structured economic-calendar windows in addition to headline-based news protection:

| # | What | Where to Get It | Cost |
|---|------|-----------------|------|
| 1 | **Python 3.10+** | [python.org](https://www.python.org/downloads/) | Free |
| 2 | **MetaTrader 5** (Windows desktop app) | [metatrader5.com](https://www.metatrader5.com/en/download) | Free |
| 3 | **An AI API Key** (Claude **or** GPT — you only need one) | [Anthropic Console](https://console.anthropic.com/) or [OpenAI Platform](https://platform.openai.com/) | Pay-per-use |
| 4 | **A News API Key** | [newsapi.org](https://newsapi.org/register) | Free tier available |
| 5 | **A Finnhub API Key** (optional economic calendar) | [finnhub.io](https://finnhub.io/) | Free tier available |

---

## Step 1 — Install Python

<details>
<summary>Show Step 1 details</summary>

If you already have Python installed, skip this step. To check, open **Command Prompt** or **PowerShell** and type:

```
python --version
```

If you see something like `Python 3.12.9`, you're good. If not:

1. Go to [python.org/downloads](https://www.python.org/downloads/)
2. Download the latest Python 3 installer for Windows
3. **IMPORTANT:** During installation, check the box that says **"Add Python to PATH"**
4. Click "Install Now"
5. After installation, close and reopen your terminal, then run `python --version` to confirm

</details>

---

## Step 2 — Install MetaTrader 5

<details>
<summary>Show Step 2 details</summary>

MetaTrader 5 (MT5) is the trading platform that the bot uses to get market prices and place orders. Think of it as the "bridge" between the bot and the financial markets.

1. Go to [metatrader5.com/en/download](https://www.metatrader5.com/en/download)
2. Download the installer for Windows
3. Run the installer and follow the prompts (just click "Next" through everything)
4. Once installed, **open MetaTrader 5** — you should see the application window

> **Note:** MetaTrader 5 only runs on **Windows**. If you're on Mac or Linux, you'll need to use a Windows virtual machine or Wine.

</details>

---

## Step 3 — Set Up a Demo Account (Recommended for Testing)

<details>
<summary>Show Step 3 details</summary>

A **demo account** lets you trade with fake money. This is the best way to test if the bot works before risking real money. **We strongly recommend starting here.**

### Option A — Create a Demo Account from MetaTrader 5

1. Open the **MetaTrader 5** application
2. Go to **File → Open an Account**
3. You'll see a list of brokers. You can pick any broker that offers demo accounts. Some popular ones:
   - **MetaQuotes-Demo** (built-in, easiest option)
   - **ICMarkets-Demo**
   - **Pepperstone-Demo**
   - **XM-Demo**
4. If your broker isn't listed, click **"Add"** at the bottom, type your broker's server name, and click **"Scan"**
5. Select **"Open a demo account"**
6. Fill in the form (name, email, etc.) and choose your starting balance (e.g., $10,000)
7. Click **"Next"** and your demo account will be created
8. **Write down these three things** — you'll need them later:
   - **Login** (a number like `12345678`)
   - **Password** (the one you just created)
   - **Server** (e.g., `MetaQuotes-Demo`)

### Option B — Use Your Broker's Demo Account

Most forex/CFD brokers offer demo accounts. Check your broker's website for a "Demo Account" option. Once you have the login, password, and server name, you're ready.

### Making Sure XAUUSD Is Available

Not all demo accounts have Gold (XAUUSD) available by default. To check:

1. In MetaTrader 5, look at the **"Market Watch"** panel on the left side
2. If you don't see **XAUUSD**, right-click in the Market Watch panel
3. Click **"Symbols"**
4. Search for **"XAUUSD"** or **"Gold"**
5. Select it and click **"Show"**
6. Click **"OK"**

> **Note:** Some brokers call it `XAUUSD`, others call it `GOLD` or `XAUUSDm`. If yours is different, you'll need to update the symbol in the `.env` file (see Step 5).

</details>

---

## Step 4 — Get Your API Keys

<details>
<summary>Show Step 4 details</summary>

### AI API Key (you only need ONE — Claude or GPT)

**Option 1 — Claude (Anthropic):**
1. Go to [console.anthropic.com](https://console.anthropic.com/)
2. Create an account or sign in
3. Go to **API Keys** from the dashboard
4. Click **"Create Key"**
5. Copy the key — it looks like `sk-ant-api03-...`
6. Add some credit to your account (at least $5 to start)

**Option 2 — GPT (OpenAI):**
1. Go to [platform.openai.com](https://platform.openai.com/)
2. Create an account or sign in
3. Go to **API Keys** in your account settings
4. Click **"Create new secret key"**
5. Copy the key — it looks like `sk-proj-...`
6. Add some credit to your account (at least $5 to start)

### News API Key

1. Go to [newsapi.org/register](https://newsapi.org/register)
2. Create a free account
3. Your API key will be shown on the dashboard after registration
4. Copy the key — it looks like `4443e3625c8c4658b26c1f77f011d006`

</details>

---

## Step 5 — Configure the Bot

<details>
<summary>Show Step 5 details</summary>

Start by copying **`.env.example`** to **`.env`** in the `tradingbot` folder. You can do that from PowerShell with:

```
copy .env.example .env
```

Then open **`.env`** with Notepad, VS Code, or any text editor.

At minimum, it should contain your API keys:

```
ANTHROPIC_API_KEY=your_anthropic_api_key_here
NEWS_API_KEY=your_newsapi_key_here
OPENAI_API_KEY=your_openai_api_key_here
FINNHUB_API_KEY=your_finnhub_api_key_here
```

Replace the placeholder values with your actual keys.

Optional configuration values are also supported in `.env` now, including:

```
OPENAI_MODEL=gpt-5.4
ANTHROPIC_MODEL=claude-sonnet-4-6
ECONOMIC_CALENDAR_ENABLED=true
NEWS_BLACKOUT_MAX_AGE_HOURS=6
NEWS_BLACKOUT_DECAY_ITERATIONS=8
NEWS_BLACKOUT_DECAY_MIN_AGE_HOURS=2
FAST_WATCHDOG_INTERVAL_SECONDS=2
MICRO_SCALPING_WATCHDOG_INTERVAL_SECONDS=1
MICRO_SCALP_DEFAULT_PROFIT_TARGET_PCT=0.20
SPREAD_SPIKE_ALERT_POINTS=100
MARKET_CLOSED_SPREAD_POINTS=100
MARKET_TICK_STALE_SECONDS=180
MARKET_BAR_STALE_GRACE_SECONDS=180
SPREAD_SPIKE_BLOCK_SECONDS=90
M5_CANDLE_COUNT=288
SCALPING_LOOP_SLEEP_SECONDS=300
```

What these do:

1. `OPENAI_MODEL` sets the default GPT model when there is no saved dashboard preference.
2. `ANTHROPIC_MODEL` sets the default Claude model when there is no saved dashboard preference.
3. `ECONOMIC_CALENDAR_ENABLED` turns the structured calendar layer on or off.
4. `NEWS_BLACKOUT_MAX_AGE_HOURS` controls how long a headline can qualify for blackout.
5. `NEWS_BLACKOUT_DECAY_ITERATIONS` controls how many consecutive blocked cycles the same headline needs before it can degrade into AI-visible advisory mode.
6. `NEWS_BLACKOUT_DECAY_MIN_AGE_HOURS` sets the minimum article age before that advisory decay is allowed.
7. `FAST_WATCHDOG_INTERVAL_SECONDS` controls how often the bot runs its fast protective loop for spread spikes, trailing stops, break-even updates, and live MT5 state refreshes.
8. `MICRO_SCALPING_WATCHDOG_INTERVAL_SECONDS` sets the faster watchdog cadence used by the `micro_scalping` strategy.
9. `MICRO_SCALP_DEFAULT_PROFIT_TARGET_PCT` sets the default account-profit percentage target that the micro-scalp watchdog uses for quick exits.
10. `SPREAD_SPIKE_ALERT_POINTS` sets the spread level where the fast watchdog raises a warning, even before the slower decision loop runs again.
11. `MARKET_CLOSED_SPREAD_POINTS` helps classify the market as effectively unavailable when the broker feed remains connected but spread becomes extreme.
12. `MARKET_TICK_STALE_SECONDS` sets the maximum allowed age for the latest tick before the market is treated as stale.
13. `MARKET_BAR_STALE_GRACE_SECONDS` sets the grace period for stale decision-timeframe bars before analysis is blocked.
14. `SPREAD_SPIKE_BLOCK_SECONDS` controls how long new BUY and SELL entries stay blocked after the watchdog detects a spread spike, even if the spread normalizes quickly.
15. `M5_CANDLE_COUNT` controls how much 5-minute history is loaded when a strategy uses M5 as its primary decision timeframe.
16. `SCALPING_LOOP_SLEEP_SECONDS` controls the bar-aligned decision cadence for scalping, with the default set to 300 seconds so the bot evaluates on each new M5 bar.

Runtime cadence now depends on strategy:

1. `scalping` uses M5 as its primary decision timeframe and aligns AI decisions to 5-minute bar closes.
2. `micro_scalping` also uses M5 as its primary decision timeframe, but with a faster watchdog cadence and quick-profit exits.
3. The other built-in strategies keep M15 as their primary decision timeframe.
4. The fast watchdog still runs independently between AI decisions for trailing stops, break-even management, weekend protection, spread-spike entry blocking, and micro-scalp quick exits.

The dashboard now lets you choose among the current official high-capability models for each provider before starting the bot:

1. OpenAI: `gpt-5.4-pro`, `gpt-5.4`, `gpt-5-mini`
2. Anthropic: `claude-opus-4-6`, `claude-sonnet-4-6`, `claude-haiku-4-5`

Those names were taken from the official provider model docs. Saved dashboard preferences persist your last selected model per provider.

The runtime is now hybrid:

1. AI decisions still run on the strategy decision boundary instead of every tick.
2. A separate fast watchdog loop runs every few seconds to keep open-position protection responsive and to catch spread blowouts between AI cycles.
3. Decision timing is aligned to the next bar boundary rather than sleeping a fixed 15 minutes from the end of the last run.

**You only need the key for the AI model you plan to use.** If you're using Claude, you can leave the OpenAI key empty, and vice versa.

Save the file.

</details>

---

## Step 6 — Install Python Dependencies

<details>
<summary>Show Step 6 details</summary>

1. Open **Command Prompt** or **PowerShell**
2. Navigate to the `tradingbot` folder. For example:
   ```
   cd C:\Users\YourName\Downloads\tradingbot
   ```
3. Run this command to install all required packages:
   ```
   pip install python-dotenv anthropic openai requests pandas numpy MetaTrader5 flask tzdata
   ```
4. Wait for everything to install. You should see "Successfully installed ..." at the end.

</details>

---

## Step 7 — Run the Bot

<details>
<summary>Show Step 7 details</summary>

### Using the Web Dashboard (Recommended — Easiest)

1. **Make sure MetaTrader 5 is installed on your computer** (it needs to be installed, but the bot will open/connect to it automatically — you don't need to manually open it first)
2. Open **Command Prompt** or **PowerShell**
3. Navigate to the `tradingbot` folder:
   ```
   cd C:\Users\YourName\Downloads\tradingbot
   ```
4. Run:
   ```
   python app.py
   ```
5. You'll see a message like:
   ```
   Gold (XAU/USD) AI Trading Bot — Web Dashboard
   Open your browser at:  http://127.0.0.1:5000
   ```
6. Open your web browser (Chrome, Edge, Firefox, etc.) and go to **http://127.0.0.1:5000**
7. You'll see the trading dashboard!

### Local Security Defaults

The dashboard is intended for **local use only** and now applies a few built-in protections by default:

1. The Flask server binds to `127.0.0.1` only.
2. API requests are restricted to local loopback clients and trusted local host headers.
3. State-changing dashboard actions use an in-memory local API token automatically injected into the page. You do **not** need to add this token to `.env`.
4. If you enable **Remember credentials**, the saved MT5 credentials file is encrypted with Windows DPAPI instead of being stored as plain text.

### Choosing a Trading Strategy and Sessions

The dashboard includes a **Strategy Selector** and **Trading Sessions** checkboxes. Pick a strategy and the session or sessions you want the bot to trade before starting:

| Strategy | Icon | Style |
|----------|------|-------|
| **Volatility Breakout** | 🚀 | Trades explosive momentum when price breaks out of a tight range. Wider TP targets, best during high-impact news or session opens. |
| **Scalping** | ⚡ | Quick in-and-out trades targeting small, frequent profits with M5 entries, tight SL/TP ranges, and strong preference for clean liquidity windows. |
| **Micro Scalping** | 🎯 | Tiny-account, ultra-fast M5 burst trading. The AI looks for immediate momentum and the fast watchdog can close the trade as soon as a small account-profit target is reached. |
| **Smart Money (ICT)** | 🏦 | Institutional-style trading using liquidity sweeps, order blocks, fair value gaps, and break-of-structure patterns. Fewer trades, higher conviction. |
| **Conservative** | 🛡️ | Ultra-safe, capital preservation focus. Only A+ setups with strong multi-timeframe confluence. Max 1 trade/day, minimal drawdown. |

Each strategy gives the AI a different system prompt with specific rules, entry/exit criteria, and risk parameters. The session checkboxes let you restrict new trades to specific market windows such as Sydney, Asia, London, New York, or any combination of them.

</details>

---

## Step 8 — Set Up Telegram Notifications (Optional)

<details>
<summary>Show Step 8 details</summary>

The bot can send you real-time Telegram notifications when trades are opened, closed, or when safety limits are triggered. This is entirely optional — the bot works fine without it.

### 8.1 — Create a Telegram Bot

1. Open Telegram and search for **@BotFather** (the official Telegram bot manager)
2. Send the command: `/newbot`
3. BotFather will ask you for a **name** — type anything, e.g., `Gold Trading Bot`
4. BotFather will ask you for a **username** — this must end in `bot`, e.g., `my_gold_trader_bot`
5. BotFather will reply with your **bot token** — it looks like: `7123456789:AAH1bCdEfGhIjKlMnOpQrStUvWxYz12345`
6. **Copy this token** — you'll need it in the next step

### 8.2 — Get Your Chat ID

You need your personal chat ID so the bot knows where to send messages.

1. Search for **@userinfobot** on Telegram and start a chat with it
2. Send any message — it will reply with your account info
3. Look for the **Id** field — it's a number like `123456789`
4. **Copy this number** — this is your chat ID

> **Alternative method:** Send a message to your new bot, then open this URL in your browser (replace `YOUR_BOT_TOKEN` with the token from step 8.1):
> ```
> https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates
> ```
> Look for `"chat":{"id":123456789}` in the response.

### 8.3 — Add Credentials to .env

Open the `.env` file and add these two lines:

```
TELEGRAM_BOT_TOKEN=7123456789:AAH1bCdEfGhIjKlMnOpQrStUvWxYz12345
TELEGRAM_CHAT_ID=123456789
```

Replace the values with your actual bot token and chat ID.

### 8.4 — Enable Notifications in the Dashboard

Telegram notifications are **off by default**. To enable them:

1. Start the bot's web dashboard (`python app.py`)
2. In the **Control Panel** on the left, you'll see a **"Telegram Notifications"** checkbox
3. Check the box to **enable** notifications
4. Click the **"Test"** button to send a test message and verify everything works
5. You should receive a test message in your Telegram chat

### What Notifications Will You Receive?

| Event | Message |
|-------|---------|
| Bot starts | "Bot started — engine, account mode" |
| Bot stops | "Bot stopped" |
| Trade opened | "BUY/SELL 0.08 lots @ 3059.71 — SL: 3055.00 — TP: 3070.00" |
| Trade closed | "Closed position #12345 — P&L result" |
| Circuit breaker triggered | "Trading halted — reason (daily loss / drawdown)" |
| AI decision (if notable) | AI's action and reasoning summary |

### Telegram Interactive Commands

Once your bot token and chat ID are configured, the bot automatically starts listening for commands in your Telegram chat. You can control the bot remotely without opening the web dashboard.

| Command | What It Does |
|---------|--------------|
| `/start_trading` | Starts the trading bot (enables Telegram notifications automatically) |
| `/stop_trading` | Stops the trading bot |
| `/status` | Full status overview — running state, engine, strategy, account info, open positions count, daily P&L |
| `/current` | Lists all open positions with live ticket, type, volume, entry price, current price, and P&L |
| `/balance` | Shows live account balance, equity, free margin, and unrealised P&L |
| `/close` | Emergency close — immediately closes ALL open positions |
| `/help` | Shows the list of available commands |

> **Note:** Commands only work when sent from the Telegram chat ID configured in your `.env` file. Messages from other users are ignored for security.

</details>

---

## Trading Strategies

<details>
<summary>Show strategy overview</summary>

The bot ships with **5 built-in trading strategies**. Each strategy shapes the AI's decision-making by giving it a different system prompt with specific rules, entry/exit criteria, and risk parameters.

### Volatility Breakout 🚀

Trades explosive momentum when price breaks out of a tight range. The bot monitors volatility — when it has been unusually low and price suddenly smashes through the period high or low with news confirmation, it enters to ride the wave. Best during high-impact news events or session opens. Expects big moves, uses wider TP targets.

### Scalping ⚡

Quick in-and-out trades targeting small, frequent profits. The bot looks for short-term momentum on M5 candles, uses M15 as secondary structure confirmation, and keeps tighter stop losses and take profits than the broader strategies. The prompt is now session-aware: outside peak liquidity it expects tighter, cleaner setups; during London/New York overlap it can allow slightly more room. Best during London/NY overlap when liquidity is highest.

### Micro Scalping 🎯

Ultra-fast small-account scalping focused on tiny M5 bursts. This mode exists separately from normal scalping so small accounts do not force the main scalping strategy to become too weak or too narrow. The AI looks for immediate short-lived momentum, and the fast watchdog can close tagged micro-scalp positions every second once a user-defined account-profit target is reached.

### Smart Money (ICT) 🏦

Trades like institutional players using Smart Money Concepts. The bot identifies liquidity sweeps (stop hunts), order blocks, fair value gaps, and break-of-structure patterns. It waits for price to grab liquidity, then enters in the opposite direction. Requires patience — fewer trades, but higher conviction.

### Conservative 🛡️

Ultra-safe strategy focused on preserving capital. Only trades A+ setups with strong multi-timeframe confluence. Maximum 1 trade per day, wide stop losses, small position sizes. Rejects 90% of opportunities — patience over profit. Best for long-term steady growth with minimal drawdown.

### How to Switch Strategies

1. In the web dashboard, use the **Strategy** buttons in the Control Panel
2. Select your preferred strategy
3. Click **Start Trading** — the AI will follow the selected strategy's rules
4. You can also switch strategies via Telegram by stopping and restarting the bot

</details>

---

## Using the Web Dashboard

<details>
<summary>Show dashboard usage guide</summary>

The dashboard is designed to be simple. Here's how to use it:

### Starting the Bot

1. **Choose your AI Engine** — Click either **Claude** (purple) or **GPT** (green). This is the AI model that will analyze the market and make trading decisions.

2. **Choose your Trading Strategy** — Select a strategy from the strategy buttons: Volatility Breakout, Scalping, Micro Scalping, Smart Money (ICT), or Conservative. Each shapes how the AI analyzes the market.

3. **Choose your Account Mode:**
   - **Demo** (recommended for testing) — Select this to trade with fake money. You'll need to fill in your MT5 demo credentials:
     - **Login:** Your MT5 account number (e.g., `12345678`)
     - **Password:** Your MT5 account password
     - **Server:** Your MT5 server name (e.g., `MetaQuotes-Demo`)
   - **Live** — Select this to trade with real money. This uses whatever account is currently logged in on your MetaTrader 5 desktop app. **Only use this if you know what you're doing.**

4. **Choose your Trading Sessions** — Use the session checkboxes to decide which market windows can open new trades. You can select one session or multiple sessions.

5. **If using Micro Scalping, set the quick-profit target** — The dashboard exposes a Micro Scalping Quick Profit Target (%) input. The fast watchdog will only use this target for `micro_scalping` positions.

6. **Optional: Set Safety Override Checkboxes** — If you want the bot to keep evaluating trades even when a local pre-AI blocker would normally force HOLD, stop the bot first and enable the specific override checkbox you want. These overrides only bypass local pre-AI filters. They do **not** disable execution-time risk controls such as lot validation, spread enforcement during order placement, or account-aware trade rejection.

7. **Click "Start Trading"** — The bot will connect to MetaTrader 5, start fetching market data, and begin its strategy-aligned cycle.

### Stopping the Bot

Click the **"Stop Trading"** button. The bot will finish its current cycle and stop gracefully within a few seconds.

</details>

---

## Understanding the Dashboard

<details>
<summary>Show dashboard section reference</summary>

| Section | What It Shows |
|---------|---------------|
| **Control Panel** (left) | AI engine selector, model selector, strategy buttons, micro-scalping profit target, trading session checkboxes, safety override checkboxes, account mode, start/stop buttons, close-all panic button, Telegram toggle, session status, daily P&L, and system info |
| **Circuit Breaker Banner** (top, red) | Appears only when a safety limit is triggered (daily loss or drawdown). Disappears when the bot is restarted. |
| **Market Data** (top center) | Current gold price, price change, period high/low, and volatility |
| **AI Decision** (top right) | The AI's latest trading decision — BUY, SELL, HOLD, CLOSE_*, or REDUCE_* — plus confidence level, lot size, stop loss, take profit, and the AI's reasoning |
| **Account Info** | Balance, equity, margin, free margin, unrealised P&L, and leverage |
| **Open Positions** | Live view of all currently open positions with ticket, type, volume, entry/current price, and P&L |
| **Price Chart (M15)** | Live candlestick chart showing the most recent M15 candles, updated every 15 seconds |
| **Equity Curve** | Balance and equity plotted over time — see your account performance visually |
| **Trade History** (middle) | A table of all decisions the bot has made during this session |
| **Trade History Context** | Recent rows now also show market phase and calendar status so you can see what the memory/risk layers were seeing |
| **Memory Insights** | Compact memory lessons plus reviewed HOLD outcomes so you can see whether similar contexts usually rewarded patience, avoided noise, or missed a directional move |
| **Trade Stats** | Summary counters: total decisions, executed orders, buys, sells, holds, and closes |
| **Live Logs** (bottom) | Real-time log output so you can see exactly what the bot is doing at every step |

</details>

---

## How the Bot Works

<details>
<summary>Show runtime flow</summary>

On each strategy decision boundary, the bot runs this cycle:

```
Step 1 → Safety checks + equity snapshot
         • Check open positions + account balance
         • Record equity snapshot to SQLite (for equity curve chart)
         • Daily loss limit check (halt if account drops 5% today)
         • Daily profit target (stop trading if +3% daily profit reached)
         • Drawdown circuit breaker (halt if equity drops 20% below balance)

Step 2 → Apply trailing stops + break-even
         • For each open position in profit, move SL closer to lock gains
         • If spread is abnormally wide, the stop manager becomes more conservative
         • If spread spikes hard, trailing and break-even updates pause until conditions cool off
         • Break-even stop: move SL to entry price (+10 pts by default) when profit ≥ 200 pts

Step 3 → Market status + Session filter
         • Check if the market is open and actually tradable
         • Uses MT5 symbol state, tick freshness, bar freshness, and extreme-spread detection to catch brokers that leave trade_mode open while the feed is effectively unusable
         • Skip trading entirely if market is closed or effectively unavailable (weekends, holidays, frozen feed, extreme spread)
         • Check if current time is inside any user-selected session
         • Session windows are timezone-aware, so Sydney, Asia, London, and New York adjust automatically to local market time
         • Skip opening new trades if outside the selected sessions (positions still managed)

Step 4 → Fetch multi-timeframe market data from MetaTrader 5
         • Primary intraday candles from the selected strategy cadence (M5 for scalping and micro_scalping, M15 otherwise)
         • M15 candles (last ~1 day) — for secondary intraday structure
         • H1  candles (last ~1 day) — for intermediate confirmation
         • H4  candles (last ~1 day) — for macro trend direction

Step 5 → Fetch news + blackout check
         • Fetch latest gold/macro news headlines from NewsAPI
         • News articles are cached in memory and refreshed on a dynamic schedule
         • No open positions: refresh at most every 30 minutes
         • Open positions: refresh at most every 15 minutes
         • Only fetches new articles since last check (incremental)
         • Falls back to cached articles if the API request fails
         • Checks a structured economic calendar layer with Finnhub first and a cached Forex Factory snapshot fallback for scheduled USD events
         • Check for high-impact event keywords (FOMC, NFP, CPI, etc.)
         • Headline blackouts only apply to recent articles and can decay into advisory mode after repeated persistence

Step 6 → Centralized risk gate + AI analysis
         • Ordered blockers now run through one centralized risk-gate surface before the AI call
         • Skips the AI entirely when market/session/account/calendar/local setup filters already know the answer should be HOLD
         • Reuses the last HOLD if market/news/account context is materially unchanged
         • Refreshes reviewed HOLD memory when enough later candles exist, so old HOLD decisions can be labeled as correct, noisy-but-protective, or missed-move cases
         • Sends a compact payload: summarized candles, top news items, positions, recent decision memory, session context, calendar context, headline-blackout context, confidence calibration, and similar trade memory
         • Includes recent AI decisions + outcomes (AI memory), similar winner/loser trade memory, compact memory lessons, and reviewed HOLD outcomes
         • AI returns a structured JSON decision with action, confidence, lot size, SL, TP, and reasoning

Step 7 → Execute the AI's decision via MetaTrader 5
         • A rule-based trade-memory layer checks whether similar historical setups have underperformed
         • Trade memory can reject a setup or cut the AI's requested size before execution
         • The AI may also return `REDUCE_BUY` or `REDUCE_SELL` to trim existing exposure without fully closing
         • Validate the AI's proposed lot size against the live account balance and stop-loss distance
         • Confidence-aware risk budget: LOW uses less risk, HIGH uses the full risk budget
         • If the setup is too risky for the account, convert the trade to HOLD instead of forcing it
         • Spread check (skip if spread too wide)
         • Max open positions check (skip if already at limit)
         • Trade cooldown (wait N cycles between trades)
         • Record decision to SQLite database
         ↓
         Wait until the next strategy boundary, then repeat
```

The AI receives a strict system prompt (based on the selected trading strategy) forcing it to act as a quantitative analyst and output a structured JSON decision with: action (BUY/SELL/HOLD/CLOSE_BUY/CLOSE_SELL/REDUCE_BUY/REDUCE_SELL), confidence (LOW/MEDIUM/HIGH), lot size, stop loss, take profit, and reasoning. The bot uses a robust 3-tier JSON parser to extract the decision even if the AI wraps it in extra text.

The AI still decides the trade idea, but the execution layer now validates whether that exact proposal fits the current account. If a setup needs too much risk for the balance, or the safe lot would fall below the broker minimum, the bot rejects the trade and records a HOLD instead of forcing an unsafe order.

</details>

## Backtesting and Regression Tests

<details>
<summary>Show backtesting and test details</summary>

The project now includes both a backtesting engine and a small regression suite to keep live and simulated behavior closer together.

### Backtesting

The backtester in `backtesting/engine.py` now models several live-bot behaviors more closely:

1. Strategy-aware decision timeframe (`M5` for `scalping` and `micro_scalping`, `M15` otherwise)
2. Session-aware spread estimates
3. Slippage-aware simulated fills
4. Live-style pre-AI and pre-execution risk gating
5. Micro-scalping quick-profit approximation

### Regression Tests

Run the current regression suite with:

```
python -m unittest discover -s tests -v
```

The tests currently cover:

1. Market-unavailable blocking
2. Spread-spike entry blocking
3. Backtest spread realism
4. Micro-scalping quick-profit behavior
5. False close-action execution prevention
6. Trade-memory lessons and reviewed HOLD outcome scoring

</details>

---

## Live Trading vs Demo Trading

<details>
<summary>Show live vs demo comparison</summary>

| | Demo | Live |
|---|------|------|
| **Money** | Fake (virtual funds) | Real |
| **Risk** | Zero — no real money at stake | You can lose real money |
| **Purpose** | Testing the bot, learning, evaluating performance | Actual trading |
| **How to use** | Select "Demo" and enter your demo credentials in the dashboard | Select "Live" — uses the account open in your MT5 desktop app |
| **Recommendation** | **Start here.** Run the bot for at least a few days on demo before considering live. | Only if you fully understand the risks |

</details>

---

## Risk Controls

<details>
<summary>Show risk controls</summary>

The bot has **hard-coded safety limits** that the AI **cannot override**:

| Control | Value | Description |
|---------|-------|-------------|
| **MAX_ALLOWED_LOT** | 0.1 | The AI can never open a position larger than 0.1 lots |
| **MIN_ALLOWED_LOT** | 0.01 | Minimum position size is 0.01 lots (micro lot) |
| **MAX_OPEN_POSITIONS** | 3 | Maximum number of simultaneous open positions |
| **DAILY_LOSS_LIMIT** | 5% | If account balance drops 5% from the day's starting balance, trading halts automatically |
| **DAILY_PROFIT_TARGET** | 3% | If daily profit reaches +3%, the bot stops opening new trades for the day |
| **DRAWDOWN_CIRCUIT_BREAKER** | 20% | If equity drops 20% below balance, the circuit breaker triggers and trading halts |
| **MAX_SPREAD** | 50 points | If the current spread exceeds 50 points, the trade is skipped to avoid slippage |
| **TRADE_COOLDOWN** | 2 cycles | Minimum 2 strategy cycles between consecutive trades |
| **TRAILING_STOP** | 300/150 pts | When a position gains 300 points of profit, the stop loss trails 150 points behind |
| **BREAK-EVEN SL** | 200/10 pts | When profit reaches 200 points, SL moves to entry price + 10 points |
| **RISK PER TRADE** | 1.5% | Each trade risks 1.5% of account balance (auto lot sizing) |
| **CONFIDENCE-BASED RISK** | 25% / 50% / 100% | LOW confidence uses 25% of the base risk budget, MEDIUM uses 50%, HIGH uses 100% |
| **SESSION FILTER** | User-selectable | Only opens new trades during the session or sessions selected in the dashboard; Sydney, Asia, London, and New York windows are timezone-aware |
| **CENTRAL RISK GATE** | Automatic | Ordered blocker system with explicit reasons shared across pre-AI screening and pre-execution screening |
| **ECONOMIC CALENDAR** | Optional | Uses Finnhub to block or reduce risk around scheduled USD events such as FOMC, NFP, CPI, GDP, and similar releases |
| **CALENDAR FALLBACK CACHE** | Automatic | Falls back to a cached Forex Factory XML snapshot when Finnhub is unavailable or rate-limited |
| **NEWS BLACKOUT** | Auto | Pauses trading when recent high-impact events are detected in live headlines; works beside the structured calendar layer |
| **NEWS BLACKOUT DECAY** | Automatic | Repeated headline-only blackouts can degrade into advisory mode after a configurable number of blocked iterations and minimum article age |
| **NEWS CACHING** | 15-30 min | News articles are cached in-memory; API refresh is slower when flat and faster when positions are open |
| **LOCAL SETUP FILTER** | Automatic | Skips the AI call entirely when spread, volatility, liquidity, or account conditions already make a trade unrealistic |
| **LOCAL FILTER OVERRIDES** | User-selectable | Lets you bypass specific local pre-AI blockers from the dashboard after explicitly opting in; execution risk controls still remain active |
| **UNCHANGED-CONDITION CACHE** | Automatic | Reuses the last HOLD when market/news/account context has not materially changed |
| **TRADE MEMORY** | Automatic | Learns from executed trades in SQLite, can block repeated weak setups, stores reflections, summarizes winners vs losers, and now reviews older HOLD decisions when enough future bars exist |
| **REVIEWED HOLD MEMORY** | Automatic | Labels older HOLD decisions as patience-correct, noise-avoiding, or missed-move outcomes and feeds that back into future AI context |
| **FAST WATCHDOG** | 1-2 sec | Runs independently of the main AI loop for spread spikes, quick micro-scalp exits, trailing stops, break-even logic, and market-availability monitoring |
| **COMPACT AI PAYLOAD** | Automatic | Sends summarized market/news/history data to reduce token usage per AI request |
| **MARKET CLOSED DETECTION** | Auto | Uses MT5 symbol state plus stale-tick, stale-bar, and extreme-spread checks to skip trading when the market is closed or effectively unavailable |
| **AI MEMORY** | Last 5 | AI receives its last 5 decisions + outcomes to learn from recent performance |
| **CONFIDENCE CALIBRATION** | Automatic | Recent decision outcomes are summarized and fed back into the AI context so confidence levels can self-correct over time |
| **EXECUTION ANALYTICS** | Automatic | Tracks fills, slippage, spread-at-decision vs spread-at-execution, and latency in SQLite for later analysis |
| **MAX_DEVIATION** | 10 points | Maximum price slippage allowed on order execution |
| **ACCOUNT-AWARE EXECUTION GUARD** | Automatic | Rejects AI proposals that are too risky for the current balance + stop-loss distance and converts them to HOLD |
| **LOCAL DASHBOARD SECURITY** | Automatic | Loopback-only API access, token-guarded state-changing requests, security headers, and encrypted saved credentials |
| **Halt Trading** | AI-triggered | If the AI detects extreme uncertainty, it can flag `halt_trading: true` and skip the cycle |
| **Fallback to HOLD** | Automatic | If the AI's response can't be parsed (bad JSON, API error, etc.), the bot automatically defaults to HOLD with halt_trading=true |
| **Close All (Panic Button)** | Manual | Dashboard button to immediately close all open positions |

These limits are defined in `config/settings.py`. You can adjust them if needed, but the defaults are conservative.

</details>

---

## Troubleshooting

<details>
<summary>Show troubleshooting</summary>

### "MT5 initialize() failed"
- Make sure MetaTrader 5 is **installed** on your computer
- If using **Live** mode, make sure the MT5 desktop app is open and logged into your account
- If using **Demo** mode, make sure your login, password, and server are correct
- MetaTrader 5 only works on **Windows**

### "Failed to get tick for XAUUSD"
- Your broker might use a different symbol name for gold. Common variants: `XAUUSD`, `GOLD`, `XAUUSDm`, `XAUUSD.raw`
- Open MetaTrader 5 → Market Watch → right-click → Symbols → search for "gold" to find the exact name
- Update the `SYMBOL` value in `config/settings.py` to match

### "Anthropic API connection error" or "OpenAI API connection error"
- Check that your API key is correct in the `.env` file
- Make sure you have credit/balance on your API account
- Check your internet connection

### "Rejected API request without valid local CSRF token"
- Refresh the dashboard page after restarting `app.py` so the browser picks up the new in-memory local API token
- Direct state-changing API calls are expected to fail without the local token
- Sensitive saved-credentials reads are also token-protected now

### "The bot keeps skipping because of one headline"
- Headline blackout now only applies to recent articles, not the full news cache lifetime
- Tune `.env` if needed:
   - `NEWS_BLACKOUT_MAX_AGE_HOURS`
   - `NEWS_BLACKOUT_DECAY_ITERATIONS`
   - `NEWS_BLACKOUT_DECAY_MIN_AGE_HOURS`
- Structured economic calendar `BLOCK` windows remain separate and do not decay into AI control

### "NewsAPI HTTP error"
- Check that your NEWS_API_KEY is correct in the `.env` file
- The free tier of NewsAPI has a limit of 100 requests per day — if you exceed it, news data will be empty (the bot will still work, just without news context)

### The bot says "HOLD" every time
- This is normal if the market conditions are unclear
- The AI is being cautious, which is the safe default behavior
- If this persists, check whether the centralized risk gate, market-unavailable detection, spread protection, or trade-memory layer is intentionally blocking analysis
- Review the dashboard's Trade Memory and Memory Insights panels to see whether similar contexts are being labeled as patience-favored or structurally weak

### The web dashboard doesn't load
- Make sure `python app.py` is running in your terminal
- Open your browser to exactly: `http://127.0.0.1:5000`
- Don't close the terminal window — the bot runs from there

### "ModuleNotFoundError: No module named '...'"
- You need to install the dependencies. Run:
  ```
   pip install python-dotenv anthropic openai requests pandas numpy MetaTrader5 flask tzdata
  ```

</details>

---

## Project Structure

<details>
<summary>Show project structure</summary>

```
tradingbot/
├── app.py                     ← Web dashboard + trading engine (run this)
├── .env                       ← Your API keys + Telegram credentials (edit this file)
├── README.md                  ← This file
├── config/
│   ├── settings.py            ← Risk limits, symbol, intervals, safety thresholds
│   └── strategies.py          ← 5 trading strategy definitions + AI system prompts
├── data/
│   ├── market_data.py         ← Fetches M5/M15/H1/H4 candles from MetaTrader 5
│   ├── news_data.py           ← Fetches gold/macro news from NewsAPI (with in-memory caching)
│   ├── trade_db.py            ← SQLite persistent trade history + equity snapshots
│   └── trade_memory.py        ← Similar-trade memory, setup evaluation, reflections, and reviewed HOLD outcomes
├── ai_engine/
│   ├── claude_client.py       ← Claude (Anthropic) AI integration
│   ├── gpt_client.py          ← GPT (OpenAI) AI integration
│   └── payload_utils.py       ← Compact AI payload + condition fingerprint helpers
├── backtesting/
│   ├── __init__.py
│   └── engine.py              ← Live-style historical replay / backtesting engine
├── execution/
│   ├── order_manager.py       ← Risk clamping, order execution, reductions, trailing/break-even stops
│   ├── risk_manager.py        ← Centralized pre-AI and pre-execution gating
│   └── session_filter.py      ← Session-aware trading + hardened market-availability detection + news blackout filter
├── notifications/
│   ├── __init__.py
│   └── telegram.py            ← Telegram notifications + interactive command listener
├── tests/
│   ├── test_backtesting_engine.py ← Backtesting regression tests
│   ├── test_risk_manager.py       ← Risk gate regression tests
│   ├── test_session_filter.py     ← Market-availability regression tests
│   └── test_trade_memory.py       ← Trade-memory regression tests
├── templates/
│   └── index.html             ← Web dashboard HTML
└── static/
    ├── css/style.css           ← Dashboard styling
    └── js/app.js               ← Dashboard frontend logic (charts, polling, UI)
```

</details>
