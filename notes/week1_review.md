# Week 1 Review (Sep 22–28)

## What got done
- Environment set up: Python 3.10.10, pandas, xgboost, scikit-learn, venv, all working
- FraudSense project moved into Git, pushed to GitHub
- PaySim (6.36M rows), MoMTSim (4.23M rows), and the synthetic Ghana dataset (10K rows) all downloaded and skimmed
- Schema mapping done for both PaySim and MoMTSim against the synthetic dataset
- Key finding: MoMTSim's fraud label is ~90% concentrated in one transaction type (TRANSFER) — a structural weakness worth flagging in the paper as a benchmarking limitation
- Confirmed all three datasets are wildly different on fraud rate (PaySim 0.13%, MoMTSim 52.8%, synthetic 50%) — raw accuracy/F1 won't be a valid way to compare across them; need AUC/precision-recall or typology-level comparison instead

## Blockers hit and resolved
- Git wasn't on PATH after install — needed a full VS Code restart, not just a new terminal
- Pasted Python code directly into the PowerShell terminal instead of a .py file — PowerShell tried to parse it as PowerShell syntax and failed
- A typo in a filename (`scheme` vs `schema`) broke `git add` until renamed

## Open questions for Week 2
- Recovered capstone methodology (HCBST resampling, XGBoost hyperparameters) hasn't been reconciled against these three very different fraud rates yet
- Typology relabeling hasn't started — Day 6's finding suggests it should lean on channel/device/region/party-type, not just transaction_type
- No modeling has happened yet — everything so far is schema and access work