# GESA: Generative Episodic Simulated Annealing — The Optimization Layer of Agentic Systems

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20533843.svg)](https://doi.org/10.5281/zenodo.20533843)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Status: Published](https://img.shields.io/badge/Status-Published-brightgreen.svg)](https://doi.org/10.5281/zenodo.20533843)

**Author:** Michael Shatny  
**ORCID:** [0009-0006-2011-3258](https://orcid.org/0009-0006-2011-3258)  
**Version:** 0.1.0 (working draft — not yet peer-reviewed)  
**Date:** June 2026  
**Related:** [Semantic Intent as Governance Primitive](https://doi.org/10.5281/zenodo.20436088) · [Agentic Traffic Control](https://doi.org/10.5281/zenodo.20533774) · [Documentation](https://gesa.semanticintent.dev)

---

*Michael Shatny — June 2026*

---

## Abstract

**Background:** Intelligent systems that sense their environment (Cormorant 3D), measure gaps (DRIFT), and decide to act (Fetch) still face a fundamental limitation: they do not improve. Each decision cycle begins without memory of prior outcomes. Failures are not learned from. Successes are not reinforced. The system is reactive rather than adaptive.

**Problem:** As AI systems move from single-agent to multi-agent enterprise deployments, the absence of a learning layer produces a compounding problem. Not only does the system fail to improve across runs — it actively accumulates a history of outcomes it cannot access. Valuable signal is discarded.

**Contribution:** This paper formalizes GESA (Generative Episodic Simulated Annealing) — the optimization layer of agentic systems. GESA introduces five components: an immutable episode store, a temperature schedule governing exploration vs exploitation, a weighted similarity function for episode retrieval, a generative synthesis step producing candidate strategies, and a cooling profile determining the system's learning trajectory. These components operate as an 8-step loop that converts every action-and-outcome into a retrievable episode and every retrieved episode set into a better candidate strategy. GESA was accumulated from practice before it was designed — first implemented inside a production 11-model AI orchestration pipeline, then recognized as a generalizable pattern, then named. The implementation evidence is production, not theoretical.

**Keywords:** generative episodic memory, simulated annealing, agentic optimization, multi-agent learning, temperature schedule, episode retrieval, exploration vs exploitation, cormorant foraging, DRIFT, Fetch

---

## 1. The Problem

Every system that senses, measures, and acts faces the same question after the action: *was that right?*

Most systems answer by discarding the question. The action is taken, the outcome is observed (if at all), and the next cycle begins from the same starting position — with no memory of what just happened, what led to it, or what a similar situation produced the last time it was encountered.

This is not a failure of intelligence. It is a failure of architecture. The signals are present. The outcomes are observable. The patterns are there to be learned. What is missing is the structural mechanism that captures those outcomes, stores them as retrievable episodes, and generates improved strategies from their aggregate.

The absence of this mechanism produces three compounding failures across any sufficiently long-running agentic system:

**Decision amnesia** — the system repeats mistakes it has already made, because the mistake was never stored as an episode it could retrieve.

**Drift blindness** — the system cannot detect that its performance has been declining across runs, because it has no historical baseline to measure against.

**Convergence failure** — the system cannot improve its strategy over time, because improvement requires both memory of prior strategies and a mechanism for generating variations.

These failures appear at every scale: a single AI agent in a loop, a multi-agent pipeline processing enterprise workloads, a strategic advisory system running 11 AI model orchestrations per report. The specific technology varies. The structural failure is identical.

GESA is the architecture that addresses all three.

---

## 2. Background: 28 Years Compressed

The insight that became GESA was not a single moment of invention. It was a gradual recognition across 28 years of consulting engagements spanning enterprise systems, billing platforms, operational infrastructure, and AI-driven products:

> Organizations accumulate decisions *without memory*, drift *without detection*, and repeat failures *without learning*.

The specific technology changed across every engagement. The structural problem did not.

What looked like a data integrity problem in one engagement was a strategic drift problem in another, and a learning failure in a third. The same architecture addressed all three: capture the episode, retrieve similar episodes, generate a better response, store the outcome, cool toward exploitation over time.

The pattern was implicit in practice for decades before it was explicit in code.

### 2.1 First Formalization — StratIQX

GESA became structural rather than intuitive through the development of StratIQX — an AI-powered strategic consulting platform built to deliver 6D cascade analysis, DRIFT scoring, and executive intelligence at production speed and scale.

The architecture that made GESA necessary:

```
Worker → Worker → Queue → Background Job → AI Model (×11) → Azure/Claude → LaTeX → PDF
   ↓         ↓        ↓          ↓               ↓                ↓            ↓
Episode   Episode  Episode    Episode         Episode           Episode      Episode
```

Eleven AI model orchestrations run in sequence and in parallel to produce a single executive consulting report. Each model call is stateless. Each crosses service boundaries. Each produces output the next model depends on.

In this architecture, failure is rarely where it appears. A degraded PDF output may trace back to a timeout in a background job that fed a context window that informed a synthesis model three hops earlier. Without deliberate episodic capture across the full chain, that bottleneck is invisible.

Every node in the chain became an episode. Every episode carried: timestamp, duration, input hash, output hash, model used, token count, queue depth at time of call, downstream dependency status.

The revelation: **the pattern of episodes across many runs began to predict bottlenecks before they occurred.** That is not logging. That is GESA.

### 2.2 The Semantic Anchoring Breakthrough

The most precise moment in GESA's formation came from a production bug. The StratIQX platform was generating executive briefs and full reports as identical PDF files (486,337 bytes each) despite their content being radically different in length and scope.

Root cause traced to a single line:

```typescript
// THE VIOLATION — structural proxy instead of semantic meaning
const isExecutiveBrief = analysisDepth === 'quick';
```

A structural proxy (`analysisDepth`) had silently substituted for semantic meaning (`document type`). Both documents were running through the same code path because neither was `quick` depth.

The fix:

```typescript
// THE FIX — anchor on what the document IS, not how it was generated
const semanticExecutiveVersion = title.includes('executive') || title.includes('brief');
```

**Result:** First-ever page differentiation — 9 pages vs. 16 pages (78% size difference).

Three principles emerged from this debugging session:

1. **Semantic over Structural** — base behaviour on meaning, not technical characteristics
2. **Intent Preservation** — maintain semantic intent through all transformation layers
3. **Observable Anchoring** — behaviour must be derivable from directly observable properties

These principles are now the foundation of GESA's immutability requirement: if episode metadata can be mutated after capture, the retrieval and generation layers build on unreliable ground. **Immutable episodes are not a design preference — they are a correctness requirement.**

### 2.3 The Roadmap That Named Itself

Four independent internal documents within the StratIQX platform each described the same missing capability without naming it:

| Document | Enhancement Language | GESA Step |
|----------|---------------------|-----------|
| Execution Logging | "Identify patterns in failures" | RETRIEVE |
| Workflow Model | "Failure Pattern Analysis" | RETRIEVE |
| Quality Score | "ML Quality Prediction" | GENERATE |
| Quality Score | "Adaptive scoring algorithms" | ANNEAL |
| Quality Score | "Client preference learning" | SELECT |

The system had been writing its own next chapter. GESA is the architecture that names and formalises what the roadmap was reaching toward.

---

## 3. GESA: Definition and Components

GESA (Generative Episodic Simulated Annealing) is defined as:

> **An optimization layer for agentic systems that converts action-outcome pairs into retrievable episodes, synthesizes improved candidate strategies from episode history, and governs the exploration-exploitation tradeoff using a simulated annealing temperature schedule.**

Five components are required:

### 3.1 Episode Store

The episode store is the persistent memory of the system. Every action-outcome pair is recorded as an immutable episode. An episode contains:

- **Context fingerprint** — the state at time of action (DRIFT score, domain, dimension, temperature)
- **Action taken** — the strategy selected and executed
- **Outcome observed** — DRIFT after action, gap change, success/failure
- **Provenance** — timestamp, authorship (human / AI agent / AI compositor), temperature at time of decision

Immutability is structural: episodes are written once and never modified. Corrections are new episodes with causal references to the episode they correct.

Temporal decay applies at retrieval: `EpisodeWeight = BaseWeight × e^(-age/τ)`. Recent episodes carry more weight. Stale episodes are not deleted — they are down-weighted.

### 3.2 Temperature Schedule

Temperature governs how exploratory or conservative the system is at any point in its history. The base formula:

```
Temperature(t) = T₀ × α^t

Where:
  T₀  = 100 (initial temperature — full exploration)
  α   = cooling rate (0 < α < 1, profile-dependent)
  t   = episode count
```

As episodes accumulate, temperature decreases. The system earns conservatism through experience.

**Temperature only decreases when a new episode is stored.** No episode, no cooling. Inactive periods do not artificially advance the system toward exploitation.

### 3.3 Similarity Function

Episode retrieval uses a weighted similarity function across five dimensions:

```
Similarity(e₁, e₂) =
    0.30 × DomainMatch
  + 0.25 × DriftProximity
  + 0.20 × DimensionMatch
  + 0.15 × TemperatureProximity
  + 0.10 × OutcomePolarity
```

Both successful and failed episodes are retrieved. Failed episodes constrain generation; successful episodes inform it.

### 3.4 Generator

The generator synthesizes candidate strategies from retrieved episodes and current context. It produces *variations and combinations*, not exact replays of past episodes. A candidate may combine patterns from three different historical episodes in a way that never occurred before.

At high temperature: the generator includes novel, less-proven candidates.
At low temperature: the generator is constrained to historically-validated strategies.

Each candidate carries full provenance: which episodes supported it, what temperature it was generated under, what alternatives were considered.

### 3.5 Cooling Profile

Four built-in profiles govern the cooling rate:

| Profile | α | Best For |
|---------|---|----------|
| Fast Cool | 0.85 | Domain well-understood; convergence speed matters |
| Standard | 0.95 | Default; balanced exploration and exploitation |
| Slow Cool | 0.99 | Problem space unknown; premature convergence risk high |
| Adaptive | f(variance) | Self-tuning based on episode outcome variance |

The Adaptive profile adjusts cooling rate dynamically: high outcome variance slows cooling; consistent outcomes accelerate it.

---

## 4. The 8-Step Loop

GESA operates as a continuous 8-step cycle:

```
OBSERVE current state → RETRIEVE similar episodes → GENERATE candidates
        ↑                                                       ↓
      COOL ←── STORE new episode ←── ACT ←── SELECT ←── ANNEAL
```

**Step 1 — OBSERVE:** Read current system state: DRIFT score and sign, Fetch threshold, domain, dimension, gap velocity (the direction DRIFT has been moving across recent episodes).

**Step 2 — RETRIEVE:** Search episode memory for situations with similar context fingerprint. Apply temporal decay weighting. Return both successful and failed episodes.

**Step 3 — GENERATE:** Synthesize candidate strategies from retrieved episodes and current context. At high temperature, include novel candidates. At low temperature, constrain to historically-supported strategies.

**Step 4 — ANNEAL:** Filter candidates by current temperature. Higher temperature = more candidates included. Lower temperature = only candidates with sufficient episodic support and confidence survive.

**Step 5 — SELECT:** Score remaining candidates. Gap velocity adjustment: widening gap weights higher-confidence strategies more heavily. Pick the highest-scoring candidate that survived the ANNEAL filter.

**Step 6 — ACT:** Execute or return the selected strategy. Full provenance accompanies every recommendation.

**Step 7 — STORE:** Write the new episode. Phase A captures context and action at time of execution. Phase B captures outcome when observable. Incomplete episodes (Phase A only) are excluded from retrieval.

**Step 8 — COOL:** Advance the temperature schedule. Temperature decreases only when Step 7 completes.

### 4.1 Three Loop Invariants

Three invariants must hold at every cycle:

1. **Every ACT produces an episode.** No action without capture.
2. **Every episode is immutable.** No retroactive modification of what happened.
3. **Temperature only decreases.** The system earns conservatism through experience; it cannot reset to exploration without explicit intervention.

Violation of invariants 1 or 2 breaks the learning guarantee. Violation of invariant 3 produces oscillation between exploration and exploitation — the system never converges.

---

## 5. Simulated Annealing as Governing Mechanism

In metallurgy, annealing is the process of heating a metal and cooling it slowly. Fast cooling produces brittle, disordered structure — the atoms lock into the first stable configuration they find, which may not be optimal. Slow, controlled cooling gives atoms time to explore alternative arrangements and find the most stable overall structure.

In optimization, this maps directly to the exploration-exploitation tradeoff:

- **High temperature** — accept suboptimal moves, explore widely, escape local optima
- **Low temperature** — narrow toward proven solutions, exploit what works

GESA's temperature schedule is this mechanism applied to agentic decision-making. The system earns the right to be conservative by exploring first. A system that begins at full exploitation has no basis for its conservatism — it has not learned what it is exploiting toward.

### 5.1 The Commercial Temperature Schedule

Before GESA was named, the temperature schedule was already running in production as StratIQX's four-tier depth system:

| Depth Tier | Tokens/Section | GESA Temperature | Commercial Range |
|------------|----------------|-----------------|-----------------|
| Quick | 512 | Low — exploitation | $1.25K–$2.5K |
| Standard | 1,024 | Medium | $5K–$10K |
| Comprehensive | 2,048 | High | $15K–$37.5K |
| Enterprise | 2,048+ | Maximum — exploration | $50K–$100K |

Higher temperature = wider search space = higher value = higher price. The annealing schedule has a commercial model. This is not coincidence — it is the correct relationship between exploration and value.

### 5.2 Biological Correspondence

Young cormorants explore widely — diving in varied locations, trying different techniques, accepting failed dives as learning. Experienced cormorants exploit proven hunting zones. The same species, the same biology, different temperatures.

```
Young cormorant   →  T = 90    Explore widely, accept failures
Adult cormorant   →  T = 20    Exploit proven hunting grounds
Expert cormorant  →  T =  5    Near-certain dives in known zones
```

GESA is the translation of this 200-million-year evolutionary annealing schedule into a deliberate, observable, tunable system.

---

## 6. Implementation Evidence

GESA is not theoretical. The following components are live in production in the StratIQX platform as of June 2026:

| Component | Status | Implementation |
|-----------|--------|---------------|
| Episode schema | ✅ Live | D1 `orchestrator_processing_log` |
| Episode capture | ✅ Live | WorkflowLogger, 12 steps, non-blocking |
| Active episode buffer | ✅ Live | KV `EXECUTION_LOGS`, 7-day TTL |
| Long-term episode store | ✅ Live | D1 persistent database |
| Temporal decay | ✅ Live | KV TTL + D1 retention policy |
| DRIFT measurement | ✅ Live | Quality Score System, 85–100 scale |
| Gap velocity tracking | ✅ Live | Quality Analytics, monthly trends |
| 6D dimensional agents | ✅ Live | 8-agent orchestrator, per-dimension |
| Temperature schedule | ✅ Live | 4-tier depth system, 512→2048 tokens |
| Synthesis / GENERATE | ✅ Live | Synthesis Engine, cross-agent patterns |
| Semantic anchoring | ✅ Live | Observable properties on every output |
| Episode retrieval layer | 📋 Roadmap | Closes the feedback loop |
| Strategy generation layer | 📋 Roadmap | Full generative synthesis |
| Adaptive cooling (full) | 📋 Roadmap | Self-tuning cooling rate |

**The critical gap:** The RETRIEVE step is on the roadmap, not live. Everything required to build it is in place — the episode store is populated, the similarity function is specified, the temperature schedule is running. The feedback loop is not yet closed. The system is accumulating memory it has not yet learned to read.

---

## 7. Position in the Cormorant Foraging Stack

GESA completes the Cormorant Foraging Framework:

```
Layer 0:  Cormorant 3D (Chirp / Perch / Wake)   →  Sense
Layer 1:  DRIFT                                  →  Measure the gap
Layer 2:  Fetch                                  →  Decide to act
Layer ∞:  GESA                                   →  Optimise across episodes
```

Without GESA, the stack is reactive. With GESA, it becomes adaptive. Layers 0–2 answer the question for this run. GESA answers the question for every future run.

The `∞` notation is deliberate. GESA does not slot between existing layers — it sits above the entire stack as the optimization dimension that operates across all cycles. It is not a step in the sequence. It is what the sequence improves toward.

### 7.1 Relationship to the Governance Primitive

The Semantic Intent as Governance Primitive paper [1] established that agents operating across handoffs require an immutable, addressable, provenance-carrying contract. GESA extends this: the episode store is the persistent memory of what those contracts produced across time. The governance primitive defines the contract. GESA learns which contracts produce the best outcomes.

### 7.2 Relationship to Agentic Traffic Control

Agentic Traffic Control [2] defines a five-layer methodology for orchestrating multiple AI agents across enterprise systems. GESA is the learning layer above all five ATC layers. The audit layer (Layer 5 in ATC) captures what happened. GESA processes those captures as episodes and generates improved orchestration configurations for the next run. ATC makes the pipeline capable. GESA makes it intelligent.

---

## 8. Limitations

The ecosystem is young — deployed at the scale of individual production platforms, not cross-organisation enterprise networks.

The RETRIEVE step is not yet implemented. Until the feedback loop closes, GESA functions as an advanced logging and temperature-scheduling system rather than a fully generative optimization layer.

The similarity function weights (0.30 / 0.25 / 0.20 / 0.15 / 0.10) are specified but not yet empirically calibrated. Different domains may require different weight distributions.

The authorship taxonomy (human / AI agent / AI compositor) is a starting classification. The governance implications of each class under the governance primitive framework remain underspecified.

---

## 9. Conclusion

GESA was accumulated, not designed. Across 28 years of consulting practice, the same structural problem appeared in different forms: systems that sense and decide but do not learn. The StratIQX implementation made the pattern explicit — 11 AI model orchestrations, each producing an episode, the aggregate of which began to predict failures before they occurred.

The five components (episode store, temperature schedule, similarity function, generator, cooling profile) and the 8-step loop (OBSERVE → RETRIEVE → GENERATE → ANNEAL → SELECT → ACT → STORE → COOL) are the formalization of what was already present in working code. The three loop invariants (every act produces an episode, every episode is immutable, temperature only decreases) are the correctness requirements that make the learning guarantee hold.

GESA sits as Layer ∞ of the Cormorant Foraging Framework — not because it is unreachable, but because it operates across all cycles rather than within any single one. A reactive stack senses, measures, and decides. An adaptive stack also learns. GESA is what the difference between those two things looks like as an architecture.

---

## References

1. Shatny, M. (2026). *Semantic Intent as Governance Primitive for Agentic Systems*. DOI: [10.5281/zenodo.20436088](https://doi.org/10.5281/zenodo.20436088)
2. Shatny, M. (2026). *Agentic Traffic Control: Orchestrating AI Agents Across Enterprise Systems*. DOI: [10.5281/zenodo.20533774](https://doi.org/10.5281/zenodo.20533774)
3. Shatny, M. (2025). *Semantic Intent as Single Source of Truth: Immutable Governance for AI-Assisted Development*. DOI: [10.5281/zenodo.17114972](https://doi.org/10.5281/zenodo.17114972)
4. Shatny, M. (2026). *Rune Protocol: A Reactive Binding Grammar for Human-AI Collaborative Systems*. DOI: [10.5281/zenodo.20007883](https://doi.org/10.5281/zenodo.20007883)
5. Shatny, M. (2026). *Wake Intelligence MCP: A Five-Layer Temporal Intelligence Server for AI Agents*. [wake.semanticintent.dev](https://wake.semanticintent.dev)
6. Shatny, M. (2026). *EMBER: A Semantic Intent Language for Legacy System Modernisation*. DOI: [10.5281/zenodo.19751387](https://doi.org/10.5281/zenodo.19751387)
7. Shatny, M. (2026). *Mere: A Workbook Format for Self-Contained Reactive Applications*. DOI: [10.5281/zenodo.19751778](https://doi.org/10.5281/zenodo.19751778)
8. Shatny, M. (2026). *Strata: A Database Archaeology Methodology for Legacy SQL Systems*. DOI: [10.5281/zenodo.19768151](https://doi.org/10.5281/zenodo.19768151)

---

*Related: [GESA Documentation](https://gesa.semanticintent.dev) · [Cormorant Foraging Framework](https://cormorantforaging.dev) · [DRIFT](https://drift.cormorantforaging.dev) · [Fetch](https://fetch.cormorantforaging.dev) · [StratIQX](https://stratiqx.com)*

*© 2026 Michael Shatny / semanticintent. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).*  
*ORCID: [0009-0006-2011-3258](https://orcid.org/0009-0006-2011-3258)*
