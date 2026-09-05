# 05 · Data & Code Availability —— Draft v0(2026-09-05)

## β2-AR main study (primary manuscript data)
- All source data are ChEMBL-derived and publicly releasable: the 30,000-compound screening library, the docking score tables (149,325 runs; validation and ex8 sets), DUD-E decoys, actives lists, trained XGBoost models (pickle) with JSON model cards, re-ranked leaderboards, and chemical review tables live in the companion repository `Beta2AR_EnOpt_GPCR_Screening` (`results/`, `data/`, `configs/`). Model cards report training data, features, hyper-parameters, and out-of-fold metrics for every model.
- Code: numbered scripts 01–11 plus `revalidation_ex8/` reproduce preparation, docking, training, validation, and figure generation. Environments: `environment-vina.yml` (conda-forge) and `requirements.txt`.

## FtsZ transfer case
- Processed tables, scripts, and figures are public in the FtsZ reconstruction repository. The original thesis spreadsheets (BP1/BP2), the source receptor PDB, and pocket-detection exports are local inputs and are not redistributed; `docs/data_availability.md` in that repository describes the exact raw-input layout needed to rerun from source. Vina/Open Babel are pinned (Vina 1.2.7, Open Babel 3.1.1).
- Provenance: the FtsZ reconstruction is the author's master's-project output and predates this study; the ensemble-optimizer idea piloted there is the intellectual starting point of the β2-AR work described here.

## This manuscript
- Drafts and figures: this repository (`manuscript/`, `figures/`, `docs/preprint_outline.md`).
- Pending before posting (checklist, in order): (1) verify all reference details (use citation management); (2) confirm target venue and author list; (3) generate journal-grade figures (300+ dpi TIFF/PDF/SVG) from the source-repository data; (4) decide whether to deposit trained models on Zenodo in addition to GitHub.