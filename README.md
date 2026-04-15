<!-- GitAds Banner — Replace YOUR_GITADS_SNIPPET below with your actual GitAds code -->
<!-- [![GitAds](https://images.gitads.dev/YOUR_REPO_ID)](https://tracking.gitads.dev/?repo=postgres-vs-mysql) -->

# PostgreSQL vs MySQL (2026)

> **The definitive comparison for developers choosing between PostgreSQL and MySQL in 2026.** Covers performance benchmarks, features, licensing, cloud costs, replication, JSON support, and when to use (or avoid) each database.

[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Table of Contents

- [Quick Comparison](#quick-comparison)
- [Performance](#performance)
- [Scalability & Replication](#scalability--replication)
- [JSON & NoSQL Features](#json--nosql-features)
- [Extensions & Ecosystem](#extensions--ecosystem)
- [Indexing](#indexing)
- [SQL Standards & Advanced Queries](#sql-standards--advanced-queries)
- [Licensing & Cost](#licensing--cost)
- [Cloud Provider Support & Pricing](#cloud-provider-support--pricing)
- [Security](#security)
- [Developer Experience](#developer-experience)
- [When to Use PostgreSQL](#-when-to-use-postgresql)
- [When to Use MySQL](#-when-to-use-mysql)
- [When NOT to Use Each](#-when-not-to-use-each)
- [Migration Considerations](#migration-considerations)
- [Related Comparisons](#related-comparisons)
- [Contributing](#contributing)

---

## Quick Comparison

| Feature | PostgreSQL | MySQL |
|---------|-----------|-------|
| **Latest stable** | 17.x | 8.4+ / 9.x |
| **License** | PostgreSQL License (MIT-like) | GPL v2 (with proprietary Oracle editions) |
| **ACID compliance** | Full | Full (InnoDB only) |
| **MVCC** | Native | InnoDB only |
| **JSON support** | `jsonb` — binary, indexable, very fast | `JSON` type — functional but slower |
| **Full-text search** | Built-in (`tsvector`, `tsquery`) | Built-in (InnoDB) |
| **Replication** | Logical + streaming | Binary log (statement/row/mixed) |
| **Partitioning** | Declarative (range, list, hash) | Range, list, hash, key |
| **Extensions** | 1,000+ (PostGIS, TimescaleDB, pgvector…) | Limited plugin architecture |
| **Stored procedures** | PL/pgSQL, PL/Python, PL/Perl, PL/V8… | SQL, PL/SQL-like |
| **Max DB size** | Unlimited | Unlimited |
| **Max row size** | 1.6 TB | 64 KB (InnoDB) |
| **Default port** | 5432 | 3306 |
| **Best for** | Complex queries, analytics, geospatial, compliance | High-throughput reads, web apps, replication simplicity |

---

## Performance

### Read Performance

| Workload | PostgreSQL | MySQL |
|----------|-----------|-------|
| Simple `SELECT` by PK | Fast | Slightly faster (InnoDB clustered index) |
| Complex `JOIN` (5+ tables) | Excellent query planner | Good, but optimizer is less sophisticated |
| Aggregations (`GROUP BY`, window functions) | Excellent | Good (improved significantly in 8.0+) |
| Full-text search | Good (built-in `tsvector`) | Good (InnoDB FTS) |
| JSON queries | Excellent (`jsonb` with GIN indexes) | Functional but slower |

### Write Performance

| Workload | PostgreSQL | MySQL |
|----------|-----------|-------|
| Single-row `INSERT` | Fast | Fast |
| Bulk `INSERT` | Fast (`COPY` command) | Fast (`LOAD DATA INFILE`) |
| `UPDATE`-heavy workloads | Good (MVCC creates row versions → vacuum needed) | Good (in-place updates with InnoDB) |
| `DELETE`-heavy workloads | Requires `VACUUM` maintenance | Handles well natively |
| Write-ahead logging | WAL (reliable, tunable) | Redo/undo logs (InnoDB) |

### Concurrency

| Aspect | PostgreSQL | MySQL |
|--------|-----------|-------|
| MVCC model | Native, row-level | InnoDB only |
| Connection overhead | Higher per-connection memory | Lower per-connection |
| Connection pooling needed? | Yes (PgBouncer, pgpool) at scale | Yes at scale, but tolerates more raw connections |
| Parallel queries | Yes (parallel seq scan, joins, aggregates) | Limited (MySQL 8.0+ for some operations) |

**Bottom line:** PostgreSQL wins on complex queries and analytics. MySQL wins on simple, high-throughput read workloads with its clustered index architecture.

---

## Scalability & Replication

### Replication Options

| Feature | PostgreSQL | MySQL |
|---------|-----------|-------|
| **Streaming replication** | Yes (physical — byte-level) | N/A (uses binlog) |
| **Logical replication** | Yes (table-level, selective) | Yes (binlog, row/statement/mixed) |
| **Multi-source replication** | Via logical replication | Native multi-source |
| **Synchronous replication** | Yes (configurable per-transaction) | Yes (semi-sync plugin, Group Replication) |
| **Delayed replicas** | Yes (`recovery_min_apply_delay`) | Yes (`CHANGE REPLICATION SOURCE TO ... DELAY`) |
| **Automatic failover** | Requires external tooling (Patroni, repmgr) | MySQL InnoDB Cluster / Group Replication |

### Horizontal Scaling

| Approach | PostgreSQL | MySQL |
|----------|-----------|-------|
| **Read replicas** | Easy (streaming replication) | Easy (standard replication) |
| **Sharding** | Citus extension, or manual | Vitess, ProxySQL, or manual |
| **Distributed SQL** | Citus, YugabyteDB (PG-compatible) | TiDB, PlanetScale (MySQL-compatible) |

**Bottom line:** MySQL has simpler built-in HA (InnoDB Cluster). PostgreSQL needs external tools for failover but offers more flexible replication (logical replication is excellent for selective table sync and zero-downtime upgrades).

---

## JSON & NoSQL Features

| Feature | PostgreSQL | MySQL |
|---------|-----------|-------|
| **JSON storage type** | `json` (text) and `jsonb` (binary) | `JSON` (binary-ish, validated) |
| **Indexing JSON** | GIN indexes on `jsonb` — very fast | Generated columns + B-tree (workaround) |
| **JSON path queries** | `jsonb @> '{"key": "val"}'` operators | `JSON_EXTRACT()`, `->`, `->>` operators |
| **Partial JSON updates** | `jsonb_set()`, `||` merge operator | `JSON_SET()`, `JSON_REPLACE()` |
| **Performance** | `jsonb` with GIN index ≈ document DB speed | Slower for complex JSON queries |
| **Use as document store** | Viable (many companies do this) | Possible but not recommended at scale |

**Bottom line:** If you need JSON-heavy workloads, PostgreSQL's `jsonb` is significantly ahead. MySQL's JSON support works for simple use cases but lacks efficient indexing for complex queries.

---

## Extensions & Ecosystem

### PostgreSQL Extensions (standout examples)

| Extension | Purpose |
|-----------|---------|
| **PostGIS** | Geospatial data — industry standard |
| **TimescaleDB** | Time-series data at scale |
| **pgvector** | Vector similarity search (AI/ML embeddings) |
| **Citus** | Distributed/sharded PostgreSQL |
| **pg_cron** | Scheduled jobs inside the database |
| **pg_stat_statements** | Query performance monitoring |
| **pgAudit** | Audit logging for compliance |
| **Foreign Data Wrappers** | Query external data sources (MySQL, MongoDB, S3, etc.) |

### MySQL Ecosystem

| Tool/Feature | Purpose |
|-------------|---------|
| **MySQL Router** | Connection routing and load balancing |
| **MySQL Shell** | Advanced admin CLI |
| **Group Replication** | Built-in HA clustering |
| **MySQL HeatWave** | In-database analytics (Oracle Cloud) |
| **ProxySQL** | Connection pooling and query routing |
| **Vitess** | Horizontal sharding (used by YouTube, Slack) |

**Bottom line:** PostgreSQL's extension ecosystem is unmatched. If you need geospatial (PostGIS), vector search (pgvector), or time-series (TimescaleDB), PostgreSQL is the clear choice. MySQL's ecosystem is more focused on scaling and HA tooling.

---

## Indexing

| Index Type | PostgreSQL | MySQL (InnoDB) |
|-----------|-----------|----------------|
| **B-tree** | Yes (default) | Yes (default, clustered on PK) |
| **Hash** | Yes | Yes (adaptive hash, internal) |
| **GIN (inverted)** | Yes — arrays, `jsonb`, full-text | No |
| **GiST** | Yes — geospatial, range types | No |
| **BRIN** | Yes — block range, great for time-series | No |
| **Partial indexes** | Yes (`WHERE` clause on index) | No |
| **Expression indexes** | Yes (`CREATE INDEX ON tbl (lower(col))`) | Generated columns (workaround) |
| **Covering indexes** | Yes (`INCLUDE` clause) | Yes (InnoDB covering index) |
| **Full-text indexes** | Yes (`tsvector`) | Yes (InnoDB FTS) |
| **Spatial indexes** | Yes (GiST, SP-GiST) | Yes (R-tree) |

**Bottom line:** PostgreSQL offers significantly more indexing flexibility. Partial indexes and expression indexes alone can eliminate entire categories of performance problems that require workarounds in MySQL.

---

## SQL Standards & Advanced Queries

| Feature | PostgreSQL | MySQL |
|---------|-----------|-------|
| **Window functions** | Full support (since 8.4, 2009) | Added in 8.0 (2018) |
| **CTEs (WITH)** | Full support, recursive | Added in 8.0, now optimized |
| **LATERAL JOIN** | Yes | Yes (8.0+) |
| **RETURNING clause** | Yes (`INSERT/UPDATE/DELETE ... RETURNING`) | No (use `LAST_INSERT_ID()` workaround) |
| **UPSERT** | `INSERT ... ON CONFLICT` (flexible) | `INSERT ... ON DUPLICATE KEY UPDATE` |
| **MERGE** | Yes (PG 15+) | No native `MERGE` |
| **Custom types** | Yes (composite types, enums, domains) | ENUMs only |
| **Array columns** | Yes (native array type) | No |
| **Range types** | Yes (`int4range`, `tsrange`, etc.) | No |
| **Table inheritance** | Yes | No |
| **Generated columns** | Yes (stored + virtual) | Yes (stored + virtual) |
| **CHECK constraints** | Yes (enforced) | Yes (enforced since 8.0.16) |

**Bottom line:** PostgreSQL adheres more closely to the SQL standard and offers advanced features (arrays, range types, `RETURNING`, `LATERAL`) that simplify application code. MySQL has closed the gap significantly since 8.0 but still lags on advanced SQL features.

---

## Licensing & Cost

| Aspect | PostgreSQL | MySQL |
|--------|-----------|-------|
| **License** | PostgreSQL License (permissive, MIT-like) | GPL v2 |
| **Fully open source?** | Yes — one edition | Community Edition is open source; Enterprise is proprietary (Oracle) |
| **Commercial use** | No restrictions | GPL requires source distribution if redistributing; or buy Oracle commercial license |
| **Enterprise features** | All features in one free edition | Some features locked behind Oracle MySQL Enterprise (audit, firewall, backup, monitoring) |
| **Vendor lock-in risk** | None — community-governed | Moderate — Oracle controls MySQL development |
| **Support options** | EDB, Percona, Crunchy Data, community | Oracle, Percona, MariaDB Corp, community |

**Bottom line:** PostgreSQL is simpler licensing-wise — everything is free, no enterprise tier. MySQL's GPL license and Oracle's proprietary Enterprise edition add complexity, especially for companies embedding MySQL in distributed products.

---

## Cloud Provider Support & Pricing

### Managed Services

| Provider | PostgreSQL | MySQL |
|----------|-----------|-------|
| **AWS** | RDS PostgreSQL, Aurora PostgreSQL | RDS MySQL, Aurora MySQL |
| **Google Cloud** | Cloud SQL for PostgreSQL, AlloyDB | Cloud SQL for MySQL |
| **Azure** | Azure Database for PostgreSQL (Flexible Server) | Azure Database for MySQL (Flexible Server) |
| **DigitalOcean** | Managed PostgreSQL | Managed MySQL |
| **PlanetScale** | — | Yes (Vitess-based, MySQL-compatible) |
| **Neon** | Yes (serverless PostgreSQL) | — |
| **Supabase** | Yes (PostgreSQL-based) | — |
| **Tembo** | Yes (PostgreSQL stacks) | — |

### Approximate Pricing (2 vCPU / 8 GB RAM, single instance, 2026)

| Provider | PostgreSQL | MySQL |
|----------|-----------|-------|
| **AWS RDS** | ~$140-170/mo | ~$140-170/mo |
| **AWS Aurora** | ~$200-250/mo | ~$200-250/mo |
| **Google Cloud SQL** | ~$120-150/mo | ~$120-150/mo |
| **Google AlloyDB** | ~$200+/mo | — |
| **Azure Flexible Server** | ~$130-160/mo | ~$130-160/mo |

> **Note:** Prices vary by region and configuration. Always check current pricing on provider websites.

**Bottom line:** Pricing is nearly identical across providers. The real differentiator is the **ecosystem around it** — PostgreSQL has more serverless/developer-focused options (Neon, Supabase), while MySQL has PlanetScale for edge/serverless MySQL.

---

## Security

| Feature | PostgreSQL | MySQL |
|---------|-----------|-------|
| **Row-level security (RLS)** | Yes — native policies | No (application-level workaround) |
| **Column-level permissions** | Yes | Yes |
| **SSL/TLS** | Yes | Yes |
| **Authentication methods** | Password, SCRAM-SHA-256, LDAP, GSSAPI, certificate, RADIUS, PAM | Password (caching_sha2_password), LDAP, PAM, certificate |
| **Audit logging** | pgAudit extension | Enterprise only (MySQL Enterprise Audit) or community plugins |
| **Data masking** | Extensions available | Enterprise only |
| **Encryption at rest** | TDE via extensions or OS-level | InnoDB tablespace encryption (Community), TDE (Enterprise) |

**Bottom line:** PostgreSQL's row-level security (RLS) is a standout feature for multi-tenant applications. MySQL's audit and masking capabilities are locked behind the Enterprise edition.

---

## Developer Experience

| Aspect | PostgreSQL | MySQL |
|--------|-----------|-------|
| **Default CLI** | `psql` (powerful, `\d` commands, tab completion) | `mysql` client (functional, less discovery) |
| **GUI tools** | pgAdmin, DBeaver, DataGrip | MySQL Workbench, DBeaver, DataGrip |
| **ORM support** | Excellent (all major ORMs) | Excellent (all major ORMs) |
| **Driver maturity** | Excellent across all languages | Excellent across all languages |
| **Error messages** | Detailed, with hints and context | Functional, sometimes cryptic |
| **Documentation** | Excellent — comprehensive official docs | Good — Oracle official docs, community wikis |
| **Learning curve** | Moderate — more features to learn | Lower — simpler defaults, fewer knobs |
| **Docker image** | `postgres:17` (official, well-maintained) | `mysql:8.4` (official) |
| **Startup time** | Fast | Fast |

**Bottom line:** Both have mature tooling. PostgreSQL's `psql` is more powerful for interactive use. MySQL is slightly easier to get started with for beginners.

---

## ✅ When to Use PostgreSQL

- **Complex queries** — heavy JOINs, window functions, CTEs, subqueries
- **Analytics & reporting** — OLAP-style workloads alongside OLTP
- **Geospatial data** — PostGIS is the industry standard
- **JSON-heavy workloads** — `jsonb` with GIN indexes
- **AI/ML vector search** — pgvector for embedding similarity
- **Time-series data** — TimescaleDB extension
- **Multi-tenant SaaS** — row-level security (RLS) for tenant isolation
- **Strict compliance** — permissive license, audit logging, encryption
- **Schema complexity** — arrays, range types, custom types, table inheritance
- **You want one free edition** with all features included

---

## ✅ When to Use MySQL

- **High-throughput simple reads** — web apps, content platforms, e-commerce catalogs
- **Read-heavy workloads** — clustered index on InnoDB is optimized for PK lookups
- **Simpler applications** — CRUD apps where advanced SQL features aren't needed
- **Existing MySQL ecosystem** — WordPress, Drupal, Magento, many PHP frameworks default to MySQL
- **Built-in HA** — InnoDB Cluster / Group Replication is simpler than PostgreSQL HA setup
- **Edge/serverless MySQL** — PlanetScale (Vitess-based)
- **Your team knows MySQL** — familiarity reduces onboarding time
- **WordPress / PHP stack** — MySQL is the default, switching adds complexity

---

## ❌ When NOT to Use Each

### Don't use PostgreSQL when:

| Scenario | Why | Use instead |
|----------|-----|-------------|
| Ultra-simple CRUD app | Overkill — more config than needed | MySQL or SQLite |
| Your team only knows MySQL | Migration + retraining cost is real | MySQL |
| You need built-in HA without external tools | PostgreSQL HA requires Patroni/repmgr setup | MySQL InnoDB Cluster |
| WordPress / PHP legacy stack | MySQL is the default; fighting it adds friction | MySQL |
| Embedded / edge database | PostgreSQL is not designed for embedding | SQLite |

### Don't use MySQL when:

| Scenario | Why | Use instead |
|----------|-----|-------------|
| Complex analytical queries | Query optimizer is less sophisticated | PostgreSQL |
| Geospatial workloads | PostGIS far exceeds MySQL spatial | PostgreSQL + PostGIS |
| JSON-heavy document patterns | `jsonb` with GIN indexes is far superior | PostgreSQL |
| Multi-tenant RLS needed | MySQL has no row-level security | PostgreSQL |
| You need vector search (AI/ML) | pgvector doesn't exist for MySQL | PostgreSQL + pgvector |
| Licensing concerns (redistribution) | GPL is more restrictive than PostgreSQL License | PostgreSQL |
| You want audit logging without paying Oracle | pgAudit is free; MySQL audit is Enterprise-only | PostgreSQL |

---

## Migration Considerations

### PostgreSQL → MySQL

- `RETURNING` clause doesn't exist — rewrite to `LAST_INSERT_ID()`
- Arrays and range types don't exist — normalize to separate tables
- `jsonb` operators differ — rewrite to `JSON_EXTRACT()` / `->` syntax
- Row-level security must be replaced with application logic
- PL/pgSQL stored procedures must be rewritten

### MySQL → PostgreSQL

- `AUTO_INCREMENT` → `SERIAL` or `GENERATED ALWAYS AS IDENTITY`
- `INSERT ... ON DUPLICATE KEY UPDATE` → `INSERT ... ON CONFLICT`
- Backtick quoting (`` ` ``) → double-quote (`"`) for identifiers
- `GROUP_CONCAT()` → `STRING_AGG()`
- `IFNULL()` → `COALESCE()` (works in both, but COALESCE is standard)
- `LIMIT offset, count` → `LIMIT count OFFSET offset`

### Tools for migration

- **pgLoader** — MySQL to PostgreSQL (handles schema + data conversion)
- **AWS DMS** — Database Migration Service (supports both directions)
- **pg_chameleon** — MySQL to PostgreSQL replication-based migration

---

## Related Comparisons

> More comparisons coming soon — star and watch to get notified.

- [Docker vs Podman](https://github.com/atryx/docker-vs-podman) *(coming soon)*
- [Kafka vs RabbitMQ](https://github.com/atryx/kafka-vs-rabbitmq) *(coming soon)*
- [Redis vs Memcached](https://github.com/atryx/redis-vs-memcached) *(coming soon)*

---

## Contributing

Found an error? Have updated benchmarks? Know a better way to explain something?

**Contributions are welcome!** Please open an issue or submit a PR.

- Keep comparisons **neutral and factual**
- Cite sources for benchmarks and pricing
- Update the year if information becomes outdated

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

> **Last updated:** April 2026 | **PostgreSQL 17.x** vs **MySQL 8.4+ / 9.x**
