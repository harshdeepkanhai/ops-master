# GENIE-LEARN CURRICULUM

The map of the dojo. Read by the Master when planning a session.
Every stage is run through the Trial Cycle in SKILL.md (Riddle, Build, Break, Fix & Log, Decide, Boss).

---

## GATE 0: FOUNDATIONS (must clear before any Track)

No track opens until all eight are cleared. Each is a small Boss: Build it, Break it, Fix it, Log it.

| # | Foundation | Win condition (the learner proves it) |
|---|---|---|
| F1 | **Linux shell** | Navigate, create users, set permissions (`chmod`/`chown`), chain pipes, find a file by content, read a process list, kill a process cleanly. |
| F2 | **Text tools** | Filter and transform a 100k-line log with `grep`, `sed`, `awk`, `sort`, `uniq`, `cut`, `head`/`tail` using only the shell. |
| F3 | **Networking** | Explain a TCP connection, a port, DNS lookup, an HTTP status code family (2xx/3xx/4xx/5xx), and TLS in plain words. Use `curl`, `dig`, `ss` to prove it. |
| F4 | **Git** | Branch, commit, merge, rebase, resolve a conflict, revert a bad commit, explain why history matters. |
| F5 | **Python scripting** | Write a CLI script that reads a file, parses JSON, handles an error, and exits with a proper code. Virtual env and `pip` in use. |
| F6 | **SQL** | `SELECT`, `WHERE`, `GROUP BY`, `HAVING`, `JOIN` (inner and left), one window function (`ROW_NUMBER` or running total). |
| F7 | **Config formats** | Write valid YAML and JSON by hand. Spot the indentation bug that breaks a pipeline. |
| F8 | **Containers** | Explain image vs container. Write a Dockerfile, expose a port, mount a volume, read logs, and remove a stuck container. |
| F9 | **Statistics for ops and ML** | Explain mean, median, percentile (p95/p99), variance, train/test split, overfitting, and why averages lie for latency. |

**Gate 0 Boss (Small):** A shell script that reads an access log, reports the top 5 slowest endpoints by p95, and fails with exit code 2 if any p99 is above a threshold given as an argument.

---

## TRACK A: DEVOPS (build, ship, run, recover)

Core question of this track: *How does code get from a laptop to users safely, and how does it get back when it breaks?*

| Stage | Topic | Riddle (sample) | Build | Break | Boss (Medium) |
|---|---|---|---|---|---|
| A1 | Automation & scripting | "Why is a script you run by hand a debt, not a tool?" | Idempotent setup script | Run it twice; find the non-idempotent step | Provision a dev box from zero with one command |
| A2 | CI pipelines | "What is the cheapest place to find a bug?" | Pipeline: lint, test, build on every push | Break a test; see the pipeline go red | Pipeline that blocks a merge on failing tests |
| A3 | Containers in delivery | "Why does 'works on my machine' survive inside an image?" | Dockerfile with pinned base image and non-root user | Build with a missing file; read the layer cache | Multi-stage image under 100MB, runs as non-root |
| A4 | Infrastructure as Code | "What happens when the code and the real world disagree?" | Terraform (or OpenTofu) module, local provider, plan then apply | Edit a resource by hand; run plan and read the drift | Two environments (dev, prod) from one module with separate state |
| A5 | Config & secrets | "Where does a password go so it never reaches git?" | Env-based config, secret from a file outside the repo | Accidentally commit a fake key; use history rewrite locally to remove it | Config layering: defaults, env, secret store (local stand-in) |
| A6 | Observability | "A service is slow. What do you look at first, and why?" | Service exposing a health endpoint, logs in JSON, a metric counter | Overload it; find the saturation signal | Dashboard-style view: request rate, error rate, latency (RED method) |
| A7 | Deploy strategies | "How do you ship a change that might break everyone at once?" | Blue/green switch with a script | Ship a bad version; roll back in under 2 minutes | Canary release with automatic rollback on error rate |
| A8 | Reliability & incidents | "Who is responsible for the 3am page, and what do they need?" | SLO document: 99.5% availability over 30 days, error budget math | Simulated outage; run the runbook cold | Write a runbook and a blameless postmortem for a real drill |

**Track A Boss (Large):** Take a small app (a Python or Go API with a database), deliver it through CI, containers, IaC for two environments, canary deploy, dashboards, and a runbook. Survive the curveball: the database disk fills at 3am during the canary.

---

## TRACK B: DATAOPS (data moves, is checked, and is trusted)

Core question of this track: *How do you make data pipelines repeatable, tested, and trustworthy?*

| Stage | Topic | Riddle | Build | Break | Boss (Medium) |
|---|---|---|---|---|---|
| B1 | Data formats | "Why is CSV a trap for types?" | Convert the same dataset to CSV, JSON, Parquet, and compare sizes and types | Load a CSV with a leading-zero ID; watch it lose its zeros | Format decision record with cost and speed numbers |
| B2 | Batch ingestion | "What happens if the job runs twice?" | Ingest a file into a local database | Run the ingest twice; count the duplicates | Idempotent ingest keyed on a natural key or file hash |
| B3 | Orchestration | "What does a retry do to a half-finished write?" | Three-step pipeline with dependencies and retries | Kill step 2 halfway; restart | Scheduled pipeline with retries, alerting on failure, and safe re-runs |
| B4 | Data quality | "How do you know the data is wrong before a person does?" | Checks: not null, unique, in range, row count within expected band | Feed bad rows; watch which check catches them | Quality gate that stops the pipeline and writes a report |
| B5 | Schema & contracts | "Who breaks when a column is renamed?" | Schema file with versioned contract | Rename a column upstream; find the broken consumer | Schema change process with backwards-compatible rollout |
| B6 | Modeling & warehouse basics | "Why do analysts want one row per event, and why do dashboards want one row per day?" | Star schema: one fact table, two dimensions | Join on the wrong key; watch totals double | Model 3 source tables into a fact and dimensions with tests |
| B7 | Data CI/CD | "How do you test a query?" | Tests for models run in CI against a small sample | Change a model; find which test fails and why | Pipeline: branch, test on sample data, promote to prod with a manual gate |
| B8 | Streaming concepts | "What is the difference between 'at least once' and 'exactly once', in money terms?" | Consume a stream of events with a local broker | Duplicate messages on purpose; find the double-count | Stream job with dedup and a dead-letter queue |

**Track B Boss (Large):** A pipeline from raw files to a modeled warehouse layer, with quality gates, orchestration, schema contracts, CI tests, lineage notes, and a backfill plan. Curveball: the source sends a new column in the middle of a backfill.

---

## TRACK C: MLOPS (models that survive contact with production)

Core question of this track: *How do you train, version, deploy, and watch a model so that the same input gives the same answer next month?*

| Stage | Topic | Riddle | Build | Break | Boss (Medium) |
|---|---|---|---|---|---|
| C1 | Model basics | "Why is a 99% training score a warning, not a win?" | Train a simple classifier (scikit-learn), hold out a test set | Leak the test set into training; see the score jump | Honest baseline with a written evaluation |
| C2 | Experiment tracking | "Six months later, which run produced this model?" | Log parameters, metrics, and artifacts to a local tracker (e.g. MLflow) | Delete a run; find what it cost you | Tracked sweep of 10 runs with a chosen winner and reason |
| C3 | Reproducibility | "Why does the same code give a different score on another laptop?" | Pin dependencies, set seeds, record the data hash | Change the library version; rerun; watch the number move | Reproduce a run from its record on a clean machine |
| C4 | Packaging & serving | "What does the model need at runtime, besides the weights?" | Serve a model behind an HTTP API (FastAPI) with input validation | Send a malformed payload; find the crash and fix the contract | Containerized model service with a health check and a versioned endpoint |
| C5 | Training pipelines | "What should run automatically, and what should a human approve?" | Pipeline: data check, train, evaluate, write metrics | Make the evaluation step fail; confirm the pipeline stops | Scheduled retraining pipeline with a quality gate |
| C6 | Model registry & promotion | "What does 'promote to production' actually change?" | Registry with staging and production stages | Promote a worse model; find the gate that should have stopped it | Promotion rule: new model must beat production on a fixed test set, with rollback |
| C7 | Monitoring & drift | "The inputs look the same. How can the model be wrong?" | Track input distribution per feature against training baseline | Shift one feature on purpose; watch the alert | Drift monitor plus prediction-quality proxy when labels are late |
| C8 | Retraining triggers | "When should you retrain: on a clock, on drift, or on a human request?" | Trigger on drift threshold with a cooldown | Trigger a retrain loop by accident; find the missing cooldown | Retraining policy written as an ADR, implemented and tested |

**Track C Boss (Large):** A model from raw data to a monitored API in production-shaped environments, with tracked experiments, a reproducible pipeline, a registry with a promotion gate, drift monitoring, and a rollback drill. Curveball: a feature upstream changes its units from meters to feet.

---

## TRACK D: AIOPS (machines helping operators, under human control)

Core question of this track: *How do you use data and models to reduce operator noise and find problems earlier, without letting the machine do something dangerous?*

| Stage | Topic | Riddle | Build | Break | Boss (Medium) |
|---|---|---|---|---|---|
| D1 | Monitoring fundamentals | "Why is an alert that fires every night worse than no alert?" | Alert rules for error rate and latency with thresholds | Set a threshold too tight; count false pages in a week | Alert set with a written reason and owner for each rule |
| D2 | Time-series anomaly detection | "What counts as 'normal' for a traffic curve that has a daily cycle?" | Rolling z-score on a metric with seasonality ignored; then add weekly baseline | Feed a holiday spike; count false positives | Detector with seasonality, evaluated on labelled incidents |
| D3 | Log parsing & clustering | "How do you turn a million log lines into ten problems?" | Template extraction from logs (regex first, then a simple clustering method) | Feed a new log format; watch the clusters explode | Cluster report: top 10 error templates, counts, first-seen time |
| D4 | Alert correlation & noise reduction | "Ten alerts fire at once. Is that ten problems or one?" | Group alerts by time window and by service dependency | Remove the dependency map; watch the grouping fail | Correlation step that reduces pages by a measured percentage without missing the labelled incidents |
| D5 | Automated remediation | "What is the difference between a runbook step a machine can do and one it must never do?" | Remediation script behind a dry-run flag | Run it without the flag in a sandbox; see the damage, then add the approval gate | Remediation with approval gate, audit log, and automatic stop on repeated failure |
| D6 | Evaluating AIOps | "How do you prove your detector is useful and not just busy?" | Precision and recall on labelled incidents | Tune the threshold to hide a real incident; find it in the numbers | Evaluation report with false-positive rate per week and missed-incident list |
| D7 | Guardrails & human-in-the-loop | "What must stay a human decision, always?" | Written policy: actions that need approval, actions that are forbidden | Attempt a forbidden action through the bot; see the guard stop it | Guardrail test suite that runs in CI |

**Track D Boss (Large):** Take a service with real-shaped logs and metrics. Build detection, clustering, correlation, and one approved remediation. Evaluate on labelled incidents. Curveball: the monitoring system itself goes silent. The AIOps layer must say so, not guess.

---

## CROSS-TRACK CAPSTONE (after all four tracks)

Build one system that touches all four: an app (A) delivered by CI and IaC, with a data pipeline (B) feeding a model (C), monitored and triaged by AIOps (D), with an ADR for every major decision and a runbook for every failure you have logged.

The Master grades the capstone by asking the learner to defend three decisions out loud, with the journal open, and by reading the Error Log for patterns.

---

## RANK TABLE (for the journal)

| Rank | Requirement |
|---|---|
| Apprentice | Gate 0 cleared |
| Journeyman | One full track cleared (all stages and Boss) |
| Master | Two full tracks cleared, and the Cross-Track Capstone started |
| Genie Master | All four tracks, the capstone defended, and at least 40 Error Log entries written |
