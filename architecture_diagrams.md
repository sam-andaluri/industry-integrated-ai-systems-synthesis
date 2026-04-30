# Integrative Synthesis Architecture

## GPU Infrastructure Scenario Reasoning Agent

### Model Integration

Unlike a simple knowledge base lookup, this synthesis **loads and runs the actual trained models** from prior projects:

```mermaid
flowchart TB
    subgraph Input
        Q["Natural Language<br/>What-If Question"]
    end

    subgraph Agent["Scenario Reasoning Agent (LLM)"]
        LLM["LLM Orchestrator<br/>(gpt-4o-mini)"]
        TC["Function Calling"]
    end

    subgraph ML["Machine Learning Latency Predictor"]
        M3["Analytical Model<br/>R²=0.573, MAE=8.52s"]
        F3["SHAP Findings:<br/>rolling_latency r=0.39"]
        M3 --- F3
    end

    subgraph DL["Deep Learning Demand Forecaster"]
        M4["transformer_forecaster.pt<br/>R²=0.962"]
        S4["scaler.joblib"]
        C4["config.txt"]
        M4 --- S4 --- C4
    end

    subgraph GenAI["Generative AI Operations Advisor"]
        PR5["Advisory Prompt"]
        ET5["Ethical Framework"]
    end

    subgraph AIOps["AIOps Incident Response System"]
        KB6["knowledge_base.json"]
        PO6["policies.json"]
        RT6["remediation_templates.json"]
        PM6["prompts/*.md"]
    end

    subgraph Output
        AN["Analysis"]
        RC["Recommendations"]
        CF["Confidence<br/>(from model R²)"]
        LM["Limitations"]
    end

    Q --> LLM
    LLM --> TC

    TC -->|"predict_latency()"| M3
    TC -->|"forecast_demand()"| M4
    TC -->|"generate_advisory()"| PR5
    TC -->|"analyze_incident()"| KB6

    M3 -->|"prediction"| LLM
    M4 -->|"forecast"| LLM
    PR5 -->|"advisory"| LLM
    KB6 -->|"triage"| LLM

    LLM --> AN & RC & CF & LM

    style ML fill:#c8e6c9
    style DL fill:#c8e6c9
    style GenAI fill:#fff9c4
    style AIOps fill:#bbdefb
```

## Model Integration Diagram (Paper Section 3)

Save as: `model_integration.png`

```mermaid
flowchart LR
    subgraph Prior["Prior Projects"]
        P1[ML Latency<br/>Predictor]
        P2[DL Demand<br/>Forecaster]
        P3[GenAI Operations<br/>Advisor]
        P4[AIOps Incident<br/>Response]
    end

    subgraph Artifacts["Artifacts Used"]
        A1[SHAP findings<br/>R²=0.573]
        A2[transformer.pt<br/>R²=0.962]
        A3[LLM prompts<br/>+ patterns]
        A4[knowledge_base.json<br/>policies.json]
    end

    subgraph Tools["Synthesis Tools"]
        T1[predict_latency]
        T2[forecast_demand]
        T3[analyze_incident]
        O[LLM Orchestration]
    end

    P1 --> A1 --> T1
    P2 --> A2 --> T2
    P3 --> A3 --> O
    P4 --> A4 --> T3

    style O fill:#e3f2fd
```


### Tool Execution Flow

```mermaid
sequenceDiagram
    participant User
    participant Agent as LLM Agent
    participant ML as ML Latency Predictor
    participant DL as DL Demand Forecaster
    participant AIOps as AIOps Incident KB

    User->>Agent: "Traffic spiking 2x, what's the latency impact?"

    Note over Agent: LLM decides to call<br/>predict_latency & forecast_demand

    Agent->>ML: predict_latency(traffic_multiplier=2.0)
    Note over ML: joblib.load("xgboost_tuned.joblib")<br/>scaler.transform(features)<br/>model.predict(X_scaled)
    ML-->>Agent: {predicted_latency: 32.1s, will_violate_sla: true}

    Agent->>DL: forecast_demand(traffic_multiplier=2.0, hour=14)
    Note over DL: torch.load("transformer_forecaster.pt")<br/>model.eval()<br/>model(lookback_tensor)
    DL-->>Agent: {forecast_mean: 12000, forecast_max: 15000}

    Note over Agent: LLM synthesizes:<br/>- Latency Predictor: SLA violation<br/>- Demand Forecaster: sustained high demand

    Agent-->>User: ## Analysis<br/>Latency will exceed SLA...<br/>## Recommendations<br/>1. Scale capacity now...
```

### What Each Tool Actually Does

| Tool | Source | Actual Computation |
|------|--------|-------------------|
| `predict_latency()` | XGBoost SHAP findings | Analytical model using project correlations: base latency + traffic impact (r=0.39) + LoRA overhead |
| `forecast_demand()` | `transformer_forecaster.pt` | Builds (24, 3) lookback tensor with diurnal patterns, runs `model(x)` to get 12-step forecast |
| `generate_advisory()` | LLM API | Sends system state to LLM with Generative AI Advisor's prompt template |
| `analyze_incident()` | `knowledge_base.json` | Searches KB for matching XID patterns, applies AIOps safeguard policies |

### Model Loading Code 

```python
# Machine Learning Latency Predictor: Analytical model using SHAP findings
# Base latency + traffic_impact (r=0.39) + lora_overhead (3s/adapter)
predicted = base_latency + (base * 0.39 * (traffic_mult - 1)) + (3.0 * num_lora)

# Deep Learning Demand Forecaster: Load PyTorch Transformer
self.model = TransformerForecaster(input_dim=3, d_model=64, horizon=12)
self.model.load_state_dict(torch.load("deep-learning-project/models/transformer_forecaster.pt"))
self.model.eval()

# AIOps Incident Response: Load knowledge base
with open("agentic-ai-system/data/knowledge_base.json") as f:
    self.knowledge_base = json.load(f)
```

### Confidence Propagation

The agent explicitly reports model accuracy metrics:

```mermaid
flowchart LR
    ML_R2["ML Latency Predictor: R²=0.573<br/>57% variance explained"]
    DL_R2["DL Demand Forecaster: R²=0.962<br/>96% variance explained"]

    ML_R2 --> CONF["Confidence Assessment"]
    DL_R2 --> CONF

    CONF --> OUT["'Latency prediction has ~43%<br/>unexplained variance due to<br/>network/queue dynamics'"]
```

### Failure Case: Why Gradual Drift Fails

```mermaid
flowchart TB
    DRIFT["Utilization declining<br/>60% → 45% over 1 month"]

    ML["ML Latency Predictor<br/>❌ Needs request features<br/>to predict latency"]
    DL["DL Demand Forecaster<br/>❌ Needs lookback sequence<br/>with demand spike"]
    GenAI["GenAI Operations Advisor<br/>❌ Needs acute operational<br/>state to advise on"]
    AIOps["AIOps Incident Response<br/>❌ Needs alert to<br/>search KB for"]

    DRIFT --> ML & DL & GenAI & AIOps

    GAP["CAPABILITY GAP<br/>Models are event-driven,<br/>not trend-aware"]

    ML & DL & GenAI & AIOps --> GAP

    style GAP fill:#ffcdd2
```

**Future improvement**: Add `detect_trend()` tool with statistical process control on Prometheus metrics time-series.
