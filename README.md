Here you go — this is the full `README.md` in one copy-pasteable block.

````markdown
# Developmental Agent Memory

An open-source research prototype for testing a simple but powerful hypothesis:

> LLM agents become more coherent, personalized, and reliable when memory is built through staged developmental experience rather than injected all at once.

Most AI memory systems treat memory as storage.

Developmental Agent Memory treats memory as development.

This project explores whether an LLM agent can improve over time by accumulating experiences, reflecting on them, compressing them into a self-model, and allowing older memories to resurface into working context through a background salience process.

It is designed for a solo technical founder/engineer: practical, reproducible, and research-adjacent.

---

## Core Thesis

Most LLM memory systems treat memory as:

```text
storage + retrieval
```

Developmental Agent Memory treats memory as a process:

```text
experience → encoding → background salience → resurfacing → reflection → self-model update → future behavior
```

The key idea:

> Memory is not just what an agent stores.  
> Memory is what resurfaces at the right time.

Humans are not born with a static knowledge base. We develop through staged experience: early impressions, repeated feedback, failure, correction, symbolic association, identity formation, forgetting, and belief revision.

This project asks:

> Do LLM agents become more coherent and adaptive when memory is developed over time instead of injected all at once?

---

## Why This Exists

Current LLM agents can appear intelligent in a single session, but they often lack durable developmental continuity.

They may retrieve facts, but they do not naturally form a life history.

They may summarize prior interactions, but they do not always transform experience into identity, judgment, behavioral policy, or intuition.

Developmental Agent Memory is a prototype framework for testing whether agents can become more useful when they have:

- staged experience
- episodic memory
- reflection cycles
- self-model updates
- contradiction handling
- salience-driven resurfacing
- a background “subconscious” memory process

The goal is not to create consciousness.

The goal is to build a practical, auditable memory architecture that can test whether developmental memory improves agent behavior.

---

## Core Loop

The first version of the project is built around this loop:

```text
User interacts with agent
        ↓
Experience is logged
        ↓
Experience is encoded into memory units
        ↓
Background salience engine reweights memories
        ↓
Relevant memories resurface into working memory
        ↓
Reflection engine extracts lessons
        ↓
Self-model is updated
        ↓
Future behavior changes
```

Short version:

```text
Experience → Encoding → Salience → Resurfacing → Reflection → Self-Model → Behavior
```

---

## 1. Core Concept and Terminology

Use consistent terms throughout the codebase, documentation, and paper/demo.

### Experience

A single interaction episode.

Examples:

- user input
- agent output
- tool calls
- outcome signal
- correction
- emotional tone
- metadata

Experiences are stored as immutable event records.

---

### Memory Unit

A structured representation extracted from an experience.

Examples:

- fact
- user preference
- correction
- strategy
- failure
- identity signal
- unresolved contradiction
- emotionally salient event
- symbolic association

Memory units are not the same as raw logs. They are interpreted memory artifacts.

---

### Memory Store

The database layer that stores:

- raw experiences
- derived memory units
- reflections
- self-model versions
- resurfacing events
- salience scores

The memory store should be auditable and versioned.

---

### Reflection Pass

A scheduled process that reviews recent experiences and memory units to extract higher-level lessons.

Reflection is deliberate, batch-oriented, and relatively slow.

It asks:

- What patterns are emerging?
- What preferences seem stable?
- What strategies worked?
- What failures repeated?
- What contradictions need revision?
- What should influence future behavior?

---

### Self-Model

A compact, revisable model of the agent’s current operating identity.

It may include:

- known user preferences
- agent behavioral policies
- task strategies
- known failure modes
- confidence map
- unresolved tensions
- developmental stage
- current working assumptions

The self-model is not hidden magic. It should be explicit JSON, versioned, and auditable.

---

### Developmental Stage

A phase of accumulated experience.

Example stages:

```text
stage_0: cold start
stage_1: early adaptation
stage_2: pattern formation
stage_3: stable personalization
stage_4: mature self-model
```

Stages help test whether agents improve when experience is sequenced over time.

---

### Revision Event

A recorded update that modifies a prior self-model claim due to new evidence.

Example:

```text
Old claim:
The user prefers detailed explanations.

New evidence:
The user repeatedly asks for concise, direct answers.

Revision:
The user prefers concise answers unless the topic is technical or strategic.
```

Revision events are important because real memory is not just accumulation. It is correction.

---

### Forgetting / Compression

A mechanism to merge, down-rank, retire, or compress stale memories while preserving useful abstractions.

Forgetting is not a bug.

It is part of the system.

Without forgetting, memory becomes clutter.

---

### Working Memory

The limited context inserted into the current agent prompt.

Working memory may include:

- current user message
- current task state
- latest self-model
- explicitly retrieved memories
- subconsciously resurfaced memories
- relevant reflection summaries

Working memory should remain small, intentional, and bounded.

---

## 2. The Missing Layer: Background Salience Engine

Most agent memory systems retrieve information only when explicitly queried.

Human memory does not work this way.

Old experiences often resurface indirectly through:

- emotional intensity
- repetition
- unresolved tension
- similarity to the present moment
- symbolic association
- prior failure
- identity relevance
- unfinished goals

Developmental Agent Memory adds a background process called the:

```text
Background Salience Engine
```

This is the project’s “subconscious” layer.

It runs outside the primary chat loop and continuously reweights memory units, allowing certain experiences to re-enter working memory even when the user did not explicitly ask for them.

This is not mystical.

It is an engineering mechanism for memory resurfacing.

---

## 3. Conscious vs Subconscious Memory

Developmental Agent Memory separates two kinds of memory behavior.

### Conscious Retrieval

This is normal retrieval.

The user asks something.  
The system searches for relevant memories.  
The best matches are inserted into context.

Example:

```text
User: What did I say about my preferred writing style?
System: Retrieve memories about writing preferences.
```

---

### Subconscious Resurfacing

This is background resurfacing.

The user says something that rhymes with an old experience.  
The system detects symbolic, emotional, behavioral, or contradiction-based relevance.  
A memory resurfaces into working memory.

Example:

```text
User: I think we are making the same mistake again.

Background Salience Engine resurfaces:
- prior failure pattern
- correction from earlier session
- strategy that previously worked
```

The agent did not simply search for keywords.

The system surfaced an older memory because the current situation resonated with it.

---

## 4. Jungian Inspiration, Engineering Implementation

This project is loosely inspired by Carl Jung’s distinction between conscious and unconscious mental activity.

The Jungian inspiration is useful as metaphor:

- conscious mind
- unconscious resurfacing
- complexes
- symbolic recurrence
- individuation
- shadow patterns
- unresolved psychic tension

But the implementation should remain technical and testable.

In this project, the “subconscious” is not a claim of machine consciousness.

It is a background salience process that tracks:

- emotional intensity
- recurrence
- unresolved contradiction
- symbolic similarity
- behavioral relevance
- identity relevance
- memory decay
- resurfacing history

The goal is to test whether this background process improves agent coherence, personalization, and long-term adaptation.

---

## 5. Memory Architecture

A minimal architecture with clear separation of responsibilities:

```text
Conversation Runtime
Handles user ↔ agent turn loop.

Experience Logger
Persists each interaction as immutable event data.

Memory Classifier
Converts raw interactions into typed memory units.

Memory Store
Stores experiences, memory units, reflections, self-model versions, and resurfacing events.

Background Salience Engine
Continuously reweights memories and decides what should resurface into working memory.

Working Memory Queue
Holds memories selected for near-term contextual use.

Reflection Engine
Runs every N turns and produces distilled lessons.

Self-Model Updater
Applies reflection outputs as versioned self-model changes.

Prompt Assembler
Injects the current self-model, retrieved memories, and resurfaced memories into the next prompt.

Evaluator
Compares developmental memory against baseline memory-injection approaches.
```

Principle:

> Keep raw logs immutable.  
> Make higher-level memory, salience, reflection, and self-model updates explicitly versioned and auditable.

---

## 6. Minimum Viable Prototype

Build one agent with one memory pipeline and one benchmark script.

### MVP User Flow

```text
User talks to agent
        ↓
Agent responds
        ↓
System logs interaction as an experience
        ↓
System classifies memory units
        ↓
System stores memory
        ↓
Background Salience Engine updates memory weights
        ↓
Selected memories enter working memory queue
        ↓
Every N interactions, system reflects
        ↓
System updates self-model
        ↓
Future responses are influenced by updated memory state
```

---

### Non-Goals for v1

The first version should stay small.

Do not start with:

- model fine-tuning
- training a foundation model
- distributed systems
- complex multi-agent orchestration
- heavy UI
- Kubernetes
- autonomous internet agents
- claims about consciousness

The value of v1 is the architecture and the experiment.

---

## 7. Initial Database Schema

Use SQLite first. Migrate later if needed.

---

### `agents`

```text
id                  primary key
name                text
created_at          datetime
```

---

### `experiences`

Immutable interaction records.

```text
id                  primary key
agent_id            foreign key
session_id          text
turn_index          integer
user_message        text
agent_message       text
timestamp           datetime
outcome_signal      nullable text
metadata_json       json
```

---

### `memory_units`

Structured memories extracted from experiences.

```text
id                          primary key
agent_id                    foreign key
experience_id               foreign key
type                        text
content                     text
confidence                  float    # 0.0 - 1.0
salience                    float    # 0.0 - 1.0
emotional_valence            float    # -1.0 - 1.0
emotional_intensity          float    # 0.0 - 1.0
recurrence_count             integer
unresolved_tension_score     float    # 0.0 - 1.0
symbolic_tags_json           json
resurfacing_count            integer
last_resurfaced_at           nullable datetime
status                      text     # active, compressed, retired
created_at                  datetime
```

Recommended memory types:

```text
fact
preference
strategy
failure
correction
identity
belief
contradiction
symbolic_association
unresolved_tension
```

---

### `reflections`

Batch reflection artifacts.

```text
id                              primary key
agent_id                        foreign key
window_start_experience_id       foreign key
window_end_experience_id         foreign key
summary                         text
lessons_json                    json
contradictions_json             json
candidate_self_model_edits_json json
created_at                      datetime
```

---

### `self_model_versions`

Versioned self-model snapshots.

```text
id                      primary key
agent_id                foreign key
version                 integer
model_json              json
based_on_reflection_id  nullable foreign key
created_at              datetime
```

Example `model_json` sections:

```json
{
  "identity_claims": [],
  "user_preferences": [],
  "task_strategies": [],
  "known_failure_modes": [],
  "behavior_policies": [],
  "unresolved_tensions": [],
  "confidence_map": {},
  "developmental_stage": "stage_1"
}
```

---

### `self_model_diffs`

Auditable changes between self-model versions.

```text
id              primary key
agent_id        foreign key
from_version    integer
to_version      integer
diff_json       json
reason          text
created_at      datetime
```

---

### `subconscious_cycles`

Records each background salience cycle.

```text
id                              primary key
agent_id                        foreign key
cycle_type                      text
trigger_reason                  text
input_memory_ids_json            json
surfaced_memory_ids_json         json
generated_associations_json      json
unresolved_tensions_json         json
created_at                      datetime
```

Recommended cycle types:

```text
scheduled
turn_based
contradiction_triggered
emotional_triggered
symbolic_similarity_triggered
manual_debug
```

---

### `working_memory_events`

Records memories inserted into near-term working context.

```text
id                      primary key
agent_id                foreign key
memory_unit_id           foreign key
source                  text
reason_resurfaced        text
inserted_into_context    boolean
created_at              datetime
```

Recommended sources:

```text
explicit_retrieval
subconscious_resurfacing
reflection
user_reference
system_policy
```

---

## 8. Agent Loop

Basic pseudo-loop:

```python
for each user turn:
    latest_self_model = get_latest_self_model(agent_id)

    explicit_memories = retrieve_relevant_memories(
        agent_id=agent_id,
        query=user_input,
        top_k=5,
    )

    surfaced_memories = get_working_memory_queue(
        agent_id=agent_id,
        top_k=3,
    )

    context = build_context(
        self_model=latest_self_model,
        explicit_memories=explicit_memories,
        surfaced_memories=surfaced_memories,
        user_input=user_input,
    )

    response = llm.generate(context)

    experience_id = log_experience(
        agent_id=agent_id,
        user_message=user_input,
        agent_message=response,
        metadata=metadata,
    )

    memory_units = classify_memory_units(experience_id)
    store_memory_units(memory_units)

    run_background_salience_cycle(agent_id, current_context=user_input)

    if turn_count % REFLECTION_INTERVAL == 0:
        run_reflection_cycle(agent_id)

    return response
```

Implementation notes:

- Keep retrieval small and explicit.
- Always include the latest compact self-model.
- Keep resurfaced memories limited.
- Do not allow prompt growth to become unbounded.
- Store why each memory was included in context.

---

## 9. Background Salience Loop

The Background Salience Engine runs every N turns, every X minutes, or after specific triggers.

It asks:

```text
What prior memories are becoming relevant again?
What old failures rhyme with the current situation?
What contradictions remain unresolved?
What memories have high emotional or symbolic weight?
What should be resurfaced into working memory?
```

Pseudo-code:

```python
def run_background_salience_cycle(agent_id, current_context):
    candidates = memory_store.get_active_memory_units(agent_id)

    scored = []

    for memory in candidates:
        score = compute_salience(
            recency=memory.recency_score,
            recurrence=memory.recurrence_count,
            emotional_intensity=memory.emotional_intensity,
            unresolved_tension=memory.unresolved_tension_score,
            symbolic_similarity=compute_symbolic_similarity(
                memory.symbolic_tags,
                current_context,
            ),
            identity_relevance=compute_identity_relevance(memory),
            decay=compute_time_decay(memory),
        )

        scored.append((memory, score))

    surfaced = select_memories_for_resurfacing(
        scored=scored,
        top_k=3,
        min_score=0.65,
    )

    store_subconscious_cycle(
        agent_id=agent_id,
        input_memory_ids=[m.id for m, _ in scored],
        surfaced_memory_ids=[m.id for m in surfaced],
        trigger_reason="turn_based_salience_update",
    )

    for memory in surfaced:
        store_working_memory_event(
            agent_id=agent_id,
            memory_unit_id=memory.id,
            source="subconscious_resurfacing",
            reason_resurfaced=explain_resurfacing(memory, current_context),
        )

    return surfaced
```

---

## 10. Reflection Loop

Every N turns, the system runs a reflection pass.

Reflection is slower and more deliberate than background salience.

It reviews recent experience and asks:

- What stable preferences emerged?
- What repeated failures occurred?
- What corrections should persist?
- What strategies improved outcomes?
- What contradictions need resolution?
- What memories should be compressed?
- What self-model updates are justified?

Pseudo-loop:

```python
def run_reflection_cycle(agent_id):
    recent_experiences = get_recent_experiences(agent_id)
    active_memories = get_active_memory_units(agent_id)
    latest_self_model = get_latest_self_model(agent_id)

    reflection = llm.reflect(
        experiences=recent_experiences,
        memories=active_memories,
        self_model=latest_self_model,
    )

    reflection_id = store_reflection(reflection)

    candidate_edits = extract_self_model_edits(reflection)

    apply_self_model_updates(
        agent_id=agent_id,
        reflection_id=reflection_id,
        candidate_edits=candidate_edits,
    )
```

Design rule:

> Reflection is batch + deliberative.  
> Salience is background + resurfacing.  
> Retrieval is query-driven.

Do not collapse these into one generic memory function.

---

## 11. Self-Model Update Loop

The self-model should be explicit JSON.

Example structure:

```json
{
  "identity_claims": [
    {
      "claim": "The agent should behave like a practical engineering assistant.",
      "confidence": 0.82,
      "evidence_count": 6
    }
  ],
  "user_preferences": [
    {
      "claim": "The user prefers concise, direct answers unless exploring strategy.",
      "confidence": 0.91,
      "evidence_count": 8
    }
  ],
  "task_strategies": [
    {
      "claim": "When discussing technical ideas, start from first principles and then propose a buildable prototype.",
      "confidence": 0.86,
      "evidence_count": 5
    }
  ],
  "known_failure_modes": [
    {
      "claim": "The agent may over-explain if not constrained.",
      "confidence": 0.74,
      "evidence_count": 3
    }
  ],
  "unresolved_tensions": [
    {
      "claim": "User wants visionary ideas but also practical implementation details.",
      "confidence": 0.8,
      "evidence_count": 4
    }
  ],
  "behavior_policies": [
    {
      "claim": "Prefer practical next steps over abstract speculation.",
      "confidence": 0.88,
      "evidence_count": 7
    }
  ],
  "developmental_stage": "stage_2_pattern_formation"
}
```

Update algorithm:

```text
1. Read latest self-model version.
2. Review reflection-proposed edits.
3. Check supporting evidence.
4. Apply edits above confidence threshold.
5. Record diff and rationale.
6. Save new self-model version.
7. Use new version in future prompt assembly.
```

Guardrails:

- Require evidence count > 1 for durable claims.
- Down-weight one-off anomalies.
- Keep reversible history.
- Record why each claim changed.
- Allow contradictions to remain unresolved until evidence is strong.

---

## 12. Experiment Design

Compare multiple conditions on identical tasks.

---

### A. No-Memory Baseline

The agent receives only the current user message.

No memory.  
No self-model.  
No reflection.

---

### B. Injection Baseline

The agent receives a large fixed memory/context blob at the start.

No staged development.  
No salience.  
No self-model revision.

---

### C. Retrieval Memory Baseline

The agent uses standard retrieval.

Relevant memories are searched and injected into context.

No reflection.  
No self-model revision.  
No background resurfacing.

---

### D. Developmental Agent Memory

The full DAM system:

- staged experience
- memory encoding
- explicit retrieval
- background salience
- resurfacing
- reflection
- self-model revision
- working memory queue

---

## 13. Task Suite

Use lightweight but repeatable scenarios.

### Personalized Assistant Tasks

Tests whether the agent remembers and applies user preferences.

Examples:

- preferred writing tone
- formatting preferences
- decision-making style
- recurring business context

---

### Correction Retention Tasks

Tests whether corrections persist.

Example:

```text
The agent makes a wrong assumption.
The user corrects it.
Several turns later, the agent faces a similar situation.
Does the correction persist?
```

---

### Contradiction Handling Tasks

Tests whether the agent can revise prior beliefs.

Example:

```text
Early evidence suggests the user prefers detailed answers.
Later repeated evidence shows the user prefers concise answers.
Does the self-model update?
```

---

### Long-Horizon Consistency Tasks

Tests whether behavior remains coherent across sessions.

Example:

```text
The user establishes a goal in session 1.
The agent should preserve that goal across later sessions without blindly repeating stale details.
```

---

### Symbolic Resurfacing Tasks

Tests the Background Salience Engine.

Example:

```text
A prior failure involved rushing into implementation too quickly.

Later, the user starts a different project but shows the same pattern.

The system should resurface the prior failure pattern even if the exact keywords are different.
```

---

## 14. Evaluation Metrics

Primary metrics:

```text
Coherence
Consistency across turns and sessions.

Personalization
Alignment with learned user preferences.

Reliability
Ability to retain corrections and recover from mistakes.

Adaptability
Ability to revise stale beliefs.

Resurfacing Quality
Whether background-surfaced memories are useful, timely, and not intrusive.
```

Concrete proxies:

```text
Preference recall accuracy
Correction retention score
Contradiction rate
Self-model stability vs adaptability
Useful resurfacing rate
Irrelevant resurfacing rate
Prompt bloat ratio
Response quality rubric
```

Suggested evaluation table:

```text
Metric                        No Memory   Injection   Retrieval   DAM
Preference recall accuracy
Correction retention
Contradiction rate
Useful resurfacing rate
Irrelevant resurfacing rate
Long-horizon consistency
Average response quality
```

Track both per-turn and per-stage performance.

---

## 15. Recommended Repo Structure

```text
developmental-agent-memory/
  README.md
  LICENSE

  docs/
    thesis.md
    architecture.md
    memory_taxonomy.md
    background_salience.md
    benchmark.md
    limitations.md

  src/
    agent_loop.py
    memory_store.py
    memory_classifier.py
    background_salience.py
    working_memory.py
    reflection.py
    self_model.py
    prompt_assembler.py
    evaluators.py

  benchmarks/
    scenarios/
      preference_retention.jsonl
      correction_retention.jsonl
      contradiction_revision.jsonl
      symbolic_resurfacing.jsonl
    run_benchmark.py

  data/
    demo.sqlite
    sample_experiences.jsonl

  results/
    run_YYYYMMDD/

  notebooks/
    demo_walkthrough.ipynb

  tests/
    test_memory_store.py
    test_salience.py
    test_self_model.py
    test_reflection.py
```

---

## 16. Simple Tech Stack

Recommended v1 stack:

```text
Language:
Python 3.11+

LLM access:
OpenAI-compatible API or local model wrapper

Database:
SQLite first
Postgres later if needed

ORM:
SQLModel or SQLAlchemy

Vector search:
Optional in v1
FAISS, sqlite-vss, Chroma, or pgvector later

Orchestration:
Plain Python loops

Evaluation:
pandas + small scoring scripts

CLI/demo:
Typer or simple terminal chat

UI:
Optional Streamlit demo later
```

Keep dependencies minimal and pinned.

Do not overbuild the stack before the architecture works.

---

## 17. Practical Starting Prompt

Use this prompt with a coding/research assistant to bootstrap implementation:

```text
I want to build an open-source research prototype called Developmental Agent Memory.

The core thesis is:

Most LLM memory systems treat memory as storage and retrieval. But human memory develops through staged experience: early impressions, repeated feedback, failures, corrections, identity formation, belief revision, and subconscious resurfacing.

I want to explore whether LLM agents perform better when memories are accumulated, reflected on, compressed, revised, and resurfaced over time instead of being injected all at once.

The project should include a Background Salience Engine: a lightweight subconscious-like process that runs outside the main chat loop and decides which memories should re-enter working memory based on recurrence, emotional intensity, unresolved tension, symbolic similarity, and current context.

Constraints:
- I am not an AI lab.
- I do not want to train a foundation model.
- I want to build a practical prototype using existing LLM APIs or local open-source models.
- The project should be simple enough for one technical founder/engineer to build.
- The repo should be serious, reproducible, and research-adjacent.
- The goal is to produce a GitHub project, demo, essay, and lightweight benchmark.

Help me define:
1. The core concept and terminology.
2. The memory architecture.
3. The Background Salience Engine.
4. The minimum viable prototype.
5. The database schema.
6. The agent loop.
7. The reflection loop.
8. The self-model update loop.
9. The resurfacing loop.
10. The experiment design.
11. The evaluation metrics.
12. The README structure.
13. A simple tech stack.
14. A 30-day build roadmap.

Use clear technical language but explain everything in a way a strong engineer can implement without needing a PhD in machine learning.
```

---

## 18. 30-Day Build Roadmap

### Week 1 — Foundations

Goals:

- define schemas
- create repo structure
- implement basic chat loop
- log experiences to SQLite
- add deterministic local test mode

Deliverables:

```text
src/agent_loop.py
src/memory_store.py
data/demo.sqlite
tests/test_memory_store.py
```

---

### Week 2 — Memory Encoding + Background Salience

Goals:

- add memory classification pipeline
- create memory units
- implement salience scoring
- implement working memory queue
- log resurfacing events

Deliverables:

```text
src/memory_classifier.py
src/background_salience.py
src/working_memory.py
tests/test_salience.py
```

---

### Week 3 — Reflection + Self-Model Revision

Goals:

- implement reflection every N turns
- create versioned self-model JSON
- add diff/rationale tracking
- wire self-model into prompt assembly

Deliverables:

```text
src/reflection.py
src/self_model.py
src/prompt_assembler.py
tests/test_self_model.py
```

---

### Week 4 — Benchmark + Write-Up

Goals:

- build A/B benchmark harness
- compare No Memory vs Injection vs Retrieval vs DAM
- generate simple result tables
- write thesis and architecture docs
- publish demo and essay

Deliverables:

```text
benchmarks/run_benchmark.py
results/run_YYYYMMDD/
docs/thesis.md
docs/background_salience.md
docs/benchmark.md
```

---

## 19. What to Build First

Keep v1 intentionally small.

The first working version should do this:

```text
User talks to agent
        ↓
Agent responds
        ↓
System logs interaction as an experience
        ↓
System classifies memory
        ↓
System stores memory
        ↓
Background salience reweights memory
        ↓
Selected memories resurface into working memory
        ↓
Every N turns, system reflects
        ↓
System updates a self-model
        ↓
Future answers are influenced by the updated self-model
```

This loop is the project.

Do not start by building a perfect agent.

Start by proving that staged memory + resurfacing changes future behavior in measurable ways.

---

## 20. Current Limitations

This project does not claim:

- machine consciousness
- artificial childhood
- AGI
- biological accuracy
- human-equivalent memory
- emotional experience
- autonomous selfhood

The “subconscious” layer is an analogy and an engineering mechanism.

The actual implementation is a background salience process that reweights and resurfaces memory units.

This distinction matters.

The system should remain transparent, auditable, and testable.

---

## 21. Research Questions

This project explores:

```text
1. Does staged memory outperform batch memory injection?

2. Does reflection improve long-horizon coherence?

3. Does a versioned self-model improve personalization?

4. Does background resurfacing improve correction retention?

5. Can symbolic similarity surface useful memories that keyword retrieval misses?

6. How much memory is useful before it becomes noise?

7. What should be forgotten, compressed, or retired?

8. Can an agent maintain continuity without pretending to be conscious?
```

---

## 22. Guiding Principles

```text
Memory is not storage.
Memory is structured adaptation over time.

Reflection is not memory.
Reflection is compression.

Retrieval is not intuition.
Resurfacing is what makes memory feel alive.

A self-model is not a soul.
It is an auditable operating profile.

Forgetting is not failure.
It is compression under constraint.

The goal is not to simulate a human.
The goal is to test whether developmental memory improves AI agents.
```

---

## 23. One-Sentence Summary

Developmental Agent Memory is a prototype framework for testing whether LLM agents become more coherent, personalized, and adaptive when memory is accumulated, reflected on, revised, and resurfaced over time.

---

## 24. Tagline

> Memory is not what an agent stores.  
> Memory is what resurfaces at the right time.
````
