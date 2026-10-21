# Phase 02 · Part B — Hands-On Labs and Project 1

**Python for Professional Data Engineering** · Step-by-step procedures

> **How this file fits with Part A**
> - **Part A (`phase02_part-a_content-notes.md`):** the ideas and examples.
> - **Part B (this file):** the labs. Each lab names the section of Part A to read first.
>
> **Tip:** read the matching notes for 20–30 minutes, then come here and practise. If you get stuck, go back to the notes, then to the troubleshooting table at the end.

---

## Contents

- [Lab map and timing](#lab-map-and-timing)
- [Ground rules](#ground-rules)
- [Lab 0 — Set up Python](#lab-0--set-up-python)
- [Lab 1 — Python fundamentals practice](#lab-1--python-fundamentals-practice)
- [Lab 2 — The sales CSV cleaner](#lab-2--the-sales-csv-cleaner)
- [Lab 3 — Intermediate Python practice](#lab-3--intermediate-python-practice)
- [Lab 4 — Data processing](#lab-4--data-processing)
- [Project 1 — Public API to structured dataset](#project-1--public-api-to-structured-dataset)
- [Troubleshooting guide](#troubleshooting-guide)
- [Final checklist](#final-checklist)

---

## Lab map and timing

| Week | Lab | Read first (Part A) | Time |
| --- | --- | --- | --- |
| 1 | Lab 0: Python setup | Section 1 | 1 h |
| 1 | Lab 1: Fundamentals practice | Module 2.1 | 6 h |
| 1 | Lab 2: Sales CSV cleaner | Module 2.1 | 3 h |
| 2 | Lab 3: Intermediate practice | Module 2.2 | 10–12 h |
| 3 | Lab 4: Data processing | Module 2.3 | 10–12 h |
| 4 | Project 1 | Module 2.4 | 12 h |

## Ground rules

1. **Type, don't paste.**
2. **Lines starting with `$`** are typed in the terminal (not the `$`); lines starting with `>>>` are typed in the Python REPL.
3. **Always activate your virtual environment** (`source .venv/bin/activate`) in each new terminal. The prompt should start with `(.venv)`.
4. **Commit as you go,** on branches, with clear messages (Phase 01 habits).
5. Each lab ends with a **"You are done when…"** box. Tick every item before moving on.

---

## Lab 0 — Set up Python

*Read first: Part A, section 1.* About 1 hour.

### Step 1 — Check your Python

Python 3.10 or newer is needed (Ubuntu 24.04 ships 3.12).

```bash
$ python3 --version
$ which python3
```

### Step 2 — Make sure venv and pip are installed

```bash
$ sudo apt update && sudo apt install -y python3-venv python3-pip
```

### Step 3 — Create a practice folder and a virtual environment

```bash
$ mkdir -p ~/de-journey/python-practice && cd ~/de-journey/python-practice
$ python3 -m venv .venv
$ source .venv/bin/activate
$ python --version
```

The prompt now starts with `(.venv)`. Run `deactivate` to leave it, and **activate it every time** you open a new terminal for this project.

### Step 4 — Install the practice tools

```bash
$ pip install --upgrade pip
$ pip install pandas pyarrow requests pytest ruff jupyterlab
$ pip list
```

### Step 5 — Connect VS Code

Open the folder (`code .`), install the **Python** extension, then press `Ctrl+Shift+P` → "Python: Select Interpreter" → choose the one inside `.venv`.

### Step 6 — Never commit the environment

```bash
$ echo ".venv/" >> ~/de-journey/.gitignore
```

### Alternative: Miniforge (conda-style environments)

If you prefer conda-style environments (useful for packages with heavy native dependencies), install **Miniforge** from its GitHub releases page, then:

```bash
$ conda create -n de python=3.12 -y
$ conda activate de
$ pip install pandas pyarrow requests pytest ruff
```

Use **one** approach per project. This guide uses `venv` + `pip`.

### Step 7 — Health check

```bash
$ python -c "import pandas, pyarrow, requests; print(pandas.__version__, pyarrow.__version__)"
$ pytest --version
$ ruff --version
```

> ✅ **You are done when:** all three commands work inside the activated environment.

---

## Lab 1 — Python fundamentals practice

*Read first: Part A, Module 2.1.* Create a folder `~/de-journey/python-practice/exercises/` and one file per exercise. Each exercise has hints; try before you peek.

### Exercise 1 — Receipt (Part A 3.1)

Write `receipt.py` that stores three items (name, quantity, unit price) in variables, prints one formatted line per item, and prints a total with thousands separators.

- *Hint:* use f-strings like `f"{total:,.2f}"`.
- *Check:* change the numbers and verify the total by hand.

### Exercise 2 — Valid quantities (Part A 3.2)

Given `quantities = [3, 10, 0, 25, 4]`, build:

- (a) a list of only the valid quantities (greater than 0),
- (b) the total of valid quantities,
- (c) a dictionary mapping each valid quantity to its band (reuse `size_band`).

- *Hint:* list comprehension with an `if`; `sum()`; dict comprehension.
- *Expected:* (a) `[3, 10, 25, 4]` (b) `42`.

### Exercise 3 — Count stores two ways (Part A 3.3)

Using the Phase 01 `sales_sample.csv`:

1. Read the file with plain `open()` and `str.split(",")` (no `csv` module).
2. Count records per store with a normal dictionary.
3. Repeat with `collections.Counter`.

- *Hint:* skip the header; skip malformed lines (they do not have 6 fields).
- *Notice:* what happens to the line that has too few fields? Write one sentence about it.

### Exercise 4 — `parse_price` (Part A 3.4)

Write `parse_price(text: str) -> float` that accepts `"1,250.50"` and returns `1250.5`. Then write **three examples** you will later turn into tests, for instance `"185"`, `"0.99"`, `"1,250.50"`.

### Exercise 5 — `safe_ratio` (Part A 3.5)

Write `safe_ratio(a, b)` that returns `None` when `b == 0`, **without** using a bare `except`.

- *Think and write one sentence:* why might returning `None` hide a problem in a pipeline? (Think about what happens when `None` flows into a sum.)

### Exercise 6 — `read_json_file` (Part A 3.6)

Write `read_json_file(path)` that returns the parsed content. It must raise a **clear error that includes the path** if the file is missing or contains invalid JSON.

- *Hint:* catch `FileNotFoundError` and `json.JSONDecodeError`, then `raise ... from exc`.
- *Check:* try a missing file and a file containing `{not json`.

### Exercise 7 — A tiny package (Part A 3.7)

1. Create the layout:

   ```
   my_project/
   └── src/
       └── my_package/
           ├── __init__.py
           ├── cleaning.py
           └── report.py
   ```

2. Put `normalize_store` in `cleaning.py` and import it from `report.py`.
3. Run it from `my_project/src` with:

   ```bash
   $ python -m my_package.report
   ```

4. Run `pip freeze` and note which packages appear that you did not install directly (**transitive dependencies**).

### Exercise 8 — Find two bugs (Part A 3.8)

This code has two bugs. Find and fix them using the debugging procedure (traceback bottom-up, smallest input, `breakpoint()`).

```python
def average(values):
    total = 0
    for v in values:
        total += v
    return total / len(values)

print(average([]))            # bug 1: what happens?
print(average(["3", "4"]))    # bug 2: what happens?
```

- *Hint:* one raises `ZeroDivisionError`, the other `TypeError`. Decide what the function **should** do in each case, then make it do that on purpose.

> ✅ **You are done when:**
> - [ ] I can explain the difference between `None`, `0`, `""` and `[]`.
> - [ ] I can read a CSV with `csv.DictReader` and write a cleaned file (next lab).
> - [ ] I can explain why a bare `except: pass` is dangerous in a pipeline.
> - [ ] I can create and activate a virtual environment and install packages.
> - [ ] I found both bugs using a traceback and `breakpoint()`.

---

## Lab 2 — The sales CSV cleaner

*Read first: Part A, Module 2.1 (3.5 and 3.6).* About 3 hours. This is the warm-up for Project 1: validate each row, split data into *clean* and *rejected* with a reason, and never lose a record silently.

**Input:** the `sales_sample.csv` from Phase 01 (12 data rows with deliberate errors).

### Step 1 — Copy the sample into your practice folder

```bash
$ cd ~/de-journey/python-practice
$ mkdir -p data/raw data/processed data/rejected scripts
$ cp ~/de-journey/data/raw/sales_sample.csv data/raw/
```

### Step 2 — Create `scripts/clean_sales.py`

```python
# scripts/clean_sales.py
"""Clean the Phase 01 sales CSV: validate each row and split clean from rejected."""
import csv
import sys
from datetime import date
from pathlib import Path

EXPECTED = ["order_id", "store", "product", "quantity", "unit_price", "sale_date"]


def validate(row: dict) -> str | None:
    """Return a rejection reason, or None if the row is valid."""
    # DictReader puts extra fields under the key None and gives None for missing fields.
    if None in row or any(value is None for value in row.values()):
        return "wrong_field_count"
    if row["quantity"].strip() == "":
        return "missing_quantity"
    try:
        quantity = int(row["quantity"])
    except ValueError:
        return "bad_quantity"
    if quantity <= 0:
        return "non_positive_quantity"
    try:
        float(row["unit_price"])
    except ValueError:
        return "bad_unit_price"
    try:
        date.fromisoformat(row["sale_date"])
    except ValueError:
        return "bad_date"
    return None


def clean_file(src: Path, out_dir: Path) -> dict:
    seen_ids: set[str] = set()
    clean, rejected = [], []

    with open(src, newline="", encoding="utf-8") as f:
        for line_no, row in enumerate(csv.DictReader(f), start=2):
            reason = validate(row)
            if reason is None and row["order_id"] in seen_ids:
                reason = "duplicate_order_id"
            if reason:
                rejected.append({"line": line_no, "reason": reason, "order_id": row.get("order_id")})
                continue
            seen_ids.add(row["order_id"])
            row["line_total"] = round(int(row["quantity"]) * float(row["unit_price"]), 2)
            clean.append(row)

    out_dir.mkdir(parents=True, exist_ok=True)
    with open(out_dir / "sales_clean.csv", "w", newline="", encoding="utf-8") as f:
        writer = csv.DictWriter(f, fieldnames=EXPECTED + ["line_total"])
        writer.writeheader()
        writer.writerows(clean)
    with open(out_dir / "sales_rejected.csv", "w", newline="", encoding="utf-8") as f:
        writer = csv.DictWriter(f, fieldnames=["line", "reason", "order_id"])
        writer.writeheader()
        writer.writerows(rejected)

    return {"read": len(clean) + len(rejected), "clean": len(clean), "rejected": len(rejected)}


if __name__ == "__main__":
    if len(sys.argv) != 3:
        sys.exit("Usage: python scripts/clean_sales.py INPUT.csv OUTPUT_DIR")
    summary = clean_file(Path(sys.argv[1]), Path(sys.argv[2]))
    print(summary)
    if summary["read"] != summary["clean"] + summary["rejected"]:
        sys.exit("Reconciliation failed: records were lost")
```

### Step 3 — Run it and inspect the outputs

```bash
$ python scripts/clean_sales.py data/raw/sales_sample.csv data/processed
$ cat data/processed/sales_rejected.csv
```

**What you should see:** the summary `{'read': 12, 'clean': 6, 'rejected': 6}`, and these rejection reasons:

| Order | Reason |
| --- | --- |
| 1004 | `missing_quantity` |
| 1002 (second occurrence) | `duplicate_order_id` |
| 1007 | `wrong_field_count` (too few fields) |
| 1010 | `wrong_field_count` (too many fields) |
| 1008 | `non_positive_quantity` |
| 1009 | `bad_date` |

### Step 4 — Reconcile

Read count = clean + rejected. The script checks this itself. This is the first appearance of a principle you will use in every phase: **account for every record.**

### Step 5 — Extension tasks

- Add a `--strict` option (using `argparse`) that exits with an error if any row was rejected.
- Write the rejected rows to a `.jsonl` file that includes the original raw values.
- Add a revenue-by-store summary from the clean rows and compare it with your Phase 01 Bash report.

### Step 6 — Commit your work

```bash
$ git switch -c feat/sales-cleaner
$ git add scripts/clean_sales.py
$ git commit -m "feat: add sales CSV cleaner"
$ git push -u origin feat/sales-cleaner
```

Then open a pull request on GitHub.

> ✅ **You are done when:** the summary matches, you can explain each rejection reason, and the PR is open or merged.

---

## Lab 3 — Intermediate Python practice

*Read first: Part A, Module 2.2.* Work in `~/de-journey/python-practice/`. **Every function you write here gets a test.**

### Exercise 1 — `batched` generator (Part A 4.1)

Write a generator `batched(iterable, size)` that yields lists of up to `size` items (useful for batch inserts).

- *Test:* with 10 items and `size=4`, the batch lengths must be `[4, 4, 2]`.
- *Also test:* an empty input yields nothing.

### Exercise 2 — `working_directory` context manager (Part A 4.2)

Write a context manager `working_directory(path)` that changes the current directory inside the block and **always** restores it, even if the block raises an error.

- *Hint:* `@contextmanager`, `os.chdir`, and a `try/finally`.
- *Test:* use `tmp_path`; assert the directory is restored after a normal exit **and** after an exception (`pytest.raises`).

### Exercise 3 — Retry decorator, improved (Part A 4.4)

Start from the `retry` decorator in Part A and:

1. Replace `print` with `logging.warning`.
2. Write a test showing that a function that always fails is called **exactly `times` times** and then raises.
3. Write a test showing that an exception **not** in `exceptions` is raised immediately (called once).

- *Tip:* pass `delay=0` in tests so they run instantly.

### Exercise 4 — `parse_quantity` tests (Part A 4.9)

Create `test_quantity.py` with a parametrised test covering `"3"`, `""`, `"-1"`, `"abc"`, `" 7 "` and `"3.5"`.

- *Decide first:* what is the correct behaviour for each input? Let the tests **document your decisions**.
- *Note:* `int(" 7 ")` works but `int("3.5")` raises. Is that what you want?

### Exercise 5 — `to_utc_iso` (Part A 4.10)

Write `to_utc_iso(local_text: str, tz_name: str) -> str` converting `"2026-10-21 17:30"` in `Africa/Nairobi` into UTC ISO format.

- *Expected:* `2026-10-21T14:30:00+00:00`.
- *Test:* include a second time zone and an invalid time zone name (what should happen?).

### Exercise 6 — Dataclass settings (Part A 4.5)

Create a frozen `Settings` dataclass with `country`, `per_page` and `tags`. Show that `replace(s, per_page=50)` returns a new object and that assigning to a frozen field raises an error. Write a test for both.

### Exercise 7 — Logging (Part A 4.6)

Write a small script that configures logging to **both** the console and `logs/practice.log`, then logs one message at each level. Run it, then `cat logs/practice.log`.

- *Check:* with the level set to `INFO`, the `DEBUG` message must not appear.

> ✅ **You are done when:**
> - [ ] I can explain why a generator uses less memory than a list.
> - [ ] I wrote a context manager and a decorator, each with a test.
> - [ ] I write tests with `parametrize`, `pytest.raises` and `tmp_path`.
> - [ ] I store timestamps in UTC and can convert from `Africa/Nairobi`.
> - [ ] `pytest -v` is all green and `ruff check .` is clean.

---

## Lab 4 — Data processing

*Read first: Part A, Module 2.3.* Four parts: measure, compare, prove idempotency, build an incremental loader.

### Part 1 — Generate a larger dataset

```bash
$ cd ~/de-journey/python-practice
$ mkdir -p labs data/raw
$ touch labs/__init__.py
```

Create `labs/make_big_csv.py`:

```python
# labs/make_big_csv.py
"""Generate a reproducible synthetic sales CSV for performance experiments."""
import csv
import random
import sys
from datetime import date, timedelta

STORES = ["Nairobi CBD", "Nairobi Westlands", "Mombasa Nyali", "Kisumu Central", "Nakuru Town", "Eldoret Town"]
PRODUCTS = [("Maize Flour 2kg", 185), ("Cooking Oil 1L", 320), ("Sugar 1kg", 150), ("Rice 2kg", 420), ("Milk 500ml", 60), ("Bread 400g", 65)]


def main(path: str, rows: int, seed: int = 42) -> None:
    rng = random.Random(seed)
    start = date(2026, 1, 1)
    with open(path, "w", newline="", encoding="utf-8") as f:
        writer = csv.writer(f)
        writer.writerow(["order_id", "store", "product", "quantity", "unit_price", "sale_date"])
        for i in range(1, rows + 1):
            product, price = rng.choice(PRODUCTS)
            writer.writerow([i, rng.choice(STORES), product, rng.randint(1, 12), price,
                             (start + timedelta(days=rng.randint(0, 270))).isoformat()])


if __name__ == "__main__":
    main(sys.argv[1], int(sys.argv[2]))
```

```bash
$ python labs/make_big_csv.py data/raw/big_sales.csv 200000
$ ls -lh data/raw/big_sales.csv
```

### Part 2 — Compare four ways to process 200,000 rows

Create `labs/compare_methods.py`:

```python
# labs/compare_methods.py
"""Compare csv streaming, full pandas, chunked pandas and Parquet column reads."""
import csv
import sys
import time
import tracemalloc
from collections import defaultdict
from pathlib import Path

import pandas as pd


def measure(label, func):
    tracemalloc.start()
    start = time.perf_counter()
    result = func()
    elapsed = time.perf_counter() - start
    _, peak = tracemalloc.get_traced_memory()
    tracemalloc.stop()
    print(f"{label:<28} {elapsed:6.2f}s   peak memory {peak / 1e6:7.1f} MB")
    return result


def stream_csv(path):
    totals = defaultdict(float)
    with open(path, newline="", encoding="utf-8") as f:
        for row in csv.DictReader(f):
            totals[row["store"]] += int(row["quantity"]) * float(row["unit_price"])
    return dict(totals)


def pandas_full(path):
    df = pd.read_csv(path)
    return (df["quantity"] * df["unit_price"]).groupby(df["store"]).sum().to_dict()


def pandas_chunked(path):
    totals = pd.Series(dtype="float64")
    for chunk in pd.read_csv(path, chunksize=50_000):
        part = (chunk["quantity"] * chunk["unit_price"]).groupby(chunk["store"]).sum()
        totals = totals.add(part, fill_value=0)
    return totals.to_dict()


def parquet_columns(path):
    df = pd.read_parquet(path, columns=["store", "quantity", "unit_price"])
    return (df["quantity"] * df["unit_price"]).groupby(df["store"]).sum().to_dict()


if __name__ == "__main__":
    csv_path = Path(sys.argv[1])
    parquet_path = csv_path.with_suffix(".parquet")
    pd.read_csv(csv_path).to_parquet(parquet_path, index=False)
    print(f"CSV size:     {csv_path.stat().st_size / 1e6:6.1f} MB")
    print(f"Parquet size: {parquet_path.stat().st_size / 1e6:6.1f} MB\n")

    a = measure("csv module, streaming", lambda: stream_csv(csv_path))
    b = measure("pandas, whole file", lambda: pandas_full(csv_path))
    c = measure("pandas, chunks of 50,000", lambda: pandas_chunked(csv_path))
    d = measure("parquet, 3 columns only", lambda: parquet_columns(parquet_path))

    same = all(abs(a[k] - x[k]) < 1e-6 for x in (b, c, d) for k in a)
    print("\nAll four methods agree:", same)
```

```bash
$ python labs/compare_methods.py data/raw/big_sales.csv
```

**Read your results.** Your absolute numbers depend on your machine, and `tracemalloc` itself slows code down, so compare the methods **relative to each other**. Copy your own output into `notes/04-processing-comparison.md` using a table like this:

| Method | Time (s) | Peak memory (MB) |
| --- | --- | --- |
| csv module, streaming | | |
| pandas, whole file | | |
| pandas, chunks of 50,000 | | |
| parquet, 3 columns only | | |

Then answer in your notes:

- Which method used the least memory? The least time?
- How much smaller is Parquet than CSV, and why?
- When would streaming with the `csv` module beat pandas, and when would pandas win?
- What happens to your answer at 20 million rows?

*What to expect (the pattern, not exact numbers):* streaming keeps memory low but is slower per row; whole-file pandas is fast but holds everything in memory; chunking sits between them; Parquet is much smaller on disk and reading only three columns is fast. The final line must say `All four methods agree: True`.

### Part 3 — Prove idempotency with a test

Create `labs/upsert.py`:

```python
# labs/upsert.py
import pandas as pd

KEY = ["order_id"]


def upsert(existing: pd.DataFrame, new: pd.DataFrame) -> pd.DataFrame:
    """Combine and keep the last occurrence per key; same inputs always give the same output."""
    combined = pd.concat([existing, new], ignore_index=True)
    return (
        combined.drop_duplicates(subset=KEY, keep="last")
        .sort_values(KEY)
        .reset_index(drop=True)
    )
```

Create `tests/test_upsert.py`:

```python
# tests/test_upsert.py
import pandas as pd
from labs.upsert import upsert


def test_rerun_is_idempotent():
    base = pd.DataFrame({"order_id": [1, 2], "amount": [10.0, 20.0]})
    batch = pd.DataFrame({"order_id": [2, 3], "amount": [25.0, 30.0]})

    once = upsert(base, batch)
    twice = upsert(once, batch)          # run the same batch again

    pd.testing.assert_frame_equal(once, twice)
    assert once["amount"].tolist() == [10.0, 25.0, 30.0]   # order 2 updated, order 3 added
```

Run the test (use `python -m pytest` so your project folder is on the import path):

```bash
$ mkdir -p tests
$ python -m pytest -v
```

### Part 4 — Build an incremental loader

First create `labs/watermark.py`:

```python
# labs/watermark.py
import json
from pathlib import Path

STATE = Path("state/watermark.json")


def load_watermark(default: str = "1970-01-01") -> str:
    if STATE.exists():
        return json.loads(STATE.read_text(encoding="utf-8"))["last_sale_date"]
    return default


def save_watermark(value: str) -> None:
    STATE.parent.mkdir(parents=True, exist_ok=True)
    STATE.write_text(json.dumps({"last_sale_date": value}), encoding="utf-8")
```

Now build `labs/incremental_load.py` yourself. Follow this outline:

```python
# labs/incremental_load.py  (skeleton: fill in the TODOs)
import sys
from pathlib import Path

import pandas as pd

from labs.upsert import upsert
from labs.watermark import load_watermark, save_watermark

TARGET = Path("data/processed/sales.parquet")


def load_incrementally(csv_path: str) -> dict:
    # TODO 1: read the watermark
    # TODO 2: read the CSV and keep only rows with sale_date AFTER the watermark
    # TODO 3: if a target Parquet exists, read it; otherwise start with an empty frame
    # TODO 4: upsert the new rows into the existing data
    # TODO 5: write the result to TARGET (write to a .tmp file, then os.replace)
    # TODO 6: ONLY NOW advance the watermark to the latest sale_date seen
    # TODO 7: return a summary: rows read, rows new, rows total
    ...


if __name__ == "__main__":
    print(load_incrementally(sys.argv[1]))
```

Then:

1. Run it twice on `big_sales.csv`.
2. Write a test (in `tests/`) that runs it twice on a small CSV in `tmp_path` and asserts the **second run changes nothing**. (Use `monkeypatch.chdir(tmp_path)` so the watermark and output files land in the temp folder.)
3. Write a second test: if writing fails (for example, simulate with `monkeypatch` raising an error), the watermark must **not** advance.

> ✅ **You are done when:**
> - [ ] I can choose between CSV, JSON, JSON Lines and Parquet for a task and justify it.
> - [ ] I check `df.shape` after every pandas transformation.
> - [ ] I measured time and memory and wrote my conclusions in `notes/04-processing-comparison.md`.
> - [ ] I can explain which operations cannot be chunked.
> - [ ] My incremental loader passes an idempotency test.

---

## Project 1 — Public API to structured dataset

*Read first: Part A, Module 2.4 (the thinking behind the project).* About 12 hours.

**Completion test (keep this in mind for every step):** if the API is temporarily unavailable, the script **fails clearly, preserves previously collected data and provides enough logs to diagnose the issue.**

### Deliverables

- [ ] A reusable API client
- [ ] Pagination and retry handling
- [ ] Raw JSON saved with an extraction timestamp
- [ ] A cleaned CSV and Parquet dataset
- [ ] Logging and a summary of rejected records
- [ ] Automated tests for transformation logic
- [ ] A GitHub README with setup instructions and sample output

### Repository layout (the target)

```
api-pipeline-project/
├── README.md
├── pyproject.toml
├── .gitignore
├── .env.example
├── src/
│   └── api_pipeline/
│       ├── __init__.py
│       ├── __main__.py        # command-line entry point
│       ├── config.py          # settings from environment
│       ├── logging_setup.py   # console + file logging
│       ├── client.py          # HTTP client, retries, pagination
│       ├── transform.py       # validation and cleaning
│       └── pipeline.py        # extract -> raw -> transform -> load -> summary
├── tests/
│   ├── conftest.py            # shared fixture: a local mock API
│   ├── mock_api.py            # tiny World Bank-shaped server
│   ├── test_transform.py
│   ├── test_client.py
│   └── test_pipeline.py
├── data/                      # generated; git-ignored
│   ├── raw/
│   ├── processed/
│   └── rejected/
└── logs/                      # generated; git-ignored
```

### Step 1 — Create the repository and environment

```bash
$ mkdir -p ~/de-journey/api-pipeline-project/{src/api_pipeline,tests} && cd ~/de-journey/api-pipeline-project
$ git init
$ python3 -m venv .venv && source .venv/bin/activate
```

> **Tip:** this folder is its own Git repository inside `~/de-journey`. To keep the two from confusing each other, add `api-pipeline-project/` to the parent repo's `.gitignore`, or create the project outside `~/de-journey`.

### Step 2 — Project files

```bash
$ cat > .gitignore <<'EOF'
.venv/
__pycache__/
*.pyc
.pytest_cache/
.ruff_cache/
*.egg-info/
.env
data/
logs/
EOF

$ cat > .env.example <<'EOF'
# Copy to .env for local use. Never commit real values.
API_BASE_URL=https://api.worldbank.org/v2
API_COUNTRY=KEN
API_INDICATOR=NY.GDP.MKTP.CD
API_PER_PAGE=100
API_TIMEOUT_S=10
API_MAX_RETRIES=4
API_BACKOFF_S=1.0
EOF
```

Create `pyproject.toml`:

```toml
# pyproject.toml
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"

[project]
name = "api-pipeline"
version = "0.1.0"
description = "Public API to structured dataset: a small, tested ETL pipeline"
requires-python = ">=3.10"
dependencies = ["requests", "pandas", "pyarrow"]

[project.optional-dependencies]
dev = ["pytest", "ruff"]

[tool.setuptools.packages.find]
where = ["src"]

[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.ruff]
line-length = 100
target-version = "py310"

[tool.ruff.lint]
select = ["E", "F", "I", "B", "UP"]
```

Install the project in **editable** mode with the development tools:

```bash
$ pip install --upgrade pip
$ pip install -e ".[dev]"
```

### Step 3 — Configuration and logging

```python
# src/api_pipeline/__init__.py
__version__ = "0.1.0"
```

```python
# src/api_pipeline/config.py
"""Settings read from environment variables, with safe defaults."""
import os
from dataclasses import dataclass
from pathlib import Path


@dataclass(frozen=True)
class Settings:
    base_url: str = "https://api.worldbank.org/v2"
    country: str = "KEN"
    indicator: str = "NY.GDP.MKTP.CD"
    per_page: int = 100
    timeout_s: float = 10.0
    max_retries: int = 4
    backoff_s: float = 1.0
    data_dir: Path = Path("data")
    log_dir: Path = Path("logs")

    @classmethod
    def from_env(cls) -> "Settings":
        return cls(
            base_url=os.getenv("API_BASE_URL", cls.base_url),
            country=os.getenv("API_COUNTRY", cls.country),
            indicator=os.getenv("API_INDICATOR", cls.indicator),
            per_page=int(os.getenv("API_PER_PAGE", cls.per_page)),
            timeout_s=float(os.getenv("API_TIMEOUT_S", cls.timeout_s)),
            max_retries=int(os.getenv("API_MAX_RETRIES", cls.max_retries)),
            backoff_s=float(os.getenv("API_BACKOFF_S", cls.backoff_s)),
        )
```

```python
# src/api_pipeline/logging_setup.py
"""Console + file logging."""
import logging
from pathlib import Path


def setup_logging(log_dir: Path | None = None, level: int = logging.INFO) -> None:
    handlers: list[logging.Handler] = [logging.StreamHandler()]
    if log_dir is not None:
        log_dir.mkdir(parents=True, exist_ok=True)
        handlers.append(logging.FileHandler(log_dir / "pipeline.log", encoding="utf-8"))
    logging.basicConfig(
        level=level,
        format="%(asctime)s %(levelname)s %(name)s %(message)s",
        handlers=handlers,
        force=True,
    )
```

Commit: `git add . && git commit -m "chore: project skeleton, config and logging"`

### Step 4 — The API client (the heart of the project)

Read it slowly. Every rule from Part A section 4.11 appears here.

```python
# src/api_pipeline/client.py
"""HTTP client with timeouts, retries with backoff, and page-number pagination."""
from __future__ import annotations

import logging
import random
import time
from collections.abc import Callable

import requests

log = logging.getLogger(__name__)

RETRY_STATUS = {429, 500, 502, 503, 504}


class ApiError(Exception):
    """The API failed in a way we could not recover from."""


class ApiClient:
    def __init__(
        self,
        base_url: str,
        timeout_s: float = 10.0,
        max_retries: int = 4,
        backoff_s: float = 1.0,
        session: requests.Session | None = None,
        sleep: Callable[[float], None] = time.sleep,
    ) -> None:
        self.base_url = base_url.rstrip("/")
        self.timeout_s = timeout_s
        self.max_retries = max_retries
        self.backoff_s = backoff_s
        self.session = session or requests.Session()
        self._sleep = sleep            # injectable so tests do not really wait

    def _delay(self, attempt: int, retry_after: str | None) -> float:
        if retry_after is not None:
            try:
                return max(0.0, float(retry_after))
            except ValueError:
                pass
        return self.backoff_s * 2 ** (attempt - 1) + random.uniform(0, self.backoff_s / 10)

    def get_json(self, path: str, params: dict | None = None):
        url = f"{self.base_url}/{path.lstrip('/')}"
        last_error: Exception | None = None
        attempt = 0
        while attempt <= self.max_retries:
            attempt += 1
            retry_after = None
            try:
                resp = self.session.get(url, params=params, timeout=self.timeout_s)
            except (requests.ConnectionError, requests.Timeout) as exc:
                last_error = exc
                log.warning("attempt %d/%d failed for %s: %s", attempt, self.max_retries + 1, url, exc)
            else:
                if resp.status_code == 200:
                    try:
                        return resp.json()
                    except ValueError as exc:
                        raise ApiError(f"invalid JSON from {url}") from exc
                if resp.status_code not in RETRY_STATUS:
                    raise ApiError(f"HTTP {resp.status_code} for {url} (not retryable)")
                last_error = ApiError(f"HTTP {resp.status_code}")
                retry_after = resp.headers.get("Retry-After")
                log.warning(
                    "attempt %d/%d got HTTP %d from %s",
                    attempt, self.max_retries + 1, resp.status_code, url,
                )
            if attempt > self.max_retries:
                break
            delay = self._delay(attempt, retry_after)
            log.info("retrying in %.2fs", delay)
            self._sleep(delay)
        raise ApiError(f"giving up after {attempt} attempts: {url}") from last_error


def fetch_all_pages(
    client: ApiClient,
    path: str,
    params: dict | None = None,
    per_page: int = 100,
) -> tuple[list[dict], dict]:
    """Collect every page of a World Bank-style API: the payload is [meta, rows]."""
    records: list[dict] = []
    page = 1
    while True:
        query = {**(params or {}), "format": "json", "per_page": per_page, "page": page}
        payload = client.get_json(path, query)
        if not isinstance(payload, list) or len(payload) < 2 or not isinstance(payload[0], dict):
            raise ApiError(f"unexpected response shape on page {page}: {str(payload)[:200]}")
        meta, rows = payload[0], payload[1] or []
        pages = int(meta["pages"])
        log.info("page %d/%d: %d records", page, pages, len(rows))
        records.extend(rows)
        if page >= pages:
            return records, meta
        page += 1
```

Commit: `feat: add API client with retries and pagination`

### Step 5 — The local mock API

Real APIs rate-limit you, change and go offline. A local server with the same shape lets you test every failure on demand.

```python
# tests/mock_api.py
"""A tiny World Bank-shaped API for offline practice and tests.

Run by hand:  python tests/mock_api.py --port 8099 --fail-first 2
"""
from __future__ import annotations

import argparse
import json
import threading
from http.server import BaseHTTPRequestHandler, ThreadingHTTPServer
from urllib.parse import parse_qs, urlparse


class MockApi:
    def __init__(self, total=25, fail_first=0, fail_status=503, bad_rows=True, port=0):
        self.total = total
        self.fail_status = fail_status
        self.bad_rows = bad_rows
        self.always_fail = False
        self.requests: list[str] = []
        self._failures_left = fail_first
        self._server = ThreadingHTTPServer(("127.0.0.1", port), self._make_handler())
        self._thread: threading.Thread | None = None

    @property
    def base_url(self) -> str:
        return f"http://127.0.0.1:{self._server.server_address[1]}/v2"

    def fail_next(self, n: int) -> None:
        self._failures_left = n

    def records(self) -> list[dict]:
        rows = [
            {
                "indicator": {"id": "NY.GDP.MKTP.CD", "value": "GDP (current US$)"},
                "country": {"id": "KE", "value": "Kenya"},
                "countryiso3code": "KEN",
                "date": str(2023 - i),
                "value": 1.0e10 + i * 1.0e8,
                "unit": "",
                "obs_status": "",
                "decimal": 0,
            }
            for i in range(self.total)
        ]
        if self.bad_rows and self.total > 10:
            rows[3]["value"] = None            # null value        -> rejected
            rows[7]["date"] = "20x5"           # malformed year    -> rejected
            rows[9]["date"] = rows[8]["date"]  # duplicate key     -> one superseded
        return rows

    def _make_handler(self):
        api = self

        class Handler(BaseHTTPRequestHandler):
            def log_message(self, *args):      # keep test output quiet
                pass

            def _send(self, status, body, headers=None):
                data = json.dumps(body).encode()
                self.send_response(status)
                self.send_header("Content-Type", "application/json")
                self.send_header("Content-Length", str(len(data)))
                for key, value in (headers or {}).items():
                    self.send_header(key, value)
                self.end_headers()
                self.wfile.write(data)

            def do_GET(self):
                api.requests.append(self.path)
                url = urlparse(self.path)
                if api.always_fail or api._failures_left > 0:
                    if api._failures_left > 0:
                        api._failures_left -= 1
                    return self._send(api.fail_status, {"message": "temporary failure"}, {"Retry-After": "0"})
                if "/indicator/" not in url.path:
                    return self._send(404, {"message": "not found"})
                query = parse_qs(url.query)
                page = int(query.get("page", ["1"])[0])
                per_page = int(query.get("per_page", ["50"])[0])
                rows = api.records()
                pages = max(1, -(-len(rows) // per_page))
                meta = {"page": page, "pages": pages, "per_page": str(per_page), "total": len(rows)}
                return self._send(200, [meta, rows[(page - 1) * per_page : page * per_page]])

        return Handler

    def start(self) -> None:
        self._thread = threading.Thread(target=self._server.serve_forever, daemon=True)
        self._thread.start()

    def stop(self) -> None:
        if self._thread is not None:
            self._server.shutdown()
            self._server.server_close()
            self._thread = None


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Run the mock API.")
    parser.add_argument("--port", type=int, default=8099)
    parser.add_argument("--total", type=int, default=25)
    parser.add_argument("--fail-first", type=int, default=0)
    parser.add_argument("--fail-status", type=int, default=503)
    args = parser.parse_args()
    api = MockApi(args.total, args.fail_first, args.fail_status, port=args.port)
    print(f"Mock API listening at {api.base_url}   (Ctrl+C to stop)")
    try:
        api._server.serve_forever()
    except KeyboardInterrupt:
        pass
```

**Play with it by hand.** In terminal 1:

```bash
$ python tests/mock_api.py --port 8099 --fail-first 2
```

In terminal 2:

```bash
$ curl -i "http://127.0.0.1:8099/v2/country/KEN/indicator/NY.GDP.MKTP.CD?format=json&per_page=10&page=1"
```

**What you should see:** the first two calls return `503` with a `Retry-After` header; the third succeeds with JSON. Restart the server with a different `--fail-first`, then with `--fail-status 429`, and watch the behaviour change. Everything you learn here is what the next steps turn into code and tests.

Commit: `test: add mock World Bank API`

### Step 6 — Validation and cleaning

Every failure has a named **reason**. Nothing is dropped silently.

```python
# src/api_pipeline/transform.py
"""Validate and clean raw records. Every rejected record keeps a reason."""
from __future__ import annotations

from dataclasses import dataclass

import pandas as pd

COLUMNS = ["country_code", "country_name", "indicator_id", "indicator_name", "year", "value"]


@dataclass
class Rejected:
    reason: str
    record: dict


def clean_record(rec: dict) -> dict:
    """Return a flat, typed row, or raise ValueError(reason)."""
    try:
        country_code = rec["countryiso3code"]
        country_name = rec["country"]["value"]
        indicator_id = rec["indicator"]["id"]
        indicator_name = rec["indicator"]["value"]
        date = rec["date"]
        value = rec["value"]
    except (KeyError, TypeError) as exc:
        raise ValueError(f"missing_field:{exc}") from exc

    if not country_code:
        raise ValueError("empty_country_code")
    if value is None:
        raise ValueError("null_value")
    if isinstance(value, bool) or not isinstance(value, int | float):
        raise ValueError("non_numeric_value")
    if not (isinstance(date, str) and date.isdigit() and len(date) == 4):
        raise ValueError("bad_year")

    return {
        "country_code": country_code,
        "country_name": country_name,
        "indicator_id": indicator_id,
        "indicator_name": indicator_name,
        "year": int(date),
        "value": float(value),
    }


def transform_records(records: list[dict]) -> tuple[pd.DataFrame, list[Rejected]]:
    """Clean all records; keep the last record per (country, indicator, year)."""
    latest: dict[tuple, tuple[dict, dict]] = {}
    rejected: list[Rejected] = []

    for rec in records:
        try:
            row = clean_record(rec)
        except ValueError as exc:
            rejected.append(Rejected(str(exc), rec))
            continue
        key = (row["country_code"], row["indicator_id"], row["year"])
        if key in latest:
            rejected.append(Rejected("duplicate_superseded", latest[key][1]))
        latest[key] = (row, rec)

    rows = [row for row, _ in latest.values()]
    df = pd.DataFrame(rows, columns=COLUMNS).astype({"year": "int32", "value": "float64"})
    df = df.sort_values(["country_code", "indicator_id", "year"]).reset_index(drop=True)
    return df, rejected
```

Commit: `feat: add record validation and cleaning`

### Step 7 — The pipeline

Extract → raw → transform → load → summary, with atomic writes and reconciliation.

```python
# src/api_pipeline/pipeline.py
"""Extract -> save raw -> transform -> load -> summary."""
from __future__ import annotations

import json
import logging
import os
import tempfile
from collections import Counter
from datetime import datetime, timezone
from pathlib import Path

import pandas as pd

from .client import ApiClient, fetch_all_pages
from .config import Settings
from .transform import transform_records

log = logging.getLogger(__name__)


class ReconciliationError(Exception):
    """The number of records received does not match what the API promised."""


def atomic_write_text(path: Path, text: str) -> None:
    """Write via a temp file and rename, so readers never see a half-written file."""
    path.parent.mkdir(parents=True, exist_ok=True)
    fd, tmp = tempfile.mkstemp(dir=path.parent, suffix=".tmp")
    try:
        with os.fdopen(fd, "w", encoding="utf-8") as f:
            f.write(text)
        os.replace(tmp, path)
    except BaseException:
        Path(tmp).unlink(missing_ok=True)
        raise


def atomic_write_df(df: pd.DataFrame, path: Path) -> None:
    path.parent.mkdir(parents=True, exist_ok=True)
    tmp = path.with_name(path.name + ".tmp")
    if path.suffix == ".parquet":
        df.to_parquet(tmp, index=False)
    else:
        df.to_csv(tmp, index=False)
    os.replace(tmp, path)


def run(settings: Settings, client: ApiClient | None = None, now: datetime | None = None) -> dict:
    now = now or datetime.now(timezone.utc)
    run_id = now.strftime("%Y%m%dT%H%M%SZ")
    client = client or ApiClient(
        settings.base_url,
        timeout_s=settings.timeout_s,
        max_retries=settings.max_retries,
        backoff_s=settings.backoff_s,
    )
    stem = f"{settings.country}_{settings.indicator}"
    log.info("run_id=%s starting: country=%s indicator=%s", run_id, settings.country, settings.indicator)

    # 1. Extract (any failure here raises before anything is written)
    path = f"country/{settings.country}/indicator/{settings.indicator}"
    records, meta = fetch_all_pages(client, path, per_page=settings.per_page)
    if len(records) != int(meta["total"]):
        raise ReconciliationError(f"received {len(records)} records but API reported {meta['total']}")

    # 2. Preserve the raw response, untouched, with an extraction timestamp
    raw_path = settings.data_dir / "raw" / f"{stem}_{run_id}.json"
    envelope = {
        "run_id": run_id,
        "extracted_at": now.isoformat(),
        "source": {"base_url": settings.base_url, "path": path},
        "meta": meta,
        "records": records,
    }
    atomic_write_text(raw_path, json.dumps(envelope, indent=2))

    # 3. Transform
    df, rejected = transform_records(records)

    # 4. Load: processed output is replaced atomically, so reruns are safe
    parquet_path = settings.data_dir / "processed" / f"{stem}.parquet"
    atomic_write_df(df, parquet_path)
    atomic_write_df(df, settings.data_dir / "processed" / f"{stem}.csv")

    rejected_path = settings.data_dir / "rejected" / f"{stem}_{run_id}.jsonl"
    lines = "".join(json.dumps({"reason": r.reason, "record": r.record}) + "\n" for r in rejected)
    atomic_write_text(rejected_path, lines)

    summary = {
        "run_id": run_id,
        "extracted": len(records),
        "clean": len(df),
        "rejected": len(rejected),
        "reject_reasons": dict(Counter(r.reason for r in rejected)),
        "raw_file": str(raw_path),
        "processed_file": str(parquet_path),
        "rejected_file": str(rejected_path),
    }
    log.info("run_id=%s finished: %s", run_id, summary)
    return summary
```

Commit: `feat: add pipeline with atomic writes and reconciliation`

### Step 8 — The command-line entry point

```python
# src/api_pipeline/__main__.py
"""Command line: python -m api_pipeline --country KEN --indicator NY.GDP.MKTP.CD"""
from __future__ import annotations

import argparse
import json
import logging
import sys
from dataclasses import replace

from .config import Settings
from .logging_setup import setup_logging
from .pipeline import run


def main(argv: list[str] | None = None) -> int:
    parser = argparse.ArgumentParser(prog="api_pipeline", description="Public API to structured dataset.")
    parser.add_argument("--country", help="ISO3 country code, e.g. KEN")
    parser.add_argument("--indicator", help="indicator id, e.g. NY.GDP.MKTP.CD")
    parser.add_argument("--per-page", type=int, help="records per page")
    args = parser.parse_args(argv)

    overrides = {
        "country": args.country,
        "indicator": args.indicator,
        "per_page": args.per_page,
    }
    settings = replace(Settings.from_env(), **{k: v for k, v in overrides.items() if v is not None})
    setup_logging(settings.log_dir)

    try:
        summary = run(settings)
    except Exception:
        logging.getLogger("api_pipeline").exception("pipeline failed; previous outputs are unchanged")
        return 1
    print(json.dumps(summary, indent=2))
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

Commit: `feat: add command-line entry point`

### Step 9 — Tests

Write the tests *before* trusting any of the code above. Read each test name aloud: together they are the **specification** of your pipeline.

```python
# tests/conftest.py
import pytest
from mock_api import MockApi


@pytest.fixture
def api():
    server = MockApi(total=25)
    server.start()
    yield server
    server.stop()
```

```python
# tests/test_transform.py
import pytest

from api_pipeline.transform import clean_record, transform_records


def make(**overrides):
    rec = {
        "indicator": {"id": "NY.GDP.MKTP.CD", "value": "GDP (current US$)"},
        "country": {"id": "KE", "value": "Kenya"},
        "countryiso3code": "KEN",
        "date": "2023",
        "value": 1.5e10,
    }
    rec.update(overrides)
    return rec


def test_clean_record_valid():
    row = clean_record(make())
    assert row["year"] == 2023
    assert row["value"] == 1.5e10
    assert row["country_name"] == "Kenya"


@pytest.mark.parametrize(
    "overrides, reason",
    [
        ({"value": None}, "null_value"),
        ({"value": "12"}, "non_numeric_value"),
        ({"value": True}, "non_numeric_value"),
        ({"date": "20x5"}, "bad_year"),
        ({"date": None}, "bad_year"),
        ({"countryiso3code": ""}, "empty_country_code"),
    ],
)
def test_clean_record_rejects(overrides, reason):
    with pytest.raises(ValueError, match=reason):
        clean_record(make(**overrides))


def test_missing_field_is_rejected():
    rec = make()
    del rec["value"]
    with pytest.raises(ValueError, match="missing_field"):
        clean_record(rec)


def test_transform_keeps_last_duplicate_and_sorts():
    records = [make(date="2022", value=1.0), make(date="2021", value=2.0), make(date="2022", value=3.0)]
    df, rejected = transform_records(records)
    assert df["year"].tolist() == [2021, 2022]
    assert df.loc[df["year"] == 2022, "value"].item() == 3.0
    assert [r.reason for r in rejected] == ["duplicate_superseded"]


def test_transform_accounts_for_every_record():
    records = [make(), make(value=None), make(date="bad")]
    df, rejected = transform_records(records)
    assert len(df) + len(rejected) == len(records)


def test_transform_empty_input():
    df, rejected = transform_records([])
    assert df.empty and rejected == []
    assert list(df.columns)[:2] == ["country_code", "country_name"]
```

```python
# tests/test_client.py
import pytest

from api_pipeline.client import ApiClient, ApiError, fetch_all_pages

PATH = "country/KEN/indicator/NY.GDP.MKTP.CD"


def make_client(api, max_retries=3):
    return ApiClient(api.base_url, timeout_s=2, max_retries=max_retries, backoff_s=0, sleep=lambda s: None)


def test_retries_then_succeeds(api):
    api.fail_next(2)
    payload = make_client(api).get_json(PATH, {"format": "json"})
    assert payload[0]["total"] == 25
    assert len(api.requests) == 3          # two failures + one success


def test_gives_up_after_max_retries(api):
    api.always_fail = True
    with pytest.raises(ApiError, match="giving up"):
        make_client(api, max_retries=2).get_json(PATH)
    assert len(api.requests) == 3          # first try + 2 retries


def test_does_not_retry_client_errors(api):
    with pytest.raises(ApiError, match="not retryable"):
        make_client(api).get_json("nothing-here")
    assert len(api.requests) == 1


def test_connection_refused_is_reported(api):
    client = make_client(api, max_retries=1)
    api.stop()                              # nothing is listening any more
    with pytest.raises(ApiError, match="giving up"):
        client.get_json(PATH)


def test_pagination_collects_every_page(api):
    records, meta = fetch_all_pages(make_client(api), PATH, per_page=10)
    assert len(records) == 25 == meta["total"]
    assert len(api.requests) == 3          # pages of 10, 10 and 5
```

```python
# tests/test_pipeline.py
import json
from datetime import datetime, timezone

import pandas as pd
import pytest

from api_pipeline.client import ApiClient, ApiError
from api_pipeline.config import Settings
from api_pipeline.pipeline import run


def build(api, tmp_path):
    settings = Settings(base_url=api.base_url, data_dir=tmp_path / "data", per_page=10)
    client = ApiClient(api.base_url, timeout_s=2, max_retries=2, backoff_s=0, sleep=lambda s: None)
    return settings, client


def parquet_path(tmp_path):
    return tmp_path / "data" / "processed" / "KEN_NY.GDP.MKTP.CD.parquet"


def test_end_to_end(api, tmp_path):
    settings, client = build(api, tmp_path)
    summary = run(settings, client)

    assert summary["extracted"] == 25
    assert summary["clean"] == 22
    assert summary["rejected"] == 3
    assert summary["clean"] + summary["rejected"] == summary["extracted"]

    df = pd.read_parquet(parquet_path(tmp_path))
    assert len(df) == 22 and df["year"].is_monotonic_increasing

    raw_files = list((tmp_path / "data" / "raw").glob("*.json"))
    assert len(raw_files) == 1
    raw = json.loads(raw_files[0].read_text(encoding="utf-8"))
    assert len(raw["records"]) == 25 and "extracted_at" in raw

    rejected_lines = next((tmp_path / "data" / "rejected").glob("*.jsonl")).read_text().splitlines()
    assert len(rejected_lines) == 3


def test_rerun_is_idempotent(api, tmp_path):
    settings, client = build(api, tmp_path)
    run(settings, client, now=datetime(2026, 10, 21, 8, 0, tzinfo=timezone.utc))
    first = pd.read_parquet(parquet_path(tmp_path))
    run(settings, client, now=datetime(2026, 10, 21, 9, 0, tzinfo=timezone.utc))
    second = pd.read_parquet(parquet_path(tmp_path))

    pd.testing.assert_frame_equal(first, second)                    # same result
    assert len(list((tmp_path / "data" / "raw").glob("*.json"))) == 2   # raw is kept per run


def test_failed_run_preserves_previous_output(api, tmp_path):
    settings, client = build(api, tmp_path)
    run(settings, client)
    before = parquet_path(tmp_path).read_bytes()

    api.always_fail = True                                          # the API goes down
    with pytest.raises(ApiError):
        run(settings, client)

    assert parquet_path(tmp_path).read_bytes() == before            # untouched
    assert len(list((tmp_path / "data" / "raw").glob("*.json"))) == 1   # no partial raw file
```

Run the tests and the linter:

```bash
$ pytest -v
$ ruff check .
$ ruff format .
```

**All tests must pass before you continue.**

Commit: `test: add transform, client and pipeline tests`

### Step 10 — Run it for real

```bash
$ python -m api_pipeline --country KEN --indicator NY.GDP.MKTP.CD
```

Expected shape of the output (values depend on what the live API returns):

```json
{
  "run_id": "20261021T080000Z",
  "extracted": 65,
  "clean": 64,
  "rejected": 1,
  "reject_reasons": {"null_value": 1},
  "raw_file": "data/raw/KEN_NY.GDP.MKTP.CD_20261021T080000Z.json",
  "processed_file": "data/processed/KEN_NY.GDP.MKTP.CD.parquet",
  "rejected_file": "data/rejected/KEN_NY.GDP.MKTP.CD_20261021T080000Z.jsonl"
}
```

Inspect your results:

```bash
$ ls -lh data/raw data/processed data/rejected
$ python -c "import pandas as pd; df = pd.read_parquet('data/processed/KEN_NY.GDP.MKTP.CD.parquet'); print(df.tail()); print(df.dtypes)"
$ tail -n 15 logs/pipeline.log
```

Try another indicator by changing `--indicator` (for example a population or inflation indicator code from the World Bank website). A different indicator should produce a different output file and leave the earlier one untouched.

### Step 11 — The completion test: prove the failure behaviour

1. Run the pipeline successfully once. Note the **size and timestamp** of `data/processed/*.parquet`.
2. Break the connection, either by disconnecting from the network or by pointing to a wrong address:

   ```bash
   $ API_BASE_URL=http://127.0.0.1:9/v2 API_MAX_RETRIES=2 API_BACKOFF_S=0.2 python -m api_pipeline
   $ echo "exit code: $?"
   ```

3. Check every item below and record the result in your README's "Failure behaviour" section:
   - [ ] The command printed a clear error and the exit code is non-zero (`1`).
   - [ ] The log shows each attempt, each retry delay and the final "giving up" message with the URL.
   - [ ] The files in `data/processed/` are **unchanged** (same size and timestamp as in step 1).
   - [ ] No partial or temporary (`.tmp`) files were left behind.
4. Run the pipeline again with the connection restored. It must succeed and produce a **new raw file**, while the processed output is replaced cleanly.

### Step 12 — Write the README

Use this outline and fill every section with real content from your run:

```markdown
# API Pipeline: Public API to structured dataset

One-sentence purpose, the API used and the question the dataset answers.

## What it does
Short description of the flow: extract -> raw -> transform -> load -> summary (paste the diagram).

## Setup
Exact commands: clone, create venv, `pip install -e ".[dev]"`, copy `.env.example`.

## Run
`python -m api_pipeline --country KEN --indicator NY.GDP.MKTP.CD`

## Output
Folder map of data/raw, data/processed, data/rejected and logs, plus a trimmed sample of the
run summary and the first rows of the cleaned dataset.

## Design decisions
Why raw is immutable, why writes are atomic, why only temporary errors are retried,
how duplicates are decided, which records are rejected and why.

## Failure behaviour
The completion test: what happens when the API is down, with a log excerpt.

## Tests
`pytest -v`, what is covered, how the mock API works.

## Known limitations and next steps
For example: no scheduling yet (Phase 06), no database load yet (Phase 04), single indicator per run.
```

### Step 13 — Commit and publish

```bash
$ ruff check . && pytest -q
$ git add .
$ git commit -m "docs: add README with setup, output and failure behaviour"
$ git remote add origin git@github.com:<your-username>/api-pipeline-project.git
$ git branch -M main && git push -u origin main
```

Then do a **fresh-clone check**: clone into `/tmp`, follow *only* your own README, and confirm that `pytest` passes and the pipeline runs. Fix the README wherever you got stuck.

### Project acceptance checklist

- [ ] Reusable `ApiClient` with a timeout, retries with exponential backoff and no retries for client errors
- [ ] Pagination collects every page and reconciles the count against the API's reported total
- [ ] Raw JSON saved per run with an extraction timestamp and never modified
- [ ] Clean dataset written as Parquet **and** CSV, replaced atomically
- [ ] Rejected records saved with a reason; clean + rejected = extracted
- [ ] Logging to console and file with a run id; a run summary printed
- [ ] Tests cover valid, invalid and empty input, retries, pagination, idempotent reruns and failed runs
- [ ] `ruff check` is clean and all tests pass
- [ ] No secrets in the repository; `.env.example` present; `data/` and `logs/` ignored
- [ ] README works from a fresh clone, including sample output and the failure behaviour
- [ ] Completion test demonstrated and documented

### Stretch goals (optional)

- Add `--since-year` to fetch only recent years and store a watermark (Part A 5.7).
- Support several indicators per run and write one file per indicator.
- Add `--dry-run` that validates and reports without writing processed files.
- Replace the "latest" CSV/Parquet files with **date-partitioned** output (`year=2023/part.parquet`).
- Add a `Makefile` or `justfile` with `make test`, `make lint` and `make run`.
- Run the pipeline from `cron` (Phase 01) and inspect the logs after two scheduled runs.

---

## Troubleshooting guide

| Problem | Likely cause | Fix |
| --- | --- | --- |
| `externally-managed-environment` when running `pip install` | Installing into the system Python | Create and activate a virtual environment first (`python3 -m venv .venv && source .venv/bin/activate`) |
| `ModuleNotFoundError: No module named 'api_pipeline'` | Package not installed or wrong environment | Activate `.venv`; run `pip install -e ".[dev]"` from the project root |
| `ModuleNotFoundError: No module named 'mock_api'` in tests | Missing `tests/conftest.py` or run from the wrong folder | Run `pytest` from the project root; confirm `tests/mock_api.py` exists |
| `ModuleNotFoundError: No module named 'labs'` in Lab 4 | Project root not on the import path | Run `python -m pytest` from `python-practice/`; make sure `labs/__init__.py` exists |
| `pytest: command not found` | Environment not active | `source .venv/bin/activate`; or use `python -m pytest` |
| `UnicodeDecodeError` reading a file | File not UTF-8 | Open with the right `encoding=` (for example `utf-8-sig` for files from Excel) |
| `KeyError` on a JSON field | Optional key missing in some records | Use `.get()`, or validate and reject such records with a reason |
| `SettingWithCopyWarning` in pandas | Modifying a slice | Use `.copy()` or assign with `.loc[...]` |
| `ArrowInvalid` / type errors writing Parquet | Mixed types in one column | Convert the column to one type first (`astype`, `pd.to_numeric`) |
| Pipeline hangs for a long time | No request timeout | Always pass `timeout=` to `requests`; check `API_TIMEOUT_S` |
| `429 Too Many Requests` | Rate limit | Increase backoff, honour `Retry-After`, reduce page frequency |
| Tests pass locally but fail after a fresh clone | Undeclared dependency or untracked file | Add the dependency to `pyproject.toml`; check `git status` |
| Naive/aware datetime comparison error | Mixing naive and timezone-aware datetimes | Use `datetime.now(timezone.utc)` and parse ISO strings with offsets |
| Memory error on a large CSV | Loading the whole file | Stream with generators or `chunksize`; read fewer columns with `usecols` |
| Code works but is slow | Row-by-row Python loops on big data | Vectorise with pandas, read Parquet columns, profile before changing anything |

---

## Final checklist

Before moving to Phase 03, confirm:

- [ ] Lab 0: environment works and `.venv/` is git-ignored
- [ ] Lab 1: all eight exercises done and understood
- [ ] Lab 2: cleaner summary is `{'read': 12, 'clean': 6, 'rejected': 6}` and a PR is open or merged
- [ ] Lab 3: every function has a test; `pytest -v` is green and `ruff check .` is clean
- [ ] Lab 4: comparison notes written; incremental loader passes its idempotency test
- [ ] Project 1: every item in the acceptance checklist ticked, completion test documented
- [ ] Self-rating of 2 or higher in all five areas (see Part A, section 10)

Well done. Next up is **Phase 03: SQL and database engineering**, where your cleaned datasets finally get a database to live in.
