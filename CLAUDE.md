# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Factory Inventory Management System Demo with GitHub integration - Full-stack application with Vue 3 frontend, Python FastAPI backend, and in-memory mock data (no database).

## Critical Tool Usage Rules

### Subagents
Use the Task tool with these specialized subagents for appropriate tasks:

- **vue-expert**: Use for Vue 3 frontend features, UI components, styling, and client-side functionality
  - Examples: Creating components, fixing reactivity issues, performance optimization, complex state management
  - **MANDATORY RULE: ANY time you need to create or significantly modify a .vue file, you MUST delegate to vue-expert**
- **code-reviewer**: Use after writing significant code to review quality and best practices
- **Explore**: Use for understanding codebase structure, searching for patterns, or answering questions about how components work
- **general-purpose**: Use for complex multi-step tasks or when other agents don't fit

### Skills
- **backend-api-test** skill: Use when writing or modifying tests in `tests/backend` directory with pytest and FastAPI TestClient

### MCP Tools
- **ALWAYS use GitHub MCP tools** (`mcp__github__*`) for ALL GitHub operations
  - Exception: Local branches only - use `git checkout -b` instead of `mcp__github__create_branch`
- **ALWAYS use Playwright MCP tools** (`mcp__playwright__*`) for browser testing
  - Test against: `http://localhost:3000` (frontend), `http://localhost:8001` (API)

## Stack
- **Frontend**: Vue 3 + Composition API + Vite (port 3000)
- **Backend**: Python FastAPI (port 8001)
- **Data**: JSON files in `server/data/` loaded via `server/mock_data.py`

## Quick Start

```bash
# One-command (macOS/Linux)
./scripts/start.sh   # start both servers
./scripts/stop.sh    # stop both servers

# Manual
cd server && uv run python main.py       # backend
cd client && npm install && npm run dev  # frontend
```

## Running Tests

```bash
# All backend tests
cd tests && uv run pytest -v

# Single test file
cd tests && uv run pytest backend/test_inventory.py -v

# With coverage
cd tests && uv run pytest --cov=../server
```

Test files live in `tests/backend/`: `conftest.py` (fixtures), `test_inventory.py`, `test_orders.py`, `test_dashboard.py`, `test_misc_endpoints.py`.

## Architecture

### Data Flow
Vue filters → `useFilters` composable → `client/src/api.js` → FastAPI → in-memory filtering → Pydantic validation → component computed properties

### Filter System
4 global filters managed by `useFilters.js` composable: Time Period, Warehouse, Category, Order Status. Applied as query params on all API calls. Important mapping: UI `location` → API `warehouse`, UI `period` → API `month`. Filter state is shared across all views via the composable.

### Frontend Composables (`client/src/composables/`)
- **`useFilters.js`**: Shared reactive filter state (`selectedPeriod`, `selectedLocation`, `selectedCategory`, `selectedStatus`). All views consume this; `FilterBar.vue` controls it.
- **`useAuth.js`**: Mock user data (name, job title, tasks). Returns language-aware values based on current locale.
- **`useI18n.js`**: EN/JA internationalization. Persists locale to `localStorage`. Provides `t(key)`, `setLocale()`, and translation helpers for product names, warehouses, customer names.

### Backend (`server/main.py`)
- `filter_by_month()` — filters orders by month string or Q1–Q4 quarter keys
- `apply_filters()` — standard warehouse/category/status filtering
- `QUARTER_MAP` — maps Q1–Q4 to lists of month strings

### API Endpoints
- `GET /api/inventory` — Filters: warehouse, category
- `GET /api/orders` — Filters: warehouse, category, status, month
- `GET /api/dashboard/summary` — All filters
- `GET /api/demand`, `/api/backlog` — No filters
- `GET /api/spending/*` — summary, monthly, categories, transactions
- `GET /api/reports/quarterly`, `/api/reports/monthly-trends`
- `GET/POST /api/tasks`, `PATCH/DELETE /api/tasks/{id}`
- `POST /api/purchase-orders`, `GET /api/purchase-orders/{backlog_item_id}`

## Key Patterns & Gotchas

Always document non-obvious logic changes with comments.
1. Use unique keys in `v-for` (not `index`) — use `sku`, `month`, order `id`, etc.
2. Validate dates before calling `.getMonth()` — data can have null dates
3. Update Pydantic models in `server/main.py` when changing JSON data structure
4. Inventory filters don't support month (no time dimension on inventory data)
5. Revenue goals: $800K/month (single month), $9.6M YTD (all months selected)
6. Raw data in `ref()`, derived values in `computed()` — don't store computed results in refs

## File Locations
- Views: `client/src/views/*.vue` (Dashboard, Inventory, Orders, Demand, Spending, Reports, Backlog)
- Components: `client/src/components/*.vue` (FilterBar, modals, ProfileMenu, LanguageSwitcher)
- Composables: `client/src/composables/` (useFilters, useAuth, useI18n)
- API Client: `client/src/api.js`
- Backend: `server/main.py`, `server/mock_data.py`
- Data: `server/data/*.json`
- Global styles: `client/src/App.vue`

## Design System
- Colors: Slate/gray (#0f172a, #64748b, #e2e8f0)
- Status: green/blue/yellow/red
- Charts: Custom SVG, CSS Grid for layouts
- No emojis in UI
