<div align="center">

<h1>HackAlem — Money Graph (AML analytics)</h1>
<hr>

**Transaction-graph analytics for AML triage: explainable role rules, priorities, date-aware routes, and an LLM assistant behind a strict validation boundary**

![Python](https://img.shields.io/badge/PYTHON-3.12-3776AB?style=flat-square&logo=python&logoColor=white)
![NetworkX](https://img.shields.io/badge/NETWORKX-3.7-orange?style=flat-square)
![React](https://img.shields.io/badge/REACT-19.3-61DAFB?style=flat-square&logo=react&logoColor=black)
![Docker](https://img.shields.io/badge/DOCKER-COMPOSE-2496ED?style=flat-square&logo=docker&logoColor=white)
![License](https://img.shields.io/badge/LICENSE-AGPL--3.0-blue?style=flat-square)

</div>

## What it is

A local tool for an AML analyst that decides which accounts to review first and why. It reads three Parquet files (nodes, edges, transactions), builds a directed graph with NetworkX, assigns role hypotheses with rule-based scoring, and serves a React UI plus CSV exports. Roles are hypotheses, not proof of wrongdoing.

Built solo during the 5-hour HackAlem hackathon; scored 85/100.

## Highlights

- Graph analytics over 2,248 accounts: PageRank, betweenness, Louvain communities (88 clusters in the output), explainable role rules with threshold sensitivity.
- Strict LLM query boundary: the model output must be JSON with 1-3 operations from a 4-type allowlist, and every account id must exist in the graph and in the request context. Optional; the app works without it.
- Input checks before any computation, atomic publication of results with a SHA-256 manifest, and a healthcheck that verifies the served files.
- Local HTTP server hardened with Host/Origin checks, body and concurrency limits.

## Tech stack

| Area | Technologies |
|---|---|
| Backend | Python 3.12, pandas, NumPy, PyArrow, NetworkX, SciPy, stdlib `http.server` |
| Frontend | React 19, TypeScript, Vite, Cytoscape.js |
| LLM (optional) | Alem GPT OSS over HTTPS (`assistant.py`) |
| Tooling | Docker Compose, uv, pytest, Playwright |

## Quick start

```sh
docker compose up -d --build --wait --wait-timeout 360
```

Open http://localhost:8080. The `.env` file and API key are not needed for the core features. To enable the assistant, set `ALEM_API_KEY`, `ALEM_CHAT_URL` and `ALEM_MODEL` in `.env` (all three are required).

Without Docker (Python 3.12, Node.js, uv):

```sh
uv sync --locked
npm --prefix web ci
npm --prefix web run build
uv run python run.py --data case/data --out out --serve
```

Tests: `uv run pytest -q`. Details are in [docs/](docs/): [DEMO.md](docs/DEMO.md), [ANALYTICS.md](docs/ANALYTICS.md), [ASSISTANT.md](docs/ASSISTANT.md), [VERIFICATION.md](docs/VERIFICATION.md). Generated CSV outputs are in [submission/](submission/).

> [!NOTE]
> Local use only: there is no authentication or TLS. The dataset in `case/data/` was provided by the hackathon organizers.

## License

AGPL-3.0, see [LICENSE](LICENSE).
