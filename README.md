# Database Query Optimizer

AI-native query optimization platform that diagnoses slow queries in plain English, generates safe index DDL, opens migration PRs, and reasons across workload-wide trade-offs — eliminating the DBA bottleneck in query tuning.

## The Problem

Database performance tuning is a bottleneck:

- **Slow queries are identified but not fixed** — every tool shows "full table scan on orders table" but doesn't generate the CREATE INDEX statement
- **Index recommendations ignore workload trade-offs** — recommending the index that fixes query A but hurts queries B and C requires manual analysis
- **Diagnosis requires DBA expertise** — explaining why a query is slow in terms a developer can understand is not a solved problem
- **Data warehouses are orphaned from OLTP tools** — pgBadger/PMM/pev2 are PostgreSQL/MySQL-only; Snowflake/BigQuery users have no open-source equivalent

Current state: slow query logs accumulate; DBAs manually investigate, propose indexes, and hope for the best. No tool closes the loop from diagnosis to a deployed migration.

## What This Does

### Natural Language Query Diagnosis (LLM-Native)
- **Translates EXPLAIN (ANALYZE) output into plain English** — "this full-table scan on `orders` is examining 4.2M rows because the `status` column lacks an index"
- **Explains root cause** — stale statistics, missing index, inefficient join order
- **Accessible to developers** — no DBA expertise required to understand bottleneck
- **No current tool does this** — even commercial tools show EXPLAIN plans without narration

### Automated Index DDL Generation
- **Generates the CREATE INDEX statement** for detected missing indexes
- **Explains the change** — "this index covers the filter and sort in query 47, eliminating the full-table scan"
- **Predicts plan change** — before applying, shows the new EXPLAIN plan
- **Estimates cost reduction** — "expected latency reduction: 340ms → 12ms"
- **Opens a PR for review** — fully integrated into the deployment workflow
- **Closes the detection-to-fix gap** that all current tools require manually

### Cross-Workload Multi-Query Optimization
- **Reasons about index trade-offs** across all queries in the workload
- **"Index X helps query A but causes a 15% write regression for queries B and C"** — recommends partial index instead
- **Embedding-based query similarity** — finds semantically similar queries to understand interaction patterns
- **No open-source tool does this** — only Moderne/OpenRewrite handle multi-file transformations in code

### Cardinality Estimation Correction
- **Learned model trained on real execution feedback** continuously updates cardinality estimates
- **Fixes stale statistics** without manual ANALYZE runs
- **Improves join-order decisions** — optimizer now has better row count estimates
- **Research validated**: Cardinality estimation is the bottleneck in query optimization (VLDB 2025)

### Polyglot Data Stack Coverage
- **Unified explain-plan schema** for PostgreSQL, MySQL, Snowflake, BigQuery, DuckDB
- **Same diagnostic and remediation approach** across all databases
- **Team can optimize queries** regardless of which database they're written against

## Key Differentiators

| Feature | This Platform | pgBadger | pev2 | SolarWinds DPA | Datadog DBM |
|---------|---|---|---|---|---|
| **NL diagnosis** | ✓ (LLM) | — | — | — | — |
| **Auto index PR** | ✓ | — | — | — | — |
| **Cross-workload trade-offs** | ✓ | — | — | — | — |
| **Cardinality correction** | ✓ (Learned model) | — | — | — | — |
| **Multi-DB support** | ✓ (PostgreSQL/MySQL/Snowflake/BigQuery) | PostgreSQL only | PostgreSQL | Multi-DB | Multi-DB |
| **Open source** | ✓ | ✓ | ✓ | — | — |

## Market & Opportunity

- **Market size**: Database performance monitoring $3.5B (2024) → $8.1–$8.98B by 2032–2033 at 12–12.5% CAGR
- **Buyers**: DBAs, backend engineers, FinOps teams, platform teams
- **Open-source gap**: No tool generates index DDL with PR integration; cardinality correction is unaddressed in OSS

## Research Foundation

- **Cardinality estimation is the bottleneck in query optimization** (Leis et al., VLDB 2025)
- **Learned cost models outperform traditional optimizers** — neural networks predict query cost more accurately than hand-tuned cost models (Li et al., VLDB 2025)
- **Reinforcement learning-based database knob tuning** predates this project but no tool achieved commercial scale (OtterTune research, 2020–2024)
- **Multi-objective optimization** required — index creation must balance read performance against write overhead

## Quick Start

```bash
# Enable slow query logging
psql:
  log_min_duration_statement: 100  # log queries >100ms

# Analyze slow queries with AI diagnosis
optimizer diagnose --database=mydb --slow-log=/path/to/slowlog

# Generate index recommendations with trade-off analysis
optimizer recommend --database=mydb --consider-workload

# Create migration PR
optimizer migrate --create-pr --target-branch=optimize/indexes-2026-05
```

## Target Users

1. **Database Administrators** — query analysis, index tuning, capacity planning
2. **Backend/Platform Engineers** — self-serve query optimization without DBA overhead
3. **FinOps Teams** — cloud database cost attribution and optimization
4. **Data Engineers** — optimization of analytical workloads across data warehouses
5. **Startups** — zero-cost query optimization tools without vendor licensing

## Related Standards

- ISO/IEC 9075 (SQL Standard) — semantic preservation in query rewrites
- EXPLAIN / EXPLAIN ANALYZE — vendor-specific but converging on JSON output
- TPC Benchmarks (TPC-H, TPC-DS, TPC-C) — standard evaluation corpus
- JOB (Join Order Benchmark) — de-facto academic benchmark for optimizer quality

---

Built on research from VLDB 2025 (cardinality estimation, learned cost models), OtterTune legacy, and production learnings from pgBadger, pev2, and commercial tools. [Read the full research](./research.md) | [Feature roadmap](./features.md)
