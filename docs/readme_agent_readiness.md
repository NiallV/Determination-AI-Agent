# Agent Lifecycle — Complete Workflow

> **Updated: June 5, 2026** — Verified against live codebase. All claims traced to source files.
>
> **What this doc is:** the full lifecycle of every agent on the Vertex Agent Platform — how agents
> are created, governed, published, executed, observed, controlled, and retired. Use this to
> onboard engineers, update the wiki, and audit the platform's actual behavior.

---

## Overview

Every agent goes through a **7-stage lifecycle:**

```
CREATE ──▶ GOVERN ──▶ PUBLISH ──▶ EXECUTE ──▶ OBSERVE ──▶ CONTROL ──▶ RETIRE
  │           │          │           │           │           │           │
Register    Policy     Visible    Chat       Telemetry   Kill-sw     Decommis
Config      PII rules  to users   Tool call  Sessions    Circuit     Unpublish
Probe       Autonomy   Tenant     LLM call   Spend       DLQ         Version
Audit       Budget     scope      Response   Stats       Schedule    history
```

---

## Stage 1: CREATE — Agent Registration

**Endpoint:** `POST /api/v1/agents/`
**Auth:** `CustomerDep` (customer role or higher)
**Source:** `core/agent_registry/routes.py` L65–120

### Registration Flow

```
User submits form (Portal Builder / My Agents tab / API)
  │
  ├─ 1. Auth check — caller must have customer/developer/admin role
  ├─ 2. Tenant injection — if caller has tenant_id, auto-inject into agent record
  ├─ 3. Runtime type resolution — "managed" | "external" | "built_in" (default: "external")
  ├─ 4. Endpoint probe — non-blocking HEAD to validate external endpoint is reachable
  ├─ 5. DB insert — AgentRecord created with all fields
  ├─ 6. Audit log — AuditService.log(action=register, agent_name, caller_identity, caller_ip)
  ├─ 7. Auto-create run policy — default AgentRunPolicy (retry + circuit breaker config)
  └─ 8. Version snapshot — AgentVersion record (immutable history)
```

### Three Runtime Types

| Type | What Happens | Who Runs It |
|------|-------------|-------------|
| **managed** | Platform runs via LangGraph. You provide system prompt + tool selection. | Platform (`core/runtime/executor.py`) |
| **external** | Platform proxies chat to your server. You provide endpoint URL + field mapping. | Your server (n8n, LangServe, VITR, any HTTP API) |
| **built_in** | Platform's own pre-built agents (tax-lookup, doc-qa, github-search). | Platform (`core/example_agents/`) |

### Key Fields on AgentRecord

**Source:** `core/agent_registry/models.py`

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `name` | String(128) | required | URL-safe slug (e.g. `tax-rate-helper`) |
| `endpoint` | String(512) | required | Agent URL (auto-set for managed; your URL for external) |
| `version` | String(32) | `"1.0.0"` | Semver string |
| `runtime_type` | String(32) | `"external"` | `managed` / `external` / `built_in` |
| `managed_config` | JSON | `null` | For managed: `{system_prompt, mcp_servers, model, max_iterations}` |
| `endpoint_config` | JSON | `null` | For external: `{chat_path, request_map, response_field, response_mode, ...}` |
| `model` | String(64) | `null` | LLM model name |
| `mcp_servers` | ARRAY(String) | `[]` | MCP servers this agent uses |
| `tenant_id` | String(128) | `null` | Tenant isolation key |
| `published` | Boolean | `False` | Customers only see published agents |
| `is_public` | Boolean | `False` | Visible to all tenants |
| `autonomy_level` | String(16) | `"L1"` | L1–L5 autonomy tier |
| `total_sessions` | Integer | `0` | Lifetime session count (updated per chat) |
| `success_rate` | Float | `null` | Rolling success rate (last 100 runs) |
| `avg_response_ms` | Integer | `null` | Rolling average latency |
| `certification_status` | String(32) | `null` | `pending` / `certified` / `failed` / `expired` |
| `status` | String(32) | `"active"` | Agent lifecycle status |
| `category` | String(64) | `null` | Tax & Compliance, Finance, Custom, etc. |
| `tags` | ARRAY(String) | `[]` | Searchable tags |

### External Agent `endpoint_config` Fields

| Field | Default | Description |
|-------|---------|-------------|
| `chat_path` | `/chat` | Path appended to endpoint URL |
| `request_map` | `{}` | Field name mapping (e.g. `{"message": "Prompt"}` for n8n) |
| `response_field` | `"response"` | JSON key containing the answer |
| `response_mode` | `"json"` | `"json"` or `"sse"` |
| `sse_answer_event` | `"answer"` | SSE event type to extract (SSE mode only) |
| `timeout_seconds` | `60` | HTTP timeout |
| `extra_request_fields` | `{}` | Static fields merged into every request |

### Related Tables

- **AgentVersion** — immutable snapshot on every PATCH (full history)
- **AgentChangeLog** — per-field changelog (who changed what, when)
- **AgentScorecard** — 5-dimension scoreboard (accuracy, safety, latency, cost, governance)
- **AgentPromotion** — environment promotion tracking (dev → staging → prod)

---

## Stage 2: GOVERN — Governance Configuration

After registration, governance is applied through 6 modules. Each can be configured per-agent via API.

### 2A. Policy Rules (allow / deny / require_approval)

**API:** `POST /api/v1/policy/rules`
**Source:** `core/policy/engine.py`

```
Policy decisions: allow | deny | require_approval

When checked:
  ✅ Chat gate — before any execution starts (routes.py L1073)
  ✅ Tool filter — before LangGraph runs, denied tools removed (executor.py L149–167)
```

### 2B. PII Protection

**API:** `POST /api/v1/pii/patterns`
**Source:** `core/pii/service.py`

```
Built-in patterns: SSN, email, phone, credit_card, IP address

When enforced:
  ✅ BEFORE sending to agent — tokenize(user_message) → PII replaced with [PII_TOKEN_xxx]
  ✅ AFTER agent responds — detokenize(response) → tokens restored to original values
  ✅ Applied to ALL three runtime types (managed, external, built_in)
```

### 2C. Autonomy Levels (L1–L5)

**API:** `PATCH /api/v1/agents/{id}` with `autonomy_level`
**Source:** `core/autonomy/service.py`

| Level | Name | System Prompt Suffix |
|-------|------|---------------------|
| L1 | Full Human Control | "You are in advisory mode. Always present options, never act autonomously." |
| L2 | Human Confirms | "You are in augmented mode. Suggest actions, wait for confirmation." |
| L3 | Human Audits | "You are in supervised mode. Take action, but log everything for review." |
| L4 | AI Decides, Human Monitors | "You are in monitored mode. Act independently, flag edge cases." |
| L5 | Full Autonomy | "You are in policy-governed mode. Act independently within policy bounds." |

```
When enforced:
  ✅ Managed agents — autonomy suffix injected into system prompt (executor.py L99–102)
  ❌ External agents — autonomy level stored but NOT enforced on your server
```

### 2D. Budget & Kill-Switch

**API:** `POST /api/v1/finops/budgets`
**Source:** `core/finops/service.py`

```
Set monthly budget cap per agent. Kill-switch auto-triggers when exceeded.

When enforced:
  ✅ Chat gate — blocks execution if agent is killed (routes.py L1058)
  ✅ Executor — blocks managed agents if budget exceeded (executor.py L110)
  ✅ Post-execution — spend recorded, kill-switch triggered if breach (routes.py L1340)
```

### 2E. HITL (Human-in-the-Loop)

**API:** `POST /api/v1/hitl/requests`
**Source:** `core/hitl/service.py`

```
When policy returns "require_approval":
  ✅ Execution pauses immediately
  ✅ HITL request created with pending status
  ✅ Agent returns "⏸ Action requires human approval. Request ID: {id}"
  ✅ Admin approves/denies via PATCH /api/v1/hitl/requests/{id}
```

### 2F. Tax Guardrails

**API:** `POST /api/v1/guardrails/tax`
**Source:** `core/guardrails/service.py`

```
Cross-checks LLM tax claims against Babelfish/GoRules ground truth.

When checked:
  ✅ After every agent response — guardrails.check(response_text)
  ⚠️ Non-blocking — logs warning if flagged, does NOT block response
```

---

## Stage 3: PUBLISH — Making Agents Visible

### Publishing Model

```
                        ┌─────────────────┐
                        │  Agent Created   │
                        │  published=false │
                        └────────┬────────┘
                                 │
                    Only admin/maintainer can toggle
                                 │
                        ┌────────▼────────┐
                        │  published=true  │──── Visible to customers
                        └────────┬────────┘
                                 │
                    is_public=true (optional)
                                 │
                        ┌────────▼────────┐
                        │  Cross-tenant   │──── Visible to ALL tenants
                        └─────────────────┘
```

### Visibility Rules

| Role | Sees What |
|------|-----------|
| **Admin / Maintainer** | ALL agents (published + unpublished, all tenants) |
| **Developer** | Own tenant agents + published public agents |
| **Customer** | Only `published=true` agents scoped to their tenant + `is_public=true` agents |

### How to Publish

**Endpoint:** `PATCH /api/v1/agents/{id}` with `{"published": true}`
**Auth:** Admin or Maintainer only

```python
# core/agent_registry/routes.py — publish gating
if "published" in update_data and caller.role not in (Role.admin, Role.maintainer):
    raise HTTPException(403, "Only admin or maintainer can publish/unpublish agents")
```

### Pre-Built Agent Seeding

Pre-built agents (`tax-rate-lookup`, `document-qa`, `github-pr-reviewer`) are seeded with
`published=true` and `is_public=true` on:
- Platform startup
- Partner signup (scoped to new tenant)

---

## Stage 4: EXECUTE — Agent Chat (8-Step Pipeline)

**Route:** `POST /api/v1/agents/{agent_name}/chat`
**Source:** `core/agent_registry/routes.py` L1012
**Auth:** Any authenticated user (`AuthDep`)

### Full Execution Flow

```
User sends: POST /api/v1/agents/my-agent/chat {"message": "What is PA tax rate?"}
```

#### Step 1: Kill-Switch Check (L1058–1070)
```
BudgetService.check(agent_name)
  → If killed: HTTP 403 "Agent is killed by budget policy"
  → Non-blocking on error: logs warning, proceeds
```

#### Step 2: Policy Check (L1073–1093)
```
PolicyService.check(agent_name, action="chat", caller, tenant_id)
  → allow: proceed to Step 4
  → deny: HTTP 403 "Policy denied"
  → require_approval: proceed to Step 3
  → Non-blocking on error: defaults to "allow"
```

#### Step 3: HITL Gate (L1096–1127)
```
If policy_decision == "require_approval":
  HITLService.create_request(agent_name, tool="chat", caller, reason)
  → Returns immediately: "⏸ Action requires human approval. Request ID: {uuid}"
  → Admin approves via PATCH /api/v1/hitl/requests/{id}
```

#### Step 4: Session Start (L1130–1143)
```
SessionService.start_session(agent_name, session_id, model, metadata)
  → Creates SessionRecord in DB
  → metadata: {runtime_type, tenant_id}
```

#### Step 5: Execute Agent — branches on runtime_type

**A) Managed** (`runtime_type="managed"`) — `core/runtime/executor.py` L63

```
execute_managed_agent(agent, message, session_id, tenant_id, db)

Internal pipeline:
  a. Autonomy suffix injection — L1-L5 appended to system prompt (L99)
  b. PII tokenize — scrub SSN/email/phone/CC from input (L105)
  c. Budget kill-switch check — second gate inside executor (L110)
  d. Load MCP tools — MultiServerMCPClient from mcp_servers config (L126)
  e. Policy filter on tools — check each tool, remove denied ones (L149–167)
  f. LangGraph create_react_agent() → ainvoke() with timeout (L174–200)
  g. Guardrails check — tax hallucination validation on output (L250)
  h. PII detokenize — restore tokens in response (L263)
```

**B) External** (`runtime_type="external"`) — `routes.py` L1169–1286

```
  a. PII tokenize user message (L1179)
  b. Read endpoint_config (chat_path, request_map, response_field, response_mode)
  c. Build URL: agent.endpoint + chat_path
  d. Map fields: {"message": msg} → {"Prompt": msg} (per request_map)
  e. Merge extra_request_fields into payload
  f. Send POST to external server:
     - JSON mode (L1239): Standard POST/response, extract response_field
     - SSE mode (L1191): Stream line-by-line, handle event types
       (answer, status, citations, done, error)
  g. PII detokenize response (L1282)
  h. Guardrails check on output (L1283)
```

**C) Built-In** (`runtime_type="built_in"`) — `routes.py` L1151–1167

```
  a. PII tokenize user message (L1154)
  b. Internal HTTP to 127.0.0.1:8080/examples/{agent_name}/chat
  c. PII detokenize response (L1162)
  d. Guardrails check on output (L1164)
```

#### Step 6: Session End (L1315–1330)
```
SessionService.update_session(session_id, {
  status: "completed" | "failed",
  tool_calls: count,
  total_cost_usd: calculated,
  model, input_tokens, output_tokens,
  error_message (if failed)
})
```

#### Step 7: Spend Recording (L1335–1348)
```
BudgetService.record_spend(agent_name, model, cost_usd, run_id, source)
  → Auto-triggers kill-switch if monthly budget exceeded
  → Non-blocking on error
```

#### Step 8: Agent Stats Update (L1351–1370)
```
agent.total_sessions += 1
agent.avg_response_ms = rolling average (weighted by history)
agent.success_rate = rolling success rate (last 100 runs, decay-weighted)
  → Makes Overview KPI cards real
  → Non-blocking on error
```

### Chat Response Schema

```json
{
  "agent": "vertex-transaction-analyst",
  "response": "## Executive Summary\n...",
  "tools_called": ["list_report_definition", "get_report_result"],
  "tool_details": [{"tool": "...", "latency_ms": 120, "policy_decision": "allow"}],
  "model": "gpt-5-mini",
  "latency_ms": 17348,
  "cost_usd": 0.042,
  "session_id": "bd910e87-...",
  "autonomy_level": "L2",
  "policies_passed": true,
  "runtime_type": "external",
  "tenant_id": "partner-abc123",
  "error": null,
  "tools_loaded": 5,
  "tools_requested": 3
}
```

---

## Stage 5: OBSERVE — Telemetry & Monitoring

### Session Tracking

**Source:** `core/sessions/service.py`

Every chat creates a `SessionRecord` with:
- `session_id`, `agent_name`, `status` (active → completed / failed)
- `tool_calls` count, `total_cost_usd`, `model`
- `input_tokens`, `output_tokens`
- `started_at`, `ended_at`, `error_message`
- `metadata` (runtime_type, tenant_id)

**API:** `GET /api/v1/sessions/` — query by agent, date range, status

### Agent Stats (Live KPIs)

Updated after every chat in Step 8:
- `total_sessions` — lifetime count
- `success_rate` — decay-weighted rolling average (last ~100 runs)
- `avg_response_ms` — weighted rolling average latency

These power the **Overview dashboard** KPI cards in the portal.

### OpenTelemetry

**Source:** `core/observability/setup.py`

Triple-exporter sends spans to:
1. **OTel Collector** (OTLP endpoint) → fan-out to any backend
2. **Langtrace** (LLM-specific tracing) → gen_ai spans
3. **Datadog** (APM + infra metrics) → APM traces

Auto-instrumented:
- FastAPI routes (all HTTP requests)
- httpx outbound calls (external agent proxying, MCP tool calls)
- SQLAlchemy DB queries

### FinOps Spend Tracking

**Source:** `core/finops/service.py`

Every execution records:
- `agent_name`, `model`, `cost_usd`, `run_id`, `source`
- Aggregated per agent per month
- Budget utilization visible in FinOps portal tab

Cost calculation uses per-model pricing from `models.yaml` (real tokens when available, time-based estimate as fallback).

---

## Stage 6: CONTROL — Runtime Management

### Kill-Switch

**API:** `POST /api/v1/finops/kill-switch/{agent_name}` (AdminDep)
**API:** `POST /api/v1/finops/kill-switch/{agent_name}/reset` (AdminDep)

Two triggers:
- **Manual:** Admin kills agent via API or portal
- **Automatic:** Budget exceeded → `is_killed=true`

Effect: All subsequent chat requests return HTTP 403 immediately.

### Circuit Breaker

**Source:** `core/agent_control/service.py`

States: `CLOSED` → `OPEN` → `HALF_OPEN` → `CLOSED`

| State | Meaning |
|-------|---------|
| CLOSED | Normal operation — all requests pass |
| OPEN | Too many failures — all requests blocked |
| HALF_OPEN | Allow one test request to check recovery |

**API:**
- `POST /api/v1/control/policies/{agent_name}/circuit/reset` → force CLOSED
- `POST /api/v1/control/policies/{agent_name}/circuit/open` → force OPEN (AdminDep)

### Dead Letter Queue

Failed executions are captured in the DLQ for replay:

- `GET /api/v1/control/dead-letters?agent_name=X` — list failed runs
- `GET /api/v1/control/dead-letters/{id}` — inspect failed run details
- `POST /api/v1/control/dead-letters/{id}/replay` — retry failed run

### Schedules

**API:** `POST /api/v1/control/schedules` — create cron / interval / once schedules for agents

---

## Stage 7: RETIRE — Agent Decommission

### Deletion

**API:** `DELETE /api/v1/agents/{id}` (owner or admin)

### Unpublish (Soft Retire)

**API:** `PATCH /api/v1/agents/{id}` with `{"published": false}`

Effect: Agent hidden from customers but still exists in registry. Admin/developer can still see and use it.

### Version History

Every PATCH creates an `AgentVersion` snapshot + `AgentChangeLog` entry. Full history preserved.

---

## Governance Enforcement Matrix

| Governance Layer | Managed | External | Built-In | When |
|-----------------|---------|----------|----------|------|
| **Kill-switch** | ✅ | ✅ | ✅ | Before execution (Step 1) |
| **Policy gate** (allow/deny/require_approval) | ✅ | ✅ | ✅ | Before execution (Step 2) |
| **HITL approval** | ✅ | ✅ | ✅ | Before execution (Step 3, if policy=require_approval) |
| **PII tokenize** (input) | ✅ | ✅ | ✅ | Before sending to agent (Step 5) |
| **PII detokenize** (output) | ✅ | ✅ | ✅ | After agent responds (Step 5) |
| **Autonomy injection** (L1–L5) | ✅ | ❌ | ❌ | System prompt modification (managed only) |
| **Policy tool filter** | ✅ | ❌ | ❌ | Before LangGraph runs (managed only) |
| **Tax guardrails** | ✅ | ✅ | ✅ | After response, non-blocking (Step 5) |
| **Budget/spend tracking** | ✅ | ✅ | ✅ | After execution (Step 7) |
| **Session recording** | ✅ | ✅ | ✅ | Start + end of execution (Steps 4 & 6) |
| **Agent stats update** | ✅ | ✅ | ✅ | After execution (Step 8) |

---

## Portal UI — Where Each Stage Lives

| Lifecycle Stage | Portal Tab | What You See |
|----------------|-----------|-------------|
| **Create** | Builder (managed + external) / My Agents (quick create) | Runtime toggle, endpoint config, MCP tools, presets (n8n/LangServe/VITR) |
| **Govern** | Control (circuit breaker, DLQ) / FinOps (budgets, kill-switch) | Budget cards, kill-switch toggle, schedule table |
| **Publish** | Agents (admin view) | Published toggle per agent (admin/maintainer only) |
| **Execute** | Agent Console (slide-over chat) | Chat bubbles, governance bar, tool call cards |
| **Observe** | Overview (KPIs) / FinOps (spend charts) | Total sessions, success rate, avg latency, cost |
| **Control** | Control tab | Circuit breaker status, DLQ count, schedules |
| **Retire** | Agents (admin view) | Delete button, unpublish toggle |

---

## Partner / Customer Signup Flow

**Endpoint:** `POST /api/v1/partners/signup`
**Source:** `core/partner/service.py` L33–95

```
Customer visits portal → clicks "Sign Up"
  │
  ├─ 1. Check duplicate email → 409 if exists
  ├─ 2. Generate tenant_id → "partner-{12-char-hex}"
  ├─ 3. Create API key (role=customer, expires=365d, scopes=[partner, tenant_id])
  ├─ 4. Create PartnerAccount in DB
  ├─ 5. Seed pre-built agents scoped to tenant:
  │       - {tenant_id}-tax-rate-lookup  (built_in, published=true)
  │       - {tenant_id}-document-qa      (built_in, published=true)
  │       - {tenant_id}-github-search    (built_in, published=true)
  ├─ 6. Send notification to admins (type=partner_signup)
  └─ Response: { tenant_id, api_key (shown once!), key_expires_at }
       │
       └─ Customer auto-logged in → sees My Agents tab with 3 pre-built agents
```

---

## What's Real vs Synthetic — Honest Audit

> Verified against codebase June 5, 2026. Don't assume — check the code.

### ✅ Fully Functional (wired into execution path)

| Feature | Evidence |
|---------|----------|
| Agent registration + endpoint probing | `routes.py` L65–120 |
| Kill-switch enforcement (budget) | `routes.py` L1058 + `executor.py` L110 |
| Policy gate checks (allow/deny/require_approval) | `routes.py` L1073 |
| Policy tool filtering (managed only) | `executor.py` L149–167 |
| HITL approval gates | `routes.py` L1096–1127 |
| PII tokenization / detokenization (all 3 runtimes) | `executor.py` L105 + `routes.py` L1154, L1179, L1282 |
| Tax guardrails check (non-blocking) | `executor.py` L250 + `routes.py` L1164, L1283 |
| Autonomy suffix injection (managed only) | `executor.py` L99–102 |
| Session recording (start + end) | `routes.py` L1130, L1315 |
| Spend recording + auto kill-switch | `routes.py` L1335 |
| Agent stats (total_sessions, success_rate, avg_response_ms) | `routes.py` L1351 |
| External agent proxy (JSON + SSE modes) | `routes.py` L1169–1286 |
| Managed agent execution (LangGraph + MCP tools) | `executor.py` L63–300 |
| OTel telemetry emission (triple exporter) | `observability/setup.py` |
| Published field filtering (customers see published only) | `routes.py` L224 + `service.py` L171 |
| Partner signup (tenant + API key + agent seeding) | `partner/service.py` L33–95 |
| Audit logging (register, update, delete) | `routes.py` L91–104 |

### ⚠️ Partially Functional

| Feature | What Works | What Doesn't |
|---------|------------|--------------|
| Policy enforcement | Gate-level check works | Per-call enforcement during tool execution incomplete |
| Autonomy | System prompt injection works | Tool restrictions per autonomy level NOT enforced |

### ❌ Shelf-Ware (built but never called during execution)

| Feature | Location | Status |
|---------|----------|--------|
| PolicyRule table (per-agent rules) | `core/policy/models.py` | API exists, executor uses default ALLOW |
| ToolPolicy table | `core/autonomy/models.py` | API exists, executor never consults it |
| PolicyDecisionLog | `core/policy/models.py` | Table exists, executor never writes to it |
| Data classification enforcement | `core/mcp_registry/models.py` | Tools tagged PII/Confidential, executor never checks tags |
| HITL polling during runtime | `core/hitl/models.py` | Only at chat gate, not during multi-step execution |
| Custom OTel spans for LLM/tool calls | `core/observability/setup.py` | `get_tracer()` available, not used in executor |

---

## Quick Reference — Key Files

| Area | File | Key Functions |
|------|------|---------------|
| Agent registration | `core/agent_registry/routes.py` L65 | `register_agent()` |
| Agent model | `core/agent_registry/models.py` | `AgentRecord`, `AgentVersion`, `AgentChangeLog` |
| Chat endpoint (8-step) | `core/agent_registry/routes.py` L1012 | `agent_chat()` |
| Managed executor | `core/runtime/executor.py` L63 | `execute_managed_agent()` |
| External proxy | `core/agent_registry/routes.py` L1169 | Inline in `agent_chat()` |
| Policy engine | `core/policy/engine.py` | `PolicyService.check()` |
| PII tokenizer | `core/pii/service.py` | `PIIService.tokenize()`, `.detokenize()` |
| Tax guardrails | `core/guardrails/service.py` | `TaxGuardrailService.check()` |
| Autonomy | `core/autonomy/service.py` | `AutonomyService.get_level_prompt()` |
| HITL | `core/hitl/service.py` | `HITLService.create_request()` |
| FinOps / Kill-switch | `core/finops/service.py` | `BudgetService.check()`, `.record_spend()` |
| Sessions | `core/sessions/service.py` | `SessionService.start_session()`, `.update_session()` |
| Partner signup | `core/partner/service.py` L33 | `PartnerService.signup()` |
| Observability | `core/observability/setup.py` | `setup_telemetry()` |
| Agent control | `core/agent_control/service.py` | Circuit breaker, DLQ, schedules |
| Governance wrappers | `core/runtime/governance.py` | `tokenize_pii()`, `detokenize_pii()`, `check_guardrails()`, `inject_autonomy()`, `probe_endpoint()` |

---

## Appendix: Production Readiness Standards (10 Governance Standards)

> **Source of truth:** `AI-Agent-Readiness.docx` — the 10 standards every Vertex-branded agent must
> meet. Codified in `core/platform_generate/schemas.py` (YAML schema) and
> `core/platform_generate/readiness.py` (scorecard logic).

### The model

```
AI-Agent-Readiness.docx  (the 10 standards — human policy)
        │  codified as
        ▼
1. DECLARE   governance: block in agent-definition.yaml        ← every agent ships this
        │
        ▼
2. ADMIT     Production Readiness Scorecard (BUILT today)       ← generate-time gate
             core/platform_generate/readiness.py
        │
        ▼
3. ENFORCE   OPA / Rego policy at the runtime PEP (PLANNED)     ← per-call enforcement
```

Two field types per standard:
- **Enforceable** (#2,3,5,6,7,8,9) — a *control* the agent declares (booleans/enums). Can be checked on every call.
- **Attestable** (#1,4,10) — *human-signed evidence* (text). Checked at admission; blocks promotion until present.

### Standards Summary

| # | Standard | Type | Key YAML Fields |
|---|----------|------|-----------------|
| 1 | Outcome & Workflow Ownership | Attestable | `outcome.{workflow, success_metric, recovery_plan}` |
| 2 | Autonomy Boundaries | Enforceable | `autonomy.{tier, kill_switch, low_confidence_behavior}` |
| 3 | Access & Permission Model | Enforceable | `access.{least_privilege, tenant_scoped, default_access}` |
| 4 | Release Readiness Gates | Attestable | `release.{checklist_url, incident_owner, sla, exec_sponsor}` |
| 5 | Traceability & Auditability | Enforceable | `traceability.{deterministic_replay, trace_redaction}` |
| 6 | Observability & Runtime Visibility | Enforceable | `observability.{dashboards, alerts, telemetry_baseline}` |
| 7 | Reliability & Failure Handling | Enforceable | `reliability.{idempotent, retry, resumable}` |
| 8 | IP Protection (external only) | Enforceable | `ip_protection.redaction_by_default` |
| 9 | Rollout & Change Control (external only) | Enforceable | `rollout.{feature_flags, versioned_configs, instant_rollback}` |
| 10 | Monetization & Certification (external only) | Attestable | `monetization.{pricing_model, value_attribution, certification}` |

### Status
- ✅ **DECLARE + ADMIT built**: governance YAML schema + Production Readiness Scorecard + generate-time gate
- ⏳ **ENFORCE (OPA runtime)**: the PEP + OPA bundle are not wired yet. Controls are *declared and admission-checked*, not *prevented live*.
