# Developmental Agent Memory

An open-source research prototype for testing a simple but powerful hypothesis:

> **LLM agents become more coherent, personalized, and reliable when memory is built through staged developmental experience rather than injected all at once.**

This project is designed for a solo technical founder/engineer: practical, reproducible, and research-adjacent.

---

## Core Thesis

Most LLM memory systems treat memory as static storage + retrieval.

Developmental Agent Memory (DAM) treats memory as a **process**:

1. Experience is captured and encoded over time.
2. A background salience process continuously reweights memory.
3. Selected memories resurface into working memory before response generation.
4. Reflection updates a compact self-model.
5. Future behavior is conditioned on both self-model and resurfaced memory.

**Core loop:**

`Experience → Encoding → Background Salience → Resurfacing → Reflection → Self-model update → Future behavior`

---

## 1) Core Concept and Terminology

Use consistent terms throughout the codebase and paper/demo.

- **Experience**: A single interaction episode (user input, agent output, outcome signals, metadata).
- **Memory Unit**: Structured representation of an experience (facts, preference hints, strategy notes, failures, corrections).
- **Memory Store**: Database layer that stores raw experiences and derived memory units.
- **Reflection Pass**: A scheduled process that reviews recent experiences and extracts higher-level lessons.
- **Self-Model**: A compact, revisable profile of the agent’s current identity, preferences, strengths, weaknesses, and operating policies.
- **Developmental Stage**: A phase of accumulated experience (e.g., stage_0 cold start, stage_1 early adaptation, stage_2 stable behavior).
- **Revision Event**: Any update that modifies a self-model claim due to new evidence.
- **Forgetting/Compression**: Mechanism to merge, down-rank, or retire stale memories while preserving useful abstractions.
- **Background Salience Engine**: A daemon-like process that re-scores and resurfaces memories without explicit retrieval requests.
- **Working Memory Queue**: A short list of memories primed for the next response, including explicit retrieval and subconscious resurfacing sources.

---

## 2) Memory Architecture

A minimal architecture with clear separation of responsibilities:

1. **Conversation Runtime**
   - Handles user ↔ agent turn loop.
2. **Experience Logger**
   - Persists each interaction as immutable event data.
3. **Memory Classifier**
   - Converts raw interaction into typed memory units.
4. **Memory Store**
   - Stores events + memory units + stage snapshots.
5. **Background Salience Engine**
   - Runs continuously (time- or turn-triggered), re-scores memory, and selects resurfacing candidates.
6. **Working Memory Manager**
   - Combines explicit retrieval with surfaced memories and tracks provenance.
7. **Reflection Engine**
   - Runs every `N` turns; produces distilled lessons.
8. **Self-Model Updater**
   - Applies reflection outputs as versioned changes.
9. **Prompt Assembler**
   - Injects current self-model + selected memories into future prompts.

### Principle

Keep raw logs immutable; make higher-level memory and self-model updates explicitly versioned and auditable.

---

## 3) Minimum Viable Prototype (MVP)

Build **one agent** with **one memory pipeline** and **one benchmark script**.

### MVP User Flow

User talks to agent  
↓  
Agent responds  
↓  
System logs interaction as an experience  
↓  
System classifies and encodes memory  
↓  
System stores memory  
↓  
Background salience engine reweights and resurfaces memory  
↓  
Resurfaced memory is added to working-memory queue  
↓  
Every `N` interactions, system reflects  
↓  
System updates self-model  
↓  
Future responses are influenced by updated self-model

### Non-goals (v1)

- No model fine-tuning.
- No distributed systems.
- No complex multi-agent orchestration.
- No heavy UI requirements.

---

## 4) Database Schema (Initial)

Use SQLite first; migrate later if needed.

### `agents`
- `id` (pk)
- `name`
- `created_at`

### `experiences`
- `id` (pk)
- `agent_id` (fk)
- `session_id`
- `turn_index`
- `user_message`
- `agent_message`
- `timestamp`
- `outcome_signal` (nullable; e.g., user rating/correction)

### `memory_units`
- `id` (pk)
- `agent_id` (fk)
- `experience_id` (fk)
- `type` (`fact`, `preference`, `strategy`, `failure`, `correction`, `identity`)
- `content`
- `confidence` (0-1)
- `salience` (0-1)
- `emotional_valence` (-1 to +1)
- `emotional_intensity` (0-1)
- `recurrence_count` (int)
- `unresolved_tension_score` (0-1)
- `symbolic_tags` (json/text array)
- `resurfacing_count` (int)
- `last_resurfaced_at` (nullable timestamp)
- `status` (`active`, `compressed`, `retired`)
- `created_at`

### `reflections`
- `id` (pk)
- `agent_id` (fk)
- `window_start_experience_id`
- `window_end_experience_id`
- `summary`
- `lessons_json`
- `created_at`

### `self_model_versions`
- `id` (pk)
- `agent_id` (fk)
- `version`
- `model_json` (identity/policies/preferences/known limits)
- `based_on_reflection_id`
- `created_at`

### `self_model_diffs`
- `id` (pk)
- `agent_id` (fk)
- `from_version`
- `to_version`
- `diff_json`
- `reason`
- `created_at`

### `subconscious_cycles`
- `id` (pk)
- `agent_id` (fk)
- `cycle_type` (`periodic`, `pre_response`, `goal_shift`, `conflict_triggered`)
- `trigger_reason`
- `input_memory_ids` (json)
- `surfaced_memory_ids` (json)
- `generated_associations_json`
- `unresolved_tensions_json`
- `created_at`

### `working_memory_events`
- `id` (pk)
- `agent_id` (fk)
- `source` (`explicit_retrieval`, `subconscious_resurfacing`, `reflection`, `user_reference`)
- `memory_unit_id` (fk)
- `reason_resurfaced`
- `inserted_into_context` (bool)
- `created_at`

---

## 5) Agent Loop

Pseudo-loop:

```text
for each user turn:
  surfaced_units = get_working_memory_queue(agent_id, limit=3)
  retrieved_units = retrieve_explicit_memories(user_input, top_k=5)
  context = build_context(self_model_latest, retrieved_units, surfaced_units)
  response = llm.generate(user_input, context)

  experience_id = log_experience(user_input, response, metadata)
  units = classify_memory(experience_id)
  store_memory_units(units)

  if should_run_subconscious_cycle(turn_count, wall_clock):
    run_subconscious_cycle(agent_id, current_context=user_input)

  if turn_count % N == 0:
    run_reflection_cycle(agent_id)
```

Implementation notes:
- Keep retrieval small and explicit (top-K by salience + recency).
- Always include current self-model (compact) in system prompt.
- Do not allow unconstrained prompt growth.

---

## 6) Reflection Loop

Every `N` turns (e.g., 10):

1. Pull recent experiences and active memory units.
2. Ask model to produce:
   - stable user preferences,
   - recurring failure patterns,
   - useful strategies,
   - contradictions needing revision.
3. Save a reflection artifact.
4. Emit candidate self-model edits.

Design rule: reflection is **batch + deliberative**, not per-turn reactive noise.

---

## 7) Self-Model Update Loop

Self-model should be explicit JSON with sections like:
- `identity_claims`
- `user_preferences`
- `task_strategies`
- `known_failure_modes`
- `behavior_policies`
- `confidence_map`

Update algorithm:
1. Read latest model version.
2. Apply reflection-proposed edits with confidence thresholds.
3. Record diff + rationale.
4. Save new version.
5. Use new version in subsequent prompt assembly.

Guardrails:
- Require evidence count > 1 for durable claims.
- Down-weight one-off anomalies.
- Keep reversible history.

---

## 7A) Background Salience Engine: The “Subconscious” Layer

Most agent memory systems retrieve information only when explicitly queried. Human memory often resurfaces indirectly through emotional intensity, repetition, unresolved tension, symbolic similarity, and context-rhyme with the present moment.

Developmental Agent Memory adds a background salience process: a lightweight “subconscious” layer that continuously reweights stored memories and decides which experiences should re-enter working memory.

This layer is not mystical or autonomous consciousness. It is an auditable engineering mechanism for memory resurfacing. Jung is inspiration at the metaphor level; implementation remains disciplined around salience, recurrence, contradiction detection, priming, and latent association.

The Background Salience Engine runs outside the primary chat loop and performs four functions:

1. Re-score memory units based on recency, recurrence, emotional intensity, unresolved tension, and symbolic similarity.
2. Detect unresolved tensions between prior beliefs, corrections, and current behavior.
3. Surface selected memories into a working-memory queue before future responses.
4. Record why each memory resurfaced, making the process testable and auditable.

Pseudo-code:

```python
def subconscious_cycle(agent_id, current_context):
    candidates = memory_store.get_active_memories(agent_id)

    for memory in candidates:
        memory.salience = compute_salience(
            recency=memory.recency,
            recurrence=memory.recurrence_count,
            emotional_intensity=memory.emotional_intensity,
            unresolved_tension=memory.unresolved_tension_score,
            symbolic_similarity=similarity(memory.symbolic_tags, current_context),
            decay=memory.age_decay,
        )

    surfaced = select_top_memories(candidates, k=3)

    store_working_memory_events(
        agent_id=agent_id,
        memories=surfaced,
        source="subconscious_resurfacing",
    )

    return surfaced
```

This changes the core loop from:

`Experience → Reflection → Self-Model Update → Future Behavior`

to:

`Experience → Encoding → Background Salience → Resurfacing → Reflection → Self-Model Update → Future Behavior`

Research hypothesis extension:

LLM agents may need more than retrieval. They may need background memory resurfacing: a process that continuously reweights prior experiences and reintroduces them into working memory when they become psychologically or contextually relevant.

---

## 8) Experiment Design

Compare two conditions on identical tasks:

### A. **Injection Baseline**
- Provide a large fixed memory/context blob at start.
- No staged reflection or self-model revision.

### B. **Developmental Memory (DAM)**
- Start minimal.
- Grow memory from interaction.
- Reflect every `N` turns.
- Update self-model over time.

### Task Suite (lightweight)
- Personalized assistant tasks (preference retention).
- Multi-step planning tasks (strategy continuity).
- Correction tasks (ability to recover from mistakes).
- Long-horizon consistency tasks (identity + policy coherence).

Run each condition over multiple seeded conversations.

---

## 9) Evaluation Metrics

Primary metrics:
- **Coherence**: consistency across turns and across sessions.
- **Personalization**: alignment with user-specific preferences learned in prior turns.
- **Reliability**: error recovery after corrections.

Concrete proxies:
- Preference recall accuracy (% correct preference application).
- Contradiction rate (lower is better).
- Correction retention score (does fix persist after K turns?).
- Self-model stability vs adaptability (diff entropy over time).
- Response quality rubric (LLM judge or human rating).

Track both per-turn and per-stage metrics.

---

## 10) Recommended README / Repo Structure

```text
/docs
  thesis.md
  architecture.md
  benchmark.md
/src
  agent_loop.py
  memory_store.py
  reflection.py
  self_model.py
/benchmarks
  scenarios/
  run_benchmark.py
/data
  demo.sqlite
/results
  run_YYYYMMDD/
README.md
```

README sections should include:
- Thesis
- Architecture diagram
- Quickstart
- Experimental protocol
- Example outputs
- Current limitations
- Roadmap

---

## 11) Simple Tech Stack

Recommended v1 stack:
- **Language**: Python 3.11+
- **LLM access**: OpenAI-compatible API or local model wrapper
- **DB**: SQLite + SQLModel/SQLAlchemy
- **Vector search (optional v1.1)**: FAISS or sqlite-vss
- **Orchestration**: plain Python loops (no heavy framework required)
- **Evaluation**: pandas + small scoring scripts
- **CLI/demo**: Typer + simple terminal chat

Keep dependencies minimal and pinned.

---

## 12) 30-Day Build Roadmap

### Week 1 — Foundations
- Define schemas and repo structure.
- Implement basic chat loop.
- Log experiences to SQLite.
- Add deterministic local test mode.

### Week 2 — Memory + Reflection
- Add memory classification pipeline.
- Implement reflection every `N` turns.
- Persist reflection artifacts.

### Week 3 — Self-Model Revision
- Implement versioned self-model JSON.
- Add diff/rationale tracking.
- Wire self-model into prompt assembly.

### Week 4 — Benchmark + Write-up
- Build A/B harness (Injection vs DAM).
- Run small benchmark suite.
- Generate plots/tables.
- Publish demo + essay + reproducibility notes.

---

## Practical Starting Prompt (for design sessions)

Use this exact prompt with your coding/research assistant to bootstrap implementation:

```text
I want to design an open-source research prototype called Developmental Agent Memory.

The core thesis is:
Most LLM memory systems treat memory as storage and retrieval. But human memory develops through staged experience: early impressions, repeated feedback, failures, corrections, identity formation, skill acquisition, and belief revision.

I want to explore whether LLM agents perform better when memories are accumulated, reflected on, compressed, and revised over time instead of being injected all at once.

Help me design this project from first principles.

Constraints:
- I am not an AI lab.
- I do not want to train a foundation model.
- I want to build a practical prototype using existing LLM APIs or local open-source models.
- The project should be simple enough for one technical founder/engineer to build.
- The repo should be serious, reproducible, and research-adjacent.
- The goal is to produce a GitHub project, demo, essay, and lightweight benchmark.

Please help me define:
1. The core concept and terminology.
2. The memory architecture.
3. The minimum viable prototype.
4. The database schema.
5. The agent loop.
6. The reflection loop.
7. The self-model update loop.
8. The experiment design.
9. The evaluation metrics.
10. The README structure.
11. A simple tech stack.
12. A 30-day build roadmap.

Use clear technical language but explain everything in a way a strong engineer can implement without needing a PhD in machine learning.
```

---

## What to Build First

Keep v1 intentionally small:

User talks to agent  
↓  
Agent responds  
↓  
System logs interaction as an “experience”  
↓  
System classifies and encodes the memory  
↓  
System stores it  
↓  
Background salience engine reweights and resurfaces memories  
↓  
Resurfaced memories are queued into working memory  
↓  
Every `N` interactions, system reflects  
↓  
System updates a self-model  
↓  
Future answers are influenced by self-model + resurfaced memory

This loop is the project:

> **Experience → Encoding → Background Salience → Resurfacing → Reflection → Self-model update → Future behavior**

Design mantra:

> **Memory is not storage. Memory is resurfacing.**

