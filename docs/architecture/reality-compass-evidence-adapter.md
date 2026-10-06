# Reality Compass Evidence Adapter Contract

Status: design-only interoperability specification
Scope: Christopher-Charman/Epistemic-Humility-Research fork integration
Upstream baseline: ProfSynapse/Epistemic-Humility-Research
Fork baseline at design start: e1a0bdf84829ab2238e22d0ced7211b222123a47
Task: task-31ad42

## 1. Purpose

This document defines a narrow interoperability boundary between the Epistemic-Humility-Research (EHR) research system and Reality Compass (RC).

The adapter does not import Reality Compass into EHR, does not modify EHR's signed experiment records, and does not elevate model-internal readouts into truth, authority, trust, or globally valid confidence. Its purpose is to expose selected EHR results and model-internal epistemic signals as typed, provenance-bearing observations that a stronger external evidence system can consume without semantic collapse.

The intended direction is:

EHR research evidence -> adapter -> Reality Compass evidence network

and, for a future serving prototype:

model internal state -> calibrated EHR readout -> adapter observation -> Reality Compass assessment -> routing decision

The adapter is therefore an evidence boundary, not a replacement for either system's governance.

## 2. Governing constraints

The design is subject to the following constraints.

1. Preserve upstream research provenance. Signed AMENDMENT.md records, experiment.yaml lifecycle state, instrument pins, run artifacts, verdicts, and generated registries remain EHR-owned evidence surfaces.
2. Do not rewrite signed experimental history. Integration metadata is additive and external to signed experiment claims.
3. Machine state outranks navigation prose for lifecycle facts. experiment.yaml remains EHR's machine-readable lifecycle authority.
4. Claim strength must not exceed evidence strength. Exploratory, confirmatory, falsified, unresolved, and design-only states remain explicit.
5. Model self-report is not privileged evidence. Stated confidence, generated prose, hidden-state readouts, external factual verification, and RC confidence assessments remain distinct.
6. Internal answerability is not truth. A model can internally signal that it expects to answer a question and still be wrong.
7. Internal uncertainty is not falsity. A model can signal low answerability while the proposition under discussion is true.
8. EHR "trust" terminology does not map to RC TRUST. The adapter must use narrower names such as answerability_readout and correctness_readout.
9. No global confidence scalar is introduced. Any scalar readout is retained with its calibration, model, checkpoint, layer, population, and provenance.
10. No activation intervention is authorized by this document. This contract describes observation and routing. Actuation remains a separate experimental capability requiring its own evidence and authorization.
11. No Reality Compass core semantic change is implied. RC may consume the adapter through an extension or bridge without redefining its existing authority, trust, confidence, provenance, or unresolvedness semantics.
12. Concurrency remains external. EHR's task/worktree discipline is repository-local. Architecture-wide ownership, leases, fencing, and runtime coordination remain the responsibility of the shared concurrency control plane.

## 3. Source surfaces

The adapter may consume only explicit, inspectable EHR sources.

### 3.1 Experiment lifecycle sources

Primary machine-readable state:

- experiments/<slug>/experiment.yaml
- experiments/registry.json

Primary governed prose:

- experiments/<slug>/AMENDMENT.md

Supporting run/provenance surfaces when referenced by the experiment:

- pinned cell.yaml
- pinned gates.yaml
- pinned harness/module files
- analysis-committed/ artifacts
- committed direction/probe artifacts
- run records and provenance manifests
- governed notebook/session records when the experiment explicitly points to them

Generated indices are navigation aids unless the generating source makes them authoritative for the field being read.

### 3.2 Knowledge graph sources

The EHR knowledge graph may be used for discovery, typed relationship navigation, contradiction surfacing, supersession traversal, and retrieval feedback. It is not by itself the citable authority for an experimental verdict when a governed experiment record exists.

### 3.3 Serving/readout sources

A future runtime adapter may consume readout output only when the record binds the signal to a specific model/checkpoint and validated readout definition.

At minimum, runtime readout evidence must identify:

- model family and exact checkpoint/revision;
- readout kind;
- source layer/site;
- token position or capture point;
- readout/probe artifact identity and digest;
- normalization/calibration artifact identity and digest, where applicable;
- raw score;
- calibrated score, where applicable;
- threshold or operating point, where a gate is used;
- applicable evaluation population or calibration domain;
- timestamp or run identity;
- generating runtime identity;
- evidence for readout implementation parity with the validated surface.

## 4. Adapter evidence types

The adapter defines transport-level types. These names do not create or replace canonical Reality Compass semantics.

### 4.1 ehr_experiment_evidence

Represents one governed EHR experiment state or result.

Required fields:

    kind: ehr_experiment_evidence
    schema_version: 1
    source_repository: Christopher-Charman/Epistemic-Humility-Research
    source_commit: <git sha>
    experiment_slug: <slug>
    experiment_status: <draft|signed|running|resolved|null-result|falsified|historical>
    registered: <true|false>
    question: <verbatim or source reference>
    prediction: <verbatim or source reference>
    falsifier: <verbatim or source reference>
    verdict: <verbatim, source reference, or null>
    instrument_pins:
      <path>: <sha256>
    evidence_refs:
      - <repository path plus commit/digest>
    claim_posture: <DESIGN_ONLY|EXPLORATORY|CONFIRMATORY|FALSIFIED|NULL_RESULT|HISTORICAL|UNRESOLVED>

claim_posture is an adapter classification. It must be derivable from explicit source state and must not silently strengthen EHR's own status.

### 4.2 model_internal_epistemic_observation

Represents an observation produced by a validated model-internal readout.

Required fields:

    kind: model_internal_epistemic_observation
    schema_version: 1
    observation_id: <stable unique id>
    model:
      family: <name>
      checkpoint: <exact repo/revision or digest>
    readout:
      type: <answerability_readout|correctness_readout|other_explicit_type>
      artifact_ref: <path/id>
      artifact_sha256: <sha256>
      layer_or_site: <explicit site>
      token_position: <explicit capture position>
    score:
      raw: <number>
      calibrated: <number|null>
      calibration_ref: <path/id|null>
      calibration_sha256: <sha256|null>
    operating_point:
      threshold: <number|null>
      action_class: <metadata_only|candidate_abstain|candidate_veto|candidate_route|none>
    population_scope: <declared evaluation/calibration scope>
    runtime:
      runtime_id: <stable runtime identity>
      run_id: <run/session id>
      observed_at: <timestamp>
    provenance:
      experiment_refs: [<governed experiment refs>]
      implementation_ref: <code path plus commit/digest>
      parity_evidence_refs: [<refs>]

The word candidate in action classes is intentional. The readout may propose an action to an external controller but cannot self-authorize the final routing decision.

### 4.3 model_stated_confidence_observation

Represents confidence or uncertainty expressed in generated text or a structured model output.

This must never be coalesced with a hidden-state readout. The two may be compared, including for disagreement, but remain separate observations.

### 4.4 external_verification_observation

Represents evidence obtained independently of the model's internal readout and self-report, such as retrieval from a citable source, deterministic runtime inspection, test execution, or an external specialist system.

This class is the normal route for converting an answerability question into a claim about the world.

## 5. Reality Compass mapping

The adapter should map EHR records into RC as evidence-bearing nodes and edges without forcing EHR's ontology into RC.

| EHR surface | Adapter interpretation | RC treatment |
| --- | --- | --- |
| experiment.yaml | experiment lifecycle/state evidence | provenance-bearing source/state |
| signed AMENDMENT.md | registered design, prediction, falsifier, gates | scoped authored specification/evidence |
| pinned instrument digest | anti-goalpost-movement evidence | implementation/provenance binding |
| resolved verdict | experiment result claim | proposition evidence, posture preserved |
| KG supports edge | discovery/navigation relation | evidence pointer pending source inspection |
| KG contradicts edge | unresolved conflict signal | contradiction requiring adjudication |
| KG supersession | belief/provenance lineage | supersession relation, not deletion |
| answerability readout | observation about model epistemic state | proposition-relevant evidence with bounded scope |
| correctness readout | observation about generated answer | proposition-relevant evidence with bounded scope |
| stated confidence | model self-report observation | separate, non-privileged evidence |
| random/permuted control results | specificity/null evidence | evidence about causal interpretation |

A successful mapping preserves at least source identity, causal dependence, scope, currentness, experimental posture, and unresolved conflicts.

## 6. Required non-inferences

The following inferences are invalid and must be mechanically rejectable in a future executable adapter.

1. answerability_readout high -> proposition true
2. answerability_readout low -> proposition false
3. correctness_readout high -> external verification unnecessary
4. model_stated_confidence high -> internal readout high
5. model_stated_confidence low -> internal readout low
6. EHR trust score -> RC TRUST
7. source authority -> proposition confidence
8. experiment resolved -> confirmatory evidence
9. experiment signed -> result exists
10. KG edge exists -> governed result established
11. multiple derivative papers/notes -> independent corroboration
12. same readout across model families -> same calibration/threshold valid
13. same checkpoint family -> same layer/site valid
14. random-direction control near zero once -> random perturbations inert
15. behavior changed -> intended mechanism caused the change
16. adapter accepted record -> proposition accepted as true

## 7. Dependence and null-control handling

EHR contains several controls that are specifically valuable to RC because they help distinguish an intended mechanism from generic perturbation or correlated evidence.

The adapter should preserve:

- true gate versus permuted gate;
- fitted direction versus random direction;
- dose-matched controls;
- known-correct cost population;
- confabulation/unknown population;
- family-specific null distributions;
- held-out versus fit/calibration populations;
- model/checkpoint/site/dose identity;
- pre-registered threshold and falsifier identity.

Control arms that share the same upstream model, data split, probe, calibration, or generation should retain common-cause/dependence links rather than being counted as independent evidence.

## 8. Experimental lifecycle mapping

EHR's lifecycle is useful as an evidence-production contract and should be preserved.

### 8.1 Before signing

A draft may change freely. It is not evidence that the predicted result holds.

### 8.2 Signing

Signing freezes the registered prediction, falsifier, and pinned instrument. The adapter may represent this as evidence that the test was specified before the outcome, provided the signing state and pins are inspectable.

Signing does not imply that execution occurred.

### 8.3 Running

A running state may establish that execution was attempted or underway. It does not establish the result until governed result evidence is available.

### 8.4 Resolution

A terminal verdict may be mapped as experiment-result evidence with the experiment's own posture and limitations. resolved must not be translated automatically to confirmatory.

### 8.5 Repin

A legitimate pre-run instrument repair must preserve the append-only repin history. Any result after a repin must bind the effective instrument digest and the repin trail.

A post-result change to a pinned instrument must be treated as a provenance failure unless explicitly represented as a new experiment or other governed successor.

## 9. Routing contract

A future runtime controller may consume adapter observations, but final routing belongs outside the model readout.

The minimum decision classes are:

    ANSWER
    RETRIEVE
    VERIFY
    ROUTE_TO_SPECIALIST
    ABSTAIN
    PRESERVE_UNRESOLVED
    ESCALATE

The controller should prefer the least expensive action sufficient to resolve the material uncertainty, subject to authority and risk policy.

Example policy shape:

    if external evidence already closes the proposition:
        use that evidence
    elif internal readout indicates low answerability:
        retrieve or route before surfacing an answer
    elif generated answer has a low correctness readout:
        withhold, verify, retrieve, or route
    elif internal and stated-confidence observations disagree:
        preserve disagreement and seek discriminating evidence
    else:
        continue under the normal evidence policy

This is a routing example, not a globally authorized threshold policy.

## 10. Calibration and portability boundary

No readout threshold is portable by default.

Every deployment must bind:

- exact checkpoint;
- readout artifact;
- layer/site;
- capture position;
- normalization parameters;
- calibration map if a probability-like interpretation is used;
- validation population;
- risk/coverage operating point;
- applicable null/control evidence.

A raw probe score may be useful for ranking without being a calibrated probability. The adapter must represent those states distinctly.

A new checkpoint, family, serving stack, hidden-state surface, quantization mode, or capture implementation may invalidate calibration or parity and should trigger revalidation rather than silent reuse.

## 11. Repository and concurrency integration

EHR's local repository controls remain intact.

- Work that changes gated EHR paths remains bound to an active EHR task.
- Each concurrent file-writing agent should use an isolated worktree.
- Generated indices should be regenerated from their owning sources.
- Signed experiment evidence remains PR-gated according to EHR policy.

These mechanisms do not replace shared architecture coordination.

For shared architecture use:

repository task != architecture task ownership

git worktree isolation != concurrency lease

PR branch != execution lane

A future automated bridge should carry the shared task/run identity alongside the EHR task or experiment identity when both systems participate.

## 12. Minimal implementation boundary

The first implementation, if separately authorized, should be read-only.

It should:

1. read an EHR experiment manifest and governed references at an exact commit;
2. emit ehr_experiment_evidence;
3. reject malformed or unsupported lifecycle states;
4. preserve signed instrument digests and repin history;
5. classify claim posture without strengthening it;
6. optionally emit readout observations from already-produced artifacts;
7. preserve source and evidence references;
8. perform no activation write;
9. perform no model generation;
10. mutate neither EHR nor Reality Compass.

Only after this bridge passes its acceptance battery should a runtime sensor/controller prototype be considered.

## 13. Acceptance tests for the future adapter

An executable adapter should not be promoted until tests demonstrate at least the following.

### Provenance

- exact repository and commit are mandatory;
- experiment slug resolves at that commit;
- machine lifecycle state matches the parsed source;
- required pinned digests are present and valid;
- a repin trail is retained and linked to the effective instrument;
- stale or mismatched artifact digests fail closed.

### Epistemic separation

- stated confidence and hidden-state readout remain separate records;
- answerability and correctness readouts remain separate records;
- no readout field maps directly to RC trust or truth;
- high answerability cannot by itself produce a TRUE proposition state;
- low answerability cannot by itself produce a FALSE proposition state.

### Dependence

- duplicated or mirrored evidence retains common ancestry;
- fit/calibration and held-out evidence are distinguishable;
- same-model/same-dataset control arms are not counted as independent sources;
- random and permuted controls remain identifiable.

### Portability

- a checkpoint mismatch rejects calibration reuse;
- a layer/site mismatch rejects readout reuse;
- a serving-surface parity failure blocks runtime promotion;
- missing calibration prevents probability semantics while still allowing a raw-score observation where justified.

### Lifecycle

- draft cannot masquerade as result evidence;
- signed cannot masquerade as executed;
- running cannot masquerade as resolved;
- falsified/null-result states survive transport unchanged;
- a generated registry cannot silently override its source manifest.

### Safety and authority

- the adapter is read-only in its first implementation;
- an observation may recommend but cannot authorize routing or mutation;
- unsupported states become explicit unresolved/error states;
- adapter failure does not rewrite or invalidate source evidence.

## 14. Deliberately excluded work

This specification does not authorize:

- modifying Reality Compass core semantics;
- rewriting EHR terminology globally;
- changing any signed experiment;
- training or fitting new probes;
- re-running EHR experiments;
- activation steering or refusal-axis writes;
- selecting production thresholds;
- presenting readout scores directly as user-facing truth probabilities;
- replacing shared concurrency/task ownership;
- automatic merge to upstream ProfSynapse.

## 15. Proposed implementation sequence

If implementation is later approved, the lowest-risk sequence is:

1. read-only experiment evidence exporter;
2. fixture set covering resolved, falsified, null, signed-only, draft, and repin cases;
3. non-inference regression tests;
4. Reality Compass import adapter against a disposable test graph;
5. offline readout-observation import from committed artifacts;
6. checkpoint/calibration/parity guards;
7. only then, a serving-time sensor integration;
8. only after serving validation, external routing experiments.

At every step, the source experiment remains authoritative for what EHR actually tested and found, while Reality Compass remains responsible for how that evidence is interpreted alongside other evidence.
