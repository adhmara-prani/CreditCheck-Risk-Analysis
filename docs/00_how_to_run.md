# How to run the ingestion pipeline

This is the reproduce-from-scratch guide for loading the raw LendingClub CSV into
Postgres. It assumes nothing about prior state beyond a clone of this repo and the
source file. If a step needs something external (a running database, the raw CSV),
that is called out — the scripts never invent credentials or data.

## Prerequisites

- **Python 3.12+** and **PostgreSQL 14+** running and reachable.
- The raw CSV placed at `data/raw/accepted_2007_to_2018Q4.csv`. It is immutable and
  gitignored (~1.6 GB); obtain it from the source (the `wordsforthewise` LendingClub
  mirror) and drop it in — do not edit or re-encode it.

## One-time setup

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1          # PowerShell;  source .venv/bin/activate on *nix
pip install -r requirements.txt

Copy-Item .env.example .env          # then edit .env and set DB_PASSWORD
```

Create the target database (the loader will not create it for you):

```sql
CREATE DATABASE lending;
```

## The pipeline

Run in order. Each step is independent, re-runnable, and writes a generated
artifact you can inspect before moving on. Generated files are never hand-edited —
fix the script and re-run.

| # | Command | Produces | Touches DB? |
|---|---------|----------|-------------|
| 1 | `python scripts/00_profile_csv.py`  | `docs/01_profiling_notes.md` | No |
| 2 | `python scripts/01_generate_ddl.py` | `sql/01_create_raw.sql`      | No |
| 3 | `python scripts/02_load_raw.py`     | `public.loans_raw`, `docs/02_load_log.md` | Yes |

**1. Profile** — measures the file (size, hash, encoding, columns, value sets,
emptiness). Read the notes before generating any schema.

**2. Generate DDL** — emits `CREATE TABLE loans_raw` from the file's header (every
column `text`, every identifier quoted). Confirm its column count matches the
profile's.

**3. Load** — applies the DDL and streams the CSV into `loans_raw` via native
`COPY`. Re-run with `--force` to drop and reload:

```powershell
python scripts/02_load_raw.py --force
```

## Building the analytical model

With `loans_raw` populated, run the SQL in numeric order against the `lending`
database. Each file drops and rebuilds what it owns, so the whole set is safe to
re-run from any state.

| # | File | Produces |
|---|------|----------|
| 4 | `sql/02_staging.sql` | `loans` — 2,260,668 typed at-origination rows + target |
| 5 | `sql/03_performance.sql` | `loan_performance` — realised economics, keyed by `id` |
| 6 | `sql/05_bi_view.sql` | `vw_loans_bi` — the 28-column Power BI import contract |
| 7 | `sql/06_pd_features.sql` | `vw_pd_features` — modelling population (settled loans ≤2015) |

`sql/04_analysis.sql` is read-only: seven queries that reproduce the findings in the
README. Run it any time after step 5.

**Order matters, and so does re-running.** Steps 4 and 5 drop the views built in
steps 6 and 7 before rebuilding their tables, because Postgres will not drop a table
a view depends on. So a rebuild of `loans` or `loan_performance` must be followed by
re-running `05_bi_view.sql` and `06_pd_features.sql`. Any new view added over those
tables has to be listed in the `DROP VIEW` block at the top of both files.

## Running the PD model

```powershell
python models/pd_model.py --plot
```

Requires the modelling dependencies in `requirements.txt` (pandas, scikit-learn,
matplotlib) and `vw_pd_features` from step 7. Trains on 2007–2014, scores the 2015
vintage once, and writes figures to `models/charts/`. Findings and the accepted-loans
-only caveat are in [`04_pd_model.md`](04_pd_model.md).

## Verifying a load

After step 3, `docs/02_load_log.md` should show:
- `sent == COPY rowcount == count(*)` (reconciliation passed, or the load raises),
- zero NULLs in `loans_raw`,
- every source column present in `information_schema` (the load-nothing guarantee),
- any malformed rows listed with line number and content.

## Troubleshooting

- **Connection fails** → the loader prints the exact driver error and stops. Check
  `.env` and that Postgres is running; the script will not guess credentials.
- **`loans_raw already exists`** → re-run with `--force`.
- **Malformed rows reported** → expected and non-fatal; they are logged and skipped.
  Inspect them in the load log.
