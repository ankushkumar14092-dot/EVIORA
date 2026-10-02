# Persistent Affective Multimodal Conversational Agent — System Architecture

> Research Goal: Create an embodied AI agent whose persistent affective state and multimodal understanding produce coherent human-like language, voice, facial expression, timing and behavior over long-term interaction.

---

## TABLE OF CONTENTS

1. Project Definition
2. Existing Systems Studied
3. Gaps Identified
4. Research Hypothesis
5. Proposed Architecture
6. Component-by-Component Design
7. Data Flow
8. Emotional State Model
9. Memory Model
10. User State Model
11. Relationship Model
12. Reasoning Model
13. Behavior Model
14. Voice Architecture
15. Avatar Architecture
16. Real-Time Architecture
17. Database Design
18. API Design
19. Tech Stack
20. Security
21. Latency Strategy
22. Failure Handling
23. Experiments
24. Evaluation Metrics
25. MVP Architecture
26. Research Prototype Architecture
27. Production Architecture
28. Research Contribution / Gap

---

## 1. PROJECT DEFINITION

### Vision
Build a highly human-like multimodal AI conversational agent where the user feels they are talking to a real human rather than an AI or avatar.

### Core Research Focus
**Persistent affective intelligence + multimodal human-state understanding + synchronized embodied behavior.**

### What the System Must Understand (Inputs)
- Speech (words + acoustics)
- Voice tone / prosody
- Facial expressions
- Gaze / visual attention
- Emotional state
- Conversational context
- Long-term interaction history

### What the System Must Control (Outputs)
- What it says (language)
- How it says it (prosody, rhythm, pauses)
- Voice emotion
- Facial expression + micro-expressions
- Gaze direction
- Gestures + posture
- Response timing
- Turn-taking behavior
- Personality / relationship behavior

### Core Loop

```
User Experience
→ Multimodal Perception
→ User State Estimation
→ Cognitive Appraisal
→ AI Persistent Affective State
→ Memory / Relationship State
→ Reasoning
→ Behavior Planning
→ Text + Voice + Facial Expression + Timing
→ Avatar
→ New Interaction
→ Update Internal State
```

---

## 2. EXISTING SYSTEMS STUDIED

### 2.1 Anam [EXISTING]

| Dimension | Detail |
|---|---|
| Architecture | Client SDK (JS/TS) + cloud backend + WebRTC streaming |
| Perception | Audio only (STT via cloud) |
| STT | Cloud-based, streamed |
| LLM | Pluggable (OpenAI, Anthropic, custom) |
| Memory | None built-in; developer must inject via system prompt |
| Emotion Model | None — no internal affective state |
| Persistent State | None |
| Appraisal | None |
| Reasoning | Delegated entirely to LLM |
| TTS | Cloud TTS (ElevenLabs-compatible) |
| Avatar Generation | Proprietary real-time neural avatar |
| Facial Expression | Driven by TTS audio (lip sync + basic expression) |
| Gaze | Limited, not user-adaptive |
| Gestures | Basic idle animations |
| Turn-Taking | VAD-based interruption detection |
| Latency | ~800ms–1.2s first token to avatar |
| Streaming | Full WebRTC streaming pipeline |
| Emotional Sync | None — voice and face not emotionally coordinated |
| Relationship Modeling | None |
| Open Source | SDK is open (npm: @anam-ai/js-sdk) |
| APIs/SDKs | REST session API + JS SDK |
| Limitations | No emotion, no memory, no persistent state, no multimodal perception |

**What Anam Solves:**
- Real-time avatar rendering over WebRTC
- Session token architecture (client/server separation)
- Utterance ID tracking for interruption handling
- Director Notes for runtime behavioral cues
- PersonaConfig for voice/avatar/LLM separation
- createTalkMessageStream for streamed LLM → TTS → avatar pipeline

**What Anam Does NOT Solve:**
- No persistent internal emotional state
- No multimodal user perception (camera, facial expression, gaze)
- No cognitive appraisal
- No emotion-conditioned reasoning
- No relationship modeling
- No cross-modal emotional coherence
- No emotion-aware memory

**Architectural Lessons from Anam:**
- Separate session token server from client (security)
- Use utterance IDs to manage interruptions cleanly
- Director Notes pattern: inject behavioral metadata at runtime without re-prompting
- WebRTC is the right transport for real-time avatar + audio
- Decouple avatar renderer from AI brain completely

**Gap We Implement Ourselves:**
- Everything above the audio stream: emotion, memory, appraisal, relationship, behavior planning

---

### 2.2 Tavus [EXISTING]

| Dimension | Detail |
|---|---|
| Architecture | Cloud API, video generation pipeline |
| Perception | Text/audio input only |
| Emotion Model | None |
| Persistent State | None |
| Avatar | Personalized video clone (async + real-time CVI) |
| Limitations | No emotion, no memory, video-focused not conversational |

---

### 2.3 HeyGen LiveAvatar [EXISTING]

| Dimension | Detail |
|---|---|
| Architecture | Cloud streaming avatar API |
| Perception | Text input |
| Emotion Model | None |
| Avatar | High-quality streaming video avatar |
| Limitations | No conversational AI, no emotion, pure rendering service |

---

### 2.4 Beyond Presence [EXISTING]

| Dimension | Detail |
|---|---|
| Architecture | Real-time avatar + voice AI |
| Perception | Audio |
| Emotion Model | Minimal sentiment |
| Persistent State | None documented |
| Limitations | Closed system, no research access |

---

### 2.5 EmpaAva [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Architecture | Research prototype — empathetic avatar |
| Perception | Text + audio |
| Emotion Model | Valence/arousal from text |
| Persistent State | Session-level only |
| Avatar | Blendshape-driven 3D avatar |
| Limitations | No persistent state across sessions, no multimodal fusion |

---

### 2.6 Chain-of-Emotion (Research Paper 2024) [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Architecture | LLM chain with explicit emotion reasoning steps |
| Key Idea | Emotion is reasoned step-by-step before response generation |
| Limitation | No persistent state, no avatar, no voice |

**Lesson:** Emotion should be an explicit reasoning step, not a prompt suffix.

---

### 2.7 Emotional RAG (Research 2024–2025) [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Key Idea | Retrieval-augmented generation where emotional salience weights memory retrieval |
| Limitation | Retrieval only — no persistent affective state, no behavior planning |

**Lesson:** Memory retrieval should be emotion-weighted, not purely semantic.

---

### 2.8 Third-Person Appraisal Agent [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Key Idea | Agent appraises events from a third-person perspective before responding |
| Limitation | Single-turn, no persistent state |

**Lesson:** Appraisal should be a distinct module, not embedded in the LLM prompt.

---

### 2.9 Generative Agents (Park et al., 2023) [EXISTING]

| Dimension | Detail |
|---|---|
| Key Idea | Agents with memory streams, reflection, planning |
| Memory | Episodic + semantic + reflection tree |
| Limitation | No voice, no avatar, no real-time interaction, no emotion persistence |

**Lesson:** Memory architecture (importance scoring, reflection, retrieval) is directly applicable.

---

### 2.10 MATE / REMT / PsychoAgent [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Key Idea | Emotion-aware agents with theory-of-mind and psychological modeling |
| Limitation | Text-only, no embodiment, no real-time |

---

### 2.11 Emotion2Skill [EXISTING / RESEARCH]

| Dimension | Detail |
|---|---|
| Key Idea | Maps emotional state to behavioral skill selection |
| Limitation | Robotics-focused, not conversational |

**Lesson:** Emotion → behavior mapping should be explicit and structured.

---

### 2.12 Relevant Open-Source Projects [EXISTING]

| Project | Relevance |
|---|---|
| OpenFace 2.0 | Facial AU detection, gaze estimation |
| py-feat | Python facial expression analysis |
| SpeechBrain | Open-source speech emotion recognition |
| Whisper (OpenAI) | Streaming ASR |
| LivePortrait | Real-time portrait animation |
| MuseTalk | Real-time lip sync |
| Coqui TTS / StyleTTS2 | Controllable open-source TTS |
| mem0 | Persistent memory for AI agents |
| LangGraph | Stateful agent graph execution |
| Livekit | Open-source WebRTC infrastructure |

---

## 3. GAPS IDENTIFIED [RESEARCH GAP]

| Gap | Existing Coverage | Our Contribution |
|---|---|---|
| A. Persistent internal emotional state | None in production systems | Full continuous affective state engine |
| B. Multimodal user emotion understanding | Audio-only in most systems | Audio + video + text fusion |
| C. Emotion-changing memory retrieval | Emotional RAG (partial) | Emotion-weighted retrieval with appraisal context |
| D. Emotion-changing reasoning | Chain-of-Emotion (text only) | Affect-conditioned LLM reasoning pipeline |
| E. Emotion-changing decisions | None | Behavior planner driven by affective state |
| F. Relationship evolution | Generative Agents (partial) | Full relationship state with trust/closeness dynamics |
| G. Personality adaptation | None | Personality vector constrained by relationship state |
| H. Emotion influencing voice | None in research systems | TTS prosody controlled by affective state |
| I. Emotion influencing facial expression | EmpaAva (partial) | Blendshape weights driven by behavior plan |
| J. Emotion influencing timing | None | Response latency + pause modeled by arousal/valence |
| K. Cross-modal emotional consistency | None | Centralized behavior plan as single source of truth |
| L. Long-term emotional trajectory | None | Mood baseline + session-to-session state persistence |
| M. Emotional recovery / decay | None | Time-decay functions on affective state variables |
| N. Same situation + different emotional history | None tested | Key experiment in our research design |
| O. Causal effect of affect on behavior | None measured | Causal intervention experiment |

---

## 4. RESEARCH HYPOTHESIS

**H1:** An agent with persistent affective state will produce measurably more emotionally appropriate responses than a stateless agent given the same input.

**H2:** Multimodal user state fusion (audio + video + text) will produce more accurate user emotion estimates than any single modality alone.

**H3:** Emotion-weighted memory retrieval will surface more contextually relevant memories than purely semantic retrieval.

**H4:** A centralized behavior plan will produce higher cross-modal emotional coherence scores than independently generated modality outputs.

**H5:** Agents with relationship state will be rated as more human-like over long-term interaction than agents without.

**H6:** Affect is causally responsible for measurable differences in memory retrieval, reasoning, response strategy, voice, and facial behavior — not merely correlated.

---

## 5. PROPOSED ARCHITECTURE — HIGH LEVEL

```mermaid
graph TD
    subgraph INPUT["LAYER 1 — USER PERCEPTION"]
        MIC[Microphone]
        CAM[Camera]
        TXT[Text Input]
        ASR[ASR — Whisper Streaming]
        FEA[Facial Expression Analysis]
        GAZE[Gaze / Attention Estimation]
        VPROS[Voice Prosody / Emotion]
        TEMO[Text Emotion / Intent]
        CTX[Context Extractor]
    end

    subgraph FUSION["LAYER 2 — MULTIMODAL FUSION"]
        FUSE[Multimodal Fusion Engine]
        USV[User State Vector]
    end

    subgraph APPRAISAL["LAYER 3 — COGNITIVE APPRAISAL"]
        APP[Appraisal Engine]
        APS[Appraisal State]
    end

    subgraph AFFECT["LAYER 4 — PERSISTENT AFFECTIVE STATE"]
        PAS[Affective State Engine]
        AST[Affective State t]
    end

    subgraph MEMORY["LAYER 5 — MEMORY"]
        STM[Short-Term Memory]
        EPI[Episodic Memory]
        SEM[Semantic Memory]
        EMO[Emotional Memory]
        REL[Relationship Memory]
        MRET[Emotion-Aware Retrieval]
    end

    subgraph REASON["LAYER 6 — REASONING"]
        RENG[Affect-Conditioned Reasoning Engine]
        LLM[LLM]
    end

    subgraph PLAN["LAYER 7 — BEHAVIOR PLANNER"]
        BP[Behavior Plan]
    end

    subgraph OUTPUT["LAYER 8 — OUTPUT GENERATION"]
        TTS[TTS — Prosody Controlled]
        FACE[Facial Expression Generator]
        GZOUT[Gaze Controller]
        GEST[Gesture Controller]
        TIMING[Timing / Turn-Taking Controller]
    end

    subgraph AVATAR["LAYER 9 — AVATAR"]
        AV[Avatar Renderer — WebRTC]
    end

    MIC --> ASR
    MIC --> VPROS
    CAM --> FEA
    CAM --> GAZE
    TXT --> TEMO
    ASR --> CTX
    ASR --> TEMO

    ASR --> FUSE
    VPROS --> FUSE
    FEA --> FUSE
    GAZE --> FUSE
    TEMO --> FUSE
    CTX --> FUSE

    FUSE --> USV
    USV --> APP
    APP --> APS
    APS --> PAS
    PAS --> AST

    AST --> MRET
    USV --> MRET
    MRET --> STM
    MRET --> EPI
    MRET --> SEM
    MRET --> EMO
    MRET --> REL

    AST --> RENG
    MRET --> RENG
    USV --> RENG
    RENG --> LLM
    LLM --> BP

    AST --> BP
    BP --> TTS
    BP --> FACE
    BP --> GZOUT
    BP --> GEST
    BP --> TIMING

    TTS --> AV
    FACE --> AV
    GZOUT --> AV
    GEST --> AV
    TIMING --> AV

    AV --> |New Interaction| FUSE
    AV --> |Update State| PAS
```

---

## 6. COMPONENT-BY-COMPONENT DESIGN

### LAYER 1 — USER PERCEPTION [OUR PROPOSED]

#### 1.1 ASR — Automatic Speech Recognition
- Technology: Whisper large-v3 with streaming via faster-whisper or whisper-live
- Output: Transcript + word-level timestamps + confidence scores
- Why: Best open-source accuracy, supports streaming, multilingual

#### 1.2 Voice Prosody / Emotion Analysis
- Technology: SpeechBrain emotion recognition + openSMILE feature extraction
- Features: pitch, energy, speaking rate, jitter, shimmer, MFCCs
- Output: `{ valence, arousal, emotion_label, confidence }`
- Why: Captures what words cannot — stress, sadness, excitement

#### 1.3 Facial Expression Analysis
- Technology: py-feat (Action Units) + MediaPipe (landmarks + gaze)
- Output: `{ AU_vector[64], emotion_label, valence, arousal, confidence }`
- Why: AU-level granularity enables micro-expression detection

#### 1.4 Gaze / Attention Estimation
- Technology: MediaPipe FaceMesh + L2CS-Net gaze estimation
- Output: `{ gaze_direction, eye_contact_ratio, attention_score }`
- Why: Gaze reveals engagement, discomfort, deception

#### 1.5 Text Emotion / Intent Analysis
- Technology: Fine-tuned RoBERTa on GoEmotions + intent classifier
- Output: `{ emotion, intent, sentiment, confidence }`
- Why: Semantic layer that voice and face cannot provide

#### 1.6 User State Vector Output

```json
{
  "emotion": "sadness",
  "valence": -0.62,
  "arousal": 0.31,
  "dominance": 0.28,
  "confidence": 0.74,
  "stress": 0.55,
  "engagement": 0.48,
  "intent": "seeking_support",
  "conversational_state": "mid_turn",
  "gaze_contact": 0.61,
  "facial_AU": [0.1, 0.8, 0.0, 0.6, ...],
  "voice_prosody": {
    "pitch_mean": 142.3,
    "speaking_rate": 0.72,
    "energy": 0.41
  },
  "modality_confidence": {
    "text": 0.81,
    "voice": 0.74,
    "face": 0.69
  },
  "timestamp": 1720000000.123
}
```

---

### LAYER 2 — MULTIMODAL FUSION ENGINE [OUR PROPOSED]

#### Design Principles
- Do NOT blindly trust text — voice and face often contradict words
- Weight modalities by reliability and confidence
- Handle missing modalities gracefully
- Apply temporal smoothing to prevent jitter

#### Conflict Resolution Example
```
User says: "I'm fine"       → text_valence = +0.3
Voice prosody:              → voice_valence = -0.6
Facial expression:          → face_valence = -0.4

Fusion result:              → fused_valence = -0.43  (text overridden)
Conflict flag:              → text_voice_conflict = true
```

#### Fusion Algorithm [OUR PROPOSED]

```python
def fuse_user_state(text_state, voice_state, face_state, history):
    # Dynamic confidence weighting
    w_text  = text_state.confidence  * modality_reliability["text"]
    w_voice = voice_state.confidence * modality_reliability["voice"]
    w_face  = face_state.confidence  * modality_reliability["face"]

    total_w = w_text + w_voice + w_face

    fused_valence  = (w_text * text_state.valence +
                      w_voice * voice_state.valence +
                      w_face * face_state.valence) / total_w

    fused_arousal  = (w_text * text_state.arousal +
                      w_voice * voice_state.arousal +
                      w_face * face_state.arousal) / total_w

    # Temporal smoothing (exponential moving average)
    alpha = 0.3
    fused_valence = alpha * fused_valence + (1 - alpha) * history.last_valence
    fused_arousal = alpha * fused_arousal + (1 - alpha) * history.last_arousal

    conflict = detect_conflict(text_state, voice_state, face_state)

    return UserState(valence=fused_valence, arousal=fused_arousal,
                     conflict=conflict, uncertainty=1 - (total_w / 3))
```

#### Missing Modality Handling
| Scenario | Strategy |
|---|---|
| No camera | Use text + voice only; increase uncertainty |
| Poor audio | Use text + face only; flag low confidence |
| No text (non-verbal) | Use voice + face; infer intent from context |
| All modalities low confidence | Fall back to last known state + uncertainty flag |

---

### LAYER 3 — COGNITIVE APPRAISAL ENGINE [OUR PROPOSED]

Inspired by Scherer's Component Process Model and Lazarus appraisal theory.

#### Appraisal Dimensions

```python
@dataclass
class AppraisalState:
    relevance: float          # Is this event relevant to the agent?
    valence: float            # Beneficial (+) or harmful (-)?
    novelty: float            # How unexpected is this?
    goal_congruence: float    # Does it align with current goals?
    causal_agent: str         # "user", "self", "environment"
    controllability: float    # Can the agent influence the outcome?
    social_significance: float # Social/relational importance
    urgency: float            # How time-sensitive?
```

#### Appraisal Process

```
Event (User State + Context)
→ Primary Appraisal: Is this relevant? Is it good or bad?
→ Secondary Appraisal: Who caused it? Can I control it?
→ Reappraisal: Given memory + relationship, reinterpret
→ Appraisal State Output
```

#### Example
```
User input: "You never understand me" (frustrated tone, averted gaze)

Appraisal:
  relevance = 0.95          (direct accusation)
  valence = -0.78           (harmful)
  novelty = 0.4             (has happened before per memory)
  goal_congruence = -0.8    (conflicts with connection goal)
  causal_agent = "user"
  controllability = 0.6     (can respond empathetically)
  social_significance = 0.9 (relationship threat)
  urgency = 0.7
```

---

## 8. EMOTIONAL STATE MODEL [OUR PROPOSED]

### State Variables

```python
@dataclass
class AffectiveState:
    # Continuous dimensional variables
    valence: float        # -1.0 to +1.0  (negative ↔ positive)
    arousal: float        # 0.0 to 1.0    (calm ↔ excited)
    dominance: float      # 0.0 to 1.0    (submissive ↔ dominant)

    # Discrete emotion intensities (0.0 to 1.0)
    happiness: float
    sadness: float
    anger: float
    fear: float
    surprise: float
    disgust: float
    trust: float
    curiosity: float
    frustration: float

    # Social / relational variables
    attachment: float     # 0.0 to 1.0
    confidence: float     # 0.0 to 1.0

    # Mood baseline (slow-changing)
    mood_valence: float   # -1.0 to +1.0
    mood_arousal: float   # 0.0 to 1.0

    # Stress / fatigue
    stress: float         # 0.0 to 1.0
    fatigue: float        # 0.0 to 1.0

    timestamp: float
    session_id: str
```

### State Transition Function [OUR PROPOSED]

```
state(t+1) = f(
    state(t),           # current state
    appraisal(t),       # cognitive appraisal of current event
    memory_context(t),  # retrieved emotional memories
    personality,        # stable personality constraints
    relationship(t),    # current relationship state
    delta_t             # time elapsed
)
```

### Decay and Recovery

```python
def update_affective_state(state, appraisal, delta_t, personality):
    # Emotional decay toward mood baseline
    decay_rate = personality.emotional_stability  # 0.1 (volatile) to 0.9 (stable)

    new_valence = (state.valence
                   + appraisal.valence * appraisal.relevance * 0.4
                   - decay_rate * (state.valence - state.mood_valence) * delta_t)

    new_arousal = (state.arousal
                   + appraisal.urgency * 0.3
                   - 0.1 * delta_t)  # arousal decays faster

    # Personality constraints (clamp to personality range)
    new_valence = clamp(new_valence,
                        personality.valence_min,
                        personality.valence_max)

    # Mood baseline drifts slowly
    new_mood_valence = (state.mood_valence
                        + 0.01 * (new_valence - state.mood_valence) * delta_t)

    return AffectiveState(valence=new_valence,
                          arousal=new_arousal,
                          mood_valence=new_mood_valence, ...)
```

### Continuous vs Categorical Variables

| Variable | Type | Reason |
|---|---|---|
| valence, arousal, dominance | Continuous | Gradual change, interpolation needed |
| happiness, sadness, anger, fear | Continuous intensity | Emotions blend and co-occur |
| mood_valence, mood_baseline | Continuous slow | Mood drifts gradually |
| emotion_label | Categorical (derived) | For downstream display/logging |
| personality_type | Categorical | Stable trait category |
| relationship_phase | Categorical | Discrete relationship stages |

### Emotional State Architecture Diagram

```mermaid
graph LR
    AP[Appraisal State] --> SE[State Equation]
    MEM[Memory Context] --> SE
    PERS[Personality Vector] --> SE
    REL[Relationship State] --> SE
    TIME[Time Delta] --> SE
    PREV[State t] --> SE
    SE --> NEXT[State t+1]
    NEXT --> MOOD[Mood Baseline Update]
    NEXT --> DECAY[Decay Function]
    DECAY --> NEXT2[State t+2]
```

---

## 9. MEMORY MODEL [OUR PROPOSED]

### Memory Architecture

```mermaid
graph TD
    subgraph MEM["MEMORY SYSTEM"]
        STM[Short-Term Memory\nLast 10 turns\nRedis]
        EPI[Episodic Memory\nEvent records\nPostgres + pgvector]
        SEM[Semantic Memory\nFacts about user\nPostgres + pgvector]
        EMO[Emotional Memory\nEmotionally salient events\nPostgres + pgvector]
        RELM[Relationship Memory\nInteraction history\nPostgres]
        REF[Reflection Memory\nSynthesized insights\nPostgres + pgvector]
    end

    EVENT[New Event] --> STM
    STM --> |importance > threshold| EPI
    EPI --> |pattern detected| REF
    EPI --> EMO
    EPI --> SEM
```

### Memory Record Schema

```json
{
  "id": "uuid",
  "type": "episodic | semantic | emotional | relationship",
  "event_summary": "User expressed frustration about being misunderstood",
  "timestamp": 1720000000,
  "session_id": "sess_abc",
  "entities": ["user", "agent"],
  "semantic_embedding": [0.12, -0.34, ...],
  "emotional_valence": -0.65,
  "emotional_arousal": 0.72,
  "emotional_intensity": 0.78,
  "appraisal": {
    "relevance": 0.9,
    "causal_agent": "user",
    "social_significance": 0.85
  },
  "relationship_relevance": 0.8,
  "importance_score": 0.82,
  "confidence": 0.91,
  "decay_factor": 0.95
}
```

### Emotion-Aware Memory Retrieval [OUR PROPOSED]

```python
def retrieve_memories(query, current_affect, relationship_state, k=5):
    query_embedding = embed(query)

    # Multi-factor retrieval score
    for memory in memory_store:
        semantic_score    = cosine_similarity(query_embedding, memory.embedding)
        recency_score     = exp(-lambda * (now - memory.timestamp))
        emotional_score   = 1 - abs(current_affect.valence - memory.emotional_valence)
        importance_score  = memory.importance_score
        rel_score         = memory.relationship_relevance * relationship_state.closeness

        final_score = (0.35 * semantic_score +
                       0.20 * recency_score +
                       0.25 * emotional_score +
                       0.10 * importance_score +
                       0.10 * rel_score)

    return top_k(memories, k, key=final_score)
```

### Memory Importance Scoring

```python
def score_importance(event, appraisal, affect_delta):
    return (0.3 * appraisal.relevance +
            0.3 * abs(affect_delta.valence) +   # how much it changed state
            0.2 * appraisal.social_significance +
            0.2 * appraisal.novelty)
```

---

## 10. USER STATE MODEL [OUR PROPOSED]

The User State Vector is the unified output of the multimodal fusion engine representing the agent's best estimate of the user's current internal state.

```json
{
  "emotion": "sadness",
  "valence": -0.62,
  "arousal": 0.31,
  "dominance": 0.28,
  "confidence": 0.74,
  "stress": 0.55,
  "engagement": 0.48,
  "intent": "seeking_support",
  "conversational_state": "mid_turn",
  "gaze_contact": 0.61,
  "conflict_detected": true,
  "conflict_type": "text_voice_mismatch",
  "uncertainty": 0.26,
  "modality_availability": {
    "text": true,
    "voice": true,
    "face": true
  }
}
```

---

## 11. RELATIONSHIP MODEL [OUR PROPOSED]

### Relationship State Variables

```python
@dataclass
class RelationshipState:
    user_id: str
    trust: float           # 0.0 to 1.0
    familiarity: float     # 0.0 to 1.0
    closeness: float       # 0.0 to 1.0
    conflict_level: float  # 0.0 to 1.0
    attachment: float      # 0.0 to 1.0
    respect: float         # 0.0 to 1.0
    interaction_count: int
    total_interaction_time: float  # seconds
    shared_experiences: list[str]  # memory IDs
    relationship_phase: str  # "stranger" | "acquaintance" | "familiar" | "close"
    last_interaction: float  # timestamp
```

### Relationship Dynamics

```
positive_interaction  → trust ↑ 0.05, closeness ↑ 0.03
deep_disclosure       → closeness ↑ 0.08, attachment ↑ 0.05
conflict_unresolved   → trust ↓ 0.10, conflict_level ↑ 0.15
apology_accepted      → trust ↑ 0.06, conflict_level ↓ 0.10
long_absence          → familiarity ↓ 0.02/day, closeness ↓ 0.01/day
repeated_support      → attachment ↑ 0.04, trust ↑ 0.03
```

### Relationship Phase Transitions

```mermaid
stateDiagram-v2
    [*] --> Stranger
    Stranger --> Acquaintance: interaction_count > 3 AND trust > 0.3
    Acquaintance --> Familiar: interaction_count > 10 AND closeness > 0.5
    Familiar --> Close: closeness > 0.75 AND trust > 0.7
    Close --> Familiar: conflict_level > 0.6
    Familiar --> Acquaintance: long_absence > 30_days
```

### How Relationship Affects Agent Behavior

| Relationship Phase | Language Style | Disclosure Level | Emotional Expression |
|---|---|---|---|
| Stranger | Formal, careful | Minimal | Neutral, professional |
| Acquaintance | Friendly, warm | Moderate | Mild emotional expression |
| Familiar | Casual, direct | High | Open emotional expression |
| Close | Intimate, honest | Full | Full emotional range |

---

## 12. REASONING MODEL [OUR PROPOSED]

### Affect-Conditioned Cognition vs Affect-Conditioned Generation

| Approach | Description | Problem |
|---|---|---|
| Naive: "You are sad" in prompt | Appends emotion label to system prompt | LLM ignores it or overreacts |
| Affect-conditioned generation | Emotion label in generation prompt | Superficial — changes words not reasoning |
| **Our approach: Affect-conditioned cognition** | Emotion shapes attention, retrieval, interpretation, goal prioritization BEFORE generation | Deep — changes what the agent thinks about |

### Reasoning Pipeline [OUR PROPOSED]

```
1. ATTENTION FILTER
   Affective state biases which aspects of user input are attended to
   (high stress → attend to distress signals; high curiosity → attend to novel info)

2. MEMORY RETRIEVAL
   Emotion-weighted retrieval (see Section 9)

3. INTERPRETATION LAYER
   Appraisal reframes the event before reasoning
   (same words interpreted differently based on trust level)

4. GOAL PRIORITIZATION
   Current affect shifts goal weights
   (high attachment → prioritize connection goal over information goal)

5. RISK SENSITIVITY
   Arousal + valence modulate risk tolerance
   (negative valence → more cautious responses)

6. RESPONSE STRATEGY SELECTION
   Behavior planner selects strategy before LLM generates text

7. LLM GENERATION
   LLM receives: context + retrieved memories + behavior plan + response strategy
   NOT just "you are sad"
```

### LLM Context Construction

```python
def build_llm_context(user_state, affect_state, memories, behavior_plan, relationship):
    return {
        "system": build_system_prompt(relationship, affect_state),
        "retrieved_memories": format_memories(memories),
        "behavior_directive": {
            "intent": behavior_plan.intent,
            "strategy": behavior_plan.response_strategy,
            "emotional_register": behavior_plan.emotional_register,
            "disclosure_level": relationship.phase_disclosure_level()
        },
        "user_state_summary": summarize_user_state(user_state),
        "conversation_history": get_recent_turns(n=10),
        "current_user_input": user_state.transcript
    }
```

---

## 13. BEHAVIOR MODEL [OUR PROPOSED]

### Behavior Plan — Single Source of Truth

The behavior plan is generated BEFORE any output modality. All modalities (text, voice, face, gesture, timing) are driven by this single plan.

```json
{
  "intent": "supportive",
  "response_strategy": "validate_then_explore",
  "emotional_register": "warm_concerned",
  "emotion": "concern",
  "valence": -0.3,
  "arousal": 0.4,
  "intensity": 0.65,
  "speaking_rate": "slow",
  "pitch_direction": "low_falling",
  "volume": "soft",
  "pause_before_response_ms": 800,
  "pause_pattern": "thoughtful",
  "gaze": "attentive_soft",
  "facial_expression": "concerned_empathetic",
  "micro_expression": "brow_furrow_slight",
  "gesture": "subtle_forward_lean",
  "head_movement": "slow_nod",
  "turn_taking": "wait_for_completion",
  "backchannel": "mm_hmm_soft",
  "response_length": "medium",
  "disclosure_level": "moderate"
}
```

### Behavior Planner Architecture

```mermaid
graph TD
    AS[Affective State] --> BP[Behavior Planner]
    US[User State] --> BP
    REL[Relationship State] --> BP
    APP[Appraisal State] --> BP
    PERS[Personality] --> BP
    BP --> STRAT[Strategy Selector]
    STRAT --> PLAN[Behavior Plan]
    PLAN --> TXT[Text Generator]
    PLAN --> TTS[TTS Controller]
    PLAN --> FACE[Face Controller]
    PLAN --> GAZE[Gaze Controller]
    PLAN --> GEST[Gesture Controller]
    PLAN --> TIME[Timing Controller]
```

### Strategy Selection Rules (Examples)

```
IF user_state.emotion == "distress" AND relationship.trust > 0.5:
    strategy = "validate_and_support"
    pause_before = 800ms
    speaking_rate = slow
    facial = concerned

IF user_state.emotion == "joy" AND affect_state.valence > 0.5:
    strategy = "share_enthusiasm"
    speaking_rate = slightly_fast
    facial = warm_smile
    gesture = open_hands

IF appraisal.causal_agent == "self" AND appraisal.valence < -0.5:
    strategy = "acknowledge_and_repair"
    pause_before = 1200ms
    facial = apologetic
```

---

## 14. VOICE ARCHITECTURE [OUR PROPOSED]

### Pipeline

```
LLM Response Text
→ Prosody Planner (from Behavior Plan)
→ SSML / Prosody Markup
→ TTS Engine
→ Audio Stream
→ Avatar Lip Sync
```

### Prosody Control Parameters

```python
@dataclass
class ProsodyPlan:
    pitch_shift: float        # semitones relative to baseline
    speaking_rate: float      # 0.5 (slow) to 2.0 (fast), baseline 1.0
    volume: float             # 0.0 to 1.0
    emphasis_words: list[str] # words to stress
    pause_before_ms: int      # pre-utterance pause
    pause_pattern: str        # "none" | "thoughtful" | "hesitant" | "dramatic"
    breathing: bool           # insert breath sounds
    hesitation: bool          # insert "um", "uh" naturally
    emotional_intensity: float # 0.0 to 1.0
    voice_quality: str        # "clear" | "breathy" | "tense" | "soft"
```

### TTS Technology Options

| Option | Pros | Cons | Use Case |
|---|---|---|---|
| ElevenLabs | Highest quality, emotion control | Cost, latency | Production |
| Cartesia Sonic | Low latency streaming | Less expressive | Real-time research |
| StyleTTS2 | Open source, controllable | Setup complexity | Research / local |
| Coqui XTTS | Open source, multilingual | Quality gap | Offline research |

**Recommended:** Cartesia for real-time streaming + StyleTTS2 for research experiments

### Voice Emotion Coherence Check

```python
def validate_voice_emotion(prosody_plan, behavior_plan):
    # Ensure voice matches intended emotion
    if behavior_plan.emotion == "concern":
        assert prosody_plan.speaking_rate < 1.0
        assert prosody_plan.pitch_shift <= 0
        assert prosody_plan.volume < 0.7
    # Flag incoherence for logging
```

---

## 15. AVATAR ARCHITECTURE [OUR PROPOSED + EXISTING]

### Component Decision Matrix

| Component | Approach | Technology |
|---|---|---|
| Real-time streaming transport | [EXISTING] Reuse | Anam SDK / LiveKit WebRTC |
| Lip synchronization | [EXISTING] Reuse | MuseTalk / Anam |
| Facial expression blendshapes | [OUR PROPOSED] | Custom blendshape controller from Behavior Plan |
| Gaze control | [OUR PROPOSED] | Procedural gaze + attention model |
| Micro-expressions | [OUR PROPOSED] | AU-based blendshape injection |
| Head movement | [INSPIRED BY EXISTING] | Procedural + emotion-driven |
| Gestures | [OUR PROPOSED] | Gesture library keyed to behavior plan |
| Idle behavior | [EXISTING] Reuse | Anam idle animations |
| Avatar rendering | [EXISTING] Reuse | Anam / Ready Player Me + Three.js |

### Facial Expression Pipeline

```
Behavior Plan { facial_expression, micro_expression, intensity }
→ Blendshape Weight Calculator
→ AU Vector → Blendshape Weights [52 ARKit / 64 custom]
→ Temporal Smoother (prevent snapping)
→ Avatar Renderer
```

### Blendshape Mapping (Examples)

```python
EXPRESSION_TO_BLENDSHAPES = {
    "concerned_empathetic": {
        "browInnerUp": 0.4,
        "browDownLeft": 0.2,
        "browDownRight": 0.2,
        "eyeSquintLeft": 0.15,
        "eyeSquintRight": 0.15,
        "mouthFrownLeft": 0.1,
        "mouthFrownRight": 0.1,
    },
    "warm_smile": {
        "mouthSmileLeft": 0.7,
        "mouthSmileRight": 0.7,
        "cheekSquintLeft": 0.4,
        "cheekSquintRight": 0.4,
        "eyeSquintLeft": 0.2,
        "eyeSquintRight": 0.2,
    }
}
```

### Gaze Model [OUR PROPOSED]

```python
class GazeController:
    def compute_gaze(self, behavior_plan, user_gaze, affect_state):
        if behavior_plan.gaze == "attentive_soft":
            # Look at user face with natural saccades
            target = user_face_position
            saccade_rate = 0.3  # Hz
        elif behavior_plan.gaze == "thinking":
            # Look slightly up-left (cognitive load signal)
            target = upper_left_offset
        elif affect_state.stress > 0.7:
            # Slight gaze aversion under stress
            target = slight_downward_offset

        return smooth_gaze_transition(self.current_gaze, target, speed=0.15)
```

### Avatar Pipeline Diagram

```mermaid
graph LR
    BP[Behavior Plan] --> BSC[Blendshape Calculator]
    BP --> GC[Gaze Controller]
    BP --> GEC[Gesture Controller]
    BP --> HC[Head Movement Controller]
    TTS[TTS Audio] --> LS[Lip Sync Engine]
    BSC --> BLEND[Blendshape Weights]
    GC --> GAZE[Gaze Target]
    GEC --> GEST[Gesture Animation]
    HC --> HEAD[Head Pose]
    LS --> LIPS[Lip Weights]
    BLEND --> MERGE[Merge Layer]
    GAZE --> MERGE
    GEST --> MERGE
    HEAD --> MERGE
    LIPS --> MERGE
    MERGE --> SMOOTH[Temporal Smoother]
    SMOOTH --> RENDER[WebRTC Avatar Renderer]
```

---

## 16. REAL-TIME ARCHITECTURE [OUR PROPOSED]

### System Architecture Diagram

```mermaid
graph TD
    subgraph CLIENT["BROWSER CLIENT (Next.js)"]
        UI[React UI]
        WC[WebRTC Client]
        MIC2[Mic Capture]
        CAM2[Camera Capture]
        AV2[Avatar Renderer]
    end

    subgraph GATEWAY["API GATEWAY"]
        WS[WebSocket Server\nFastAPI]
        AUTH[Auth / Session Token]
    end

    subgraph PERCEPTION["PERCEPTION SERVICE (Python)"]
        ASR2[Whisper Streaming ASR]
        FEA2[Facial Expression Analysis]
        VPROS2[Voice Prosody Analysis]
        GAZE2[Gaze Estimation]
        TEMO2[Text Emotion Analysis]
    end

    subgraph FUSION2["FUSION + APPRAISAL SERVICE"]
        FUSE2[Multimodal Fusion]
        APP2[Appraisal Engine]
    end

    subgraph STATE["STATE SERVICE"]
        PAS2[Affective State Engine]
        REL2[Relationship Engine]
        REDIS[Redis — Hot State]
    end

    subgraph MEMORY2["MEMORY SERVICE"]
        MRET2[Emotion-Aware Retrieval]
        PG[PostgreSQL + pgvector]
    end

    subgraph REASON2["REASONING SERVICE"]
        BP2[Behavior Planner]
        LLM2[LLM API / Local LLM]
    end

    subgraph OUTPUT2["OUTPUT SERVICE"]
        TTS2[TTS — Cartesia/ElevenLabs]
        FACE2[Face Expression Controller]
        LIPSYNC[Lip Sync]
    end

    subgraph TRANSPORT["REAL-TIME TRANSPORT"]
        LK[LiveKit WebRTC Server]
    end

    subgraph OBS["OBSERVABILITY"]
        LOG[Structured Logging]
        TRACE[OpenTelemetry Tracing]
        METRIC[Prometheus Metrics]
    end

    MIC2 --> WS
    CAM2 --> WS
    WS --> ASR2
    WS --> FEA2
    WS --> VPROS2
    WS --> GAZE2
    ASR2 --> TEMO2
    ASR2 --> FUSE2
    VPROS2 --> FUSE2
    FEA2 --> FUSE2
    GAZE2 --> FUSE2
    TEMO2 --> FUSE2
    FUSE2 --> APP2
    APP2 --> PAS2
    PAS2 --> REDIS
    PAS2 --> REL2
    PAS2 --> MRET2
    MRET2 --> PG
    MRET2 --> BP2
    PAS2 --> BP2
    REL2 --> BP2
    APP2 --> BP2
    BP2 --> LLM2
    LLM2 --> TTS2
    BP2 --> FACE2
    TTS2 --> LIPSYNC
    LIPSYNC --> LK
    FACE2 --> LK
    LK --> WC
    WC --> AV2
    WS --> LOG
    PAS2 --> TRACE
    LLM2 --> METRIC
```

### Synchronous vs Asynchronous Components

| Component | Type | Reason |
|---|---|---|
| ASR streaming | Async streaming | Continuous audio chunks |
| Facial analysis | Async streaming | 15fps camera frames |
| Multimodal fusion | Sync (per turn) | Needs all modalities |
| Appraisal | Sync | Blocking — needed before state update |
| Affective state update | Sync | Needed before reasoning |
| Memory retrieval | Async with timeout | Can proceed with partial results |
| LLM generation | Async streaming | Token streaming to TTS |
| TTS | Async streaming | Audio chunk streaming |
| Avatar rendering | Async continuous | Independent render loop |
| State persistence | Async write-behind | Non-blocking DB writes |

### Turn-Taking Architecture [OUR PROPOSED]

```mermaid
stateDiagram-v2
    [*] --> Listening
    Listening --> Processing: VAD end-of-turn detected
    Listening --> Interrupted: User interrupts agent
    Processing --> Speaking: Response ready
    Speaking --> Listening: Agent turn complete
    Speaking --> Interrupted: User interrupts
    Interrupted --> Processing: Handle interruption
    Listening --> Backchannel: Backchannel trigger
    Backchannel --> Listening: Backchannel sent
```

### Latency Budget

| Stage | Target Latency |
|---|---|
| ASR (end-of-turn detection) | < 200ms |
| Multimodal fusion | < 50ms |
| Appraisal + state update | < 30ms |
| Memory retrieval | < 100ms |
| Behavior planning | < 50ms |
| LLM first token | < 300ms |
| TTS first audio chunk | < 150ms |
| Avatar first frame | < 100ms |
| **Total end-to-end** | **< 1000ms** |

---

## 17. DATABASE DESIGN [OUR PROPOSED]

### Schema

```sql
-- Users
CREATE TABLE users (
    id UUID PRIMARY KEY,
    created_at TIMESTAMPTZ,
    metadata JSONB
);

-- Sessions
CREATE TABLE sessions (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    started_at TIMESTAMPTZ,
    ended_at TIMESTAMPTZ,
    summary TEXT,
    affect_start JSONB,
    affect_end JSONB
);

-- Affective State Snapshots
CREATE TABLE affective_states (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    session_id UUID REFERENCES sessions(id),
    timestamp TIMESTAMPTZ,
    valence FLOAT,
    arousal FLOAT,
    dominance FLOAT,
    happiness FLOAT,
    sadness FLOAT,
    anger FLOAT,
    fear FLOAT,
    trust FLOAT,
    curiosity FLOAT,
    frustration FLOAT,
    mood_valence FLOAT,
    mood_arousal FLOAT,
    stress FLOAT,
    full_state JSONB
);

-- Relationship State
CREATE TABLE relationship_states (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    trust FLOAT,
    familiarity FLOAT,
    closeness FLOAT,
    conflict_level FLOAT,
    attachment FLOAT,
    respect FLOAT,
    interaction_count INT,
    relationship_phase VARCHAR(20),
    updated_at TIMESTAMPTZ,
    full_state JSONB
);

-- Memory Records
CREATE TABLE memories (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    session_id UUID REFERENCES sessions(id),
    type VARCHAR(20),  -- episodic | semantic | emotional | relationship
    event_summary TEXT,
    timestamp TIMESTAMPTZ,
    entities JSONB,
    embedding VECTOR(1536),  -- pgvector
    emotional_valence FLOAT,
    emotional_arousal FLOAT,
    emotional_intensity FLOAT,
    appraisal JSONB,
    relationship_relevance FLOAT,
    importance_score FLOAT,
    confidence FLOAT,
    decay_factor FLOAT,
    metadata JSONB
);

-- Conversation Turns
CREATE TABLE conversation_turns (
    id UUID PRIMARY KEY,
    session_id UUID REFERENCES sessions(id),
    turn_index INT,
    speaker VARCHAR(10),  -- "user" | "agent"
    transcript TEXT,
    user_state JSONB,
    affect_state JSONB,
    behavior_plan JSONB,
    timestamp TIMESTAMPTZ
);

-- Indexes
CREATE INDEX ON memories USING ivfflat (embedding vector_cosine_ops);
CREATE INDEX ON memories (user_id, emotional_valence);
CREATE INDEX ON memories (user_id, importance_score DESC);
CREATE INDEX ON affective_states (user_id, timestamp DESC);
```

### Redis Schema (Hot State)

```
user:{user_id}:affect_state     → JSON (current affective state)
user:{user_id}:relationship     → JSON (current relationship state)
user:{user_id}:stm              → List (last 10 conversation turns)
session:{session_id}:context    → JSON (active session context)
```

---

## 18. API DESIGN [OUR PROPOSED]

### REST Endpoints

```
POST   /api/sessions                    Create new session, return session token
GET    /api/sessions/{id}               Get session info
DELETE /api/sessions/{id}               End session

GET    /api/users/{id}/affect           Get current affective state
GET    /api/users/{id}/relationship     Get relationship state
GET    /api/users/{id}/memories         Query memories (with filters)
POST   /api/users/{id}/memories         Store memory manually

GET    /api/users/{id}/affect/history   Affective state timeline
```

### WebSocket Events

```
CLIENT → SERVER:
  audio_chunk       { data: base64, timestamp }
  video_frame       { data: base64, timestamp }
  text_input        { text, timestamp }
  interrupt         { utterance_id }
  session_end       {}

SERVER → CLIENT:
  user_state        { user_state_vector }
  affect_update     { affect_state }
  behavior_plan     { behavior_plan }
  audio_chunk       { data: base64, utterance_id }
  face_update       { blendshapes, gaze }
  turn_state        { state: "listening" | "processing" | "speaking" }
  backchannel       { type: "mm_hmm" | "nod" }
  error             { code, message }
```

---

## 19. TECH STACK [OUR PROPOSED]

### Frontend

| Technology | Why |
|---|---|
| Next.js 14 + TypeScript | SSR, App Router, type safety |
| React | Component model for avatar + UI |
| LiveKit JS SDK | WebRTC client, proven real-time |
| Three.js / React Three Fiber | 3D avatar rendering |
| Zustand | Lightweight state management for affect/session state |
| TailwindCSS | Rapid UI development |

### Backend

| Technology | Why |
|---|---|
| Python 3.11 + FastAPI | Async, fast, AI ecosystem native |
| WebSockets (FastAPI) | Real-time bidirectional communication |
| Celery + Redis | Async task queue for non-blocking processing |
| Pydantic v2 | Data validation for all state models |

### AI / ML

| Technology | Why |
|---|---|
| faster-whisper | Streaming ASR, 4x faster than original Whisper |
| py-feat | Facial AU detection, well-maintained |
| MediaPipe | Gaze + landmark estimation, real-time |
| SpeechBrain | Voice emotion recognition |
| Transformers (HuggingFace) | Text emotion, intent classification |
| PyTorch | Custom model training |
| GPT-4o / Claude 3.5 / Llama 3 | LLM reasoning (pluggable) |
| LangGraph | Stateful agent graph for reasoning pipeline |

### Voice

| Technology | Why |
|---|---|
| Cartesia Sonic | Lowest latency streaming TTS (~80ms) |
| ElevenLabs | Highest quality for production |
| StyleTTS2 | Open-source research experiments |

### Memory / Database

| Technology | Why |
|---|---|
| PostgreSQL 16 | Reliable relational store |
| pgvector | Vector similarity search in same DB |
| Redis 7 | Hot state, session cache, STM |
| mem0 | High-level memory management layer |

### Real-Time Transport

| Technology | Why |
|---|---|
| LiveKit | Open-source WebRTC, self-hostable, proven at scale |
| WebSockets | Control channel alongside WebRTC |

### Avatar

| Technology | Why |
|---|---|
| Anam SDK | Baseline avatar + WebRTC pipeline [EXISTING] |
| MuseTalk | Open-source lip sync research [EXISTING] |
| Ready Player Me | Avatar creation pipeline |
| Custom blendshape controller | Emotion-driven expression [OUR PROPOSED] |

### Observability

| Technology | Why |
|---|---|
| OpenTelemetry | Distributed tracing across services |
| Prometheus + Grafana | Metrics and dashboards |
| Structured logging (structlog) | Machine-readable logs |

---

## 20. SECURITY [OUR PROPOSED]

### Session Token Architecture (Inspired by Anam)

```
Client → POST /api/sessions → Server generates short-lived JWT session token
Client uses session token for WebSocket auth
Session token never exposes LLM API keys or DB credentials to client
Token expires after session ends or timeout
```

### Data Privacy

- All video/audio processed server-side, not stored by default
- Emotional state data stored with explicit user consent
- Memory records encrypted at rest (AES-256)
- User data deletion API (GDPR compliance)
- No raw video/audio stored — only derived features

### API Security

- JWT authentication on all endpoints
- Rate limiting per user per session
- Input validation via Pydantic on all WebSocket messages
- CORS restricted to known origins

---

## 21. LATENCY STRATEGY [OUR PROPOSED]

### Streaming-First Design

```
ASR streams partial transcripts → Fusion starts early
LLM streams tokens → TTS starts on first sentence
TTS streams audio → Avatar starts on first audio chunk
Face expression → Sent ahead of audio (pre-expression)
```

### Pre-Expression Technique [OUR PROPOSED]

The avatar begins showing the emotional expression 200–400ms BEFORE the first audio word arrives, matching human behavior where facial expression precedes speech.

```
Behavior Plan generated
→ Face expression sent immediately (t=0)
→ Pause (behavior_plan.pause_before_ms)
→ TTS audio starts (t = pause_ms)
→ Lip sync follows audio
```

### Parallel Processing

```
User turn ends
├── ASR finalization          (parallel)
├── Facial analysis           (parallel)
├── Voice prosody analysis    (parallel)
└── Text emotion analysis     (parallel)
    ↓ all complete
    Fusion → Appraisal → State Update → Memory Retrieval
    ↓
    Behavior Plan → LLM (streaming) → TTS (streaming) → Avatar
```

---

## 22. FAILURE HANDLING [OUR PROPOSED]

### Conflicting Modalities

```python
if conflict_detected:
    # Trust non-verbal over verbal
    weight_text *= 0.5
    flag_for_logging("modality_conflict", conflict_type)
    # Use higher uncertainty in downstream reasoning
    user_state.uncertainty += 0.2
```

### Missing Modalities

| Failure | Fallback |
|---|---|
| No camera / face not visible | Use text + voice only; set face_confidence = 0 |
| Poor audio / high noise | Use text + face only; flag voice_unreliable |
| ASR failure | Use last partial transcript + context |
| All modalities failed | Use conversation history + last known state |

### Emotional Edge Cases

| Case | Handling |
|---|---|
| Sarcasm detected | Flag text_voice_conflict; trust voice + face |
| Fake smile (Duchenne check) | AU6 (cheek raise) absent → flag as non-genuine |
| Sudden mood change | Spike detection → increase appraisal urgency |
| Emotional overreaction | Clamp affect delta per turn; smooth over 3 turns |
| Emotional state drift | Periodic reanchoring to mood baseline |
| User hides emotion | Low confidence flag; use conservative response |

### System Failures

| Failure | Handling |
|---|---|
| LLM timeout | Retry with shorter context; fallback to template response |
| TTS failure | Text-only response; log for monitoring |
| Avatar failure | Audio-only mode; notify user |
| Memory DB unavailable | Proceed without memory; flag degraded mode |
| WebRTC disconnect | Reconnect with session state preserved in Redis |
| Affect state corruption | Reset to neutral + mood baseline; log incident |

### Inappropriate Emotional Response Guard

```python
def validate_behavior_plan(plan, user_state, relationship):
    # Never express anger toward distressed user
    if user_state.emotion == "distress" and plan.emotion == "anger":
        plan.emotion = "concern"
        plan.intensity *= 0.5
        log_warning("emotional_response_corrected")

    # Never be dismissive when user shows high arousal distress
    if user_state.arousal > 0.8 and user_state.valence < -0.5:
        if plan.response_strategy == "deflect":
            plan.response_strategy = "validate_and_support"
```

---

## 23. EXPERIMENTS [OUR PROPOSED]

### Baseline Conditions

| Condition | Description |
|---|---|
| A — LLM Only | GPT-4o, no memory, no emotion, no avatar |
| B — LLM + Memory | + Semantic memory retrieval |
| C — LLM + Avatar | + Anam avatar, no emotion |
| D — LLM + Emotion | + Affective state, no memory |
| E — LLM + Emotion + Memory | + Emotion-weighted memory |
| F — LLM + Multimodal Perception | + Audio + video user state |
| G — FULL SYSTEM | All components active |

### Experiment 1: Persistent Affect vs Stateless

**Design:** Same user input sequence across 10 turns. Compare A vs G.
**Measure:** Emotional appropriateness ratings, cross-modal coherence, human-likeness.

### Experiment 2: Same Situation, Different Emotional History [KEY EXPERIMENT]

```
Agent A: high trust history, positive emotional trajectory
Agent B: low trust history, negative emotional trajectory

Input: "I need to tell you something important"

Measure:
- Memory retrieved (which memories surface)
- Reasoning differences (LLM context comparison)
- Response text differences
- Voice prosody differences (pitch, rate, pause)
- Facial expression differences
- Decision differences (strategy selected)
```

### Experiment 3: Causal Affect Intervention [OUR PROPOSED]

```
Intervene on affective state while keeping ALL other variables constant:
  Condition 1: valence = +0.7 (positive)
  Condition 2: valence =  0.0 (neutral)
  Condition 3: valence = -0.7 (negative)

Same user input, same memory, same relationship state.

Measure:
- Memory retrieval differences
- LLM reasoning differences
- Response strategy differences
- Voice prosody differences
- Facial expression differences

Goal: Establish causal effect of affect on behavior
```

### Experiment 4: Multimodal Fusion Accuracy

```
Compare user emotion estimation accuracy:
  Text only vs Voice only vs Face only vs Fused

Ground truth: Human annotator ratings
Metric: Valence/arousal RMSE, emotion classification F1
```

### Experiment 5: Cross-Modal Coherence

```
Rate coherence between text, voice, face, timing:
  Condition A: Independent modality generation
  Condition B: Behavior-plan-driven generation

Metric: Cross-modal coherence score (human ratings + automated)
```

---

## 24. EVALUATION METRICS [OUR PROPOSED]

### Human Evaluation (Blind)

| Metric | Scale | Method |
|---|---|---|
| Human-likeness | 1–7 Likert | Blind rater comparison |
| Perceived naturalness | 1–7 Likert | Post-interaction survey |
| Emotional appropriateness | 1–7 Likert | Turn-level annotation |
| Social presence | IPO Social Presence Scale | Post-session |
| Voice naturalness | MOS (1–5) | Audio-only blind test |
| Facial realism | 1–7 Likert | Video-only blind test |
| Cross-modal coherence | 1–7 Likert | Full interaction rating |
| Turn-taking quality | 1–7 Likert | Naturalness of conversation flow |
| Uncanny valley | Mori scale adapted | Blind rating |
| AI detection rate | % correctly identified as AI | Turing-style test |
| Long-term consistency | 1–7 Likert | Multi-session rating |
| Relationship continuity | 1–7 Likert | Multi-session rating |

### Automated Metrics

| Metric | Method |
|---|---|
| Valence/arousal RMSE | vs ground truth annotations |
| Emotion classification F1 | vs annotated labels |
| Cross-modal coherence score | Embedding similarity across modalities |
| Response latency | P50, P95, P99 milliseconds |
| Memory retrieval relevance | Human relevance ratings |
| Affect state stability | Variance over time |
| Behavioral consistency | Cosine similarity of behavior plans across similar contexts |

---

## 25. MVP ARCHITECTURE [OUR PROPOSED]

The MVP validates the core research loop with minimal infrastructure.

### MVP Scope

```
✅ Audio input (microphone)
✅ ASR (Whisper)
✅ Text emotion analysis
✅ Voice prosody analysis
✅ Basic affective state engine (valence + arousal)
✅ Simple episodic memory (last 5 sessions)
✅ Behavior planner (basic)
✅ LLM (GPT-4o)
✅ TTS (ElevenLabs)
✅ Anam avatar (baseline)
❌ Camera / facial analysis (Phase 2)
❌ Gaze estimation (Phase 2)
❌ Full relationship engine (Phase 2)
❌ Micro-expressions (Phase 2)
```

### MVP Architecture Diagram

```mermaid
graph LR
    MIC3[Mic] --> ASR3[Whisper]
    ASR3 --> VPROS3[Voice Emotion]
    ASR3 --> TEMO3[Text Emotion]
    VPROS3 --> FUSE3[Simple Fusion]
    TEMO3 --> FUSE3
    FUSE3 --> PAS3[Affective State\nvalence + arousal]
    PAS3 --> MEM3[Episodic Memory\nPostgres]
    PAS3 --> BP3[Behavior Planner]
    MEM3 --> BP3
    BP3 --> LLM3[GPT-4o]
    LLM3 --> TTS3[ElevenLabs]
    TTS3 --> ANAM[Anam Avatar]
    PAS3 --> |persist| REDIS3[Redis]
```

### MVP Timeline

| Week | Milestone |
|---|---|
| 1–2 | ASR + voice emotion + text emotion pipeline |
| 3–4 | Affective state engine + basic memory |
| 5–6 | Behavior planner + LLM integration |
| 7–8 | TTS + Anam avatar integration |
| 9–10 | End-to-end testing + baseline experiments |

---

## 26. RESEARCH PROTOTYPE ARCHITECTURE [OUR PROPOSED]

Extends MVP with full multimodal perception and complete research components.

### Additional Components

```
✅ Camera input
✅ Facial expression analysis (py-feat)
✅ Gaze estimation (MediaPipe + L2CS-Net)
✅ Full multimodal fusion with conflict detection
✅ Cognitive appraisal engine
✅ Full affective state (all variables)
✅ Emotion-aware memory retrieval
✅ Relationship engine
✅ Custom blendshape expression controller
✅ Gaze controller
✅ Pre-expression technique
✅ Turn-taking controller
✅ Experiment logging + observability
```

### Research Prototype Diagram

```mermaid
graph TD
    subgraph PERCEPT["FULL PERCEPTION"]
        A1[Whisper ASR]
        A2[Voice Prosody]
        A3[py-feat Face]
        A4[MediaPipe Gaze]
        A5[Text Emotion RoBERTa]
    end

    subgraph CORE["CORE INTELLIGENCE"]
        B1[Multimodal Fusion]
        B2[Appraisal Engine]
        B3[Affective State Engine]
        B4[Relationship Engine]
        B5[Emotion-Aware Memory]
        B6[Behavior Planner]
        B7[LLM + LangGraph]
    end

    subgraph OUTGEN["OUTPUT GENERATION"]
        C1[TTS Cartesia]
        C2[Blendshape Controller]
        C3[Gaze Controller]
        C4[Gesture Controller]
        C5[Timing Controller]
    end

    subgraph RENDER["RENDERING"]
        D1[LiveKit WebRTC]
        D2[Avatar Renderer]
    end

    subgraph RESEARCH["RESEARCH LAYER"]
        E1[Experiment Logger]
        E2[State Snapshots]
        E3[Intervention API]
        E4[Metrics Collector]
    end

    PERCEPT --> CORE
    CORE --> OUTGEN
    OUTGEN --> RENDER
    CORE --> RESEARCH
```

---

## 27. PRODUCTION ARCHITECTURE [OUR PROPOSED]

### Deployment Architecture

```mermaid
graph TD
    subgraph CDN["CDN / Edge"]
        CF[CloudFront / Vercel Edge]
    end

    subgraph FRONTEND["Frontend (Vercel)"]
        NEXT[Next.js App]
    end

    subgraph BACKEND["Backend (AWS ECS / K8s)"]
        GW[API Gateway + Auth]
        PERC[Perception Service]
        FUSE4[Fusion + Appraisal Service]
        STATE4[State Service]
        MEM4[Memory Service]
        REASON4[Reasoning Service]
        OUTPUT4[Output Service]
    end

    subgraph REALTIME["Real-Time (LiveKit Cloud / Self-hosted)"]
        LK4[LiveKit Server]
    end

    subgraph DATA["Data Layer"]
        PG4[PostgreSQL RDS]
        REDIS4[Redis ElastiCache]
        S3[S3 — Session artifacts]
    end

    subgraph AI["AI APIs"]
        OPENAI[OpenAI / Anthropic]
        CART[Cartesia TTS]
        ELEVEN[ElevenLabs TTS]
    end

    CF --> NEXT
    NEXT --> GW
    GW --> PERC
    GW --> FUSE4
    FUSE4 --> STATE4
    STATE4 --> MEM4
    MEM4 --> PG4
    STATE4 --> REDIS4
    REASON4 --> OPENAI
    OUTPUT4 --> CART
    OUTPUT4 --> LK4
    LK4 --> NEXT
```

### Scaling Strategy

| Component | Scaling Approach |
|---|---|
| Perception Service | Horizontal (stateless, GPU nodes) |
| Fusion + Appraisal | Horizontal (stateless) |
| State Service | Single writer per user (Redis lock) |
| Memory Service | Read replicas for retrieval |
| LLM | API-based (auto-scales) |
| TTS | API-based (auto-scales) |
| LiveKit | Horizontal room-based scaling |

---

## 28. RESEARCH CONTRIBUTION / GAP [OUR PROPOSED]

### What We Contribute

**[RESEARCH GAP 1] — Persistent Affective State Engine**
No existing conversational AI system maintains a continuous, multi-variable internal emotional state that persists across sessions and decays/recovers over time. We design and implement this as a first-class system component.

**[RESEARCH GAP 2] — Multimodal User State Fusion with Conflict Resolution**
Existing systems use audio-only or text-only emotion detection. We design a fusion engine that handles conflicting signals across text, voice, and face with confidence-weighted resolution and temporal smoothing.

**[RESEARCH GAP 3] — Emotion-Conditioned Cognition (not just generation)**
Existing work (Chain-of-Emotion, emotional prompting) modifies generation. We design a system where affect modifies attention, memory retrieval, interpretation, goal prioritization, and strategy selection BEFORE generation.

**[RESEARCH GAP 4] — Centralized Behavior Plan for Cross-Modal Coherence**
No existing system uses a single behavior plan as the source of truth for text, voice, face, gesture, and timing simultaneously. We design and evaluate this approach.

**[RESEARCH GAP 5] — Causal Affect Experiment**
No existing work has performed controlled causal interventions on an agent's affective state while holding all other variables constant to measure downstream behavioral effects. We design this experiment.

**[RESEARCH GAP 6] — Long-Term Relationship + Emotional Trajectory**
Existing systems reset per session. We design persistent relationship state that evolves across sessions and influences all downstream behavior.

### Novelty Claims (Evidence-Based)

| Claim | Evidence Basis |
|---|---|
| Persistent affective state across sessions | No existing system documented with this capability |
| Emotion-weighted memory retrieval | Emotional RAG exists but without persistent state integration |
| Behavior plan as cross-modal source of truth | Not found in literature review |
| Causal affect intervention experiment | Not found in conversational AI literature |
| Relationship-phase-dependent behavior | Generative Agents has memory but not embodied behavior |

### What We Do NOT Claim

- We do not claim to solve the hard problem of machine consciousness
- We do not claim the agent "truly feels" emotions
- We do not claim 100% human indistinguishability
- We claim measurable improvements in human-likeness ratings and emotional coherence over baselines

---

## APPENDIX A — FULL DATA FLOW SEQUENCE DIAGRAM

```mermaid
sequenceDiagram
    participant U as User
    participant C as Browser Client
    participant WS as WebSocket Server
    participant P as Perception Service
    participant F as Fusion + Appraisal
    participant S as State Service
    participant M as Memory Service
    participant R as Reasoning Service
    participant O as Output Service
    participant AV as Avatar (LiveKit)

    U->>C: Speaks + shows face
    C->>WS: audio_chunk + video_frame
    WS->>P: Route to perception
    P->>P: ASR (Whisper streaming)
    P->>P: Voice prosody analysis
    P->>P: Facial expression analysis
    P->>P: Gaze estimation
    P->>P: Text emotion analysis
    P->>F: All modality outputs
    F->>F: Multimodal fusion
    F->>F: Conflict detection
    F->>F: Cognitive appraisal
    F->>S: User state + appraisal
    S->>S: Update affective state
    S->>S: Update relationship state
    S->>M: Emotion-aware memory retrieval
    M->>S: Retrieved memories
    S->>R: Affect state + memories + user state
    R->>R: Build affect-conditioned context
    R->>R: Behavior planning
    R->>R: LLM generation (streaming)
    R->>O: Response text + behavior plan
    O->>O: Prosody planning
    O->>O: TTS (streaming)
    O->>O: Blendshape calculation
    O->>AV: Audio stream + face data
    AV->>C: WebRTC stream
    C->>U: Avatar speaks + expresses
    S->>S: Post-interaction state update
    S->>M: Store new memory
```

---

## APPENDIX B — COMPONENT LABELS SUMMARY

| Component | Label |
|---|---|
| Anam SDK (WebRTC, session tokens, utterance IDs) | [EXISTING] |
| LiveKit WebRTC transport | [EXISTING] |
| Whisper ASR | [EXISTING] |
| py-feat facial analysis | [EXISTING] |
| MediaPipe gaze | [EXISTING] |
| SpeechBrain voice emotion | [EXISTING] |
| Generative Agents memory architecture | [INSPIRED BY EXISTING] |
| Chain-of-Emotion reasoning steps | [INSPIRED BY EXISTING] |
| Emotional RAG retrieval weighting | [INSPIRED BY EXISTING] |
| Scherer appraisal dimensions | [INSPIRED BY EXISTING] |
| Multimodal fusion with conflict resolution | [OUR PROPOSED] |
| Persistent affective state engine | [OUR PROPOSED] |
| Emotion-conditioned cognition pipeline | [OUR PROPOSED] |
| Centralized behavior plan | [OUR PROPOSED] |
| Pre-expression technique | [OUR PROPOSED] |
| Relationship phase dynamics | [OUR PROPOSED] |
| Causal affect intervention experiment | [OUR PROPOSED] |
| Cross-modal coherence evaluation | [OUR PROPOSED] |
| Emotion-aware memory retrieval formula | [OUR PROPOSED] |

---

*Document version: 1.0 — Research Architecture*
*Classification: Internal Research*
