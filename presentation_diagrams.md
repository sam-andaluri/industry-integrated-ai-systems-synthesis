# Mermaid Diagrams for Presentation

Generate images at https://mermaid.live and save to `/images/` folder.

---

## 1. Architecture Diagram (Slide 4)

Save as: `architecture.png`

```mermaid
flowchart TB
    Q[User Question] --> LLM[LLM gpt-4o-mini<br/>Function Calling]

    LLM --> T1[predict_latency<br/>R²=0.573]
    LLM --> T2[forecast_demand<br/>R²=0.962]
    LLM --> T3[analyze_incident<br/>KB + Policies]

    T1 --> S[Synthesized Analysis<br/>+ Recommendations]
    T2 --> S
    T3 --> S

    S --> LLM
    LLM --> R[Response with<br/>Confidence & Limitations]
```

---

## 2. Demo Sequence Diagram (Slide 6)

Save as: `demo_sequence.png`

```mermaid
sequenceDiagram
    participant U as User
    participant L as LLM
    participant T as Tools

    U->>L: Compound scenario
    L->>T: predict_latency(2x)
    L->>T: forecast_demand(hour=14)
    L->>T: analyze_incident(hardware)
    T-->>L: Results + R² metrics
    L-->>U: Synthesis + Confidence
```

---

## 3. Failure Case Diagram (Slide 8)

Save as: `failure_case.png`

```mermaid
flowchart LR
    D[Slow Drift<br/>60%→45%] --> T1[predict_latency<br/>❌ No request]
    D --> T2[forecast_demand<br/>❌ Steady demand]
    D --> T3[analyze_incident<br/>❌ No alert]

    T1 & T2 & T3 --> G[CAPABILITY GAP<br/>Tools need events]

    style G fill:#ffcdd2
```

---

## Usage

1. Go to https://mermaid.live
2. Paste the mermaid code (without the ```mermaid wrapper)
3. Click "Actions" → "Download PNG"
4. Save to `images/` folder
5. In presentation.md, replace mermaid code blocks with:
   ```
   ![Architecture](images/architecture.png)
   ```
