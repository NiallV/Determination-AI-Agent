# PROPOSED VAT LANES [CONCEPT BRIEF]
## PROBLEM STATEMENT & WHY NOW?
To achieve required VAT outcomes, Vertex customers have accumulated significant customizations across On Demand, Chain Flow Accelerator/Accelerator+ and O Series. As a result, MDUs, software updates and connector releases increasingly introduce regression risk and uncertainty. Long support-issue lead times to root cause are repeatedly cited by customers, with support processes often perceived as reactive rather than proactive.

In hybrid estates (e.g. VoD/CFA/TDT shaping ERP data prior to determination), inputs to O Series may be transformed before calculation, making it difficult to establish what actually happened versus what was determined and why. Without consistent, overarching governance across the VAT estate—where some transactions are determined in TDT and others in O Series—there is significant risk of inconsistent VAT treatment and reduced confidence in results.

ViDA is the forcing function. It increases transaction level scrutiny and materially reduces the tolerance for post facto reconstruction of VAT outcomes. In this context, continuous validation and determination traceability are no longer optional capabilities but essential requirements for enterprise VAT control.
## THE CUSTOMER CAPABILITY GAP UNDER VIDA
To operate VAT at enterprise scale under ViDA, customers require a repeatable way to:
- understand where VAT determination logic exists across VoD, CFA, connectors and O Series
- assess which parts of their implementation are bespoke vs out-of-the-box
- proactively validate the impact of changes (MDUs, releases, configuration updates) on customer determination outcomes
- and demonstrate traceability of changes and their potential impact on the customer’s VAT outcomes and environment
### IMPLICATION: VERTEX CAPABILITY GAP
While the above are customer-required capabilities, they cannot be met consistently today without corresponding capabilities within Vertex. At present, knowledge of customer-specific determination environment, bespoke logic and change impact is fragmented across teams, partners and with significant knowledge gaps. This limits Vertex’s ability to proactively assess customer environments, contributes to long lead times for root cause analysis and results in support processes that are experienced as reactive rather than predictive.
## CLOSING THE GAP
To close both the customer capability gap under ViDA and the corresponding Vertex capability gap, a repeatable, customer specific capability is required that can operate continuously across complex, hybrid VAT estates. This capability must be able to:
- Discover and model customer specific VAT determination paths across O Series On-Demand, A+/CFA/TDT and other connectors
- Classify and contextualize bespoke vs out of the box logic within a customer’s environment
- Assess the impact of change events (MDUs, releases, configuration updates) against real customer determination flows before issues surface in production
- Enable traceable explanations of what changed, where it changed, and the potential impact on VAT outcomes
- Enable targeted analysis to improve root cause identification by customer support, operations and engineering
Critically, this capability must be environment aware, repeatable and proactive, rather than relying on ad hoc analysis or individual subject matter expertise.
## WHY THIS CANNOT SCALE MANUALLY
The capability described above cannot be delivered reliably through manual or ad hoc processes given the scale, complexity and rate of change within modern enterprise VAT estates.

Large enterprise customers typically operate across multiple environments spanning O Series On Demand, CFA/Accelerator+, TDT and multiple ERP instances, often with country  or process specific customizations layered over time. Each environment introduces unique determination paths and data shaping behaviors that must be understood in context.

At the same time, the frequency of change events—including MDUs, software releases, connector updates, configuration changes and regulatory updates—continues to increase. Each change has the potential to impact customer specific determination flows, making one off regression testing or periodic reviews insufficient.

This complexity is compounded in hybrid determination models, where VAT logic and data transformation may be split across ERP logic, CFA/TDT, connectors and O Series. Understanding end to end determination behavior requires correlating signals across systems, something that currently depends heavily on individual expertise.

As a result, knowledge of customer implementations becomes person dependent and fragmented across Product, Support, Operations, partners, and system integrators. This does not scale as customer estates grow, change velocity increases and experienced resources become a bottleneck.

The outcome is a high and growing support load, characterized by long lead times to root cause, reactive issue handling and limited ability to proactively identify risk before issues surface in production—outcomes that are increasingly incompatible with ViDA’s transaction level scrutiny and reduced tolerance for post facto explanation.

In this context, a manual, human led approach cannot consistently deliver the continuous validation, traceability and confidence now required for enterprise VAT control.
## AUTOMATION AS THE ENABLING APPROACH
To deliver the required capability at enterprise scale, automation must operate continuously and consistently across customer specific VAT estates, rather than being invoked on demand or through one off analysis. Automated capability will:
- Ingest and correlate determination context across systems – combine signals from ERP, CFA/TDT, connectors and O Series to reconstruct end to end VAT determination paths, including pre determination data shaping and transformations, without re determining transactions
- Maintain a continuously updated analytical representation of VAT behavior – persist an evolving understanding of each customer’s VAT environment – derived from observed determination behavior, configuration metadata, mappings and data transformation patterns – capturing key dependencies and structural characteristics across systems and releases, without replicating or mirroring the customer’s operational environments
- Identify patterns of customization and workaround usage – detect recurring implementation patterns—such as data shaping prior to determination, connector level overrides, or replicated logic outside O Series—and distinguish these from standard, out of the box behavior.
- Surface opportunities to simplify and standardize – highlight where customer specific logic could be reduced, consolidated or migrated toward native O Series functionality, supporting more governed, supportable and testable VAT implementations over time
- Detect and evaluate change events automatically – identify when MDUs, releases, configuration changes or connector updates intersect with customer specific determination paths, mappings or transformations
- Assess impact against real customer transaction flows – evaluate the potential effect of change against observed determination behavior and transaction mixes, rather than relying solely on generic test cases or static rules
- Enable transaction level governance and traceability – capture and retain determination lineage – including how inputs were transformed, which mappings or rules applied and where determination occurred – ensuring VAT outcomes can be consistently explained, compared and defended under agreed governance models
- Generate structured, traceable evidence by design – produce explainable outputs showing what changed, where it changed and why a different VAT outcome or risk profile may emerge
- Enable rapid environment contextualization for investigation and support – leverage persisted determination context  and configuration to quickly assemble a coherent view of a customer’s environment for Support, Partners/SIs and Engineering – accelerating root cause analysis and change assessment without requiring full environment recreation/cloning
- Surface targeted signals for human intervention – provide actionable insights to customers, Partners/Sis, and Vertex Support, Operations, Product, and Engineering when risk, inconsistency, regression or governance gaps are detected
This automated layer must be additive rather than intrusive—observing, modelling and analyzing VAT behavior across systems without altering determination logic – to ensure it can be applied uniformly across heterogeneous customer environments and evolve with the VAT estate over time.
 
## AI ASSISTED VAT ENVIRONMENT ASSESSMENT AGENT
The capabilities described above requires a mechanism that can operate continuously across complex customer specific VAT estates, reason over fragmented determination context and adapt as those environments evolve. An AI assisted assessment agent provides a scalable way to deliver this capability without introducing new determination logic or disrupting existing VAT processes.

The agent operates as an observational and analytical layer, sitting alongside O Series, CFA/TDT, connectors and ERP systems. Its role is not to calculate tax, replace configuration or enforce outcomes, but to learn, model and assess how VAT determination is constructed within each customer environment. 

At a high level, the AI assisted agent is responsible for:
- Learning the structure of a customer’s VAT environment over time – build and maintain an analytical understanding of where VAT determination, mappings and data transformations occur across Vertex product instances, using observed behavior and available metadata rather than configuration replication
- Reconstructing determination paths for real transactions – assemble end to end determination context for transactions flowing through hybrid estates, enabling visibility into how inputs were shaped, where determination occurred and which logic influenced outcomes
- Identifying divergence, inconsistency and risk signals – detect patterns of behavior that indicate custom logic, workaround usage, environment drift or inconsistent VAT treatment across systems, environments or transactions populations (e.g. transactions determined via TDT versus O Series)
- Assessing the impact of change events against known behavior – evaluate how MDUs, releases, configuration changes or connector updates intersect with previously observed determination paths, both ahead of planned changes and following deployment, highlighting areas where outcomes may change or require validation
- Supporting governance, explainability and evidence generation – produce structured, traceable assessments that explain what is happening in the VAT estate, what has changed and where potential risk or impact exists, without relying on post hoc reconstruction
- Accelerating human investigation and decision making – provide Support, Partners/SIs, Operations and Engineering with rapid contextual insight into a customer’s VAT environment, reducing time to understanding and enabling targeted intervention where required

Crucially, the agent is additive rather than authoritative. It does not decide VAT outcomes or override customer configuration. Instead, it strengthens governance by making complex VAT environments observable, comparable and explainable at enterprise scale.

By combining continuous observation with accumulated environment knowledge, the AI assisted assessment agent enables Vertex and its customers to move from reactive analysis toward proactive VAT control—an essential shift as ViDA increases transaction level scrutiny and reduces tolerance for inconsistency and uncertainty.
 
# APPENDICES 
## AI ASSISTED ASSESSMENT OPERATING MODEL
The AI assisted VAT environment assessment agent operates through a staged, progressive model that aligns with both Vertex release processes and customer operating models. 
- The agent continuously learns and maintains an analytical understanding of each customer’s VAT determination environment based on observed behavior across systems
- Ahead of MDUs, releases or major configuration changes, this understanding is used to assess how planned changes intersect with known customer specific determination paths and bespoke logic, enabling early identification of potential risk areas and proactive communication
- Following deployment, the agent monitors observed behavior to detect unexpected divergence or regression relative to expected outcomes
- As an optional extension, this insight can be used to guide targeted customer or partner led validation in test environments, prioritizing scenarios most likely to be impacted
Throughout, the agent remains observational and additive, supporting governance and decision making without executing determination logic or altering customer environments.
## VAT ‘MOTORWAY’ ANALOGY
- the motorway network & satellite GPS infrastructure represents Vertex’s platform, with on ramps (connectors) allowing traffic to join an efficient road network
- vehicles represent AR & AP transactions on the motorway, entering from different places such as warehouses, stores (ERP)
- the road network seamlessly crosses borders – but rules and speed limits may change with additional checks and evidence possibly required (customs)
- speed cameras enforce legislative compliance throughout the journey
- routing changes are signaled by a Google Maps type product providing transaction updates during the journey
- exit tolls represent returns, adjacent reports & payments while penalty letters represent audits and potential fines
- a fleet management system is akin to the VAT Console leveraging the motorway infrastructure (platform) and vehicle tracking (transactions) – with a dashboard providing visibility and early warnings in [near] real time, with continuous compliance monitoring (via anomaly detection) triggering warnings, while roadworks represent MDUs/releases
## PROPOSED VAT LANES [2027]
### LANE 1 – OUT-OF-THE-BOX VAT DETERMINATION ADOPTION & ENABLEMENT
Ensure VAT determination, whether out-of-the box or customized, is governed, explainable, testable at enterprise scale, enabling customers to confidently and rapidly adopt VAT determination. As regulatory scrutiny increases (ViDA as a forcing function), continuous compliance validation will be required.
### LANE 2 – VAT LIFECYCLE COMPLIANCE & REGULATORY ENABLEMENT
Deliver the concrete, country specific ViDA-driven obligations (e Invoicing, SAF T, DRR, etc.) while ensuring they are built on very solid determination and data foundations that scale beyond one off mandates.
### LANE 3 – DATA, EVIDENCE & AUDIT READINESS
Make VAT evidence and transaction lineage available by design, not via post facto reconstruction, so enterprises can defend outcomes under continuous audit models and trust the accuracy of reports and returns.
### LANE 4 – PLATFORM INTELLIGENCE & SINGLE SOURCE OF VAT TRUTH 
Provide platform level visibility, comparison and anomaly detection across ERPs and determination paths, enabling proactive VAT risk management and consistency as ViDA shifts VAT from periodic correction to continuous control.
### LANE 5 – ECOSYSTEM & OPERATING MODEL INTEGRATION
Reduce operating friction across ERP, connectors, SIs, Product and Customer Success by clarifying responsibilities, standardizing patterns and shortening time to root cause in complex, multi system VAT estates.
## LANE 1 – OUT-OF-THE-BOX VAT DETERMINATION ENABLEMENT
O Series as the system of record for tax reasoning, even when data is shaped elsewhere.
### PURPOSE
#### ENABLE ADOPTION + REDUCE FRICTION + IMPROVE VISIBILITY, TRACEABILITY & GOVERNANCE
Enable both existing and new customers to adopt out-of-the-box VAT determination capabilities in O Series in a way that reduces bespoke logic, simplifies onboarding, improves testability and regression, and establishes clear governance and traceability across O Series and its connectors.
This ensures that all data transformation and enrichment performed during implementation (e.g. in the connectors) is visible to, and governed by, O Series — improving support outcomes, audit defensibility and preparing customers for consistent, [near] real time data flows from VoD and the ERP into the Vertex platform.
### DEPENDENCIES & ASSUMPTIONS
#### DEPENDENCY 1: CUSTOMER CONFIGURATION VISIBILITY
Ensure that sufficient, customer consented visibility into active environments, configurations, connectors and customizations exists to support determination traceability, validation and readiness assessment under UC2, UC3 and UC4. Clear delineation of responsibilities: 
- O Series product team owns the definition of required signals and metadata necessary to support Lane 1
- Operations/Platform team’s own implementation of environment telemetry
### USE CASES
#### USE CASE 1: ADOPTION & GOVERNANCE FRAMEWORK (ANCHORED IN O SERIES)
A productized onboarding/migration tooling that provides confidence gates, regression coverage and connector traceability so customers can adopt existing native determination features with lower cost, lower risk and faster time-to-production – while setting up consistent VoD/ERP data flow into the Vertex platform as a prerequisite for downstream monitoring, controls, and analytics.
#### USE CASE 2: DATA TRANSFORMATION TRACEABILITY
Ensure that any data mapping, data transformations or any enrichment performed in connectors or the ERP (e.g. SAP logic to influence determination outcomes) is explicitly captured, versioned and traceable and passed into O Series as part of the determination context for auditability and downstream use.
#### USE CASE 3: CONTINUOUS VALIDATION ENGINE
Provide standardized, reusable control and regression testing capabilities that allow customers to continuously and proactively validate VAT determination outcomes across configuration changes, releases and MDUs with confidence – from pre-launch to post go-live evolution for scalability.
#### USE CASE 4: READINESS & MIGRATION ASSESSMENT
Establish visibility and decision criteria for adoption and migration by providing a structured assessment based on declared determination flows, traceability artefacts (UC2), validation coverage (UC3) and governance artefacts to quantify bespoke logic, readiness for governed adoption, migration risk(s) and effort required — with options for migration paths under the Lane I governance framework:
- Where are we today? (how much bespoke logic exists, where does determination happen (O Series, TDT, ERP, connectors) and how opaque is the current setup).
- How ready are we to adopt the Lane 1 model? (are UC2 (traceability) requirements met, do we have sufficient coverage for UC3 (validation) and where are the risks).
- What are the safest migration paths? (which determination paths can be standardized first, what needs remediation before migration and what effort vs benefit trade offs exist).
#### USE CASE 5: EVIDENCE & EXPLAINABILITY 
Provide a consistent and auditable determination evidence and explainability layer by capturing, persisting and transparently presenting the full determination context throughout the lifecycle of the transaction so VAT outcomes can be consistently explained and defended without post facto reconstruction , including:
- the VAT determination outcome with the inputs and attributes that influenced the outcome
- the rules, mappings and transformations applied (identifying those executed outside O Series)
- the reasoning path that explains why a particular VAT treatment was applied
- any changes during the transaction lifecycle (refunds, determination changes)
