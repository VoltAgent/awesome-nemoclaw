# RKTM83 — Personal Autonomous Agent Recipe

Run a fully configurable personal autonomous agent inside a NemoClaw sandbox,
with policy-controlled egress, local Ollama inference, and pluggable skills.

**Source:** [github.com/rktm0604/RKTM83](https://github.com/rktm0604/RKTM83)

---

## What This Recipe Does

RKTM83 is a personal agent runtime — domain-agnostic, skill-based, and
fully configurable via YAML. Running it inside NemoClaw adds a second layer
of policy enforcement on top of RKTM83's own built-in PolicyEngine.

```
NemoClaw sandbox
└── OpenShell (network + filesystem + process policy)
    └── RKTM83 agent loop
        ├── PolicyEngine  (rate limits, outreach caps)
        ├── AgentMemory   (ChromaDB vector store)
        ├── AgentBrain    (LLM decisions via Ollama)
        └── Skills        (pluggable Python modules)
```

Two policy layers means the agent cannot reach any unlisted host —
even if the LLM hallucinates a tool call to an external endpoint.

---

## Prerequisites

- NemoClaw installed (`curl -fsSL https://www.nvidia.com/nemoclaw.sh | bash`)
- Ollama running on host (`ollama serve`)
- At least one model pulled (`ollama pull llama3.2:3b`)
- Python 3.10+ inside the sandbox

---

## Step 1 — Apply Policy Presets

Apply the Ollama preset to allow local inference from inside the sandbox:

```bash
openshell policy set ollama.yaml
```

If your agent uses web search (career skill), also apply:

```bash
openshell policy set pypi.yaml
```

For the career skill's Internshala access, apply:

```bash
openshell policy set internshala.yaml
```

---

## Step 2 — Install RKTM83 Inside the Sandbox

Connect to your sandbox:

```bash
nemoclaw my-assistant connect
```

Inside the sandbox shell:

```bash
pip install chromadb sentence-transformers requests pyyaml
git clone https://github.com/rktm0604/RKTM83.git
cd RKTM83
```

---

## Step 3 — Configure

Edit `config.yaml` to set your identity, goals, and which skills to load:

```yaml
agent:
  name: "RKTM83"
  cycle_sleep: 30

identity:
  name: "Your Name"
  goals:
    - "Your goal here"

skills:
  - career    # or your own skill
```

---

## Step 4 — Run

```bash
python run_agent.py
```

The agent runs autonomously. Every cycle it:
1. Reads memory + policy state
2. LLM decides next action (via Ollama, routed through OpenShell gateway)
3. PolicyEngine checks rate limits
4. Tool executes
5. Result stored in ChromaDB
6. Sleeps, repeats

---

## Policy Configuration

RKTM83 inside NemoClaw uses two policy layers:

| Layer | What it controls |
|---|---|
| OpenShell (NemoClaw) | Network egress, filesystem scope, process isolation |
| RKTM83 PolicyEngine | Rate limits, outreach caps, LLM call budget |

Customize RKTM83's internal limits in `config.yaml`:

```yaml
policy:
  outreach_per_day: 5
  llm_calls_per_day: 150
  search_calls_per_hour: 10
```

---

## Adding Your Own Skill

Copy `skills/custom_skill.py`, add your tool functions, enable in config:

```yaml
skills:
  - career
  - myskill   # skills/myskill_skill.py
```

Each skill registers tools with the agent brain. The LLM decides
autonomously when to use each tool based on context and your description.

---

## Architecture Reference

```
agent_brain.py
├── PolicyEngine    ← NemoClaw-inspired gateway (ALLOW/ROUTE/DENY)
├── AgentMemory     ← ChromaDB, 4 collections, persists across runs
├── AgentBrain      ← LLM decision engine, domain-agnostic
└── Agent           ← orchestrator, runs the cycle loop

skills/
├── career_skill.py ← job/internship hunting
└── custom_skill.py ← your own tools
```

**GitHub:** [rktm0604/RKTM83](https://github.com/rktm0604/RKTM83)
**Author:** Raktim Banerjee — BTech CSE, NIIT University 2024-28
