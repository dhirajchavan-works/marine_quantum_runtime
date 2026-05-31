# TESTING_PACKET.md
# BHIV Universal Testing Protocol v2
# Marine Intelligence Quantum Runtime — Current Task

**Prepared for:** Vinayak (Testing Department)
**Prepared by:** Dhiraj Chavan
**Protocol:** BHIV Universal Testing Protocol v2
**Date:** May 2026

---

## Pre-Test Setup

```bash
git clone <repo-url>
cd marine_quantum_runtime
python --version   # Must be Python 3.8+
# No pip installs needed — pure stdlib
```

## Quick Run — All 4 Modules

```bash
python run/run_signal.py           && echo "signal PASS"
python run/run_quantum_pipeline.py && echo "quantum PASS"
python run/run_distributed_qapp.py && echo "distributed PASS"
python run/run_operational_drift.py && echo "monitoring PASS"
```

All must exit code `0`. Any non-zero = FAIL.

---

## Domain 1 — Signal Generator

### TC-SIG-01: Single execution produces correct output

```bash
python run/run_signal.py
```

Expected Phase 4 output:
```
transition: CONVERGED
cause: confidence=0.92>=0.85, variance=0.002<=0.005, energy_delta=0.0001<=0.005
sigma: 0.04472136
ts: 2026-01-01T02:00:00Z
```

Pass criteria: transition=CONVERGED, sigma=0.04472136, ts=2026-01-01T02:00:00Z

---

### TC-SIG-02: Determinism — 5 runs identical

Phase 6 output must show `[PASS] All 5 outputs IDENTICAL`.

Pass criteria: All 5 `transition`, `sigma`, `ts` values identical.

---

### TC-SIG-03: Failure cases — 4 detections

Phase 5 must show all 4 cases handled:

| Case | Expected |
|---|---|
| Low confidence (0.55) | transition=SUSPENDED |
| High energy_delta (0.05) | transition=DIVERGED |
| Missing field | VALIDATION_ERROR |
| confidence=1.5 | VALIDATION_ERROR |

---

## Domain 2 — Quantum Pipeline

### TC-QP-01: Single quantum execution

```bash
python run/run_quantum_pipeline.py
```

Expected Phase 1:
- `degradation_probability` in `[0.0, 1.0]`
- `confidence_score` >= 0.5
- `recommended_anode_current` > 0
- `risk_level` in `{LOW, MODERATE, ELEVATED, CRITICAL}`
- `signal` in `{HOLD, INCREASE_ANODE_CURRENT}`

---

### TC-QP-02: Determinism — 5 runs same seed

Phase 3 must show identical `degradation_probability` and `dominant_state` across all 5 runs.

Pass criteria: `[PASS] All 5 outputs IDENTICAL`.

---

### TC-QP-03: Failure rejection

Phase 2 must show:
- `salinity=999.0` → `INPUT_VALIDATION_FAILED` or `VALIDATION_ERROR`
- Missing `pH` → validation error status
- Negative dissolved oxygen → validation error status

---

## Domain 3 — Distributed QApp Propagation

### TC-DIST-01: Propagation and replay

```bash
python run/run_distributed_qapp.py
```

Phase 4 must show all 3 nodes with `✅` hash match:
```
Node_A  live=<hash>  replay=<hash>  ✅
Node_B  live=<hash>  replay=<hash>  ✅
Node_C  live=<hash>  replay=<hash>  ✅
```

---

### TC-DIST-02: Determinism 5× replay (Proof A)

Phase 7 Proof A must show `[PASS] All 5 hashes IDENTICAL`.

---

### TC-DIST-03: Shuffle convergence (Proof B)

Phase 7 Proof B: all 3 shuffle trials must show `✅ YES`.

Pass criteria: `[PASS] All shuffled replays converge to canonical`.

---

### TC-DIST-04: 4 failure cases detected

Phase 5 summary must show:
```
✅  delayed_propagation      : DELAYED
✅  duplicate_propagation    : REJECTED
✅  missing_propagation      : HALTED
✅  out_of_order_sequence    : HALTED
```

---

## Domain 4 — Operational Drift Monitor

### TC-MON-01: Stream ingest

```bash
python run/run_operational_drift.py
```

Phase 2 must show `events_ingested=10` and at least 1 drift event detected
(confidence drops in test stream are designed to trigger drift).

---

### TC-MON-02: Determinism — 5 runs

Phase 4 must show `[PASS] All 5 runs IDENTICAL`.

---

### TC-MON-03: invoke_runtime surface

Phase 5 must list all 4 modules as `AVAILABLE`:
```
signal               : AVAILABLE
quantum_pipeline     : AVAILABLE
distributed_qapp     : AVAILABLE
operational_monitor  : AVAILABLE
```

---

## Domain 5 — QApp Descriptor Registry

### TC-DESC-01: marine_corrosion_qapp registered

```bash
python3 -c "
import sys; sys.path.insert(0, '.')
from src.quantum.descriptors import list_registered, get
print('Registered:', list_registered())
d = get('marine_corrosion_qapp')
print('Name:', d.name, '| Version:', d.version)
"
```

Expected: `marine_corrosion_qapp` in registered list, version=`1.0.0`.

---

### TC-DESC-02: invoke through descriptor

```bash
python3 -c "
import sys; sys.path.insert(0, '.')
from src.quantum.descriptors import invoke
result = invoke('marine_corrosion_qapp', {
    'node_id': 'qnode_01', 'energy_delta': 0.0001,
    'iterations': 120, 'confidence': 0.92, 'variance': 0.002
})
print('Status:', result['status'])
print('risk_level:', result['result']['risk_level'])
"
```

Expected: `status=SUCCESS`, `risk_level` in valid set.

---

## Final Verdict Form

```
Tester             : ________________________
Date tested        : ________________________
Python version     : ________________________

Domain results:

  DOMAIN 1  Signal Generator            PASS / FAIL
  DOMAIN 2  Quantum Pipeline            PASS / FAIL
  DOMAIN 3  Distributed QApp            PASS / FAIL
  DOMAIN 4  Operational Drift Monitor   PASS / FAIL
  DOMAIN 5  QApp Descriptor Registry    PASS / FAIL

Test case results:

  TC-SIG-01    PASS / FAIL
  TC-SIG-02    PASS / FAIL
  TC-SIG-03    PASS / FAIL
  TC-QP-01     PASS / FAIL
  TC-QP-02     PASS / FAIL
  TC-QP-03     PASS / FAIL
  TC-DIST-01   PASS / FAIL
  TC-DIST-02   PASS / FAIL
  TC-DIST-03   PASS / FAIL
  TC-DIST-04   PASS / FAIL
  TC-MON-01    PASS / FAIL
  TC-MON-02    PASS / FAIL
  TC-MON-03    PASS / FAIL
  TC-DESC-01   PASS / FAIL
  TC-DESC-02   PASS / FAIL

Overall verdict : PASS / FAIL

Notes:
_______________________________________________________________
```

---

*BHIV Universal Testing Protocol v2 | Marine Intelligence System | May 2026*
