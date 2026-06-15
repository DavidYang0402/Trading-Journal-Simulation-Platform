Purpose (read this first)

This is a personal trading platform. Its #1 job is to sharpen MY real-time
market judgment. The CORE is the trade journal: record my reasoning at the
moment of each trade, then compare my reasoning against the actual outcome
later. The journal is the heart of the project — not fancy indicators.

Secondary goal: this is also a portfolio piece that shows engineering
judgment to employers.

Rule of thumb: if a feature does not improve my judgment OR the code quality,
cut it. More data is not the goal; decision-relevant information is.

Tech stack


Backend: ASP.NET Core (Minimal API), EF Core, MSSQL
Frontend: React + TypeScript (strict mode), Vite
Deploy (later phase): Docker. Target Azure App Service + Azure Static Web
Apps. CI/CD via GitHub Actions.


Architecture rules (do not violate)


Layered: endpoint -> Service -> Repository. Endpoints NEVER touch DbContext
directly.
Services depend on interfaces, not concrete classes.
Two swappable boundaries, both behind interfaces:

IOrderExecutor: simulation now, real broker later. Upper layers must
not know which one is wired in.
IMarketDataSource: manual / mock data now, live feed later.



Multi-market & multi-currency FROM DAY ONE: every Position and Trade stores
Market, Currency, and the amount in its original currency. Totals are
converted to a base currency (TWD) for display.
Async all the way (async/await). No sync-over-async. One DbContext scope
per request.


Conventions


TypeScript strict. No any without a written reason in a comment.
React: handle API errors globally. NEVER show a blank screen on failure —
always show a clear error state.
Every Service method that holds real logic has a unit test (xUnit).
Money / P&L logic MUST have tests covering: zero, negative, and
multi-currency cases.


Workflow rules (how to work with me)


Plan before code. Propose the design first, wait for my approval, then
implement ONE bounded unit at a time.
Each unit must be small enough to finish in a single short session.
Before marking work done, review the diff in a fresh context for bugs and
for coupling that breaks the architecture rules above.


Build order (priority — do top first)


Core: positions + trade journal (reasoning + a "decision style" tag per
trade) + multi-currency P&L.
Minimal risk view: cash %, single-position concentration warning.
Tests + global error handling — done ALONGSIDE the above, not last.
Later: live market data (start with ONE market), more risk metrics, deploy.
Much later: real broker executor + full secrets/auth security.



Note to future me: keep this file short. If Claude already learns something
by reading the codebase, it does not need to live here.
