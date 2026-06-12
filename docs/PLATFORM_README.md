# Vertex Agent Platform

The **Agentic OS** for every AI agent at Vertex — governed execution, A2A collaboration, continuous evaluation, lifecycle management, and enterprise-grade security. Engineers bring their own runtime; we provide the infrastructure layer.

> *"The registry, governance, and collaboration layer for every AI agent at Vertex. Discover agents, delegate tasks, enforce policies, manage lifecycles — bring your own runtime and observability."*

**Live:** https://vertex-agent-platform.sr.az.vtxdev.net _(VPN required)_  
**SDK:** `from sdk import Client; vap = Client(api_key="vap_xxx")`  
**CLI:** `python -m sdk.cli agents list`

---

## Platform Status (Jun 7, 2026)

| Layer | Status | Notes |
|-------|--------|-------|
| MCP Registry + lifecycle + auth + RBAC + audit | ✅ Live | |
| Model Registry (LiteLLM gateway, cost tracking) | ✅ Live | |
| Agent Control (schedules, circuit breaker, dead-letter) | ✅ Live | |
| Deploy Intelligence (rule engine → infra recommendation) | ✅ Live | |
| FinOps (budget, spend, kill-switch, auto-kill at 100%) | ✅ Live | |
| Platform Generate CLI (18-file scaffold ZIP) | ✅ Live | |
| Developer Portal (governance dashboard, sub-nav, activity feed) | ✅ Live | 10 top-level tabs, governance sub-nav with 9 children |
| MCP Domain Servers (babelfish, gorules, o-series, confluence) | ✅ Bundled in image | Run alongside platform in same pod |
| OTel + Langfuse + AppInsights | ✅ Wired | Traces, LLM callbacks, Azure Monitor |
| **Multi-tenant governance** | ✅ **Shipped** | **27/27 E2E tests passing on live** |
| **Governance enforcement (policy, PII, autonomy, HITL)** | ✅ **Shipped** | **Wired into executor + chat endpoint** |
| **Per-tenant policy rules** | ✅ **Shipped** | **Deny/require_approval scoped per tenant** |
| **Per-tenant autonomy (L1-L5)** | ✅ **Shipped** | **Same agent, different levels per tenant** |
| **HITL approval scoping** | ✅ **Shipped** | **Tenant can only see/approve own requests** |
| **Session/audit tenant isolation** | ✅ **Shipped** | **Cross-tenant reads → 404** |
| **Publish-to-tenant** | ✅ **Shipped** | **Admin pushes marketplace agents to specific customers** |
| **Fail-closed governance** | ✅ **Shipped** | **GOVERNANCE_FAIL_CLOSED=true → errors block, not pass** |
| **Auto-computed governance scoring** | ✅ **Shipped** | **22-check engine, auto-computes on register/update/chat** |
| **Governance compliance dashboard** | ✅ **Shipped** | **Fleet donut gauge, leaderboard, 22-check drill-down, activity feed** |
| **Per-agent security posture** | ✅ **Shipped** | **Full compliance breakdown with actionable remediation hints** |
| A2A Agent Collaboration (discover, delegate, orchestrate) | ✅ **Shipped** | **Capability cards, multi-agent pipeline/fan-out/supervisor** |
| Continuous Evaluation Pipeline (datasets, runs, SLA) | ✅ **Shipped** | **Golden datasets, scheduled evals, accuracy SLA monitoring** |
| Agent Lifecycle Management (versions, promote, rollback) | ✅ **Shipped** | **Draft→staging→prod pipeline, version pinning, shadow deploy** |
| Event-Driven Triggers (webhooks, completion chains) | ✅ **Shipped** | **Webhook ingestion, event bus, agent completion chains** |
| Advanced Memory (semantic, cross-agent, scoped) | ✅ **Shipped** | **Semantic search, shared memories, agent/tenant/platform scopes** |
| Advanced Guardrails (content policies, output PII) | ✅ **Shipped** | **Denied topics, output PII detection, configurable per tenant** |
| Python SDK + CLI (`from sdk import Client`) | ✅ **Shipped** | **Typed client + `python -m sdk.cli agents list`** |
| Marketplace v2 (reviews, deploy, metrics) | ✅ **Shipped** | **One-click deploy-to-tenant, ratings, config overrides** |
| Delegated Agent Identity (credential vault) | ✅ **Shipped** | **Fernet-encrypted OAuth tokens, usage audit trail** |
| Compliance Reporting (SOX/GDPR/audit) | ✅ **Shipped** | **Automated reports, retention policies, posture summary** |
| Self-Healing (auto-rollback, failover) | ✅ **Shipped** | **SLA breach → rollback, model failover, degraded mode** |
| Entra ID / SSO enforcement | ⏳ Framework ready | Needs `AZURE_TENANT_ID` + `AZURE_CLIENT_ID` |
| OPA per-call authorization | ❌ v4 | |

**Production readiness:** All 4 sprints shipped (A-D) — A2A collaboration, continuous evals, agent lifecycle, event triggers, advanced memory, guardrails, SDK/CLI, marketplace v2, delegated identity, compliance reporting, self-healing. 132 unit tests, 4 smoke test scripts.

## Key API Endpoints

### A2A Agent Collaboration
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/agents/discover` | Find agents by capability, cost, latency |
| POST | `/api/v1/agents/{name}/delegate` | Delegate task to another agent |
| POST | `/api/v1/agents/orchestrate` | Multi-agent pipeline/fan-out/supervisor |
| GET | `/.well-known/agent-card` | A2A-compliant capability advertisement |

### Continuous Evaluation
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/evals/datasets` | Create golden test dataset |
| POST | `/api/v1/evals/run/{agent}` | Run eval against dataset |
| GET | `/api/v1/evals/sla/{agent}` | Check accuracy SLA status |
| GET | `/api/v1/evals/runs` | List recent eval runs |

### Agent Lifecycle
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/agents/{id}/versions` | Create version snapshot |
| POST | `/api/v1/agents/{id}/promote` | Promote lifecycle stage |
| POST | `/api/v1/agents/{id}/rollback/{v}` | Rollback to version |
| POST | `/api/v1/agents/{id}/shadow` | Start shadow/canary deployment |

### Event-Driven Triggers
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/events/triggers` | Create event trigger |
| POST | `/api/v1/events/webhook/{id}` | Fire webhook trigger |
| POST | `/api/v1/events/emit` | Emit internal event |
| GET | `/api/v1/events/log` | Event execution log |

### Advanced Memory
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/memory/store` | Store semantic memory |
| GET | `/api/v1/memory/search` | Search by similarity |
| GET | `/api/v1/memory/context/{agent}` | Get memory context for prompt |
| POST | `/api/v1/memory/consolidate` | Deduplicate memories |

### Advanced Guardrails
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/guardrails/policies` | Create content policy |
| POST | `/api/v1/guardrails/check` | Check text against policies |
| GET | `/api/v1/guardrails/violations` | Recent violations log |

### Governance Scoring & Compliance
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/agents/governance/summary` | Fleet-wide compliance posture (grade distribution, risk, compliance breakdown) |
| GET | `/api/v1/agents/{id}/scorecard` | Per-agent 5-dimension scorecard (accuracy, usage, cost, governance, security) |
| POST | `/api/v1/agents/{id}/scorecard/refresh` | Force recompute governance score |
| POST | `/api/v1/agents/{id}/certify` | Run certification (Bronze/Silver/Gold badge) |
| GET | `/api/v1/agents/{id}/certification` | Get latest certification result |

**Governance scores are auto-computed** on agent registration, update, and after every chat execution.
Every `GET /api/v1/agents/` response includes: `governance_score` (0-100), `governance_grade` (A+ through F),
`compliance_status` (compliant/needs_review/non_compliant), and `risk_level` (low/medium/high/critical).

### Python SDK + CLI
| Method | Endpoint | Description |
|--------|----------|-------------|
| — | `from sdk import Client` | Typed Python client for all APIs |
| — | `python -m sdk.cli agents list` | CLI agent management |
| — | `python -m sdk.cli eval run {agent}` | CLI eval execution |
| — | `python -m sdk.cli budget list` | CLI FinOps management |

### Marketplace v2
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/agents/{id}/metrics` | Aggregated usage metrics (sessions, latency, cost, rating) |
| POST | `/api/v1/agents/{id}/reviews` | Submit agent review (1-5 stars) |
| GET | `/api/v1/agents/{id}/reviews` | List agent reviews |
| POST | `/api/v1/agents/{id}/deploy` | One-click deploy to tenant with config overrides |

### Delegated Identity
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/identity/credentials` | Register agent credential (OAuth/API key/bearer) |
| GET | `/api/v1/identity/credentials` | List credentials (secrets masked) |
| POST | `/api/v1/identity/credentials/{id}/rotate` | Rotate credential secret |
| GET | `/api/v1/identity/usage` | Credential usage audit trail |

### Compliance & Reporting
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/compliance/reports` | Generate SOX/GDPR/platform audit report |
| GET | `/api/v1/compliance/posture` | Overall compliance posture summary |
| POST | `/api/v1/compliance/retention` | Set data retention policy |
| POST | `/api/v1/compliance/retention/apply` | Apply retention (archive/delete/anonymize) |

### Self-Healing
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/healing/rules` | Create healing rule (auto-rollback/failover/degrade) |
| POST | `/api/v1/healing/check/{agent}` | Manually trigger healing check |
| GET | `/api/v1/healing/status` | Platform healing status summary |
| GET | `/api/v1/healing/events` | Healing event log |

## Python SDK

```python
from sdk import Client

vap = Client(api_key="vap_xxx", base_url="https://vertex-agent-platform.sr.az.vtxdev.net")

# Chat with a governed agent
response = vap.agents.chat("tax-rate-lookup", "What's the PA tax rate?")

# Discover agents by capability
agents = vap.agents.discover(needs="tax_rate", max_cost=0.01)

# Delegate task to another agent
result = vap.agents.delegate("tax-lookup", task="Find PA rate", input_data={"state": "PA"})

# Multi-agent orchestration
result = vap.agents.orchestrate("pipeline", ["validator", "lookup", "formatter"], task="Process tax query")

# Lifecycle management
vap.agents.promote(agent_id)            # draft → staging → production
vap.agents.rollback(agent_id, 2)        # rollback to version 2

# Evaluations
vap.evals.run("tax-lookup", dataset_id="...")
vap.evals.set_sla("tax-lookup", target_accuracy=0.995)

# Memory
vap.memory.store("globex-inc", "tax-lookup", "Customer prefers detailed citations")

# Guardrails
vap.guardrails.create_policy("denied_topics", config={"keywords": ["Avalara"]}, action="block")
```

## Testing

```bash
# Unit tests (in-memory SQLite, no network needed)
cd core && python -m pytest tests/ -v -k "not test_pii and not test_policy"
# → 132 tests passing

# Sprint A smoke test — A2A, evals, lifecycle
python scripts/test_sprint_a.py --base-url http://localhost:8090

# Sprint B smoke test — events, memory, guardrails
python scripts/test_sprint_b.py --base-url http://localhost:8090

# Sprint C smoke test — SDK, CLI, marketplace v2
python scripts/test_sprint_c.py --base-url http://localhost:8090

# Sprint D smoke test — identity, compliance, self-healing
python scripts/test_sprint_d.py --base-url http://localhost:8090

# Full governance enforcement (7 scenarios)
python scripts/test_governance_enforcement.py --base-url http://localhost:8090

# Multi-tenant governance (27 scenarios)
python scripts/test_multi_tenant_governance.py --base-url http://localhost:8090
```

---

## What This Platform Is

We are **infrastructure, not a runtime stack**. Think: npm registry (not npm), Docker Hub (not Docker), Kubernetes RBAC (not Kubernetes).

| We own | We do NOT own |
|--------|---------------|
| MCP Registry — org-wide tool discovery | Agent runtime — use Foundry, LangGraph, CrewAI, ADK |
| Model Registry — unified LLM routing config | Model hosting — Azure APIM, direct providers |
| Standards — register.yaml, agent-definition.yaml | Observability hosting — bring your own Langfuse/Datadog |
| Security — auth, RBAC, audit trail, lifecycle | CI/CD pipelines — VIPER handles this |
| Agent Control — schedules, circuit breakers, dead-letter | Visual canvas — Azure AI Foundry handles this |
| FinOps — per-agent budget + auto kill-switch | |
| Deploy Intelligence — infra recommendations | |
| Platform Generate — scaffold ZIP generator | |

---

## Architecture

```
Any Agent Framework (Foundry, LangGraph, CrewAI, ADK, etc.)
        │
        │  Agents discover tools via MCP Registry API
        │  Agents discover agents via A2A /discover endpoint
        ▼
┌──────────────────────────────────────────────────────────────┐
│         VERTEX AGENT PLATFORM — AGENTIC OS                   │
│         https://vertex-agent-platform.sr.az.vtxdev.net       │
│                                                              │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────┐ │
│  │  MCP Registry    │  │  Model Registry   │  │  FinOps    │ │
│  │  (discover,      │  │  (LLM routing,    │  │  (budget,  │ │
│  │   register,      │  │   cost tracking,  │  │   spend,   │ │
│  │   health, RBAC)  │  │   OpenAI compat)  │  │   kill-sw) │ │
│  └─────────────────┘  └──────────────────┘  └────────────┘ │
│                                                              │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────┐ │
│  │  A2A Engine      │  │  Eval Pipeline    │  │  Lifecycle │ │
│  │  (discover,      │  │  (datasets, runs, │  │  (versions,│ │
│  │   delegate,      │  │   SLA monitoring, │  │   promote, │ │
│  │   orchestrate)   │  │   accuracy alerts)│  │   rollback)│ │
│  └─────────────────┘  └──────────────────┘  └────────────┘ │
│                                                              │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────┐ │
│  │  Governance      │  │  Memory           │  │  Events    │ │
│  │  (policy, PII,   │  │  (semantic search, │  │  (webhooks,│ │
│  │   autonomy L1-L5,│  │   cross-agent,    │  │   chains,  │ │
│  │   HITL, safety)  │  │   scoped memory)  │  │   event bus│ │
│  └─────────────────┘  └──────────────────┘  └────────────┘ │
│                                                              │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────┐ │
│  │  Identity        │  │  Compliance       │  │  Self-Heal │ │
│  │  (credential     │  │  (SOX/GDPR audit, │  │  (auto-    │ │
│  │   vault, OAuth,  │  │   retention,      │  │   rollback,│ │
│  │   usage audit)   │  │   posture)        │  │   failover)│ │
│  └─────────────────┘  └──────────────────┘  └────────────┘ │
│                                                              │
│  Developer Portal (17 tabs) │ Python SDK │ CLI (vap)        │
│  Marketplace v2 (reviews, deploy, metrics)                   │
└──────────────────────────────────────────────────────────────┘
        │  bundled in same pod (localhost:8001-8006)
        ▼
┌──────────────────────────────────────────────────────────────┐
│           VERTEX MCP DOMAIN LIBRARY                          │
│  babelfish-postgres  gorules-engine   o-series-cloud         │
│  confluence-mcp      github-mcp       document-rag           │
└──────────────────────────────────────────────────────────────┘
```

### How MCP Servers run on K8s

All Python MCP servers are bundled into the platform Docker image via `start.sh`:

```
One pod, one container:
  start.sh
  ├── o-series-cloud     → localhost:8001  (HTTP proxy to app.vertexinc.com)
  ├── babelfish-postgres → localhost:8002  (decode-postgres-dev/decoder/dbo)
  ├── gorules-engine     → localhost:8003  (train-postgres-dev/train)
  ├── confluence         → localhost:8004  (vertexinc.atlassian.net)
  └── platform           → 0.0.0.0:8080   (what K8s exposes publicly)
```

No separate VIPER pipelines. No new URLs. Same image, same deployment.

---

## Quick Start — Use the Live Platform

### 1. Browse available tools

Open https://vertex-agent-platform.sr.az.vtxdev.net — browse the MCP Registry, see what tools are available, check health status, view data classification badges (PII / Confidential / Internal).

### 2. Register your MCP server

```bash
# Register from a register.yaml file
python scripts/register_all.py \
  --registry https://vertex-agent-platform.sr.az.vtxdev.net \
  --auth-token dev:you@vertexinc.com:developer

# Or register directly
curl -X POST https://vertex-agent-platform.sr.az.vtxdev.net/api/v1/mcp/servers \
  -H "Authorization: Bearer dev:you@vertexinc.com:developer" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-server",
    "display_name": "My Domain Server",
    "description": "What it does",
    "endpoint": "https://my-server.dev.az.vtxdev.net/sse",
    "transport": "sse",
    "owner": "you@vertexinc.com",
    "data_classification": "internal"
  }'
```

### 3. Build an agent using the portal

1. Open https://vertex-agent-platform.sr.az.vtxdev.net (VPN required)
2. **Agent Builder tab** → 7-step wizard
3. Choose model, select skills (MCP servers), configure monitoring + FinOps budget
4. Click **Download ZIP** → 18-file scaffold pre-wired with VIPER workflow, Terraform, tests
5. Follow the README inside the ZIP — deployed in under an hour

### 4. Use the Model Registry in your agent

```python
from langchain_openai import ChatOpenAI

# Point at the platform's Model Registry (OpenAI-compatible endpoint)
llm = ChatOpenAI(
    base_url="https://vertex-agent-platform.sr.az.vtxdev.net/api/v1/models",
    api_key="dev:you@vertexinc.com:developer",
    model="claude-sonnet",  # or gpt5, gemini-pro, llama3-local
)
```

Every call is auto-reported to FinOps for spend tracking.

---

## Observability

> We instrument, you collect. Our services emit OpenTelemetry spans.
> Point `OTEL_EXPORTER_OTLP_ENDPOINT` at your own collector.

### Recommended stack

| Component | Choice | What it does |
|-----------|--------|-------------|
| LLM traces + evals | **Langfuse** | Captures every prompt, response, cost, latency |
| Infra metrics + APM | **Datadog** (existing Vertex contract) | FastAPI traces, DB queries, error rates |
| Span router | **OTel Collector** | Fans out to Langfuse + Datadog simultaneously |

### Setting up Langfuse (5 minutes)

```bash
# 1. Use the ready-to-go template
cd templates/observability/
cp .env.example .env
# Fill in: LANGFUSE_SECRET_KEY, LANGFUSE_PUBLIC_KEY (from your Langfuse project)

# 2. Start Langfuse + OTel Collector
docker compose up -d

# 3. Point your agent at the collector
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
export LANGFUSE_SECRET_KEY=sk-lf-...
export LANGFUSE_PUBLIC_KEY=pk-lf-...
```

### Setting up OpenTelemetry in your agent

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Point at your OTel Collector (which fans out to Langfuse + Datadog)
provider = TracerProvider()
provider.add_span_processor(
    BatchSpanProcessor(OTLPSpanExporter(
        endpoint=os.environ["OTEL_EXPORTER_OTLP_ENDPOINT"] + "/v1/traces"
    ))
)
trace.set_tracer_provider(provider)
```

Or use Langfuse's native SDK for richer LLM-specific tracing:

```python
from langfuse import Langfuse
from langfuse.callback import CallbackHandler

langfuse = Langfuse(
    secret_key=os.environ["LANGFUSE_SECRET_KEY"],
    public_key=os.environ["LANGFUSE_PUBLIC_KEY"],
    host=os.environ.get("LANGFUSE_HOST", "https://cloud.langfuse.com"),
)

# For LangGraph / LangChain agents — wrap the handler
handler = CallbackHandler()
result = agent.invoke(input, config={"callbacks": [handler]})
```

### Using Datadog (existing Vertex contract — $0 incremental)

```bash
# In your .env or 1Password secret
DD_API_KEY=<your-datadog-api-key>
DD_OTLP_GRPC_ENDPOINT=http://datadog-agent:4317

# Or configure in OTel Collector (templates/observability/otel-collector-config.yaml)
# Datadog pipeline is already pre-configured there
```

> **Important:** LLM traces contain prompts, responses, and potentially PII.
> Use self-hosted Langfuse (not cloud) for anything touching tax data or customer records.

---

## Azure Embeddings

For agents that need semantic search or RAG over internal documents:

```python
from langchain_openai import AzureOpenAIEmbeddings

embeddings = AzureOpenAIEmbeddings(
    azure_endpoint=os.environ["AZURE_EMBEDDING_ENDPOINT"],
    api_key=os.environ["AZURE_EMBEDDING_KEY"],
    azure_deployment="text-embedding-3-small",
    api_version="2024-02-01",
)

# Generate embeddings
vectors = embeddings.embed_documents(["tax rate for PA", "jurisdiction lookup"])
query_vector = embeddings.embed_query("what is the sales tax in Pennsylvania?")
```

**Environment variables (from 1Password):**

```bash
AZURE_EMBEDDING_ENDPOINT=https://decode-openai-dev-7ce1a.openai.azure.com/
AZURE_EMBEDDING_KEY=<key>
AZURE_EMBEDDING_DEPLOYMENT=text-embedding-3-small
```

These are the same embeddings used by the AI_DEcoding_Platform. Reuse the same endpoint — no new provisioning needed.

---

## Knowledge Bases — LanceDB (local) vs pgvector (cloud)

### When to use LanceDB (local / air-gapped)

Use LanceDB when:
- Prototyping locally — no cloud setup needed, pure Python
- Air-gapped agents (data cannot leave the machine)
- Small knowledge bases (< 1M documents) where serverless simplicity beats managed infra
- CI/testing — no external service dependency

```python
import lancedb
import os

# Local file-based vector store — no server needed
db = lancedb.connect(os.environ.get("LANCEDB_PATH", "/data/lancedb"))
table = db.create_table("tax_docs", data=[
    {"vector": embeddings.embed_query("..."), "text": "...", "source": "..."}
], mode="overwrite")

# Search
results = table.search(embeddings.embed_query("PA sales tax rate")).limit(5).to_list()
```

**Pros:** Zero infra, works offline, fast for small-medium data.
**Cons:** No multi-user access, no HA, can't query from multiple pods.

### When to use pgvector (Azure PostgreSQL — prod)

Use pgvector when:
- Multiple agents or services need the same knowledge base
- Knowledge base is updated continuously (new docs ingested)
- You need SQL joins (e.g. "find similar tax rules WHERE jurisdiction = 'PA'")
- Running on K8s where pods can't share a local file

```python
from langchain_postgres import PGVector
from langchain_openai import AzureOpenAIEmbeddings

vector_store = PGVector(
    embeddings=embeddings,
    collection_name="tax_knowledge",
    connection=os.environ["MCP_REGISTRY_DATABASE_URL"].replace("+asyncpg", ""),
)

# Add documents
vector_store.add_texts(texts, metadatas=metadatas)

# Similarity search
docs = vector_store.similarity_search("sales tax rate PA", k=5)
```

**pgvector is already installed** on `vap-postgres-dev` — no extra provisioning.

### Decision table

| Situation | Choice |
|-----------|--------|
| Local dev / prototype | LanceDB |
| Production agent on K8s | pgvector (vap-postgres-dev) |
| Air-gapped / offline | LanceDB |
| Shared knowledge base (multiple agents) | pgvector |
| RAG with SQL filtering | pgvector |
| CI tests | LanceDB |

---

## Local Development

```powershell
# 1. Copy and fill env vars
cp .env.example .env
# Fill in: BABELFISH_DATABASE_URL, GORULES_DATABASE_URL, GITHUB_TOKEN,
#          CONFLUENCE_API_TOKEN, CONFLUENCE_USERNAME

# 2. Start everything
docker compose up --build

# 3. Verify core services
Invoke-RestMethod http://localhost:8010/health   # MCP Registry
Invoke-RestMethod http://localhost:8013/health   # Deploy Intelligence
Invoke-RestMethod http://localhost:8014/health   # Agent Control

# 4. Register MCP servers
python scripts/register_all.py

# 5. Run smoke test
python scripts/test_platform.py --base-url http://localhost:8010
```

**What starts by default** (no profile):
- `postgres` (5433) — platform DB
- `mcp-registry` (8010)
- `deploy-intelligence` (8013)
- `agent-control` (8014)
- `babelfish-postgres` (8002)
- `gorules-engine` (8003)
- `o-series-cloud` (8001)
- `confluence` (8004)
- `github-mcp` (8005) — uses official GitHub image in SSE mode

**Optional profiles:**
```powershell
docker compose --profile observability up   # + OTel Collector (ports 4317/4318)
docker compose --profile portal up          # + Developer Portal (port 3002)
docker compose --profile gateway up         # + Agentgateway (port 8080)
```

---

## API Reference

### MCP Registry
```
POST   /api/v1/mcp/servers              Register (auth required)
GET    /api/v1/mcp/servers              List all
GET    /api/v1/mcp/servers/{id}         Details + schema
DELETE /api/v1/mcp/servers/{id}         Deregister (auth + owner)
GET    /api/v1/mcp/servers/{id}/health  Live health check
GET    /api/v1/mcp/servers/{id}/tools   Tools exposed by server
PATCH  /api/v1/mcp/servers/{id}/lifecycle  Lifecycle transition (active→deprecated→retired)
GET    /api/v1/mcp/foundry-manifest     Foundry-compatible manifest (public)
GET    /api/v1/mcp/audit                Audit log (?server_name=&action=)
```

### Model Registry
```
GET    /api/v1/models/                  List models + cost
GET    /api/v1/models/{name}            Model details
POST   /api/v1/models/chat/completions  OpenAI-compatible (ChatOpenAI works here)
POST   /api/v1/models/reload            Hot-reload config (no restart)
GET    /api/v1/models/proxy/health      LiteLLM proxy health
```

### Agent Control
```
POST   /api/v1/control/schedules              Create schedule (cron/interval/once)
GET    /api/v1/control/schedules              List schedules
PATCH  /api/v1/control/schedules/{id}         Update schedule
DELETE /api/v1/control/schedules/{id}         Delete schedule
POST   /api/v1/control/policies               Create run policy (retry + circuit breaker)
GET    /api/v1/control/policies/{agent}       Get policy
PATCH  /api/v1/control/policies/{agent}       Update policy
POST   /api/v1/control/policies/{agent}/circuit/open   Kill switch (admin only)
POST   /api/v1/control/policies/{agent}/circuit/reset  Reset circuit
GET    /api/v1/control/dead-letters           Failed runs queue
POST   /api/v1/control/dead-letters/{id}/replay  Replay failed run
GET    /api/v1/control/events                 Event log
```

### FinOps
```
POST   /api/v1/finops/budgets                 Set monthly budget for agent
GET    /api/v1/finops/budgets/{agent}         Budget + current spend %
POST   /api/v1/finops/spend                   Record spend event (Model Registry calls this automatically)
GET    /api/v1/finops/spend/{agent}           Spend history
POST   /api/v1/finops/kill-switch/{agent}     Kill agent immediately (admin)
POST   /api/v1/finops/kill-switch/{agent}/reset  Re-enable agent
GET    /api/v1/finops/kill-switch/{agent}/check  Check: is agent allowed to run?
```

### Platform Generate
```
POST   /api/v1/generate/preview     Preview 18 scaffold files before downloading
POST   /api/v1/generate             Download scaffold ZIP (18 files, VIPER-ready)
```

### Deploy Intelligence
```
POST   /api/v1/deploy/analyze       agent_config → {db, host, state_store, cost/mo, reasons}
```

---

## Governance & Production Readiness

### Where it comes from
The Agent Builder's **Governance — Production Readiness** panel encodes **Vertex's 10 AI Agent
Standards** (source: `AI-Agent-Readiness.docx` — the production bar every Vertex-branded agent must
meet). Your form inputs become a `governance:` block on the agent-definition; the platform scores
that block and uses it as an **admission gate** when generating a scaffold. See the **NORTH STAR**
section in `CLAUDE.md` for the full strategy (going external + runtime policy governance).

> **Runtime governance is LIVE.** Governance is enforced at runtime through a 22-step pipeline in
> `core/runtime/executor.py` — PII tokenization, policy deny→403, autonomy L1 tool removal,
> HITL approval queue, FinOps kill-switch, content safety, prompt injection scanning, and more.
> Every agent is auto-scored (22 real checks) on registration, update, and after every chat
> execution. Scores returned in every API response.

### The master switch — `Customer-facing` (`governance.external_facing`)
| Setting | Effect |
|---------|--------|
| **OFF** (internal) | Standards 8–10 = **n/a**, scorecard is **informational**, scaffold always downloadable (`deploy.target=development`) |
| **ON** (customer-facing) | Standards 8–10 **apply**, `deploy.target=production`, **gate enforced** — `POST /api/v1/generate` returns **422** until every applicable standard passes |

### Two kinds of field
- **Enforceable** (Std 2,3,5,6,7,8,9) → **toggles/selects**: the control must be *declared* (on / set).
- **Attestable** (Std 1,4,10) → **text fields**: human-signed *evidence* must be *filled in* (non-empty).

### Field reference — what each one means and when it passes
| # | Standard | Kind | Fields | Passes when |
|---|----------|------|--------|-------------|
| 1 | Outcome & Workflow Ownership | attest | Workflow, Success metric, Recovery plan | all three filled |
| 2 | Autonomy Boundaries | enforce | Autonomy tier, Low-confidence behavior, Kill switch, action lists | kill-switch on · tier set · low-confidence ≠ `guess` · an action list given |
| 3 | Access & Permission Model | enforce | Least privilege, Tenant scoped, Role scoped, All tools permissioned, No access-by-default | all five on |
| 4 | Release Readiness Gates | attest | Incident owner, SLA, Checklist URL, Exec sponsor | owner+SLA+URL filled (+ exec sponsor if customer-facing) |
| 5 | Traceability & Auditability | enforce | Deterministic replay, Trace redaction, Compliance-consumable | all three on |
| 6 | Observability & Runtime Visibility | enforce | Usage dashboards, Cost/abnormal alerts, Telemetry baseline | all three on |
| 7 | Reliability & Failure Handling | enforce | Idempotent, Retry-safe, Resumable | all three on |
| 8 | IP Protection *(external only)* | enforce | Redaction by default | on |
| 9 | Rollout & Change Control *(external only)* | enforce | Feature flags, Versioned configs, Instant rollback | all three on |
| 10 | Monetization & Certification *(external only)* | attest | Pricing/value model, Value attribution, Certification | pricing + certification filled · value attribution on |

`production_ready = (zero failing applicable standards)`.

### How it flows
```
Builder form → governance:{...} on the agent-definition
   ↓
POST /api/v1/generate/preview → returns "readiness" {production_ready, summary, standards[]}  (the panel)
POST /api/v1/generate         → customer-facing & not ready → 422 readiness_gate_failed
                                else → ZIP (includes readiness-scorecard.json)
```

### In the code
| Piece | File |
|-------|------|
| Governance schema (the 10 stanzas) | `core/platform_generate/schemas.py` |
| Scorecard logic + production gate trigger | `core/platform_generate/readiness.py` |
| Preview returns scorecard / generate enforces gate | `core/platform_generate/routes.py`, `generator.py` |
| Builder UI (fields + scorecard panel) | `web/app/page.tsx` (Generate step) |
| Unit tests | `core/platform_generate/tests/test_readiness.py` |

---

## Multi-Tenant Governance (Shipped Jun 5, 2026)

Every tenant gets full isolation — agents, policies, autonomy levels, HITL approvals, and sessions are all scoped per tenant. Admin can push marketplace agents to specific customers.

### How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                     PLATFORM (Admin View)                           │
│                                                                     │
│  Marketplace Agents         Policy Rules (Global)                   │
│  ┌──────────────────┐       ┌──────────────────┐                   │
│  │ tax-rate-lookup   │       │ deny: write_*    │  ← applies to ALL│
│  │ document-qa       │       │ (tenant_id=NULL) │    tenants       │
│  │ transaction-analyst│      └──────────────────┘                   │
│  └──────────────────┘                                               │
│         │                                                           │
│   "Publish to Tenant A"                                             │
│         │                                                           │
│         ▼                                                           │
├─────────────────────────────┬───────────────────────────────────────┤
│     TENANT A (Acme Corp)    │     TENANT B (Beta Inc)               │
│                             │                                       │
│  Agents:                    │  Agents:                              │
│   • acme-tax-lookup ✅      │   • beta-custom-agent ✅              │
│   • acme-doc-qa ✅          │   • beta-research ✅                  │
│   • transaction-analyst ✅  │                                       │
│     (published from mktpl)  │  (Cannot see Acme's agents)          │
│                             │                                       │
│  Policy Rules:              │  Policy Rules:                        │
│   • deny: database_delete   │   • (none — only global rules apply) │
│     (tenant_id=acme)        │                                       │
│                             │  (Cannot see Acme's deny rule)       │
│  Autonomy: L3 (supervised)  │  Autonomy: L1 (restricted)           │
│                             │                                       │
│  HITL Queue:                │  HITL Queue:                          │
│   • 2 pending approvals     │   • 0 pending                        │
│                             │                                       │
│  (Cannot see Beta's data)   │  (Cannot see Acme's approvals)      │
│                             │                                       │
│  Sessions: scoped           │  Sessions: scoped                    │
│   (Beta can't read Acme's   │   (Acme can't read Beta's           │
│    evidence or timeline)    │    evidence or timeline)             │
└─────────────────────────────┴───────────────────────────────────────┘
```

### Tenant Lifecycle

```
1. SIGN UP     POST /api/v1/partners/signup
               → tenant_id generated (partner-xxxx)
               → API key returned (vap_xxx)
               → 3 pre-built agents seeded (tax-rate-lookup, document-qa, github-pr-reviewer)

2. CREATE      Customer creates agents via API or portal
               → tenant_id auto-set from API key
               → agents only visible after PATCH {published: true}

3. GOVERN      Admin sets per-tenant policy rules + autonomy levels
               → deny rules block chat (HTTP 403)
               → require_approval rules pause chat (HITL pending)
               → autonomy L1-L5 injected into system prompt per tenant

4. PUBLISH     Admin pushes marketplace agent to specific tenant
               POST /api/v1/agents/{id}/publish-to-tenant
               → clones agent with tenant_id + published=true
               → only that tenant sees it

5. ISOLATE     Cross-tenant access blocked at every layer:
               → agents: tenant A can't see tenant B's agents
               → policies: tenant A's deny rule doesn't affect tenant B
               → HITL: tenant A can't see/approve tenant B's requests
               → sessions: tenant B reading tenant A's evidence → 404
               → admin: sees everything across all tenants
```

### Key Concepts

| Concept | How it works |
|---------|-------------|
| **Tenant isolation** | Every API key resolves to a `tenant_id`. All queries filter by it. |
| **Global vs tenant rules** | `tenant_id=NULL` → applies to all tenants. `tenant_id="partner-xxx"` → only that tenant. |
| **Autonomy per tenant** | Same agent can be L3 for Tenant A and L1 for Tenant B. Tenant-specific config takes priority over global. |
| **Published agents** | Customers only see agents where `published=true`. Admin/maintainer must publish explicitly. |
| **Publish-to-tenant** | `POST /agents/{id}/publish-to-tenant` clones a marketplace agent for a specific customer. |
| **Session isolation** | Sessions have `tenant_id`. Cross-tenant evidence/timeline reads return 404 (no existence leak). |
| **HITL scoping** | Approval requests carry `tenant_id`. Tenants can only see/approve their own requests. |
| **Fail-closed governance** | `GOVERNANCE_FAIL_CLOSED=true` → errors in policy/PII block the request (never fail-open). |

### API Endpoints — Multi-Tenant

```
# Tenant signup
POST   /api/v1/partners/signup          → {tenant_id, api_key, agents_seeded}

# Agent management (tenant-scoped via API key)
GET    /api/v1/agents/                   → customer sees own published agents only
POST   /api/v1/agents/                   → tenant_id auto-set from caller
PATCH  /api/v1/agents/{id}              → update agent (published, config, etc.)

# Publish marketplace agent to specific tenant (admin only)
POST   /api/v1/agents/{id}/publish-to-tenant   → clones + publishes for one tenant

# Policy rules (tenant-scoped)
POST   /api/v1/policy/rules              → create rule (tenant_id auto-set or specified)
GET    /api/v1/policy/rules              → customer sees own + global rules
DELETE /api/v1/policy/rules/{id}         → delete own tenant's rule (admin: any)

# Autonomy (tenant-scoped, admin/developer managed)
PUT    /api/v1/autonomy/{agent_name}     → set level + tenant_id (WriteDep required)
GET    /api/v1/autonomy/{agent_name}     → returns tenant-specific, falls back to global

# HITL (tenant-scoped)
GET    /api/v1/hitl/pending              → customer sees own tenant's pending approvals
POST   /api/v1/hitl/{id}/approve         → only approve own tenant's requests (admin: any)

# Sessions (tenant-scoped)
GET    /api/v1/sessions/                 → customer sees own tenant's sessions
GET    /api/v1/sessions/{id}/evidence    → cross-tenant read → 404
```

### Manual Testing — Verify Multi-Tenant Governance

Test against live: `https://vertex-agent-platform.sr.az.vtxdev.net`

```bash
BASE=https://vertex-agent-platform.sr.az.vtxdev.net
ADMIN="Authorization: Bearer dev:admin@vertexinc.com:admin"

# ── Step 1: Create two tenants ──────────────────────────────────────
curl -s -X POST $BASE/api/v1/partners/signup \
  -H "Content-Type: application/json" \
  -d '{"company_name":"Acme Corp","contact_name":"Alice","contact_email":"alice@acme.com","use_case":"tax automation"}' \
  | jq '{tenant_id, api_key}'
# Save: TENANT_A_ID, TENANT_A_KEY

curl -s -X POST $BASE/api/v1/partners/signup \
  -H "Content-Type: application/json" \
  -d '{"company_name":"Beta Inc","contact_name":"Bob","contact_email":"bob@beta.com","use_case":"compliance"}' \
  | jq '{tenant_id, api_key}'
# Save: TENANT_B_ID, TENANT_B_KEY

# ── Step 2: Each tenant creates an agent ────────────────────────────
# Tenant A creates an agent (tenant_id auto-set from API key)
curl -s -X POST $BASE/api/v1/agents/ \
  -H "X-API-Key: $TENANT_A_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"acme-test-agent","display_name":"Acme Test","runtime_type":"managed","managed_config":{"system_prompt":"You are a tax assistant.","model":"claude-sonnet"}}' \
  | jq '{id, name, tenant_id}'
# Save: AGENT_A_ID

# Tenant B creates an agent
curl -s -X POST $BASE/api/v1/agents/ \
  -H "X-API-Key: $TENANT_B_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"beta-test-agent","display_name":"Beta Test","runtime_type":"managed","managed_config":{"system_prompt":"You are a compliance bot.","model":"claude-sonnet"}}' \
  | jq '{id, name, tenant_id}'
# Save: AGENT_B_ID

# ── Step 3: Publish agents (customers only see published) ──────────
curl -s -X PATCH $BASE/api/v1/agents/$AGENT_A_ID \
  -H "$ADMIN" -H "Content-Type: application/json" \
  -d '{"published": true}'

curl -s -X PATCH $BASE/api/v1/agents/$AGENT_B_ID \
  -H "$ADMIN" -H "Content-Type: application/json" \
  -d '{"published": true}'

# ── Step 4: Verify tenant isolation ─────────────────────────────────
# Tenant A sees ONLY their own agent (not Beta's)
curl -s $BASE/api/v1/agents/ -H "X-API-Key: $TENANT_A_KEY" | jq '.[].name'
# Expected: "acme-test-agent" (NO "beta-test-agent")

# Tenant B sees ONLY their own agent (not Acme's)
curl -s $BASE/api/v1/agents/ -H "X-API-Key: $TENANT_B_KEY" | jq '.[].name'
# Expected: "beta-test-agent" (NO "acme-test-agent")

# ── Step 5: Create a deny rule scoped to Tenant A ──────────────────
curl -s -X POST $BASE/api/v1/policy/rules \
  -H "$ADMIN" -H "Content-Type: application/json" \
  -d "{\"name\":\"block-acme-writes\",\"tool_pattern\":\"*\",\"action\":\"deny\",\"reason\":\"Testing tenant deny\",\"tenant_id\":\"$TENANT_A_ID\"}"
# Save: RULE_ID

# Tenant A sees the rule
curl -s $BASE/api/v1/policy/rules -H "X-API-Key: $TENANT_A_KEY" | jq '.[].name'
# Expected: "block-acme-writes"

# Tenant B does NOT see Tenant A's rule
curl -s $BASE/api/v1/policy/rules -H "X-API-Key: $TENANT_B_KEY" | jq '.[].name'
# Expected: empty (no "block-acme-writes")

# ── Step 6: Verify policy enforcement ──────────────────────────────
# Tenant A chat → 403 (blocked by deny rule)
curl -s -o /dev/null -w "%{http_code}" -X POST $BASE/api/v1/agents/acme-test-agent/chat \
  -H "X-API-Key: $TENANT_A_KEY" -H "Content-Type: application/json" \
  -d '{"message":"hello"}'
# Expected: 403

# Tenant B chat → 200 (NOT blocked — rule is scoped to Tenant A only)
curl -s -X POST $BASE/api/v1/agents/beta-test-agent/chat \
  -H "X-API-Key: $TENANT_B_KEY" -H "Content-Type: application/json" \
  -d '{"message":"hello"}' | jq '{policies_passed}'
# Expected: {"policies_passed": true}

# ── Step 7: Set different autonomy levels per tenant ────────────────
# L3 for Tenant A's agent
curl -s -X PUT $BASE/api/v1/autonomy/acme-test-agent \
  -H "$ADMIN" -H "Content-Type: application/json" \
  -d "{\"level\":\"L3\",\"tenant_id\":\"$TENANT_A_ID\"}"

# L1 for Tenant B's agent
curl -s -X PUT $BASE/api/v1/autonomy/beta-test-agent \
  -H "$ADMIN" -H "Content-Type: application/json" \
  -d "{\"level\":\"L1\",\"tenant_id\":\"$TENANT_B_ID\"}"

# Verify each tenant reads back their level
curl -s $BASE/api/v1/autonomy/acme-test-agent -H "X-API-Key: $TENANT_A_KEY" | jq '.level'
# Expected: "L3"

curl -s $BASE/api/v1/autonomy/beta-test-agent -H "X-API-Key: $TENANT_B_KEY" | jq '.level'
# Expected: "L1"

# ── Step 8: Publish marketplace agent to specific tenant ────────────
# Find a platform agent (admin)
PLATFORM_AGENT_ID=$(curl -s $BASE/api/v1/agents/ -H "$ADMIN" | jq -r '[.[] | select(.tenant_id == null or .tenant_id == "")][0].id')

# Publish it to Tenant A only
curl -s -X POST "$BASE/api/v1/agents/$PLATFORM_AGENT_ID/publish-to-tenant?tenant_id=$TENANT_A_ID" \
  -H "$ADMIN" | jq '{name, tenant_id, published}'

# Tenant A sees it, Tenant B does NOT
curl -s $BASE/api/v1/agents/ -H "X-API-Key: $TENANT_A_KEY" | jq '.[].name'
# Should include the published marketplace agent

curl -s $BASE/api/v1/agents/ -H "X-API-Key: $TENANT_B_KEY" | jq '.[].name'
# Should NOT include the marketplace agent

# ── Cleanup: delete the test rule ───────────────────────────────────
curl -s -X DELETE $BASE/api/v1/policy/rules/$RULE_ID -H "$ADMIN"
```

### Automated Test Suite

```bash
# Run all 27 multi-tenant governance tests against live
PYTHONUTF8=1 python scripts/test_multi_tenant_governance.py \
  --base-url https://vertex-agent-platform.sr.az.vtxdev.net

# Run against local dev
PYTHONUTF8=1 python scripts/test_multi_tenant_governance.py \
  --base-url http://localhost:8090
```

**Test coverage (27 tests, 9 groups):**

| Group | Tests | What it verifies |
|-------|-------|-----------------|
| Partner signup | 2 | Two tenants created, API keys returned |
| Agent creation | 2 | Agents auto-scoped to caller's tenant |
| Agent visibility | 3 | Publish + tenant A sees own, not B's |
| Policy isolation | 3 | Deny rule scoped to tenant A, invisible to B |
| Policy enforcement | 1 | Tenant A chat → 403 (denied) |
| Policy no-bleed | 1 | Tenant B chat → 200 (not affected) |
| Autonomy isolation | 4 | L3 for A, L1 for B, each reads back correctly |
| HITL isolation | 3 | require_approval scoped, B can't see A's queue |
| Publish-to-tenant | 4 | Clone marketplace agent, A sees it, B doesn't |
| Session isolation | 3 | Cross-tenant evidence read → 404, admin → 200 |

---

## Authentication & RBAC

| Method | Format | Use case |
|--------|--------|----------|
| **Dev tokens** | `Authorization: Bearer dev:you@vertexinc.com:admin` | Local dev, testing |
| **API keys** | `X-API-Key: vap_...` | CI pipelines, machine-to-machine |
| **Entra ID JWT** | `Authorization: Bearer <jwt>` | Production (framework ready, needs Azure config) |

| Role | Permissions |
|------|------------|
| `admin` | Full access — kill switch, API keys, circuit open, any server |
| `team-lead` | Register/update/delete servers + schedules + policies |
| `developer` | Register/update/delete own servers, create schedules |
| `read-only` | Browse only — GET endpoints |

Toggle enforcement: `MCP_REGISTRY_AUTH_MODE=permissive` (default, logs warnings) or `enforced` (401 on unauthenticated writes).

---

## Adding a New MCP Server

Any Vertex team follows this pattern:

```
mcp_servers/
└── your-server-name/
    ├── server.py        # FastMCP tools — what the LLM can call
    ├── register.yaml    # Name, endpoint, auth, tags, owner
    ├── Dockerfile       # Containerize it
    └── README.md        # What it does + example tool calls
```

```python
# server.py
from fastmcp import FastMCP
import os

mcp = FastMCP("my-domain-server")

@mcp.tool()
async def my_tool(param: str) -> str:
    """Description shown to the LLM agent."""
    return result

if __name__ == "__main__":
    port = int(os.environ.get("MCP_SERVER_PORT", "8000"))
    mcp.run(transport="sse", host="0.0.0.0", port=port)
```

```yaml
# register.yaml
name: my-domain-server
display_name: "My Domain Server"
description: "What this server does"
endpoint: https://my-server.dev.az.vtxdev.net/sse  # your deployed endpoint
transport: sse
auth:
  type: none    # none | bearer | oauth2
tags: [domain, team-name]
owner: team-name@vertexinc.com
data_classification: internal  # none | internal | confidential | pii
```

Then register: `python scripts/register_all.py --registry https://vertex-agent-platform.sr.az.vtxdev.net`

---

## Pre-wired Vertex MCP Servers

All bundled in the platform image. Run on localhost inside the K8s pod.

| Server | Port | What it exposes | Data source |
|--------|------|----------------|-------------|
| `babelfish-postgres` | 8002 | Tax DB — return definitions, jurisdictions, rates | decode-postgres-dev/decoder (dbo schema) |
| `gorules-engine` | 8003 | GoRules decision table evaluation | train-postgres-dev/train |
| `o-series-cloud` | 8001 | O-Series tax engine connector | app.vertexinc.com (bearer forwarded) |
| `confluence` | 8004 | Confluence search + page creation | vertexinc.atlassian.net |
| `github-mcp` | 8005 | GitHub issues, PRs, code search | github.com (docker-compose only) |

---

## Model Registry — Available Models

| Model | Provider | Cost/1M in | Best for |
|-------|----------|-----------|----------|
| `claude-sonnet` | Anthropic | $3.00 | General reasoning, code (default) |
| `claude-opus` | Anthropic | $15.00 | Complex reasoning, best quality |
| `gpt5` | Azure OpenAI | $15.00 | Code generation |
| `gpt5-mini` | Azure OpenAI | $0.15 | Low cost, fast responses |
| `gemini-pro` | Google Vertex AI | $1.25 | Long context (1M tokens) |
| `llama3-local` | Ollama | $0 | Air-gapped, no internet, no cost |

---

## What's Pending

### Sprint 1 — SHIPPED (May 2026)
> Tool Registry + Standards + FinOps + Agent Control + Scaffold + MCP Domain Servers

### Sprint 2 — SHIPPED (Jun 5, 2026)
> Multi-tenant governance, policy enforcement, PII tokenization, HITL, fail-closed mode. 27/27 E2E tests.

### Sprint A — SHIPPED (Jun 7, 2026)
> A2A collaboration (discover/delegate/orchestrate), continuous evals (datasets/SLA), agent lifecycle (versions/promote/rollback/shadow). 31 new tests.

### Sprint B — SHIPPED (Jun 7, 2026)
> Event-driven triggers (webhooks/completion chains/event bus), advanced memory (semantic/cross-agent/scoped), advanced guardrails (denied topics/output PII/citations). 30+ new tests.

### Sprint C — SHIPPED (Jun 7, 2026)
> Python SDK (`from sdk import Client`), CLI (`python -m sdk.cli`), Marketplace v2 (reviews/metrics/deploy-to-tenant).

### Sprint D — SHIPPED (Jun 7, 2026)
> Delegated identity (credential vault/OAuth), compliance reporting (SOX/GDPR/retention), self-healing (auto-rollback/model failover/degraded mode).

### Still Pending
- [ ] Entra ID / SSO enforcement — needs `AZURE_TENANT_ID` + `AZURE_CLIENT_ID` from VIPER
- [ ] Publish SDK to internal PyPI — `pip install vertex-agent-platform`
- [ ] github-mcp on K8s (Go binary — needs separate VIPER pipeline)
- [ ] Continuous learning loop (Langfuse traces → SKILL.md PR auto-opened)

---

## Agentic OS — Complete

> **"Kubernetes for agents"** — what Kubernetes does for containers, this platform does for agents.

```
Sprint 1 (DONE — May 2026)
  ✅ Tool Registry + Standards + FinOps + Agent Control + Scaffold generator
  ✅ MCP domain servers bundled in image (babelfish, gorules, o-series, confluence)

Sprint 2 (DONE — Jun 5, 2026)
  ✅ Multi-tenant governance — per-tenant agents, policies, autonomy, HITL, sessions
  ✅ Governance enforcement — deny→403, require_approval→HITL, PII tokenization
  ✅ Fail-closed mode, publish-to-tenant, session isolation
  ✅ 27/27 multi-tenant E2E tests, 8/8 enforcement tests

Sprint A (DONE — Jun 7, 2026)
  ✅ A2A Agent Collaboration — capability cards, discover, delegate, orchestrate
     Multi-agent patterns: pipeline (A→B→C), fan-out (parallel), supervisor (LLM picks)
  ✅ Continuous Evals — golden datasets, scheduled runs, accuracy SLA monitoring
  ✅ Agent Lifecycle — draft→staging→production, version pinning, one-click rollback, shadow/canary

Sprint B (DONE — Jun 7, 2026)
  ✅ Event-Driven Triggers — webhooks, completion chains (A finishes→B starts), event bus
  ✅ Advanced Memory — semantic search, cross-agent shared memory, agent/tenant/platform scopes
  ✅ Advanced Guardrails — denied topics, output PII detection, citation requirements

Sprint C (DONE — Jun 7, 2026)
  ✅ Python SDK — from sdk import Client; vap = Client(api_key="vap_xxx")
  ✅ CLI Tool — python -m sdk.cli agents list / eval run / budget list
  ✅ Marketplace v2 — reviews, usage metrics, one-click deploy-to-tenant, config overrides

Sprint D (DONE — Jun 7, 2026)
  ✅ Delegated Identity — agent credential vault (Fernet encrypted), OAuth token management
  ✅ Compliance Reporting — automated SOX/GDPR/platform audit reports, retention policies
  ✅ Self-Healing — auto-rollback on SLA breach, model failover, degraded mode

  132 unit tests | 4 smoke test scripts | Portal with 12 governance pills
```

### Competitive Position

```
                     Vertex AP    AWS Bedrock    Salesforce    Azure Foundry    LangSmith
────────────────────────────────────────────────────────────────────────────────────────
Tool Registry        ████████     ████████       ██████        ████████         ██████
Governed Execution   ████████     ██████         ████████      ██████           ████
Multi-tenant         ██████       ████████       ████████      ████████         ██████
A2A Collaboration    ██████       ██████         ████████      ████             ████████
Memory               ██████       ████████       ██████        ████             ██████
Evals Pipeline       ██████       ██████         ████████      ████████         ████████
Event Triggers       ██████       ████████       ████████      ████████         ████
Agent Lifecycle      ██████       ████████       ████████      ████████         ██████
SDK/CLI              ██████       ████████       ██████        ████████         ████████
Content Safety       ██████       ████████       ████████      ████████         ██
Compliance           ██████       ████████       ████████      ██████           ██
Self-Healing         ██████       ████████       ██████        ████████         ████
```

**Our strength:** Governed execution path + multi-tenant isolation is production-grade.
**Unique angle:** Framework-neutral (LangGraph + CrewAI + Foundry + ADK all work).

## Demo — Acme Tax Corp

Seed a realistic enterprise tenant with 5 agents, full governance, and all features populated:

```bash
python scripts/seed_agentic_os_demo.py --base-url https://vertex-agent-platform.sr.az.vtxdev.net
```

This creates **Acme Tax Corporation** with:
- 5 agents: Tax Rate Engine (L3), Compliance Auditor (L2), Document Analyst (L1), Billing Assistant (L4), Customer Support (L5)
- 3 policy rules, 5 autonomy configs, 5 budgets
- 2 golden eval datasets (8 tax test cases), 3 SLA targets
- 10 cross-session memories (regulations, preferences, procedures)
- 3 event triggers (webhook, completion chain, event bus)
- 3 content guardrails (block competitors, block output PII, require citations)
- 2 agent credentials (O-Series OAuth, Confluence API key)
- 3 self-healing rules (auto-rollback, model failover, degraded mode)
- 1 compliance report + retention policy
- 2 versioned prompt templates

After seeding, open **Governance tab** → every pill shows real data.

---

## Infrastructure

```
Platform URL:   https://vertex-agent-platform.sr.az.vtxdev.net
K8s cluster:    cpe-eastus2-toolsdev01-a / namespace: vertex-agent-platform-development
PostgreSQL:     vap-postgres-dev.postgres.database.azure.com / db: vertex_platform
Babelfish:      decode-postgres-dev.postgres.database.azure.com / db: decoder (dbo schema)
GoRules:        train-postgres-dev.postgres.database.azure.com / db: train
Container reg:  docker.binrepo.vtxdev.net/pdd-ctd/vertex-agent-platform
1Password:      PDD - AI Decode Platform - NONPROD / item: vertex-agent-platform-dev
```

VIPER pipeline: `.github/workflows/cicd-dev.yaml` → auto-deploys on push to `main`.

---

## Repository Structure

```
Vertex_AI_PLATFORM/
├── CLAUDE.md                    # Master architecture context + decisions
├── README.md                    # This file
├── Dockerfile                   # Root Dockerfile — single image (port 8080)
├── start.sh                     # Startup: MCP servers in background + platform foreground
├── docker-compose.yml           # Local dev (all services)
│
├── core/
│   ├── consolidated.py          # Single FastAPI app — 6 routers + OTel
│   ├── mcp_registry/            # MCP Registry + auth + RBAC + audit + lifecycle
│   ├── agent_control/           # Schedules + circuit breaker + dead-letter + events
│   ├── deploy_intelligence/     # Infra recommendation rule engine
│   ├── model_registry/          # LLM routing (LiteLLM) + OpenAI-compat endpoint
│   ├── finops/                  # Budget + spend tracking + kill-switch
│   ├── platform_generate/       # Scaffold ZIP generator (Jinja2 templates)
│   └── observability/           # OTel SDK setup — triple exporter
│
├── mcp_servers/                 # Vertex domain MCP servers (bundled in image)
│   ├── _template/               # Copy this to add a new server
│   ├── babelfish_postgres/      # Tax DB queries (dbo schema)
│   ├── gorules_engine/          # GoRules JDM evaluation
│   ├── o_series_cloud/          # O-Series HTTP connector
│   ├── confluence/              # Confluence search + pages
│   └── github_mcp/              # GitHub (register.yaml only — uses official image)
│
├── web/                         # Developer Portal (Next.js + Tailwind CSS)
│
├── templates/                   # Ready-to-use configs for your stack
│   ├── observability/           # Langfuse + OTel Collector docker-compose
│   ├── litellm/                 # LiteLLM proxy with Vertex model catalog
│   ├── agentgateway/            # Solo.io Agentgateway for MCP traffic
│   └── agent-examples/          # LangGraph starter with platform pre-wired
│
├── scripts/
│   ├── register_all.py          # Register all MCP servers to the registry
│   ├── test_platform.py         # Smoke test (25 checks)
│   ├── test_agent_creation.py   # End-to-end agent creation test (27 checks)
│   └── test_db.py               # DB connectivity check
│
├── deployments/                 # VIPER Helm values
│   ├── app-values.yaml          # Base: probes, gateway, identity
│   └── app-values-azure-development.yaml  # Dev: 1Password, env vars
├── viper-config/
│   └── viper_config.yaml        # VIPER CI/CD pipeline
└── terraform/                   # Azure PostgreSQL infrastructure
    ├── environments/dev/
    └── modules/postgresql/
```
