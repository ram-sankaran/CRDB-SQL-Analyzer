#  CockroachDB SQL Statement Analyzer

Analyze CockroachDB statement bundles and get LLM-powered performance recommendations in your browser.

## Features

- Upload `.zip` bundles from CockroachDB
- View query, execution plan, trace, schema, and indexes
- Receive AI-generated recommendations
- Download detailed `.html` reports

## Setup

### Option 1: Run with Python

```bash
pip install -r requirements.txt
python3 analyze_sql_bundle.py
```

# How It Works
- Upload statement bundle (.zip)
- Analyzer extracts files: sql, plan.txt, trace.txt, schema.sql, env.sql
- Calls LLM to analyze the bundle 
- Displays results
- Option to download the results . 
