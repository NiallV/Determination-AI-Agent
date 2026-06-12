# Vertex Agentic OS — Executive Brief

> **The enterprise operating system for AI agents.**  
> Build, govern, deploy, and monetize AI agents in minutes — not months.

---

## What Is Vertex Agentic OS?

Vertex Agentic OS is the **control plane for every AI agent** across Vertex Inc. It provides the infrastructure layer that lets teams **build, deploy, govern, and sell** AI agents while maintaining enterprise-grade security, compliance, and multi-tenant isolation.

**One-line pitch:**  
> *"Kubernetes manages containers. We manage agents. Registry, governance, security, collaboration — bring your own runtime."*

**Think of it as:**  
- The **npm registry** for AI tools (discover & share)
- The **Kubernetes RBAC** for AI agents (who can do what)
- The **App Store** for enterprise AI (build, certify, sell)

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     VERTEX AGENTIC OS                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │              DEVELOPER PORTAL (Next.js)                             │ │
│  │   Overview • Agents • Chat • Governance • FinOps • Marketplace     │ │
│  │   Prompts • Tenants • Servers • Control Center                     │ │
│  └───────────────────────────────┬────────────────────────────────────┘ │
│                                  │ REST API (277 endpoints)             │
│  ┌───────────────────────────────▼────────────────────────────────────┐ │
│  │              PLATFORM CORE (FastAPI, 26 Routers)                    │ │
│  │                                                                     │ │
│  │  ┌─────────────┐ ┌──────────────┐ ┌────────────────┐              │ │
│  │  │   AGENT     │ │     MCP      │ │   GOVERNANCE   │              │ │
│  │  │  REGISTRY   │ │   REGISTRY   │ │    ENGINE      │              │ │
│  │  │ (A2A, Chat, │ │ (Discovery,  │ │ (Policy, PII,  │              │ │
│  │  │  Lifecycle) │ │  Health, SSE)│ │  Autonomy,HITL)│              │ │
│  │  └──────┬──────┘ └──────┬───────┘ └───────┬────────┘              │ │
│  │         │               │                  │                        │ │
│  │  ┌──────▼───────────────▼──────────────────▼────────────────────┐  │ │
│  │  │            RUNTIME EXECUTOR (LangGraph)                       │  │ │
│  │  │  12-step governance pipeline enforced on every agent run      │  │ │
│  │  │  Input scan → PII → Policy → HITL → LLM → Guardrails → Out  │  │ │
│  │  └──────────────────────────────────────────────────────────────┘  │ │
│  │                                                                     │ │
│  │  ┌─────────┐ ┌────────┐ ┌────────┐ ┌──────────┐ ┌──────────────┐ │ │
│  │  │ FinOps  │ │Security│ │ Memory │ │  Events  │ │ Eval Pipeline│ │ │
│  │  │ Budget, │ │Prompt  │ │Semantic│ │ Triggers │ │ Golden sets, │ │ │
│  │  │ Spend,  │ │Guard,  │ │Cross-  │ │ Webhooks │ │ SLA, CI/CD   │ │ │
│  │  │Kill-sw  │ │Content │ │Agent   │ │ Chains   │ │              │ │ │
│  │  └─────────┘ └────────┘ └────────┘ └──────────┘ └──────────────┘ │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                  │ MCP SSE Protocol                     │
│  ┌───────────────────────────────▼────────────────────────────────────┐ │
│  │              MCP DOMAIN SERVER LIBRARY                              │ │
│  │                                                                     │ │
│  │  ┌──────────┐ ┌─────────┐ ┌─────────┐ ┌────────┐ ┌────────────┐ │ │
│  │  │Babelfish │ │GoRules  │ │O-Series │ │Document│ │ Confluence │ │ │
│  │  │Postgres  │ │Engine   │ │Cloud    │ │RAG     │ │ + GitHub   │ │ │
│  │  │(Tax DB)  │ │(Rules)  │ │(Tax Eng)│ │(Vector)│ │            │ │ │
│  │  │ :8002    │ │ :8003   │ │ :8001   │ │ :8006  │ │ :8004/8005 │ │ │
│  │  └──────────┘ └─────────┘ └─────────┘ └────────┘ └────────────┘ │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │              OBSERVABILITY LAYER                                    │ │
│  │  OpenTelemetry → Langfuse (LLM Tracing) + Datadog + Azure Monitor │ │
│  │  Per-agent spans, W3C propagation, cost attribution, SLA tracking  │ │
│  └────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘

        ┌──────────────────────────────────────────────────┐
        │           EXTERNAL SYSTEMS (via A2A)              │
        │  O-Series Tax Engine • Babelfish DB • GoRules    │
        │  Confluence • GitHub • SAP • Salesforce • ERPs   │
        │  Any system with REST/MCP/A2A endpoint           │
        └──────────────────────────────────────────────────┘
```

---

## The 10 Core Pillars

### 1. 🏗️ Agent Builder — Create Agents in Minutes

| Feature | Description |
|---------|-------------|
| **3-Click Creation** | Name + system prompt + model → live agent in seconds |
| **Scaffold Generator** | 18-file ZIP: code, tests, CI/CD, governance, deployment configs |
| **Framework-Agnostic** | LangGraph, LangChain, CrewAI, Foundry — bring your own runtime |
| **MCP Tool Selection** | Browse 7+ domain tools, drag into agent config |
| **System Prompt Editor** | Write, version, A/B test prompts — rollback in one click |

**Flow:** Portal → Agent Builder Wizard → Select Tools → Write Prompt → Deploy → Live in < 5 minutes

---

### 2. 🚀 Agent Deployer — From Code to Production

| Feature | Description |
|---------|-------------|
| **Deploy Intelligence** | Auto-recommends infrastructure (ACA vs AKS, PG vs Cosmos) |
| **VIPER CI/CD Pipeline** | GitHub Actions → Snyk → SonarQube → Docker → ArgoCD → K8s |
| **Lifecycle Management** | Draft → Staging → Production pipeline with version pinning |
| **Shadow Deployment** | Canary/shadow mode before full production rollout |
| **Instant Rollback** | One-click rollback to any previous version |

**Flow:** `git push` → VIPER fires → Tests → Build → ArgoCD deploys → Live on K8s

---

### 3. 🛡️ Governance Engine — Policy-as-Code at Runtime

**12-step governance pipeline enforced on EVERY agent execution:**

```
User Message → Input Scan → Kill-Switch → Policy Check → HITL Gate →
Autonomy Inject → Prompt Anchor → PII Tokenize → Tool Filter →
LLM Execute → Hallucination Check → Content Safety → PII De-tokenize →
Response (with governance_score, anomaly_score, audit trail)
```

| Feature | Description |
|---------|-------------|
| **22-Check Compliance Engine** | Auto-scores every agent on register/update/chat |
| **Fleet Dashboard** | Donut gauge, grade distribution (A+ → F), risk heatmap |
| **Per-Agent Drill-Down** | Full compliance breakdown with remediation hints |
| **Fail-Closed Mode** | Errors block requests (not silently pass) for production tenants |
| **Governance Grades** | A+ (94%) → F (28%) — visual compliance at a glance |

---

### 4. 🔐 Security Layer — Enterprise-Grade Protection

| Layer | What It Does |
|-------|-------------|
| **Prompt Injection Defense** | Detects role-override, jailbreak, delimiter attacks |
| **System Prompt Anchoring** | XML delimiters prevent injection override |
| **PII Tokenization** | Scrubs SSN, email, phone, CC, IBAN before LLM sees input |
| **Content Safety Scanner** | Detects credential leaks, code injection, excessive agency |
| **Tool Output Sanitization** | Redacts credentials from tool responses |
| **Memory Sanitization** | Cleans context before prompt injection via memory |
| **L1 Hard Enforcement** | L1 agents have ALL tools physically removed (not just prompted) |

---

### 5. 🤝 A2A Agent Collaboration — Agents Talk to Agents

| Feature | Description |
|---------|-------------|
| **Capability Discovery** | Find agents by skills, cost, latency SLA |
| **Task Delegation** | Agent A delegates sub-task to Agent B (cross-framework) |
| **Multi-Agent Orchestration** | Pipeline, fan-out, and supervisor patterns |
| **Google A2A Protocol** | `/.well-known/agent-card` — standards-based federation |
| **Cross-ERP Communication** | Tax agent → O-Series, Compliance agent → Babelfish |

**Key Differentiator:** Works across LangGraph ↔ Foundry ↔ CrewAI — framework-neutral delegation. No walled garden.

---

### 6. 📋 Onboarding & Marketplace — Build Once, Sell Everywhere

| Step | Action |
|------|--------|
| 1. **Create** | Engineer builds agent (Builder wizard or SDK) |
| 2. **Configure** | Set tools, model, autonomy level, budget |
| 3. **Certify** | Platform runs 22-check compliance eval |
| 4. **Publish** | Admin publishes to marketplace (one-click) |
| 5. **Subscribe** | Tenant discovers → subscribes → agent cloned into their namespace |
| 6. **Govern** | Tenant's policy rules, autonomy level, budget caps applied |
| 7. **Monitor** | Real-time success rate, cost, anomaly detection |

**Marketplace features:** Reviews, ratings, one-click deploy-to-tenant, config overrides, subscription budgets.

---

### 7. ✋ Approval & HITL — Humans Stay in the Loop

| Feature | Description |
|---------|-------------|
| **Policy-Driven Approvals** | `require_approval` rule → agent pauses mid-execution |
| **Risk-Level Classification** | Read / Write / Destructive — different approval workflows |
| **Confidence Scoring** | Low-confidence actions auto-escalate |
| **Approval Queue** | Portal shows pending requests by agent, wait time, risk |
| **Approve/Deny + Reason** | Full audit trail of who approved what and why |
| **Timeout + Auto-Deny** | Requests expire after configurable timeout |

---

### 8. ⚙️ Solid Control Center — Operational Excellence

| Component | What It Does |
|-----------|-------------|
| **Schedules** | Cron/interval/once execution of agent tasks |
| **Circuit Breaker** | Auto-trips after N failures → prevents cascade |
| **Dead-Letter Queue** | Failed runs captured, inspectable, replayable |
| **Kill-Switch** | Budget exceeded → agent killed immediately |
| **Auto-Healing** | SLA breach → auto-rollback to last stable version |
| **Rate Limiting** | 30 writes/min per agent, per-tenant quotas |
| **Event Log** | Every lifecycle event tracked and queryable |

---

### 9. 📊 Telemetry & Observability — See Everything

| Feature | Description |
|---------|-------------|
| **Triple Exporter** | Langtrace (LLM tracing) + Datadog (APM) + OTLP (fan-out) |
| **Per-Agent Spans** | Every chat run traced: LLM calls, tool calls, governance steps |
| **W3C Propagation** | Trace IDs flow downstream to O-Series, Babelfish, etc. |
| **Langfuse Callbacks** | Full prompt/response/cost captured per LLM call |
| **Agent Cards Show** | Sessions, success rate, avg latency, cost, anomaly score |
| **FinOps Dashboard** | Per-agent + per-tenant spend, model cost breakdown |
| **Anomaly Detection** | Rate-burst scoring (0.0→1.0), auto-escalation |

---

### 10. 🏢 Multi-Tenant Isolation — Every Customer Gets Their Own World

| Feature | Description |
|---------|-------------|
| **Tenant Scoping** | Agents, policies, budgets, sessions — all isolated per tenant |
| **Cross-Tenant Prevention** | Tenant A cannot see/access Tenant B's data (verified 27/27 tests) |
| **Per-Tenant Policy Rules** | Same agent, different governance per customer |
| **Per-Tenant Autonomy** | Acme gets L3, Globex gets L1 — same agent, different trust |
| **Tenant Budgets** | Monthly caps per tenant, auto-kill on exceed |
| **Publish-to-Tenant** | Admin pushes platform agents to specific customers |
| **Partner Self-Signup** | Automated onboarding: tenant + API key + pre-built agents |

---

## The L1-L5 Autonomy Ladder — Our Unique Differentiator

```
     ┌─────────────────────────────────────────────────────────┐
     │                    AUTONOMY LEVELS                        │
     │                                                          │
     │  L1 ████░░░░░░  Assist     — Advisory only, NO tools     │
     │  L2 ██████░░░░  Augment    — Tools need human approval   │
     │  L3 ████████░░  Supervised — Auto-execute, sample review │
     │  L4 █████████░  Monitored  — Independent, anomaly-only   │
     │  L5 ██████████  Autonomous — Policy-governed, full auto  │
     │                                                          │
     │  As autonomy rises → governance intensifies              │
     │  Grade: A→F | Risk: low→critical | Anomaly: 0→1.0       │
     └─────────────────────────────────────────────────────────┘
```

**No competitor has this.** Salesforce has trust layers but no tiered tool removal. AWS Bedrock has guardrails but no autonomy levels. This is our #1 demo differentiator.

---

## MCP Registry — The Tool Discovery Engine

| Feature | Description |
|---------|-------------|
| **Register** | Any team registers their MCP server (SSE/Stdio) |
| **Discover** | Agents find tools by name, capability, tags, classification |
| **Health Monitor** | Continuous polling, schema drift detection |
| **Data Classification** | PII / Confidential / Internal tags enforce access rules |
| **Foundry Manifest** | Auto-generates Foundry-compatible tool manifest |
| **7 Domain Servers** | Babelfish (Tax DB), GoRules (Decision Tables), O-Series (Tax Engine), Document-RAG (Vectors), Confluence, GitHub, AI-Classifier |

---

## Persistent Chat — Conversation Memory That Works

| Feature | Description |
|---------|-------------|
| **Multi-Turn Context** | Full conversation history preserved across sessions |
| **Semantic Memory** | Cross-agent shared memories with similarity search |
| **Memory Scopes** | Agent-level, tenant-level, platform-level memories |
| **Smart Routing** | "Agentic OS" mode — auto-routes to the right agent |
| **File Upload** | PDF/TXT/CSV → chunk → embed → vector search → inject into chat |
| **@Mention Routing** | `@tax-agent look up PA rates` — direct routing |

---

## ERP & External System Integration

```
  Vertex Agentic OS
       │
       │ A2A Protocol + MCP SSE
       ▼
  ┌──────────────────────────────────────────────────┐
  │  CONNECTED SYSTEMS                                │
  │                                                   │
  │  Tax Systems:                                     │
  │    • O-Series Cloud (tax calculation engine)      │
  │    • Babelfish PostgreSQL (jurisdiction data)     │
  │    • GoRules Engine (tax decision tables)         │
  │                                                   │
  │  Enterprise Systems:                              │
  │    • Confluence (documentation, policies)         │
  │    • GitHub (code, PRs, issues)                   │
  │    • SAP / Oracle ERP (via MCP server)            │
  │    • Salesforce CRM (via MCP server)              │
  │                                                   │
  │  AI Systems:                                      │
  │    • Azure OpenAI (GPT-5, GPT-5-mini)             │
  │    • Anthropic Claude (Sonnet, Opus)              │
  │    • Google Gemini (via Vertex AI)                │
  │    • Local Ollama (air-gapped / llama3)           │
  │                                                   │
  │  Any system with REST/MCP/A2A endpoint            │
  │  → Register in MCP Registry → Instantly usable    │
  └──────────────────────────────────────────────────┘
```

**Key:** Any new ERP system becomes available to all agents by registering ONE MCP server. No per-agent integration work.

---

## Competitive Positioning — Where We're Ahead

### Feature Matrix

| Capability | Salesforce Agentforce | AWS Bedrock AgentCore | Google Vertex AI | PwC agentOS | **Vertex Agentic OS** |
|------------|:-----:|:-----:|:-----:|:-----:|:-----:|
| Tool registry (org-wide) | Partial | ✅ | Partial | ❌ | ✅ |
| Multi-tenant isolation | ✅ | ✅ | ✅ | Partial | ✅ |
| PII tokenization (pre-LLM) | ✅ | ✅ | ❌ | ❌ | ✅ |
| **L1-L5 Tiered autonomy** | ❌ | ❌ | ❌ | ❌ | **✅ UNIQUE** |
| A2A agent delegation | ✅ | ✅ | ✅ | ❌ | ✅ |
| Behavioral evals CI | ✅ | ✅ | ✅ | ❌ | ✅ |
| Agent marketplace | ✅ | ❌ | Partial | ❌ | ✅ |
| Prompt versioning + rollback | ✅ | ❌ | ❌ | ❌ | ✅ |
| Framework-neutral | ❌ (locked) | ❌ (locked) | ❌ (ADK) | ✅ | **✅** |
| Tax domain expertise | ❌ | ❌ | ❌ | ❌ | **✅ UNIQUE** |
| Governance-as-code (22 checks) | Partial | Partial | ❌ | ✅ | **✅** |
| Kill-switch + auto-kill | ❌ | ❌ | ❌ | ❌ | **✅ UNIQUE** |
| HITL approval workflow | Partial | Partial | ❌ | ❌ | ✅ |
| Publish-to-tenant | ✅ | ❌ | ❌ | ❌ | ✅ |
| Anomaly detection | ❌ | ❌ | ❌ | ❌ | **✅ UNIQUE** |

### Our 5 Unique Advantages

1. **L1-L5 Autonomy Ladder** — No competitor has tiered tool removal + progressive trust levels
2. **Tax Domain Expertise** — 7 tax-specific MCP servers (Babelfish, GoRules, O-Series) — zero competitors
3. **Framework-Neutral** — Works with ANY framework (LangGraph, CrewAI, Foundry, ADK). Big 3 lock you in.
4. **22-Check Governance Engine** — Real-time scoring, not just checkboxes. Auto-computes on every action.
5. **FinOps Kill-Switch** — Budget enforcement with auto-kill — prevents runaway AI spend

### Why We Win Against Each

| Competitor | Their Lock-In | Our Advantage |
|------------|---------------|---------------|
| **Salesforce** | Must use Salesforce ecosystem | We're framework-neutral + tax-specialized |
| **AWS Bedrock** | Must use AWS + Bedrock models | We route to ANY model (Azure, Anthropic, Gemini, local) |
| **Google Vertex** | Must use GCP + ADK | We're cloud-neutral, deployed on Azure + extensible |
| **PwC agentOS** | Consulting-heavy, no self-serve | We're self-serve platform, 5-minute agent creation |

---

## Create and Sell Agents in Minutes

```
                    THE AGENT ECONOMY

    ┌─────────────────────────────────────────────┐
    │                                             │
    │   ENGINEER (5 minutes)                      │
    │   ─────────────────────                     │
    │   1. Open Portal → Agent Builder            │
    │   2. Name: "PA Tax Rate Agent"              │
    │   3. Select tools: babelfish + gorules      │
    │   4. Write system prompt                    │
    │   5. Click Deploy → LIVE                    │
    │                                             │
    │   PLATFORM (automatic)                      │
    │   ────────────────────                      │
    │   • 22-check governance score computed      │
    │   • Budget created ($100/mo default)        │
    │   • OTel tracing wired                      │
    │   • Langfuse callbacks active               │
    │   • Rate limits applied                     │
    │                                             │
    │   MONETIZE (1 click)                        │
    │   ─────────────────                         │
    │   1. Admin clicks "Publish to Marketplace"  │
    │   2. Customers discover via marketplace     │
    │   3. Subscribe → agent cloned to tenant     │
    │   4. Per-call cost tracked (FinOps)         │
    │   5. Reviews + ratings accumulate           │
    │                                             │
    └─────────────────────────────────────────────┘
```

---

## What's Pending (In Progress)

| Item | Status | Target |
|------|--------|--------|
| Entra ID / SSO enforcement | Framework ready, needs Azure creds | Q3 2026 |
| OPA per-call authorization | Layer 2 policy (if APISix insufficient) | Q3 2026 |
| L2-L4 per-call autonomy approval | Needs custom LangGraph interrupt graph | Q3 2026 |
| Anomaly enforcement (multi-pod) | Needs Postgres-backed scorer | Q3 2026 |
| Alembic DB migrations | Using startup ALTER (works but fragile) | Q3 2026 |
| File upload RAG (production) | Needs Azure Blob + embedding keys | Q3 2026 |
| APISix MCP gateway CRDs | Policy enforcement at network layer | Q3 2026 |

---

## Future Roadmap (North Star)

### Q3 2026 — Enterprise Hardening
- Entra ID SSO enforcement (zero-trust)
- APISix policy enforcement on MCP traffic
- Alembic migrations (safe schema evolution)
- Per-call autonomy approval (L2-L4 interrupt graph)
- Production file upload (Azure Blob + pgvector)

### Q4 2026 — External Launch
- First 5 paying design partners onboarded
- SOC 2 Type I certification
- Public API documentation portal
- Partner self-registration for MCP servers
- SLA-backed accuracy guarantees

### 2027 — Agentic OS at Scale
- Agent marketplace with 100+ certified agents
- Cross-company A2A federation (Vertex agents ↔ customer agents)
- Continuous learning loop (traces → prompt improvement → auto-deploy)
- Multi-modal model support (Document Intelligence, Vision)
- Event-driven agent orchestration (Azure Event Grid integration)
- AI agent insurance / liability framework

---

## Platform Numbers (Live — Jun 8, 2026)

| Metric | Value |
|--------|-------|
| Total agents | 83 |
| MCP domain servers | 7 |
| API endpoints | 277 |
| Governance checks | 22 per agent |
| Portal tabs | 10 + 14 governance sub-pills |
| Tenants | 3 demo + unlimited via self-signup |
| Unit tests | 132 |
| E2E test suites | 6 (90/94 passing) |
| Models supported | 7 (GPT-5, Claude, Gemini, Llama, etc.) |
| Autonomy levels | 5 (L1-L5) |
| Sprints shipped | 4 (A through D) |
| Time to build | 5 weeks (1 engineer) |

---

## The Bottom Line

> **We are not another AI chatbot platform.**  
> We are the **operating system** that makes AI agents safe, governed, and sellable at enterprise scale.
>
> Every agent built on Vertex Agentic OS is **born compliant** — governance is not a bolt-on afterthought, it's baked into every execution step.
>
> Our competitors lock you into their cloud, their framework, their models.  
> **We are the neutral control plane that governs ALL of them.**

---

*Live URL: https://vertex-agent-platform.sr.az.vtxdev.net (VPN required)*  
*SDK: `pip install vertex-agent-sdk` → `from sdk import Client`*  
*Built by: CTD AI Engineering Team*
