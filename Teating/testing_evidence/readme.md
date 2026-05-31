# testing_evidence/

Required screenshots — populate after live run on your machine.

| File | Contents Required |
|---|---|
| `terminal_run.png` | Full terminal — `python run/run_signal.py` Phase 4 output |
| `determinism_run.png` | Phase 6 — all 5 CONVERGED + [PASS] line |
| `failure_case_1.png` | Phase 5 — Low confidence → SUSPENDED |
| `failure_case_2.png` | Phase 5 — High energy_delta → DIVERGED |
| `failure_case_3.png` | Phase 5 — Missing field → ValidationError |
| `failure_case_4.png` | Phase 5 — confidence out of range → ValidationError |
| `distributed_run.png` | `python run/run_distributed_qapp.py` — Phase 4 hash match |
| `determinism_distributed.png` | Phase 7 Proof A — 5× replay identical |
| `shuffle_convergence.png` | Phase 7 Proof B — 3× shuffle converges |
| `repo_tree.png` | Repository file tree |
| `git_status.png` | Output of `git status` |
| `git_log.png` | Output of `git log --oneline` |

All screenshots must show:
- Full terminal window
- Visible command executed
- Timestamp/taskbar visible where possible
- No cropped success-only screenshots
