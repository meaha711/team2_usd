# MarketSentinel: Agentic Stock Market Anomaly Detection and Research System

**AAI-520 Final Team Project, Team 2** (Sanjay Kumar, Meaha J)

MarketSentinel is an autonomous AI research agent. It watches a stock's price
and volume, detects unusual movements, and investigates *why* they happened.
Each anomaly gets one of three verdicts:

| Verdict | Meaning |
|---|---|
| `normal_market` | The move is explained by the overall market or sector (normal bull/bear action) |
| `explained_by_news` | A stock-specific move with an identifiable catalyst (earnings, news) |
| `unexplained_suspicious` | A large stock-specific move, often with abnormal volume, and no public explanation |

> The agent flags *unexplained* activity with a risk score. It does **not**
> claim that manipulation occurred. That would require order-level trading
> data held by regulators.

## Project requirements covered

| Requirement | Implementation |
|---|---|
| **Planning** | The LLM planner picks and orders tools using their descriptions and lessons from past runs |
| **Dynamic tool use** | 6 Yahoo Finance tools; the agent re-plans (skips or adds steps) and fetches insider filings only for suspicious cases |
| **Self-reflection** | Evaluator-optimizer critique plus a run-level reflection (quality, gaps, confidence) |
| **Learning across runs** | JSON memory: grades past flags against what happened next, adapts detector thresholds, stores lessons |
| **Prompt chaining** | News: ingest → preprocess → classify → extract → summarize |
| **Routing** | Each anomaly goes to a market, earnings, news, or manipulation specialist agent |
| **Evaluator-optimizer** | Draft report → LLM rubric and rule checks → refine (up to 3 rounds, plus a guardrail) |

## Quick start

```bash
git clone https://github.com/<your-org>/stock-anomaly-agent.git
cd stock-anomaly-agent
pip install -r requirements.txt
```

### Local LLM (choose one)

**Ollama**
```bash
ollama pull llama3.1:8b
```
The notebook uses `http://localhost:11434/v1` by default.

**LM Studio**: load an instruct model, click **Start Server** in the
Developer tab, then set `LLM_PROVIDER = "lmstudio"` in Section 1 of the
notebook (default `http://localhost:1234/v1`).

Environment-variable overrides: `LLM_PROVIDER`, `LLM_BASE_URL`, `LLM_MODEL`,
`AGENT_MEMORY_PATH`.

> If no model is reachable, the notebook still runs end to end using
> transparent rule-based fallbacks. Every output is tagged `llm` or `rules`.

### Run

```bash
jupyter notebook MarketSentinel_Stock_Anomaly_Agent.ipynb
```

Run all cells. A full run with a local 8B model takes about 10–30 minutes,
depending on your hardware.

### Export for submission

```bash
jupyter nbconvert --to html MarketSentinel_Stock_Anomaly_Agent.ipynb
```
Or use **File → Print → Save as PDF** from the HTML (PDF is preferred for
Canvas).

## Notebook structure and contributions

| # | Section | Owner | Support |
|---|---|---|---|
| 1 | Setup and configuration | Sanjay Kumar | Meaha J |
| 2 | Local LLM client | Sanjay Kumar | Meaha J |
| 3 | Market and news data tools | Meaha J | Sanjay Kumar |
| 4 | Data preprocessing and EDA | Meaha J | Sanjay Kumar |
| 5 | Anomaly detection logic and feature design | Sanjay Kumar | Meaha J |
| 6 | Agent memory (learning across runs) | Sanjay Kumar | Meaha J |
| 7 | Workflow 1: Prompt chaining | Sanjay Kumar | Meaha J |
| 8 | Workflow 2: Routing to specialists | Sanjay Kumar | Meaha J |
| 9 | Workflow 3: Evaluator-optimizer | Sanjay Kumar | Meaha J |
| 10 | Autonomous investigator agent | Sanjay Kumar | Meaha J |
| 11 | Demonstration runs | Meaha J | Sanjay Kumar |
| 12 | Evaluation and testing | Meaha J | Sanjay Kumar |
| 13 | Conclusions and documentation | Meaha J | Sanjay Kumar |

## Evaluation (included in the notebook)

- **Synthetic injection test:** known shocks injected into a quiet stock (KO);
  measures detector recall and false positives.
- **Labelled historical cases:** GME Jan 2021 squeeze (suspicious), NVDA May
  2023 earnings (explained), AAPL Mar 2020 COVID crash (normal market).
- **Report quality:** evaluator-optimizer score for each iteration.
- **Agent behaviour:** plan source, router agreement with rules, LLM vs
  fallback usage, and memory state.

## Data sources

Yahoo Finance via [`yfinance`](https://pypi.org/project/yfinance/): OHLCV
prices, SPY and sector SPDR ETFs, earnings dates, recent news and insider
transactions. Free Yahoo news covers only recent headlines, so historical
events are judged on market data and earnings.

## Code style

The Python code follows PEP 8 (checked with `pycodestyle`, max line length 79).

*For educational purposes only. Not investment advice.*
