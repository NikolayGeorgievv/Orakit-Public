# OraKit

Free, browser-based developer tools for Oracle databases. Paste your execution plans, PL/SQL packages, or DDL statements and get interactive visualizations and analysis — no installation, no signup, and your data never leaves your browser.

**Live:** [orakit.dev](https://orakit.dev)

> Source code is private. This repo serves as a public project showcase.

![OraKit Landing Page](static/images/orakitScreenshot.png)

## Tools

### Execution Plan Visualizer *(Live)*

Parses Oracle `DBMS_XPLAN` output and renders it as an interactive collapsible tree with real-time analysis.

**Parser capabilities:**
- Dynamic column detection — reads the pipe-delimited header and maps columns by name, handling optional columns (`TempSpc`, `Pstart/Pstop`, `TQ`, `IN-OUT`, `PQ Distrib`) that shift positions when present
- Auto-detects `ALLSTATS LAST` format from `DISPLAY_CURSOR` — shows estimated vs actual rows side-by-side with color-coded divergence
- Handles line wrapping, suffixed numbers (K/M/G), memory statistics (`OMem`, `1Mem`, `Used-Mem`), and multiple output formats
- Extracts Predicate Information, Query Block Names, Column Projection, and Notes sections

**Interactive tree:**
- Click-to-expand detail panels, expand/collapse all, node selection with root-to-node path highlighting
- Cost percentage per node, visual indicators for full table scans and expensive operations (>50% of total cost)
- Summary bar: plan hash, operation count, total cost/buffers, full scan count, temp space usage
- Nested loop execution count display (`Loops: ×N`), suppressed in favor of real `Starts` when ALLSTATS data is available

**Analysis engine — 7 insight detectors:**

| Detector | What It Finds |
|----------|--------------|
| Correlated Subquery | `SORT AGGREGATE` with null cost; three confidence levels based on predicate availability |
| Expensive Full Scan | `TABLE ACCESS FULL` / `MAT_VIEW ACCESS FULL` >10% cost, >1K rows; recognizes parallel scans |
| Cartesian Join | Explicit `MERGE JOIN CARTESIAN` and implicit cartesians via `NESTED LOOPS` without access predicates |
| Temp Space Warning | Operations using ≥100MB temp (warning) or ≥1GB (critical) |
| Cost Imbalance | Hash joins where build side has ≥100:1 row ratio vs probe side, suggesting stale statistics |
| Index Recommendation | Filter predicates on full scans with >5K rows; skips function-wrapped columns, leading wildcards, and column-to-column comparisons |
| Cardinality Divergence | ALLSTATS plans where actual vs estimated rows diverge ≥10× (warning) or ≥100× (critical), normalized by `Starts` count |

### PL/SQL Dependency Viewer *(Coming Soon)*

Paste a package body and see which procedures call which, what tables they read and write. Interactive dependency graph.

### DDL Comparison *(Coming Soon)*

Compare two `CREATE TABLE` statements and see what changed semantically. Auto-generates the `ALTER` migration script.

## Architecture

Built with **Angular 21** using the **Angular Elements** (Web Components) pattern — each tool is an Angular component registered as a custom element and embedded in its own static HTML page. This gives each tool an independent, SEO-optimized landing page while Angular handles the interactive functionality.

```
Static HTML pages (SEO-optimized)
  │
  └── Angular Elements (Web Components)
        └── Each tool = independent custom element
              └── 100% client-side processing

Hosting:
  Cloudflare (Full Strict SSL, CDN, WAF)
    └── Nginx (reverse proxy)
          └── Hetzner VPS
```

**Privacy by design:** All parsing and analysis happens in the browser. No data is transmitted to any server, making the tools safe for production database artifacts that may contain sensitive table or column names.

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Frontend** | Angular 21, TypeScript, Angular Elements (Web Components) |
| **Infrastructure** | Hetzner VPS, Nginx, Cloudflare (Full Strict SSL, CDN, WAF) |

## Contact

- **Email:** nikolay.va.georgiev@gmail.com
- **LinkedIn:** [linkedin.com/in/nikolai-georgiev](https://www.linkedin.com/in/nikolai-georgiev-55b48a1a9)
- **GitHub:** [github.com/NikolayGeorgievv](https://github.com/NikolayGeorgievv)
