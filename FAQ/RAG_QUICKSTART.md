# RAG & LLM Quickstart Guide

> **Learn how to transform raw Cognitive Blueprint AI telemetry into profound, lifelong clinical syntheses using Retrieval-Augmented Generation (RAG) and LLM context injection.**

This quickstart guides you through building a production-grade RAG pipeline using the [Cognitive Blueprint AI Engine](https://rapidapi.com/fractaldeciphersyndicate/api/cognitive-blueprint-ai-engine) and the open-source dictionaries in this repository.

---

## 🏗️ Architectural Overview: The 4-Stage Synthesis Pipeline

```text
┌─────────────────────────────────┐
│ 1. Spatiotemporal Input Data    │ ➔ Name, Date, Time, Lat/Lon, Timezone Offset
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ 2. RapidAPI Telemetry Query     │ ➔ POST /v1/criterion/{1..5}
│    (One-time per user)          │ ➔ Emits lean JSON: math metrics + vector codes + alerts
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ 3. Permanent Local Cache / DB   │ ➔ Store JSON in PostgreSQL, Redis, or Disk (LIFELONG)
│    (No repeat API calls needed) │ ➔ Zero recurring cost for existing profiles
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ 4. RAG Vector & Alert Matching  │ ➔ Join emitted vector codes with DICTIONARY/
│    (Local Markdown Lookup)      │ ➔ Join emitted alert codes with ALERTS/
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ 5. LLM Synthesis & Generation   │ ➔ Send grounded telemetry + definitions to GPT-4o / Claude / Gemini
│    (Inquiry-Driven Insights)    │ ➔ Produces profound clinical reports (like EXAMPLE/nikola_tesla_interpretation/)
└─────────────────────────────────┘
```

---

## ⚡ Step-by-Step Implementation

### Step 1: Query the API (One-Time Execution per User)

Because an individual's birth coordinates are fixed, their blueprint is **permanent and lifelong**. You only need to query each criterion **once per user** and save the resulting JSON payload into your local database or disk.

Here is a clean Python integration script:

```python
import os
import json
import requests

RAPIDAPI_KEY = os.getenv("RAPIDAPI_KEY", "YOUR_RAPIDAPI_KEY")
RAPIDAPI_HOST = "cognitive-blueprint-ai-engine.p.rapidapi.com"

# Spatiotemporal birth coordinates (Example: Nikola Tesla)
profile_payload = {
    "name": "Nikola Tesla",
    "birthDate": "1856-07-10",
    "birthTime": "00:00",
    "timezoneOffset": 1.02,
    "latitude": 44.5809,
    "longitude": 15.3,
    "gender": "male"
}

def fetch_criterion(criterion_id: int):
    url = f"https://{RAPIDAPI_HOST}/v1/criterion/{criterion_id}"
    headers = {
        "x-rapidapi-key": RAPIDAPI_KEY,
        "x-rapidapi-host": RAPIDAPI_HOST,
        "Content-Type": "application/json"
    }
    response = requests.post(url, headers=headers, json=profile_payload)
    response.raise_for_status()
    return response.json()

# Fetch Criterion 1 (Identity) and cache locally
data_c1 = fetch_criterion(1)
with open("tesla_criterion_1.json", "w", encoding="utf-8") as f:
    json.dump(data_c1, f, indent=2)

print(f"Successfully cached Criterion 1: {data_c1['totalActiveVectors']} active vectors.")
```

---

### Step 2: Extract Vectors and Match with Dictionaries

When the API returns active vectors (e.g. `VEC_ID_CORE_KINETIC_BUILDER`, `VEC_BLK_NOCTURNAL_PARASITIC_SUSCEPTIBILITY`), match them against the Markdown files located in [`DICTIONARY/`](../DICTIONARY/) and [`ALERTS/`](../ALERTS/).

```python
import re
from pathlib import Path

# Paths relative to project root (adjust as needed)
DICTIONARY_ROOT = Path("./DICTIONARY")
ALERTS_ROOT = Path("./ALERTS")

def find_vector_definition(vector_code: str, criterion_id: int) -> str:
    """Scans the criterion dictionary folder for the specified vector block."""
    criterion_dirs = {
        1: "criterion-1-identity",
        2: "criterion-2-blocks",
        3: "criterion-3-vocation",
        4: "criterion-4-direction",
        5: "criterion-5-operating"
    }
    target_dir = DICTIONARY_ROOT / criterion_dirs.get(criterion_id, "")
    
    if not target_dir.exists():
        return f"Dictionary folder not found for Criterion {criterion_id}"

    for md_file in target_dir.glob("*.md"):
        content = md_file.read_text(encoding="utf-8")
        if vector_code in content:
            # Extract section for this vector
            pattern = rf"(###\s+`?{re.escape(vector_code)}`?.*?)(?=\n###|\Z)"
            match = re.search(pattern, content, re.DOTALL)
            if match:
                return match.group(1).strip()
    return f"Definition for {vector_code} not found."

def find_alert_definition(alert_code: str, criterion_id: int) -> str:
    """Scans ALERTS for the specified systemic alert interpretation."""
    alert_file = ALERTS_ROOT / f"criterion_{criterion_id}_alerts_interpretation.md"
    if not alert_file.exists():
        return ""
    content = alert_file.read_text(encoding="utf-8")
    pattern = rf"(##\s+.*?{re.escape(alert_code)}.*?)(?=\n##\s+|\Z)"
    match = re.search(pattern, content, re.DOTALL)
    return match.group(1).strip() if match else ""
```

---

### Step 3: Inject Context into LLM for Deep Synthesis

Pass the structured telemetry, dictionary definitions, and systemic alert remediations into your LLM prompt.

```python
import openai

client = openai.OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

def generate_clinical_inquiry_synthesis(engine_data: dict, criterion_id: int, profile_name: str):
    inquiry = engine_data.get("description")
    math_metrics = engine_data.get("math", {})
    active_vectors = engine_data.get("vectors", [])
    
    # Enrich vectors from local dictionaries
    enriched_vectors = []
    for vec in active_vectors:
        defn = find_vector_definition(vec, criterion_id)
        enriched_vectors.append(f"### Vector {vec}\n{defn}\n")
    
    system_prompt = f"""You are a Master Cognitive Architect and Compassionate Mentor.
You are synthesizing a lifelong, deep clinical assessment for {profile_name}.
Avoid superficial horoscope jargon. Ground every deduction strictly in the provided mathematical metrics and phenomenological vectors.
Structure your report with:
1. Executive Telemetry Overview
2. Core Psychological & Somatic Mechanisms
3. Primary Friction & Paradox Resolution
4. Concrete Actionable Protocols & Lifelong Operating Rules.
"""

    user_prompt = f"""
## Target Clinical Inquiry
"{inquiry}"

## Quantitative Telemetry Metrics (Math):
```json
{json.dumps(math_metrics, indent=2)}
```

## Active Phenomenon Vector Interpretations:
{"".join(enriched_vectors)}

Please synthesize the authoritative clinical guidance for this inquiry.
"""

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ],
        temperature=0.3
    )
    return response.choices[0].message.content
```

---

## 📚 Real-World Output Reference: Nikola Tesla Benchmark

Want to see what completed RAG syntheses look like in practice?

Explore [`EXAMPLE/nikola_tesla_interpretation/`](../EXAMPLE/nikola_tesla_interpretation/), where all 33 inquiries were synthesized using this exact methodology:
- [`01_c1_core_life_role_decision_compass.md`](../EXAMPLE/nikola_tesla_interpretation/01_c1_core_life_role_decision_compass.md): Decision-making latency & emotional wave navigation.
- [`10_c2_reactive_defense_patterns.md`](../EXAMPLE/nikola_tesla_interpretation/10_c2_reactive_defense_patterns.md): Unconscious defense nodes & karmic friction.
- [`16_c3_vocational_calling_mastery.md`](../EXAMPLE/nikola_tesla_interpretation/16_c3_vocational_calling_mastery.md): High-octane commercial leverage & intellectual mastery.
- [`24_c4_crisis_resilience_antifragility.md`](../EXAMPLE/nikola_tesla_interpretation/24_c4_crisis_resilience_antifragility.md): Liquidity preservation & black-swan resilience.
- [`33_c5_sensory_reset_acoustic_quietude.md`](../EXAMPLE/nikola_tesla_interpretation/33_c5_sensory_reset_acoustic_quietude.md): Delta wave sleep mechanics & solitary sensory sanctuary.

---

## 💡 Best Practices for Production RAG Architectures

1. **Persistent Caching (Lifelong Storage)**:
   - Store the API response indexed by `user_id`.
   - Never re-query the API for a user whose birth coordinates have already been computed.
2. **Chunking & Vector Indexing**:
   - Embed each of the 33 synthesized inquiry markdown files as discrete documents in your vector database (Pinecone, ChromaDB, Qdrant, pgvector).
   - Tag metadata with `criterion_id`, `engine_id`, and `vector_codes` for hybrid keyword + semantic search.
3. **Conversational Agent Context**:
   - When the user asks a specific question (e.g. *"Why do I feel completely burned out by 3 PM?"*), retrieve Criterion 5 Engine 3 (`energetic_constitution_daily_rhythm`) and Engine 6 (`sensory_reset_acoustic_quietude`) into the context window.
   - This provides instant, drift-free, clinically grounded answers without hallucination.
