# Database Query Optimizer — Feature & Functionality Survey

> Candidate #19 · Researched: 2026-04-22

## Solutions Analysed

| Tool | Type | Licence / Model | URL |
|------|------|-----------------|-----|
| SolarWinds DPA | Commercial | Proprietary | https://solarwinds.com/database-performance-analyzer |
| Datadog Database Monitoring | Commercial SaaS | Proprietary | https://datadoghq.com/product/database-monitoring |
| EverSQL / Aiven AI Database Optimizer | Commercial (freemium) | Proprietary | https://aiven.io/ai-database-optimizer |
| Percona Monitoring and Management (PMM) | Open source | Apache-2.0 | https://percona.com/software/database-tools/percona-monitoring-and-management |
| pgBadger | Open source | PostgreSQL Licence | https://github.com/darold/pgbadger |
| pev2 (Postgres Explain Visualizer 2) | Open source | MIT | https://explain.dalibo.com |
| pg_stat_statements | Open source | PostgreSQL core extension | https://postgresql.org/docs/current/pgstatstatements.html |
| DBeaver Community | Open source | Apache-2.0 | https://dbeaver.io |

---

## Feature Analysis by Solution

### SolarWinds DPA (Database Performance Analyzer)

**Core features**
- Wait-time analysis: breaks query execution into specific wait categories (CPU, I/O, lock, network) for precise bottleneck identification
- Cross-platform query monitoring: SQL Server, Oracle, MySQL, PostgreSQL, Azure SQL, Amazon RDS
- ML-based anomaly detection: baselines normal wait patterns and alerts on statistically significant deviations
- Query execution plan collection: captures and stores historical explain plans for slow queries
- Advisor recommendations: index and query rewrite suggestions based on wait analysis
- Deadlock analysis: visualises lock contention graphs and identifies blocking query chains

**Differentiating features**
- Wait-time analysis approach is more actionable than throughput-only metrics: tells you *why* a query is slow, not just *how slow* it is
- Cross-platform support is the broadest of any single tool: covers Oracle, SQL Server, MySQL, and PostgreSQL with a single deployment

**UX patterns**
- Timeline-based wait analysis: drill from database-level wait trends to individual query wait breakdowns
- Top SQL view: ranked list of queries by total wait time (not just execution count × duration)
- Historical plan comparison: compare today's plan with the plan from a week ago to detect regression

**Integration points**
- SQL Server, Oracle, MySQL, PostgreSQL, IBM DB2, Sybase
- Slack and email alerting for anomaly threshold breaches
- REST API for programmatic metric retrieval
- SolarWinds Observability for cross-tool correlation

**Known gaps**
- ~$1,275–$1,399/instance/year; costs scale to $50k+ for large database fleets
- Does not generate or apply fixes: diagnosis only, no automated remediation
- Dated web UI compared to SaaS alternatives
- No data warehouse support (Snowflake, BigQuery, Redshift)

**Licence / IP notes**
- Proprietary. Requires commercial licence.

---

### Datadog Database Monitoring

**Core features**
- Real-time query metrics: execution count, mean latency, total time, rows examined per normalized query
- Explain plan collection: captures and stores EXPLAIN plans for slow queries without DBA intervention
- Live query tracing: see active queries in real time with wait state breakdown
- Query metrics timeline: correlate query performance changes with infrastructure events and deployments
- Host-level database metrics: connection pool saturation, replication lag, table bloat
- APM integration: link a slow HTTP request to the database query it triggered

**Differentiating features**
- Native APM + database monitoring integration is unique: trace a slow API request through the application stack to the specific database query in a single click
- Zero-configuration explain plan collection: Datadog agent captures plans automatically without any database-side configuration changes

**UX patterns**
- Query list with filter/sort by service, database, and time range
- Query detail page: normalized query, plan history, metric timeline, and correlation with infrastructure events
- Database Map: visualise all monitored databases with health status and query volume

**Integration points**
- PostgreSQL, MySQL, SQL Server, Oracle, MongoDB, Redis, Cassandra
- Datadog APM for request-to-query trace linking
- PagerDuty, Slack for alert routing
- Datadog Notebooks for collaborative query analysis

**Known gaps**
- ~$70/host/month; compounds with other Datadog products (APM, logs) to reach $200+/host/month at full stack
- No automated remediation: identifies slow queries but does not generate index DDL or rewritten queries
- Requires Datadog agent everywhere; not usable outside the Datadog ecosystem
- No data warehouse support

**Licence / IP notes**
- Proprietary SaaS.

---

### Percona Monitoring and Management (PMM)

**Core features**
- Deep query analytics (QAN): pg_stat_statements and performance_schema integration for normalized query metrics
- Explain plan collection and visualisation for PostgreSQL and MySQL
- Database metrics dashboards: built on Grafana with pre-built panels for slow queries, I/O, replication, and connection pools
- Database health checks: identifies tables with high bloat, missing indexes, and inefficient statistics
- Integrated Grafana and VictoriaMetrics for long-term metric retention
- Alerting via Grafana Alerting with Slack and PagerDuty connectors

**Differentiating features**
- Best open-source depth for PostgreSQL and MySQL query analytics with zero per-query licensing cost
- Pre-built Grafana dashboards eliminate the visualisation configuration burden typical of raw Prometheus + Grafana setups
- pt-query-digest integration for offline MySQL slow query log analysis

**UX patterns**
- PMM UI (Grafana-based): database-per-row dashboard with drill-down to QAN for per-query analysis
- Query Analytics view: top queries by total time, per-query explain plan, and execution count trend
- Database summary panels: connections, replication lag, table bloat, and vacuum status in one view

**Integration points**
- PostgreSQL (pg_stat_statements, pg_stat_bgwriter), MySQL/Percona Server, MongoDB
- Prometheus and VictoriaMetrics for metric storage
- Grafana for dashboarding and alerting
- Docker and Kubernetes Operator for deployment

**Known gaps**
- Requires infrastructure to run: PMM server (Docker or Kubernetes), PMM client on each database host
- MySQL-centric: PostgreSQL coverage is improving but historically thinner
- No AI-powered query rewriting or index generation; diagnosis only
- No data warehouse support (Snowflake, BigQuery, Redshift)

**Licence / IP notes**
- Apache-2.0. Unrestricted commercial use and embedding.

---

### pgBadger

**Core features**
- Fast PostgreSQL slow query log parser producing comprehensive HTML reports
- Reports: slowest queries by total time, by mean time, by number of calls; lock waits, temporary file usage
- Connection analysis: connection attempts, authentication failures, connection peak hours
- Error and warning analysis: log-level breakdown with query attribution
- Parallel processing mode for fast analysis of large log files
- Incremental analysis: process only new log lines since the last run

**Differentiating features**
- Zero-configuration: point it at a PostgreSQL log file and get a detailed HTML report with no database access required
- The fastest open-source option for retrospective slow query analysis from existing log files

**UX patterns**
- Static HTML report with tabbed sections: slow queries, locks, connections, errors
- Per-query detail: normalized query, sample parameterised form, mean time, count
- Hourly distribution graphs for all metrics

**Integration points**
- PostgreSQL log files (CSV and standard text formats)
- CI/CD pipelines: run pgBadger on staging logs as part of a pre-release check
- Grafana: pgBadger JSON output can be ingested into dashboards

**Known gaps**
- Offline/static analysis only: no real-time monitoring or alerting
- PostgreSQL only; no MySQL, SQL Server, or warehouse support
- No actionable recommendations: reports *what* is slow but not *why* or *how to fix*

**Licence / IP notes**
- PostgreSQL Licence (permissive; similar to BSD-2-Clause). Unrestricted commercial use.

---

### pev2 (Postgres Explain Visualizer 2)

**Core features**
- Browser-based interactive visualisation of `EXPLAIN (ANALYZE, BUFFERS)` output
- Node cost breakdown: which plan nodes consume the most time and buffer I/O
- Underestimate detection: flags nodes where estimated rows diverge significantly from actual rows (cardinality estimation failures)
- Exclusive vs. inclusive cost display: shows cost both including and excluding child nodes for accurate bottleneck identification
- Paste-and-analyse UX: paste JSON or text EXPLAIN output and get an interactive plan diagram instantly

**Differentiating features**
- Best single-query plan analysis UX of any tool: interactive, colour-coded, and immediately actionable without installation
- Cardinality underestimate highlighting makes it the best tool for diagnosing statistics staleness issues

**UX patterns**
- Single-page web app: paste EXPLAIN output → interactive flame-graph-style plan tree
- Node detail panel: shows estimated vs. actual rows, cost, loops, and relevant statistics
- Permalink sharing: share a plan URL with teammates for collaborative analysis

**Integration points**
- PostgreSQL (any version supporting JSON EXPLAIN output)
- Self-hostable: open-source version deployable in internal tooling
- No agent or database connection required; purely client-side analysis

**Known gaps**
- Single query only: no workload-wide analysis or aggregation across multiple queries
- PostgreSQL only
- No recommendations: shows the plan but does not suggest index creation or query rewrite

**Licence / IP notes**
- MIT. Unrestricted commercial use and embedding.

---

## Cross-Cutting Feature Themes

### Table-Stakes Features

Any viable solution in this space must provide:

- Slow query identification with normalization (aggregate across parameterised variants of the same query)
- EXPLAIN plan collection and visualisation for at least PostgreSQL and MySQL
- Wait-time or execution-time breakdown per query
- Index coverage analysis: identify queries performing full-table scans or suboptimal index usage
- Historical query performance trending: detect regressions over time
- Alert on slow query threshold breach with Slack/PagerDuty routing

### Differentiating Features

Capabilities that provide competitive advantage:

- **Reachability-aware index recommendation**: recommending not just *which* index to create but *which queries it helps vs. harms* — accounting for write overhead and multi-query workload interaction (no tool does this in OSS)
- **Automated index DDL generation and PR opening**: closing the loop from diagnosis to a deployable migration file (no current tool does this end-to-end)
- **Natural language query explanation for non-experts**: translating an EXPLAIN plan into plain English ("this full-table scan on `orders` is examining 4.2M rows because the `status` column lacks an index") without requiring DBA expertise
- **Cross-workload multi-query optimisation**: reasoning about index trade-offs across the full query workload, not one query in isolation
- **Data warehouse + OLTP unified coverage**: no single open-source tool covers PostgreSQL, MySQL, Snowflake, and BigQuery under a common interface

### Underserved Areas / Opportunities

- **Automated end-to-end remediation**: every tool diagnoses slow queries and suggests an index. None automatically generates the index DDL, predicts the plan change, estimates cost reduction, and opens a migration PR. This is the primary agentic gap.
- **Multi-query workload reasoning**: recommending the index that optimally balances all queries — not just the one that appears worst — requires cross-workload analysis that no OSS tool performs.
- **Natural-language query rewriting for non-experts**: developers who write inefficient SQL (unnecessary subqueries, Cartesian products, missing JOIN conditions) need plain-English diagnosis and a corrected rewrite, not an EXPLAIN plan.
- **Data warehouse coverage in open-source tooling**: pgBadger/PMM/pev2 are PostgreSQL/MySQL-only. Snowflake, BigQuery, and Redshift users have no open-source equivalent.

### AI-Augmentation Candidates

- **Cardinality estimation correction**: LLM trained on real execution feedback continuously updates cardinality estimates, correcting stale statistics without manual ANALYZE runs — something no current OSS tool does.
- **Automated index generation and PR opening**: AI generates the CREATE INDEX DDL, explains why, predicts the plan change, estimates cost reduction, and opens a migration PR for human review.
- **Natural language plan explanation**: LLM translates EXPLAIN (ANALYZE) output into plain-English diagnosis of bottlenecks and actionable rewrite suggestions accessible to developers without DBA expertise.
- **Cross-workload multi-query optimisation**: AI reasons across the full query workload to recommend indexes that optimally balance read performance against write overhead and multi-query interaction.
- **Polyglot data stack coverage**: AI optimizer with adapters for PostgreSQL, MySQL, Snowflake, BigQuery, and DuckDB — unified under a common explain-plan schema — serves platform teams running mixed data stacks.

---

## Legal & IP Summary

Key licence considerations:

- **Percona PMM, DBeaver Community (Apache-2.0)**: unrestricted commercial use and embedding.
- **pgBadger (PostgreSQL Licence)**: permissive; unrestricted commercial use.
- **pev2 (MIT)**: unrestricted commercial use and embedding.
- **pg_stat_statements (PostgreSQL core, PostgreSQL Licence)**: unrestricted commercial use.
- **SolarWinds DPA, Datadog DBM, EverSQL/Aiven (Proprietary)**: feature inspiration carries no IP risk; API integration requires commercial agreements.

SQL query parsing and EXPLAIN plan interpretation are generic techniques with no known active patents. Learned cardinality estimation (as described in VLDB 2025 papers by Leis et al., Li et al.) is academic research with no known specific patent claims. Reinforcement learning-based knob tuning (OtterTune lineage) was described in SIGMOD 2017 with no known active patent enforcement as of April 2026.

---

## Recommended Feature Scope

**Must-have (MVP)**
- Slow query identification and normalization across PostgreSQL and MySQL using pg_stat_statements and performance_schema
- EXPLAIN plan collection and interactive visualisation (pev2-style) with cardinality underestimate highlighting
- Index coverage analysis: detect full-table scans and suboptimal index usage with per-query impact scoring
- Historical query performance trending: alert on regressions vs. rolling baseline
- AI-powered natural language plan explanation: translate EXPLAIN output into plain-English diagnosis accessible to non-DBAs

**Should-have (v1.1)**
- Automated index DDL generation: AI generates CREATE INDEX statement with predicted plan improvement and estimated cost reduction
- Migration PR generation: AI opens a pull request with the index DDL as a migration file for human review
- Cross-workload multi-query analysis: index recommendation accounting for write overhead and multi-query interaction
- AI-powered query rewrite suggestions for common anti-patterns (N+1, Cartesian products, unnecessary subqueries)

**Nice-to-have (backlog)**
- Data warehouse support: Snowflake, BigQuery, and DuckDB under a common explain-plan interface
- Continuous cardinality estimation correction: learned model updated from real execution feedback to correct stale statistics
- OpenTelemetry semantic convention integration: attach database span metadata to distributed traces for request-level query attribution
- FinOps cost attribution: cloud database cost breakdown per query/team for showback and budget optimisation
