# KUBERA - Complete Project Documentation

> **For Beginners**: This document explains the entire KUBERA project in simple terms. Even if you've never seen this codebase before, by the end of this document, you'll understand exactly how everything works.

---

## Table of Contents

1. [What is KUBERA?](#what-is-kubera)
2. [High-Level Architecture](#high-level-architecture)
3. [Technology Stack](#technology-stack)
4. [How the Application Works](#how-the-application-works)
5. [Frontend (React)](#frontend-react)
6. [Backend (FastAPI)](#backend-fastapi)
7. [Database Design](#database-design)
8. [AI/LLM Integration](#aillm-integration)
9. [Complete User Flows](#complete-user-flows)
10. [API Reference](#api-reference)
11. [Deployment](#deployment)

---

## What is KUBERA?

KUBERA is an **AI-powered Indian stock market research assistant**. Think of it as ChatGPT specifically designed for analyzing stocks listed on NSE (National Stock Exchange) and BSE (Bombay Stock Exchange).

### What Can Users Do?

| Feature | Description |
|---------|-------------|
| 💬 **Chat with AI** | Ask questions about any Indian stock in natural language |
| 📊 **Get Stock Data** | Fetch real-time prices, fundamentals, financials, news |
| 📈 **View Charts** | AI automatically generates interactive charts |
| 📁 **Track Portfolio** | Add stocks to personal portfolio and track performance |
| 👤 **User Accounts** | Register, login, manage profile |
| 🔐 **Admin Panel** | Dashboard for managing users and system settings |

### Important Disclaimer
KUBERA is an **informational tool only**. It does NOT provide investment advice or stock recommendations.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            USER (Browser)                                   │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        FRONTEND (React + Vite)                              │
│                                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │   Landing   │ │    Login    │ │    Chat     │ │      Profile        │   │
│  │    Page     │ │  Register   │ │    Page     │ │   Portfolio Page    │   │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────────────┘   │
│                                                                             │
│                    ┌─────────────────────────────┐                          │
│                    │        API Layer            │                          │
│                    │  api.ts + adminApi.ts       │                          │
│                    └─────────────────────────────┘                          │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │
                   ┌─────────────┴─────────────┐
                   │                           │
              HTTP REST                   WebSocket
              (APIs)                      (Real-time Chat)
                   │                           │
                   ▼                           ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        BACKEND (FastAPI + Python)                           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                         main.py (Entry Point)                        │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│            ┌───────────────────────┼───────────────────────┐               │
│            │                       │                       │               │
│            ▼                       ▼                       ▼               │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐        │
│  │   REST Routes   │    │    WebSocket    │    │    Background   │        │
│  │  /auth, /user   │    │   Chat Handler  │    │    Scheduler    │        │
│  │  /portfolio...  │    │                 │    │   (APScheduler) │        │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘        │
│            │                       │                                        │
│            ▼                       ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    Services Layer (Business Logic)                   │   │
│  │         auth_service, chat_service, portfolio_service, etc.         │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│            │                       │                                        │
│            ▼                       ▼                                        │
│  ┌─────────────────┐    ┌─────────────────────────────────────────────┐   │
│  │   Repositories  │    │           LLM + MCP Integration             │   │
│  │  (Database CRUD)│    │   ┌───────────────────────────────────┐     │   │
│  └─────────────────┘    │   │  OpenRouterAPI-openai/gpt-4o-mini │     │   │
│            │            │   └───────────────────────────────────┘     │   │
│            │            │                     │                       │   │
│            │            │   ┌─────────────────▼─────────────────┐     │   │
│            │            │   │        5 MCP Tool Servers         │     │   │
│            │            │   │  (45 specialized stock tools)     │     │   │
│            │            │   └───────────────────────────────────┘     │   │
│            │            └─────────────────────────────────────────────┘   │
│            ▼                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    PostgreSQL (Supabase)                             │   │
│  │                      15 Database Tables                              │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Technology Stack

### Frontend

| Technology | Purpose |
|------------|---------|
| **React 18** | UI framework |
| **TypeScript** | Type-safe JavaScript |
| **Vite** | Fast build tool |
| **TailwindCSS** | Utility-first CSS |
| **React Router** | Page navigation |
| **TanStack Query** | Server state management |
| **WebSocket** | Real-time communication |

### Backend

| Technology | Purpose |
|------------|---------|
| **Python 3.11** | Programming language |
| **FastAPI** | Web framework (async) |
| **asyncpg** | PostgreSQL driver (async) |
| **Pydantic** | Data validation |
| **APScheduler** | Background job scheduler |
| **aiosmtplib** | Async email sending |
| **OpenRouter** | LLM API gateway |
| **FastMCP** | MCP server framework |

### Database & Storage

| Technology | Purpose |
|------------|---------|
| **PostgreSQL** | Main database (via Supabase) |
| **Supabase Storage** | Chart HTML file storage |

### AI/LLM

| Technology | Purpose |
|------------|---------|
| **OpenRouter** | API gateway for LLM |
| **GPT-4o-mini** | LLM model for chat (via OpenRouter) |
| **MCP (Model Context Protocol)** | Tool framework for LLM |

---

## How the Application Works

### The Big Picture

1. **User opens the app** → React frontend loads
2. **User registers/logs in** → Frontend calls backend auth APIs
3. **User sends a chat message** → Message sent via WebSocket
4. **Backend receives message** → Passes to LLM (GPT-4o-mini)
5. **LLM decides what tools to use** → Calls MCP tool servers
6. **Tools fetch stock data** → From Yahoo Finance, news APIs, etc.
7. **LLM generates response** → Streamed back to frontend
8. **Frontend displays response** → With charts if generated

### Data Flow for a Stock Query

```
User: "Tell me about Reliance stock"
          │
          ▼
┌─────────────────────┐
│  Frontend (Chat.tsx) │
│  sends WebSocket msg │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  Backend WebSocket Handler                   │
│  1. Validates JWT token                      │
│  2. Checks rate limits (4 levels)            │
│  3. Saves user message to database           │
│  4. Passes to LLM Orchestrator               │
└──────────┬──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  LLM Orchestrator (llm_integration.py)       │
│  1. Builds prompt with system instructions   │
│  2. Calls OpenRouter API with tools list     │
└──────────┬──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  OpenRouter / GPT-4o-mini                    │
│  Decides: "I need to fetch stock data"       │
│  Returns: tool_calls = [                     │
│    "fetch_company_fundamentals",             │
│    "fetch_current_price_data",               │
│    "generate_price_volume_chart"             │
│  ]                                           │
└──────────┬──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  MCP Tool Handler                            │
│  Executes each tool via MCP servers:         │
│  • fin_data.py → Yahoo Finance API           │
│  • market_tech.py → Technical indicators     │
│  • visualization.py → Plotly chart           │
└──────────┬──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  LLM generates final response                │
│  "Reliance Industries is trading at ₹2,450. │
│   Here's what the data shows..."             │
│  + chart_url from Supabase Storage           │
└──────────┬──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│  Response streamed to frontend               │
│  User sees: text chunks + interactive chart  │
└─────────────────────────────────────────────┘
```

---

## Frontend (React)

### Project Structure

```
kubera-frontend/kubera-wealth-insights/src/
├── App.tsx                    # Main app with routes
├── main.tsx                   # Entry point
├── index.css                  # Global styles
│
├── pages/                     # Page components
│   ├── Landing.tsx            # Home page (/)
│   ├── Login.tsx              # Login page (/login)
│   ├── Register.tsx           # 3-step registration (/register)
│   ├── ForgotPassword.tsx     # Password reset (/forgot-password)
│   ├── Chat.tsx               # Main chat interface (/chat)
│   ├── Profile.tsx            # User profile (/profile)
│   └── admin/
│       ├── AdminLogin.tsx     # Admin login (/admin-kubera)
│       └── AdminDashboard.tsx # Admin panel (/admin-kubera/dashboard)
│
├── components/                # Reusable components
│   ├── chat/
│   │   ├── ChatSidebar.tsx    # Chat history sidebar
│   │   ├── ChatMessage.tsx    # Individual message component
│   │   └── ChatInput.tsx      # Message input box
│   ├── layout/
│   │   └── Layout.tsx         # Main layout wrapper
│   └── ui/                    # UI components (buttons, inputs, etc.)
│
├── contexts/
│   ├── AuthContext.tsx        # Authentication state management
│   └── ThemeContext.tsx       # Dark/light theme
│
├── hooks/
│   ├── useChatWebSocket.ts    # WebSocket connection hook
│   └── use-toast.ts           # Toast notifications
│
└── lib/
    ├── api.ts                 # All backend API calls
    ├── adminApi.ts            # Admin-specific API calls
    └── utils.ts               # Utility functions
```

### Key Frontend Files Explained

#### 1. `api.ts` - API Communication Layer

This file handles ALL communication with the backend.

```typescript
// Base URLs
const API_BASE = 'http://localhost:8000';   // REST API
const WS_BASE = 'ws://localhost:8000';       // WebSocket

// Token Management (supports "Remember Me")
getToken()          // Get JWT from storage
setTokens()         // Store JWT after login
clearTokens()       // Remove JWT on logout

// API Functions organized by feature:
authApi = {
  login()           // POST /auth/login
  registerStep1()   // POST /auth/register/step1 (send OTP)
  registerStep2()   // POST /auth/register/step2 (verify OTP)
  registerStep3()   // POST /auth/register/step3 (complete)
  logout()          // POST /auth/logout
  // ...more
}

userApi = {
  getProfile()      // GET /user/profile
  updateProfile()   // PUT /user/profile
  changePassword()  // PUT /user/password
  // ...more
}

portfolioApi = {
  getPortfolio()    // GET /portfolio/
  addEntry()        // POST /portfolio/
  deleteEntry()     // DELETE /portfolio/{id}
  // ...more
}

chatsApi = {
  getChats()        // GET /chats/
  getChat()         // GET /chats/{chat_id}
  createChat()      // POST /chats/
  deleteChat()      // DELETE /chats/{chat_id}
  // ...more
}
```

#### 2. `AuthContext.tsx` - User Authentication State

Manages login state across the entire app:

```typescript
// Provides these values/functions to all components:
{
  user,              // Current logged-in user
  isAuthenticated,   // true if logged in
  isLoading,         // true while checking auth
  
  login(),           // Log user in
  registerStep1(),   // Start registration
  registerStep2(),   // Verify OTP
  registerStep3(),   // Complete registration
  logout(),          // Log user out
}
```

#### 3. `useChatWebSocket.ts` - Real-time Chat Communication

Custom React hook that manages WebSocket connection:

```typescript
// What it provides:
{
  isConnected,       // WebSocket connected?
  isStreaming,       // Currently receiving response?
  streamingContent,  // Partial response being streamed
  toolStatus,        // Which tools are being executed
  rateLimits,        // User's rate limit status
  
  sendMessage(),     // Send a message to AI
  reconnect(),       // Reconnect if disconnected
}

// WebSocket message types it handles:
'connected'        → Connection confirmed
'chunk'            → Text chunk being streamed
'tool_executing'   → AI is calling a tool
'tool_complete'    → Tool finished
'message_complete' → Full response ready
'rate_limit'       → Rate limit exceeded
'error'            → Error occurred
```

#### 4. `Chat.tsx` - Main Chat Interface

The heart of the application:

```
┌──────────────────────────────────────────────────────────────┐
│  ┌─────────────────┐  ┌──────────────────────────────────┐  │
│  │                 │  │                                  │  │
│  │   ChatSidebar   │  │         Chat Messages            │  │
│  │                 │  │                                  │  │
│  │  • New Chat     │  │  [User]: Tell me about TCS      │  │
│  │  • Chat 1       │  │                                  │  │
│  │  • Chat 2       │  │  [AI]: TCS is trading at ₹3800  │  │
│  │  • Chat 3       │  │        [📊 View Chart]          │  │
│  │                 │  │                                  │  │
│  │                 │  │  ┌────────────────────────────┐  │  │
│  │                 │  │  │   Type your message...    │  │  │
│  │                 │  │  │                      [➤]  │  │  │
│  │                 │  │  └────────────────────────────┘  │  │
│  └─────────────────┘  └──────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

---

## Backend (FastAPI)

### Project Structure

```
kubera-backend/
├── main.py                     # Entry point, app initialization
├── requirements.txt            # Python dependencies
│
├── app/
│   ├── core/                   # Core utilities
│   │   ├── config.py           # Environment variables & settings
│   │   ├── database.py         # Database connection pool
│   │   ├── security.py         # JWT, password hashing, OTP
│   │   └── dependencies.py     # FastAPI dependencies
│   │
│   ├── api/
│   │   ├── routes/             # REST API endpoints
│   │   │   ├── auth_routes.py  # /auth/* (11 endpoints)
│   │   │   ├── user_routes.py  # /user/* (7 endpoints)
│   │   │   ├── portfolio_routes.py  # /portfolio/* (5 endpoints)
│   │   │   ├── chat_routes.py  # /chats/* (5 endpoints)
│   │   │   └── admin_routes.py # /admin/* (19 endpoints)
│   │   └── websockets/
│   │       ├── chat_websocket.py   # WebSocket handler
│   │       └── llm_service.py      # LLM streaming service
│   │
│   ├── services/               # Business logic
│   │   ├── auth_service.py     # Authentication logic
│   │   ├── user_service.py     # User operations
│   │   ├── chat_service.py     # Chat operations
│   │   ├── portfolio_service.py # Portfolio operations
│   │   ├── email_service.py    # Email sending (15+ triggers)
│   │   ├── rate_limit_service.py # Rate limiting logic
│   │   └── admin_service.py    # Admin operations
│   │
│   ├── db/
│   │   └── repositories/       # Database operations (CRUD)
│   │       ├── user_repository.py
│   │       ├── chat_repository.py
│   │       ├── portfolio_repository.py
│   │       └── ... (9 repositories total)
│   │
│   ├── mcp/                    # AI/LLM Integration
│   │   ├── client.py           # MCP client (connects to tool servers)
│   │   ├── config.py           # MCP server configuration
│   │   ├── llm_integration.py  # LLM orchestrator (agentic loop)
│   │   └── tool_handler.py     # Tool execution handler
│   │
│   ├── background/
│   │   └── scheduler.py        # APScheduler (4 background jobs)
│   │
│   ├── schemas/                # Request/Response schemas
│   │   ├── requests/
│   │   └── responses/
│   │
│   └── exceptions/
│       └── custom_exceptions.py # 25+ custom exceptions
│
└── mcp_servers/                # MCP Tool Servers (5 servers)
    ├── fin_data.py             # Financial data (7 tools)
    ├── market_tech.py          # Technical analysis (9 tools)
    ├── gov_compliance.py       # Governance data (8 tools)
    ├── news_sent.py            # News & sentiment (10 tools)
    └── visualization.py        # Chart generation (11 tools)
```

### Key Backend Concepts

#### 1. Application Startup (`main.py`)

When the server starts, these things happen in order:

```python
async def lifespan(app):
    # STARTUP
    1. init_db()                    # Create database connection pool
    2. kubera_mcp_client.initialize() # Connect to 5 MCP servers
    3. background_scheduler.start()   # Start 4 background jobs
    
    yield  # Server runs here
    
    # SHUTDOWN
    4. Close MCP connections
    5. Close database pool
    6. Stop scheduler
```

#### 2. Routes → Services → Repositories Pattern

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│     Routes      │───▶│    Services     │───▶│  Repositories   │
│  (HTTP Layer)   │    │ (Business Logic)│    │ (Database CRUD) │
└─────────────────┘    └─────────────────┘    └─────────────────┘
        │                      │                      │
  Handles HTTP           Validates data,         Executes SQL
  requests, sends         applies rules,         queries against
  responses               coordinates            PostgreSQL
                          operations
```

**Example flow for "Get User Profile":**

```
GET /user/profile
       │
       ▼
┌─────────────────────────────────────┐
│  user_routes.py                     │
│  @router.get("/profile")            │
│  async def get_profile(user):       │
│      return user_service.get_profile│
└───────────────┬─────────────────────┘
                │
                ▼
┌─────────────────────────────────────┐
│  user_service.py                    │
│  async def get_profile(user_id):    │
│      user = await user_repo.get_... │
│      return format_user_profile()   │
└───────────────┬─────────────────────┘
                │
                ▼
┌─────────────────────────────────────┐
│  user_repository.py                 │
│  async def get_by_id(user_id):      │
│      return await db.fetch_one(     │
│          "SELECT * FROM users..."   │
│      )                              │
└─────────────────────────────────────┘
```

#### 3. Rate Limiting (4 Levels)

Every chat message is checked against 4 rate limits:

```
Level 1: BURST       → 10 messages per minute
Level 2: PER-CHAT    → 50 messages per chat session  
Level 3: HOURLY      → 150 messages per hour
Level 4: DAILY       → 1000 messages per 24 hours

Check order: Burst → Per-Chat → Hourly → Daily
(Fails fast on first violation)
```

#### 4. Background Jobs (APScheduler)

| Job | Frequency | What it Does |
|-----|-----------|-------------|
| Portfolio Price Update | Every 30 min | Fetches latest prices for all stocks in users' portfolios |
| Portfolio Reports | Daily 9 AM | Sends portfolio summary emails to users |
| Cleanup OTPs | Every hour | Deletes expired OTP records |
| Cleanup Tokens | Every 6 hours | Removes revoked refresh tokens |

---

## Database Design

### Entity Relationship Diagram

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│    users     │       │    chats     │       │   messages   │
├──────────────┤       ├──────────────┤       ├──────────────┤
│ user_id (PK) │◄──────│ user_id (FK) │       │ message_id   │
│ email        │       │ chat_id (PK) │◄──────│ chat_id (FK) │
│ username     │       │ chat_name    │       │ user_message │
│ password_hash│       │ prompt_count │       │ assistant_   │
│ full_name    │       │ created_at   │       │   response   │
│ account_status       │ updated_at   │       │ chart_url    │
│ ...          │       └──────────────┘       │ created_at   │
└──────────────┘                              └──────────────┘
       │
       │       ┌──────────────┐
       │       │  portfolio   │
       │       ├──────────────┤
       └──────▶│ user_id (FK) │
               │ portfolio_id │
               │ stock_symbol │
               │ quantity     │
               │ buy_price    │
               │ current_price│
               │ gain_loss    │
               └──────────────┘
```

### All 15 Tables

| Table | Purpose |
|-------|---------|
| `users` | User accounts (email, username, password hash) |
| `chats` | Chat sessions per user |
| `messages` | Chat messages (user + AI responses) |
| `portfolio` | User stock holdings |
| `otp_codes` | OTP verification codes |
| `refresh_tokens` | JWT refresh tokens |
| `admins` | Admin accounts |
| `admin_activity_logs` | Admin action audit trail |
| `email_logs` | Email sending records |
| `email_preferences` | User email notification settings |
| `rate_limit_config` | Global rate limit settings |
| `rate_limit_user_overrides` | Custom limits per user |
| `rate_limit_whitelist` | Users exempt from rate limits |
| `rate_limit_violations` | Rate limit violation logs |
| `system_status` | System configuration & status |

---

## AI/LLM Integration

### How the AI Works

KUBERA uses an **agentic architecture** with the LLM at the center:

```
┌────────────────────────────────────────────────────────────────┐
│                    LLM Orchestrator                            │
│              (llm_integration.py)                              │
│                                                                │
│  1. User sends: "Analyze Reliance stock"                       │
│                           │                                    │
│                           ▼                                    │
│  2. Build messages:                                            │
│     [System Prompt] + [Conversation History] + [User Message]  │
│                           │                                    │
│                           ▼                                    │
│  3. Call OpenRouter API (GPT-4o-mini)                          │
│     - Include list of 45 available tools                       │
│                           │                                    │
│                           ▼                                    │
│  4. LLM returns tool calls:                                    │
│     ["fetch_company_fundamentals", "generate_price_chart"]     │
│                           │                                    │
│                           ▼                                    │
│  5. Execute tools via MCP servers                              │
│     - Each tool fetches real data                              │
│                           │                                    │
│                           ▼                                    │
│  6. Feed tool results back to LLM                              │
│                           │                                    │
│                           ▼                                    │
│  7. LLM generates final response                               │
│     - Stream text chunks to frontend                           │
│                           │                                    │
│  8. Repeat steps 3-7 if LLM needs more tools (max 5 iterations)│
└────────────────────────────────────────────────────────────────┘
```

### MCP (Model Context Protocol)

MCP is a standard for giving LLMs access to external tools. KUBERA has 5 MCP servers:

| Server | File | Tools | Purpose |
|--------|------|-------|---------|
| Financial Data | `fin_data.py` | 7 | Fundamentals, financials, valuations |
| Market Technical | `market_tech.py` | 9 | Prices, indicators, volume analysis |
| Governance | `gov_compliance.py` | 8 | Shareholding, board, compliance |
| News & Sentiment | `news_sent.py` | 10 | News, analyst ratings, sentiment |
| Visualization | `visualization.py` | 11 | Interactive charts |

### All 45 MCP Tools

<details>
<summary><b>Click to expand full tool list</b></summary>

**Financial Data (7 tools):**
- `fetch_company_fundamentals` - PE, PB, ROE, margins
- `fetch_historical_financials` - 5-year revenue trends
- `fetch_balance_sheet` - Assets, liabilities, equity
- `fetch_cash_flow_statement` - Cash flow breakdown
- `fetch_quarterly_results` - Last 4 quarters
- `fetch_dividend_info` - Dividend history
- `fetch_valuation_metrics` - DCF, Graham number

**Market Technical (9 tools):**
- `fetch_current_price_data` - Real-time price
- `fetch_historical_price_data` - OHLCV data
- `fetch_technical_indicators` - SMA, RSI, MACD
- `fetch_volume_analysis` - Volume patterns
- `fetch_volatility_metrics` - Beta, Sharpe ratio
- `fetch_comparative_performance` - vs Nifty50
- `fetch_institutional_holding_data` - FII/DII
- `fetch_liquidity_metrics` - Volume, spreads
- `validate_technical_data` - Data quality

**Governance (8 tools):**
- `fetch_promoter_holding_data` - Promoter stakes
- `fetch_board_composition` - Board members
- `fetch_audit_quality` - Auditor info
- `fetch_regulatory_compliance` - SEBI status
- `fetch_shareholding_pattern` - Ownership breakdown
- `fetch_related_party_transactions` - RPT data
- `fetch_governance_score` - Gov. quality score
- `fetch_insider_transactions` - Insider trades

**News & Sentiment (10 tools):**
- `fetch_news_articles` - Company news
- `fetch_overall_news_sentiment` - Sentiment score
- `fetch_analyst_recommendations` - Buy/sell ratings
- `fetch_social_sentiment` - Social buzz
- `fetch_sector_news` - Sector news
- `fetch_market_news` - Market news
- `fetch_earnings_calendar` - Earnings dates
- `fetch_price_targets` - Target prices
- `fetch_trending_topics` - Hot topics
- `fetch_sentiment_timeseries` - Sentiment trend

**Visualization (11 tools):**
- `generate_price_volume_chart` - Price + volume
- `generate_candlestick_chart` - OHLC candles
- `generate_technical_indicators_chart` - RSI, MACD
- `generate_comparison_chart` - Multi-stock compare
- `generate_portfolio_composition_chart` - Pie chart
- `generate_sector_heatmap` - Sector heat
- `generate_performance_chart` - Returns
- `generate_risk_return_chart` - Scatter
- `generate_dividend_history_chart` - Dividend bars
- `generate_earnings_chart` - EPS trend
- `generate_valuation_chart` - PE history

</details>

---

## Complete User Flows

### Flow 1: User Registration (3 Steps)

```
Step 1: Enter Email
┌─────────────────────────────────────────┐
│  Frontend: Register.tsx                 │
│  User enters email → Click "Send OTP"   │
└─────────────────┬───────────────────────┘
                  │ POST /auth/register/step1
                  ▼
┌─────────────────────────────────────────┐
│  Backend: auth_routes.py                │
│  1. Check email not already registered  │
│  2. Generate 6-digit OTP                │
│  3. Hash OTP with SHA-256               │
│  4. Save to otp_codes table             │
│  5. Send email via SMTP                 │
└─────────────────────────────────────────┘

Step 2: Verify OTP
┌─────────────────────────────────────────┐
│  User enters OTP from email             │
└─────────────────┬───────────────────────┘
                  │ POST /auth/register/step2
                  ▼
┌─────────────────────────────────────────┐
│  Backend validates:                     │
│  1. OTP not expired (10 min)            │
│  2. Attempts < 3                        │
│  3. Hash matches                        │
│  4. Mark OTP as verified                │
└─────────────────────────────────────────┘

Step 3: Complete Registration
┌─────────────────────────────────────────┐
│  User enters: username, password, name  │
└─────────────────┬───────────────────────┘
                  │ POST /auth/register/step3
                  ▼
┌─────────────────────────────────────────┐
│  Backend:                               │
│  1. Verify OTP was completed            │
│  2. Check username available            │
│  3. Validate password strength          │
│  4. Hash password with bcrypt           │
│  5. Create user in database             │
│  6. Generate JWT tokens                 │
│  7. Send welcome email                  │
│  8. Return tokens to frontend           │
└─────────────────────────────────────────┘
```

### Flow 2: Chat with AI

```
┌────────────────────────────────────────────────────────────────┐
│  1. User opens Chat page                                       │
│     └─ Chat.tsx useEffect fetches chats via GET /chats/        │
│                                                                │
│  2. User selects a chat (or creates new)                       │
│     └─ useChatWebSocket connects: ws://host/ws/chat/{id}       │
│                                                                │
│  3. User types message and clicks send                         │
│     └─ sendMessage() sends: {type: "message", message: "..."}  │
│                                                                │
│  4. Backend ChatWebSocketHandler receives                      │
│     ├─ Validates JWT from query param                          │
│     ├─ Checks rate limits (4 levels)                           │
│     ├─ Saves user message to database                          │
│     └─ Calls LLMService.stream_response()                      │
│                                                                │
│  5. LLM Orchestrator processes                                 │
│     ├─ Calls OpenRouter API                                    │
│     ├─ LLM decides tool calls                                  │
│     ├─ Executes tools via MCP                                  │
│     └─ Generates response                                      │
│                                                                │
│  6. Response streamed back                                     │
│     ├─ {type: "tool_executing", tool_name: "..."}              │
│     ├─ {type: "tool_complete", tool_name: "..."}               │
│     ├─ {type: "chunk", content: "..."}  (multiple)             │
│     └─ {type: "message_complete", metadata: {chart_url: ...}}  │
│                                                                │
│  7. Frontend displays                                          │
│     ├─ Shows tool execution status                             │
│     ├─ Streams text character by character                     │
│     └─ Renders chart if generated                              │
└────────────────────────────────────────────────────────────────┘
```

---

## API Reference

### REST Endpoints Summary

| Route Group | Endpoints | Purpose |
|-------------|-----------|---------|
| `/auth` | 11 | Registration, login, logout, password reset |
| `/user` | 7 | Profile, username, password, email prefs |
| `/portfolio` | 5 | Portfolio CRUD, price updates |
| `/chats` | 5 | Chat sessions CRUD |
| `/admin` | 19 | Dashboard, user management, rate limits |
| **Total** | **47** | |

### WebSocket Messages

| Type | Direction | Purpose |
|------|-----------|---------|
| `message` | Client → Server | Send chat message |
| `ping` | Client → Server | Heartbeat |
| `connected` | Server → Client | Connection confirmed |
| `rate_limit_info` | Server → Client | Current rate limits |
| `message_received` | Server → Client | Message acknowledged |
| `tool_executing` | Server → Client | Tool started |
| `tool_complete` | Server → Client | Tool finished |
| `chunk` | Server → Client | Text chunk (streaming) |
| `message_complete` | Server → Client | Full response ready |
| `rate_limit` | Server → Client | Rate limit exceeded |
| `error` | Server → Client | Error occurred |
| `pong` | Server → Client | Heartbeat response |
| `chat_renamed` | Server → Client | Chat auto-renamed |

---

## Deployment

### Current Production Setup

| Component | Platform | URL |
|-----------|----------|-----|
| Frontend | Vercel | https://kubera-frontend-tau.vercel.app |
| Backend | Render | https://kubera-1.onrender.com |
| Database | Supabase | (PostgreSQL) |
| Storage | Supabase | (Chart HTML files) |

### Environment Variables

**Backend (.env):**
```
# Database (Supabase)
POSTGRES_HOST=aws-1-ap-southeast-1.pooler.supabase.com
POSTGRES_PORT=6543
POSTGRES_USER=postgres.rfcvgiwacvtfoacvvrmp
POSTGRES_DB=postgres

# JWT Authentication
SECRET_KEY=your-256-bit-secret
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# LLM (OpenRouter)
OPENROUTER_API_KEY=sk-or-v1-xxx
OPENROUTER_MODEL=openai/gpt-4o-mini

# Supabase (Storage for charts)
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_ANON_KEY=xxx

# Email (Gmail SMTP)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=app-password

# Stock Data APIs
ALPHA_VANTAGE_API_KEY=xxx      # 25 requests/day
FINNHUB_API_KEY=xxx            # 60 calls/minute
MARKETAUX_API_KEY=xxx          # 100 requests/day (news + sentiment)
NEWSAPI_KEY=xxx                # 100 requests/day
INDIAN_API_KEY=xxx             # NSE/BSE specific data

# Rate Limiting
RATE_LIMIT_BURST=10
RATE_LIMIT_PER_CHAT=50
RATE_LIMIT_PER_HOUR=150
RATE_LIMIT_PER_DAY=1000
```


---

## Quick Reference Card

### For Frontend Developers

- API layer: `src/lib/api.ts`
- Auth context: `src/contexts/AuthContext.tsx`
- WebSocket hook: `src/hooks/useChatWebSocket.ts`
- Main chat: `src/pages/Chat.tsx`

### For Backend Developers

- Entry point: `main.py`
- Config: `app/core/config.py`
- Routes: `app/api/routes/`
- LLM: `app/mcp/llm_integration.py`
- MCP tools: `mcp_servers/`

### Key Numbers

| Metric | Value |
|--------|-------|
| REST Endpoints | 47 |
| MCP Tools | 45 |
| Database Tables | 15 |
| Background Jobs | 4 |
| Rate Limit Levels | 4 |
| Email Triggers | 15+ |

---

*This documentation was generated for the KUBERA project. For the latest updates, refer to the codebase directly.*
