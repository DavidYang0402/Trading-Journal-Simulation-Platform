# MVP Session Plan — Trading Journal & Position Tracker

Each session = ~90 min, ONE bounded unit, ends RUNNABLE + COMMITTED.
Workflow every session: Claude proposes the design first -> I approve -> implement
one unit -> review the diff in a fresh context -> commit -> update CLAUDE.md "Current focus".
If a session marked (may split) runs long, stop at a clean point and continue next time.
[C] = core to the project's #1 goal. Do these especially carefully.

## Setup — do this FIRST (your next sit-down)
- [ ] S0: Environment + repo. Install Claude Code. `git init`. Put CLAUDE.md and
      PLAN.md in the repo root. Create an empty .NET solution (API project) and an
      empty React+TS (Vite, strict) project. Commit. Done = `dotnet run` and
      `npm run dev` both start without errors.

## Core — positions + trade journal + multi-currency P&L
- [ ] S1: Domain model + DbContext + first migration. Entities: Trade, Position,
      FxRate, Market (enum), DecisionStyle (tag). Every Trade/Position stores
      Market + Currency + native amount. Add a rowversion/concurrency token on
      Position. Done = migration runs, DB + tables created. No business logic yet.
- [ ] S2: Repository + Service skeletons behind interfaces. Define ITradeService,
      ITradeRepository, IOrderExecutor (+ SimulatedOrderExecutor stub),
      IMarketDataSource (+ mock stub). Wire DI. Done = app builds & starts, DI
      resolves, no real logic yet.
- [ ] S3 [C]: Record-a-trade write path, end to end. Endpoint -> Service ->
      Repository persists a trade WITH the journal fields: reasoning (why),
      expectation, "what would prove me wrong", and a decision-style tag. Done =
      I can POST a trade via Swagger and see it in the DB. + 1 xUnit test on the service.
- [ ] S4: Positions read path. List current holdings derived from trades; endpoint
      returns them. Done = GET positions works against seeded trades.
- [ ] S5 [C] (may split): Multi-currency P&L. Convert each position to base
      currency (TWD), compute per-position + total P&L. MUST have xUnit tests
      covering zero, negative, and multi-currency cases. Done = tests green, totals correct.
- [ ] S6: Frontend shell. React+TS strict structure, typed API client, GLOBAL error
      boundary + visible error state (no blank screen on failure). Done = one real
      API call renders, and a forced 500 shows a clear error, not a blank page.
- [ ] S7 [C]: Trade-entry form (the journal capture UI). Inputs for the journal
      fields + decision-style tag. Strict TS, inline validation, error handling.
      Done = submitting the form creates a trade via the API.
- [ ] S8: Positions + P&L view. Table of holdings showing weight and TWD P&L.
      Done = view reflects real data from the API.

## The heart — outcome review (this is why the project exists)
- [ ] S9 [C] (may split): Close-a-trade + record outcome. Mark a trade closed,
      capture actual result, and show "my reasoning at entry" vs "what happened".
      Done = I can review one past trade's thesis against its outcome.
- [ ] S10 [C]: Decision-style review. Group/filter the journal by decision-style
      tag; show simple win/PL summary per style. Done = I can see which style
      actually performs (the "style discovery" view).

## Minimal risk view
- [ ] S11: Cash % + single-position concentration warning. Service calc + tests +
      small UI badge/warning. Done = warning shows when one position exceeds a set %.

## Polish (still MVP)
- [ ] S12: MVP polish pass. Audit global error handling, write README with a
      roadmap section, tidy commits, run a fresh-context review of the whole diff
      against this plan. Done = README done, no known broken paths.

## Later (NOT now — keep out of MVP)
- Live market data (start with ONE market only)
- More risk metrics
- Docker + Azure deploy + GitHub Actions CI/CD
- Real broker IOrderExecutor + full secrets/auth security

Cut test: before adding anything, ask — does it improve my judgment OR code quality?
If neither, it does not belong in the MVP.
