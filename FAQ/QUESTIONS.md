# Frequently Asked Questions (FAQ) & Strategic Answers

> **Core architectural, technical, operational, and commercial questions regarding the Cognitive Blueprint AI Engine, API integration, and open-source knowledge base.**

---

## 📑 Table of Contents

1. [Are generated user blueprints lifelong, or do they expire?](#1-are-generated-user-blueprints-lifelong-or-do-they-expire)
2. [How does pricing work, and what is the MEGA plan soft limit?](#2-how-does-pricing-work-and-what-is-the-mega-plan-soft-limit)
3. [How are vector interpretations and systemic alerts delivered?](#3-how-are-vector-interpretations-and-systemic-alerts-delivered)
4. [Why are the 33 engines split into 5 modular criteria instead of one giant endpoint?](#4-why-are-the-33-engines-split-into-5-modular-criteria-instead-of-one-giant-endpoint)
5. [What is the exact impact of an unknown or default birth time (12:00)?](#5-what-is-the-exact-impact-of-an-unknown-or-default-birth-time-1200)
6. [Does Cognitive Blueprint AI store user data or require GDPR cookie banners?](#6-does-cognitive-blueprint-ai-store-user-data-or-require-gdpr-cookie-banners)
7. [Where can I see an end-to-end example of a real-world integration?](#7-where-can-i-see-an-end-to-end-example-of-a-real-world-integration)
8. [What are the permissible use cases and ethical boundaries?](#8-what-are-the-permissible-use-cases-and-ethical-boundaries)

---

### 1. Are generated user blueprints lifelong, or do they expire?

**Every generated blueprint is permanent, immutable, and lifelong.**

Because an individual's spatiotemporal birth coordinates (date, time, latitude, longitude) represent an immutable moment in spacetime, their foundational 900+ vectors, mathematical baselines, and constitutional metrics never expire.

* **Architecture Recommendation**:
  Query each criterion endpoint **once per user** via RapidAPI.
* **Permanent Local Caching**:
  Save the returned JSON payload into your local database (PostgreSQL, MongoDB, SQLite, Redis, or local disk).
* **Zero Recurring Cost**:
  For all subsequent user sessions, AI chat interactions, and daily dashboard views, read directly from your local cache. You never need to pay for or re-query the API for returning profiles.

---

### 2. How does pricing work, and what is the MEGA plan soft limit?

The API is accessible through [RapidAPI](https://rapidapi.com/fractaldeciphersyndicate/api/cognitive-blueprint-ai-engine) under clear subscription tiers:

| Tier | Price | Monthly Quota | Scope | Overages |
| :---: | :---: | :---: | :--- | :--- |
| **🟢 FREE** | **$0** | **20 req / mo** | **Criterion 1** *(9 Engines)* | Hard limit at 20 req. |
| **🔵 PRO** | **$79** | **999 req / mo** | **Criteria 1, 2, 3** *(21 Engines)* | Hard limit at 999 req. |
| **🟣 MEGA** | **$149** | **2,500 req / mo** | **Criteria 1, 2, 3, 4, 5** *(All 33 Engines)* | **Soft Limit: $0.12 / req** |
| **👑 SCALE** | Custom | Custom | All 33 Engines | Custom enterprise volume. |

#### 🛡️ The MEGA Soft Limit & Uninterrupted Scaling
Unlike APIs that drop incoming connections or throw `429 Too Many Requests` errors when a hard cap is hit, the **MEGA plan features a soft limit of 2,500 requests per month**.
* If your application goes viral or experiences an unexpected surge in new user registrations, requests beyond 2,500 are **seamlessly processed at a flat rate of $0.12 per request**.
* This guarantees **100% production uptime** while keeping cloud costs strictly predictable.

---

### 3. How are vector interpretations and systemic alerts delivered?

**Interpretations and alerts are delivered directly inline within the API JSON response.**

The Cognitive Blueprint AI Engine computes spatiotemporal mathematics and automatically enriches every activated vector and alert with its complete clinical guidance:
* **Quantitative metrics** (`math`): Clean numeric indicators, scores (0–100), and latency thresholds.
* **Enriched Vectors** (`vectors`): Each vector includes `phenomenon`, `mentorsMessage`, `clinicalInterpretation`, `actionableAdvice`, and `systemFriction` (compensatory conditioning to avoid).
* **Systemic Alerts** (`crossEngineAlerts`): Each alert includes `title`, `systemTension`, `mentorsDiagnosis`, and concrete `strategicRemediation` protocols.

Developers receive all semantic interpretation text directly in the payload, eliminating the need to parse external dictionary files or build local lookup mappings. Proprietary ontological dictionaries remain secure on-server.

For a concrete demonstration of this response structure, see [**`EXAMPLE/nikola_tesla_json/`**](../EXAMPLE/nikola_tesla_json/).

---

### 4. Why are the 33 engines split into 5 modular criteria instead of one giant endpoint?

Splitting into 5 criteria (`Identity`, `Blocks`, `Vocation`, `Direction`, `Operating Mode`) provides two major technical advantages:
1. **Application Latency**: Querying 6–9 engines takes ~15–30ms, whereas computing all 33 engines in a single call introduces unnecessary latency.
2. **LLM Context Economy**: Injecting all 33 engines and 260+ active vectors into an LLM context window at once wastes tokens and causes prompt drift. Modularity lets you fetch only the data relevant to the user's immediate question (e.g. query Criterion 3 for a career counseling session, or Criterion 5 for sleep and recovery coaching).

---

### 5. What is the exact impact of an unknown or default birth time (12:00)?

Earth rotates 1° every 4 minutes, causing local angular axes to move continuously. Based on our empirical laboratory testing:
* **🟢 4 of 33 Engines (12%) remain 100% Identical** across all 24 hours (`C1_E7` Numerology, `C1_E9` Name Signature, `C2_E6` Neural Overload Defense, `C4_E6` Legacy Capital).
* **🟡 24 of 33 Engines (73%) retain ~75%–85% Macro Accuracy** (Vocational calling, generational karma, macro-timing cycles).
* **🔴 5 of 33 Engines (15%) suffer Critical Distortion (<60% accuracy)**:
  * `C1_E1` (Sovereign Core Design & Decision Compass) drops to **~38% accuracy**!
  * `C1_E2` (Cognitive Processing & Environment) drops to **~40% accuracy**!
  * `C5_E1` (Sensory Intake & Nutrition) drops to **~56% accuracy**!

> 📖 **Full Empirical Report**: See the dedicated [**`EXACT_TIME.md`**](EXACT_TIME.md) guide for exact vector divergence tables and LLM guardrails when handling approximate birth times.

---

### 6. Does Cognitive Blueprint AI store user data or require GDPR cookie banners?

**Zero data retention & complete architectural amnesia.**
* Calculations occur strictly in volatile RAM for ~15–30 ms.
* The API maintains **no database, no tracking logs, and no persistent user storage**.
* The instant the JSON payload is dispatched to RapidAPI, the RAM buffer is wiped clean. The engine possesses architectural amnesia—it does not know, log, or store who was analyzed.
* This architecture is **GDPR-, CCPA-, and HIPAA-aligned by design**, requiring zero user consent tracking on our end.

---

### 7. Where can I see an end-to-end example of a real-world integration?

Explore the [`EXAMPLE/`](../EXAMPLE/) directory:
* [`EXAMPLE/README.md`](../EXAMPLE/README.md): Step-by-step benchmark using **Nikola Tesla** (`1856-07-10 00:00`, Smiljan).
* [`EXAMPLE/nikola_tesla_json/`](../EXAMPLE/nikola_tesla_json/): Raw JSON payloads for all 5 criteria exactly as returned by RapidAPI.
* [`EXAMPLE/nikola_tesla_interpretation/`](../EXAMPLE/nikola_tesla_interpretation/): 33 comprehensive clinical inquiry reports synthesized directly from the enriched API telemetry.

---

### 8. What are the permissible use cases and ethical boundaries?

* **Permitted**: Personal self-exploration, AI mentor & life coaching context enrichment, productivity & chronobiology planners, holistic wellness tools, and character modeling for gaming/narratives.
* **Prohibited**: Medical, psychiatric, or clinical diagnosis; licensed financial/investment advice; and adverse discriminatory screening (such as employment hiring/firing decisions, tenancy background checks, or insurance underwriting).

For complete legal terms, review [**TERMS_OF_USE.md**](../TERMS_OF_USE.md) and [**DISCLAIMER.md**](../DISCLAIMER.md).
