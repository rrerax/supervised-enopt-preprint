# From consensus to learned ranking: reproducible ensemble docking for β2-adrenergic receptor screening, with a label-free FtsZ transfer case

> Single-file working draft · AUTO-ASSEMBLED 2026-09-05 from `manuscript/00–07` · Tables, figure legends and references are collected at the end · All figure/table numbers and reference details remain provisional until journal submission.

# 00 · Title & Abstract

## Title —— 已定稿(2026-09-05)
From consensus to learned ranking: reproducible ensemble docking for β2-adrenergic receptor screening, with a label-free FtsZ transfer case

## Keywords
ensemble docking; virtual screening; β2-adrenergic receptor; supervised reranking; DUD-E decoys; molecular-size bias

## Abstract (v1 · 2026-09-05)

Ensemble docking reduces reliance on a single receptor conformation but leaves a second problem open: how to combine a ligand's per-conformation docking scores into one ranking. Here we present a fully reproducible ensemble-docking screen of the β2-adrenergic receptor (β2-AR) in which an XGBoost model learns this combination from known actives—following the ensemble-optimizer (EnOpt) concept—and we test, layer by layer, what supervised reranking actually adds. 29,865 library molecules were docked against five active and inactive β2-AR conformations (AutoDock Vina 1.2; 149,325 docking runs). Trained on 48 in-library actives and expanded to 206 literature actives, with 2,978 property-matched DUD-E decoys as negatives, the model separated actives from decoys with an out-of-fold AUROC of 0.696 (95% CI 0.655–0.737)—statistically indistinguishable from a correctly oriented five-conformation average, yet with roughly twofold higher top-1% enrichment. Appending per-heavy-atom-normalized scores raised the AUROC to 0.751 (0.712–0.791) and removed a molecular-size bias that had inflated large molecules in the ranking. Re-docking the validation set at exhaustiveness 8 left these conclusions unchanged (confidence intervals overlap), indicating that sampling quality is not the current bottleneck. Chemical review shows the resulting top-200 shortlist is diverse and largely novel relative to the training actives. Finally, we show the framework transfers to a label-free setting: a legacy coumarin/FtsZ screen reconstructed from thesis data, where ensemble reranking stabilizes rankings but cannot support activity claims. All code, score tables, trained models, and reports are provided.

## 版本记录与待办
- v0(2026-09-05): 初稿。
- v1(2026-09-05): 定稿标题;压缩至 ~225 词;明确“监督重排增益集中在榜单头部”;CI 统一用 en dash。
- [x] 数字互核(2026-09-05 QA):Abstract/Methods/Results 关键指标一致(0.696/0.751/ex8 0.654 与 0.733;EF1% 3.9→7.3)。
- [ ] 投稿前: 与 results/ 表格逐格复核;按目标期刊控制词数;补充 funding/author 后微调“we/our”表述。

# 01 · Introduction —— Draft v0(2026-09-05)

Docking-based virtual screening (VS) is a workhorse of early-stage hit discovery: it scores large, purchasable compound libraries against a target structure and prioritizes a shortlist for experimental follow-up [5,6]. Its practical ceiling is set by two assumptions. First, the receptor is usually treated as a single rigid structure, ignoring the conformational plasticity that often governs ligand recognition. Second, the raw docking score is read as a direct proxy for affinity, although scores frequently correlate with molecular size and other physical properties—a bias we quantify for our own screen below—that can push artifactually favorable large molecules to the top of a ranked list.

These assumptions are especially consequential for G protein-coupled receptors (GPCRs). For the β2-adrenergic receptor (β2-AR), structures are available for both the inactive state (e.g., 2RH1 [1]) and the active, G-protein-coupled state (e.g., 3SN6 [2]). The two states differ most in the cytoplasmic half of transmembrane helix 6, whose rearrangement reshapes the orthosteric binding site [1,2]. Because a ligand that fits the inactive-state pocket may dock poorly into the active-state conformation—and vice versa—a single-structure screen can systematically mis-rank genuine actives whose preferred receptor state is not represented.

Ensemble docking addresses this by docking each ligand against several receptor conformations and merging the resulting per-conformation scores [4]. The open question is the merger rule. Simple consensus statistics (e.g., the mean or best score across the ensemble) ignore target-specific knowledge; the ensemble-optimizer (EnOpt) concept made the sharper point that the optimal combination is system-specific and can be learned from data [3]. In our own label-free pilot—a legacy coumarin/FtsZ screen reconstructed from thesis spreadsheets, summarized here as a transfer case—we tested the mechanics of ensemble reranking without experimental actives, where only a rank-consistency weighting was possible and no activity claim could be made.

This paper moves the same idea into the data-rich regime and asks a deliberately narrow question: with known actives available, can a supervised model learn to combine per-conformation docking scores better than consensus baselines, and where exactly does the learned ranking add value? To answer honestly, we built a fully reproducible β2-AR screen: 29,865 ChEMBL-derived molecules docked against five active/inactive conformations (149,325 runs, AutoDock Vina 1.2 [5,6]); an XGBoost reranker trained on 48 in-library actives, expanded to 206 literature actives [8], and validated against 2,978 property-matched DUD-E decoys [7] under strict out-of-fold cross-validation. We then ran three controls that we argue should accompany any such screen: (i) per-heavy-atom-normalized scores, to expose and correct molecular-size bias; (ii) re-docking of the validation set at higher sampling exhaustiveness (exhaustiveness 8), to test whether conclusions are sampling artifacts; and (iii) chemical review of the final shortlist for scaffold diversity [9], novelty relative to training actives, and problematic substructures (PAINS/Brenk) [10,12].

Three findings stand out. Supervised reranking roughly doubles top-1% enrichment relative to a correctly oriented five-conformation average, but is statistically tied with that average on the overall decoy-separation metric (out-of-fold AUROC 0.696 vs 0.719; confidence intervals overlap)—the model's demonstrable value is at the top of the list, not in global discrimination. Appending per-heavy-atom-normalized scores simultaneously raises the AUROC to 0.751 and removes the large-molecule ranking artifact. And re-docking at exhaustiveness 8 leaves every conclusion unchanged (all confidence intervals overlap), locating the remaining bottleneck in receptor and pose modelling rather than in docking sampling. Finally, the transfer case shows the framework degrades gracefully in a label-free setting: ensemble reranking stabilizes the ranking of reconstructed FtsZ candidates but cannot, by itself, assert biological activity.

In addition to these results, this work contributes a reproducible artifact—code, score tables, trained models, and reports are deposited in the companion repositories—and a set of reporting practices (out-of-fold evaluation, property-matched decoys, confidence intervals, and failure-case disclosure, e.g., the documented CHEMBL776 under-scoring case) that we believe would strengthen docking-based screening studies generally.

# 02 · Methods —— Draft v0(2026-09-05)

## 2.1 Shared framework
Both case studies follow one pipeline (Fig. 1): (i) assemble a compound library; (ii) prepare receptor and ligand structures; (iii) dock every ligand against every member of a receptor ensemble; (iv) reshape the docking runs into a ligand × conformation score matrix; (v) reduce the matrix to a single ranking with an explicit combiner — consensus statistics, a supervised model, or a label-free rank-consistency weighting; (vi) validate the ranking against decoys and chemical review; and (vii) emit a shortlist of candidates. All docking was performed with AutoDock Vina; ligand and receptor preparation used RDKit and Open Babel; analyses were written in Python (pandas, NumPy, scikit-learn, XGBoost, RDKit, Matplotlib/seaborn). Full scripts and configuration files are in the companion repositories.

## 2.2 β2-AR main study: data and receptor ensemble
- Library. A 30,000-compound subset of ChEMBL 37 (release 37) was used as the screening library (smiles + ChEMBL ids). After desalting (largest-fragment retention), canonicalization, 3D embedding and PDBQT conversion, 29,865 structures were docking-ready; the 135 failures (desalt/embed/prep) were recorded in a manifest rather than silently dropped.
- Receptor ensemble. Five β2-AR crystal structures cover inactive and active states: 2RH1 (inactive, carazolol-bound), 5D5A (inactive, carazolol-bound), 3SN6 (active, Gs-coupled), 4LDE and 4LDL (active, agonist-bound). Receptors were prepared from the PDB (relevant chain, native ligand removed, polar hydrogens as needed) and converted to PDBQT. The docking box was centred on the co-crystallized ligand with an 8 Å padding (default 24 Å cube).
- Docking. Each prepared ligand was docked against all five conformations with AutoDock Vina (v1.2.5), exhaustiveness 4, up to three modes, fixed seed (config `configs/beta2ar_screen.yml`). Completed runs: 149,325 (29,865 × 5), i.e. one ligand × conformation × mode-1 best-affinity table (`results/tables/docking_scores.csv`).

## 2.3 β2-AR main study: supervised reranking and decoy validation
- Labels. Positives: 48 β2-AR actives present in the library, curated from ChEMBL direct human assays (Ki/IC50/EC50 ≤ 1 µM; confidence score ≥ 8). In a first supervised model (Stage 2, `scripts/06`), the remaining library molecules were labelled negative by assumption; this model is reported only for transparency and is superseded.
- Stage-3 hard test. To replace assumed negatives with measured non-binders, 3,000 DUD-E decoys were generated from the β2-AR active list (property-matched by design; 2,978 unique after tautomer collapsing) and docked against the same five conformations (15,000 runs). Positives were expanded by 158 additional ChEMBL literature actives (pChEMBL ≥ 6, human, direct assays) restricted to the decoy 5–95 % molecular-weight window (250–516 Da) so the decoys remain a fair, hard negative set. The training set is therefore 206 actives vs 2,978 decoys.
- Features. Five features per ligand: the mode-1 Vina score against each conformation.
- Model and protocol. XGBoost classifier (15 trees, learning rate 0.3, max depth 6, scale_pos_weight = negatives/positives, AUC evaluation), evaluated with 3-fold stratified out-of-fold cross-validation (no ligand is scored by a model trained on it; seed 42). Baselines: simple mean and best score across the ensemble, sign-corrected (more negative = better).
- Metrics. Out-of-fold AUROC with Hanley–McNeil 95 % confidence intervals; enrichment factors at the top 1 % and 5 % of the ranked list; additionally a screening-like accounting in which the 29,865 library, decoys and expanded actives are ranked together.
- Reporting protocol. Because the hard-test AUROC of the learned model (0.696; CI 0.655–0.737) overlaps that of the correctly oriented mean (0.719; CI 0.679–0.760), conclusions are drawn from the combination of AUROC, enrichment, and failure-case inspection rather than from AUROC alone (`scripts/07`, `docs/decoy_validation_notes.md`).

## 2.4 Size-bias control: per-heavy-atom features
Raw Vina scores correlate with molecular size, so heavy-atom counts (HAC) were recomputed from SMILES with RDKit and five per-heavy-atom-normalized scores (score/HAC per conformation) appended as features (`scripts/10`). Feature sets compared under the identical 3-fold OOF protocol: raw (5), raw + per-ha (10), raw + per-ha + HAC (11), and per-ha only (5). The raw + per-ha set is the recommended ranking model (`results/feature_experiment_heavy_atom/`).

## 2.5 Sampling control: exhaustiveness-8 re-docking
To test whether ex4 results were limited by docking sampling, two independent re-docks were run at exhaustiveness 8 with 20 modes (fixed seed, Vina 1.2.5): (i) the full validation set (206 actives + 2,978 decoys = 3,184 ligands × 5 conformations = 15,920 runs) and (ii) a 201-ligand shortlist (raw+ha top-200 plus the documented failure case CHEMBL776) × 5 = 1,005 runs. Protocol and rerun scripts: `revalidation_ex8/`, `scripts/11_ex4_ex8_rerank.py`. Ranking stability was quantified with Spearman correlation between ex4 and ex8 scores per pocket and per ranking method, plus overlap of the ex4 top-100 within the ex8 top-100.

## 2.6 Shortlist chemistry: diversity, novelty, and interference
The recommended candidate list (raw+ha top-200) was reviewed chemically (`scripts/09_review_top200.py --ranking ... --rank-col ...`): Butina clustering on Morgan (ECFP4-like) fingerprints at Tanimoto 0.5; Bemis–Murcko scaffold enumeration; novelty as the maximum Tanimoto similarity to any of the 206 training actives; and PAINS/Brenk flags from the RDKit FilterCatalog (reviewed by hand rather than auto-discarded, because genuine β2-AR pharmacophores such as catechols are Brenk-flagged).

## 2.7 FtsZ transfer case: label-free reconstruction and ensemble reranking
- Reconstruction. Two legacy spreadsheets (BP1, BP2) from a thesis-era coumarin/FtsZ screen were parsed by content rather than by (inherited, unreliable) column headers: compound id, original docking score, Tanimoto similarity to a coumarin reference, SMILES, fingerprint method. SMILES were validated, desalted and canonicalized with RDKit; duplicate structures were merged, giving 90 unique pocket-structure records. Pocket boxes were reconstructed from ezPocket pocket centers/volumes and the residue anchors described in the original notes.
- Docking rerun. All 90 unique structures were re-docked against the reconstructed BP1/BP2 receptors with AutoDock Vina (v1.2.7, Open Babel 3.1.1; exhaustiveness 4).
- Receptor ensemble. Five FtsZ receptor conformations were generated from the reconstructed receptor by ProDy anisotropic network-model (ANM) normal modes: the original structure plus positive and negative perturbations along the first two non-trivial modes (target Cα-RMSD 0.65 Å).
- Ensemble docking and ranking. All 90 ligands were docked against the 5-conformation ensemble (450 runs, exhaustiveness 3). Because no experimental labels exist, the "EnOpt-style" combiner is label-free: per-conformation weights are the softmax (temperature 0.35) of the Spearman correlation between each conformation's scores and the legacy scores (`scripts/analyze_enopt_style.py`). Ranking stability was assessed as the correlation between the legacy worksheet ranking and the ensemble-derived rankings (BP1 and BP2 separately).

## 2.8 Reproducibility
- β2-AR: scripts 01–11 + `revalidation_ex8/`; configs `beta2ar_screen.yml`, `decoy_screen.yml`, `validation_redock.yml`; all score tables, model cards (JSON), trained models (XGBoost pickles), rankings, and review tables in `results/` and `data/training/`.
- FtsZ: `scripts/*.py`; processed tables in `data/processed/` and `results/`; raw thesis spreadsheets and the receptor PDB remain local and are not redistributed (see FtsZ `docs/data_availability.md`).

# 03 · Results —— Draft v0(2026-09-05)

## 3.1 A reproducible five-conformation β2-AR screen
Of the 30,000-compound ChEMBL37 library, 29,865 molecules were prepared and docked against five β2-AR conformations (two inactive, three active; PDB 2RH1, 5D5A, 3SN6, 4LDE, 4LDL), yielding 149,325 completed docking runs (Table 1; Fig. 2). The 135 preparation failures (desalting/embedding/PDBQT conversion, incl. timeouts) were retained in an audit manifest rather than dropped. Per-conformation mode-1 scores were reshaped into a 29,865 × 5 matrix; no ligand was discarded for scoring poorly.

## 3.2 Supervised reranking: value at the top of the list, not in global AUROC
A first supervised model (Stage 2; 48 library actives, remaining library molecules as assumed negatives) reached an out-of-fold AUROC of 0.665 with top-1% enrichment 4.2, and moved known actives from a median rank in the top 39% to the top 22.6% of the library. We report this number only for transparency: the negative labels were assumed, and many library negatives are large molecules that are trivially separable from the small actives, which inflates the AUROC (Fig. 3).

Replacing assumed negatives with 2,978 property-matched DUD-E decoys and expanding positives to 206 (48 in-library + 158 literature actives, MW restricted to the decoy window) gave the honest hard test (Stage 3; Table 2; Figs. 3 and 4):
- Supervised EnOpt (3-fold OOF): AUROC 0.696 (95% CI 0.655–0.737), EF1% 3.9, EF5% 2.7.
- Correctly oriented five-conformation mean: AUROC 0.719 (0.679–0.760), EF1% 1.9.
- Correctly oriented best score: AUROC 0.707 (0.666–0.748), EF1% 1.0.

The EnOpt AUROC overlaps the simple mean (confidence intervals overlap), so on the global decoy-separation metric the learned model is statistically tied with a correctly used consensus average. Where the model demonstrably adds value is the top of the ranked list: ~2× top-1% enrichment (3.9 vs 1.9). In a screening-like accounting that ranks library + decoys + expanded actives together, EnOpt also beats averaging (0.718 vs 0.671; Table 2).

## 3.3 Size normalization: fixing a ranking artifact also improves discrimination
Raw Vina scores correlate with molecular size. Appending five per-heavy-atom-normalized scores (score / heavy-atom count) as extra features (Stage 4) raised the hard-test AUROC from 0.696 to 0.751 (0.712–0.791) and more than doubled top-1% enrichment (7.3 vs 3.9); the screening-like accounting improved to 0.735 (0.696–0.774) (Table 2; Fig. 5). Per-heavy-atom features alone (without the raw scores) performed poorly (0.637), indicating the model needs both the absolute and the size-normalized view.

The artifact is visible in the candidate lists: the Stage-3 top-200 had median molecular weight 393 Da, heavier than the actives (362 Da); the raw+per-ha top-200 has median 358 Da and median 26 heavy atoms, inside the active window. The two leaderboards overlap in only 24/200 molecules, so size normalization materially re-ranks the library rather than rescaling a fixed order.

## 3.4 Retrospective ground truth is sparse; the shortlist is diverse and novel
A whole-library retrospective against ChEMBL found only 50 of 29,865 library molecules with documented β2-AR potency, of which 48 are the training actives; the remaining 29,817 molecules contain just two additional documented strong actives (CHEMBL24, rank 17,364; CHEMBL776, rank 27,659). A top-200 hit-rate test therefore has no statistical power: zero ChEMBL records in the top-200 means "not previously measured", not "inactive". We instead reviewed the recommended (raw+per-ha) top-200 chemically: 170 unique Bemis–Murcko scaffolds and 167 Butina clusters (Tanimoto 0.5), 192/200 molecules with max-Tanimoto < 0.4 to any training active (largely new chemistry), and 75 PAINS/Brenk-flagged molecules that require case-by-case inspection rather than automatic discard (genuine β2-AR pharmacophores such as catechols are flagged; Fig. 7). This shortlist, not the raw leaderboard, is the practical deliverable for procurement/assay planning.

A concrete failure case is informative: CHEMBL776 (orciprenaline, a classic β-agonist, Kd ≈ 500 nM) ranks 27,659/29,865, while its near-isostere CHEMBL434 ranks 14. Small flexible phenylethanolamines score with high variance across conformations; a single bad pose can sink a true active.

## 3.5 Sampling is not the bottleneck: exhaustiveness-8 re-docking
Re-docking the full validation set (3,184 ligands × 5 conformations = 15,920 runs) and the 201-ligand shortlist (top-200 + CHEMBL776, 1,005 runs) at exhaustiveness 8 with 20 modes left every conclusion unchanged (Stage 5; Fig. 6): ex8 hard-test AUROC was 0.654 (0.612–0.695) for raw and 0.733 (0.692–0.772) for raw+per-ha — confidence intervals overlap the ex4 values (0.696 and 0.751), so higher sampling neither rescued nor degraded discrimination. Rankings were stable: Spearman correlation between ex4 and ex8 scores ranged 0.87–0.91 across ranking methods (per-pocket 0.77–0.86), and 89/100 of the ex4 top-100 remained in the ex8 top-100. CHEMBL776 remained last (201/201). The interpretation is that the current ceiling is set by the information content of five rigid-receptor Vina scores (receptor/pose modelling), not by docking sampling quality.

## 3.6 Transfer case: label-free ensemble reranking of a legacy FtsZ screen
Applying the same framework to the legacy coumarin/FtsZ material reconstructed 90 unique structures from 204 spreadsheet records (BP1 and BP2 pockets) and reproduced docking (90/90 reruns). A five-conformation normal-mode ensemble (ProDy ANM) was docked for all 90 structures (450/450 runs). Without experimental labels, only a rank-consistency weighting (softmax of per-conformation Spearman correlation with the legacy scores) could be applied. The ensemble/weighted rankings correlated strongly with the legacy ranking for BP2 (Spearman ≈ 0.80) and moderately for BP1 (≈ 0.53), and the top weighted candidates (BP1 CHEMBL329500, −7.69 kcal/mol; BP2 85Z, −9.70 kcal/mol) were better scored than in the legacy worksheet (Table 3). These results demonstrate that the ensemble machinery transfers to a second target and to label-free, reconstruction-grade data, while making explicit that ranking stability — not activity — is all that can be claimed without labels (Fig. 8a–c).

# 04 · Discussion & Limitations —— Draft v0(2026-09-05)

## 4.1 What supervised reranking does and does not buy
The cleanest reading of our staged results is that learning how to combine per-conformation docking scores adds value where screening decisions are actually made — at the top of the list — rather than in the global separation of actives from decoys. Against property-matched DUD-E decoys, the trained model was statistically indistinguishable from a correctly oriented five-pocket average on AUROC (0.696 vs 0.719; CIs overlap), yet it doubled top-1% enrichment and won the screening-like accounting (0.718 vs 0.671). Much of the earlier literature enthusiasm for "AI improves docking" conflates these two regimes; reporting EF at the top of the list alongside global AUROC, with CIs, is the more honest and more practically relevant summary.

## 4.2 Size bias is real, visible, and correctable
The per-heavy-atom result (AUROC 0.696→0.751; EF1% 3.9→7.3) shows that a large part of what a model can learn from docking scores is a correction of a scoring-function artifact, not new physics. Two cautionary lessons: (i) size-normalized features alone collapse performance (0.637), because the model needs both absolute and normalized views; (ii) leaderboards that are not size-normalized can be silently dominated by large molecules (Stage-3 top-200 median MW 393 vs 362 for actives). We recommend that VS reports always include molecular-weight/heavy-atom diagnostics of the top list and, when appropriate, per-heavy-atom features.

## 4.3 Sampling quality is not the current bottleneck
Exhaustiveness-8 re-docking of the validation set and shortlist left AUROC, enrichment, and rankings essentially unchanged, and the archetypal failure case CHEMBL776 (a small flexible agonist) remained last even at higher sampling. This argues that further gains require better receptor and pose modelling — flexible side chains, curated/MD receptor ensembles, pose-quality filtering, and protein–ligand interaction fingerprints — rather than more exhaustive Vina sampling of the same rigid structures. Rigid-receptor Vina appears to systematically under-score certain flexible, low-molecular-weight chemotypes; interaction fingerprints should reveal whether this is a hydrogen-bonding or desolvation artefact.

## 4.4 Reporting practices worth adopting
Three practices in this work are transferable: out-of-fold evaluation so no molecule is scored by a model trained on it; property-matched decoys (DUD-E) with CIs instead of assumed-inactive library molecules as negatives; and explicit failure-case disclosure. The Stage-2 result (OOF AUROC 0.665) illustrates the risk of assumed negatives: much of that signal was separation by molecular size against easy, non-matched library molecules, and the honest matched-decoy number is ≈ 0.70 — real but smaller, and tied with the consensus baseline.

## 4.5 The label-free transfer case
The FtsZ reconstruction shows the framework can be run end-to-end on second-party, label-free, reconstruction-grade data. Ensemble reranking there stabilizes and, in places, corrects legacy rankings (BP2 Spearman ≈ 0.80; top weighted candidates score better than in the legacy worksheet). The explicit boundary is that without actives and decoys, neither enrichment nor "improvement" over legacy scores can be claimed — only internal consistency and reproducibility.

## 4.6 Limitations
- No experimental validation: all conclusions are computational; the shortlists are candidates for assay, not hits.
- Single target class: β2-AR is one GPCR; the FtsZ case is small (90 molecules) and label-free, so cross-target generality is demonstrated as a workflow, not as a performance claim.
- Decoy limitations: DUD-E decoys are property-matched by design but are still a proxy for true non-binders; actives are literature-reported (ChEMBL) with the usual curation caveats.
- Retrospective power: only 50/29,865 library molecules have ChEMBL-documented β2-AR activity, so a hit-rate retrospective cannot be used as a validation.
- FtsZ specifics: the receptor ensemble is ANM-perturbed rather than experimentally resolved or MD-derived; docking boxes were reconstructed; the original docking configuration was unavailable.
- Sampling control is single-seed at ex8; "no change" means CIs overlap, not formal equivalence.
- Environment reproducibility: docking results depend on Vina/Open Babel versions (1.2.5 vs 1.2.7 across the two projects); version-pinned environments are provided.

## 4.7 Future work
Prospective assay of the top-200 shortlist (or a diverse cluster-selected subset); interaction-fingerprint and pose-quality features; flexible-sidechain or MD-derived receptor ensembles; redocking pose-stability filters for finalists; extension to additional GPCRs to test whether per-heavy-atom features and top-of-list enrichment generalize; and a full DUD-E-style benchmark across several targets with a fixed, published evaluation protocol.

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


# Tables (Table 1–3) —— Draft v0(2026-09-05 QA)

> 数字转录自 `03_results.md` 与 `docs/preprint_outline.md` §5 速查表(两者一致);发布前需与两个来源仓库 `results/` 下的 csv/模型卡逐格复核(见 `docs/qa_report_2026-09-05.md`)。

**Table 1. β2-AR 主研究规模与数据**

| 项 | 值 | 说明 |
|---|---|---|
| 筛选库 | 30,000 | ChEMBL 37 子集(smiles + ChEMBL id) |
| 制备成功 | 29,865 | 去盐/3D/PDBQT;135 例失败保留在 manifest |
| 受体构象 | 5 | 2RH1、5D5A(inactive);3SN6、4LDE、4LDL(active) |
| 主对接运行 | 149,325 | 29,865×5;AutoDock Vina 1.2.5,exhaustiveness 4,≤3 modes,fixed seed |
| 训练正例 | 48 → 206 | 库内 48 + 文献 158(pChEMBL ≥ 6,MW 250–516 Da) |
| 负例 | 2,978 | DUD-E decoys(3,000 生成,去互变异构后 2,978) |
| ex8 重对接 | 15,920 + 1,005 | 全验证集 3,184×5 + 短名单 201×5(Vina 1.2.5,ex8,20 modes) |

**Table 2. 验证主结果(out-of-fold AUROC,95% CI;富集因子)**

| 阶段 / 模型 | 正 / 负样本 | OOF AUROC(95% CI) | EF1% | 备注 |
|---|---|---|---|---|
| Stage 2 · assumed-negatives | 48 / 库内其余 | 0.665 | 4.2 | 仅透明性;标签为假设,已取代 |
| Stage 3 · Supervised EnOpt | 206 / 2,978 | 0.696(0.655–0.737) | 3.9 | EF5% 2.7 |
| Stage 3 · oriented mean | 206 / 2,978 | 0.719(0.679–0.760) | 1.9 | 与 EnOpt CI 重叠 |
| Stage 3 · oriented best | 206 / 2,978 | 0.707(0.666–0.748) | 1.0 | — |
| Stage 3 · combined accounting | 全库+decoys+新 actives | EnOpt 0.718 / mean 0.671 | — | 榜单场景 |
| Stage 4 · raw + per-ha | 206 / 2,978 | 0.751(0.712–0.791) | 7.3 | EF5% 4.76(速查表) |
| Stage 4 · per-ha only | 206 / 2,978 | 0.637 | — | 弱于 raw+per-ha |
| Stage 4 · accounting | 全库+decoys+新 actives | 0.735(0.696–0.774) | — | 榜单场景 |
| Stage 5 · ex8 raw | 206 / 2,978(ex8) | 0.654(0.612–0.695) | — | 与 ex4 CI 重叠 |
| Stage 5 · ex8 raw + per-ha | 206 / 2,978(ex8) | 0.733(0.692–0.772) | — | 与 ex4 CI 重叠 |

> 附注:Stage-3 top-200 中位 MW 393 Da vs actives 362 Da;raw+per-ha top-200 中位 MW 358 Da / 中位 26 heavy atoms;两份 top-200 仅重叠 24/200;ex4 top-100 有 89/100 留在 ex8 top-100;ex4/ex8 Spearman 0.87–0.91(per-pocket 0.77–0.86);CHEMBL776 rank 27,659/29,865,ex8 201/201(失败案例)。

**Table 3. FtsZ 无标签迁移案例(重构数据)**

| 项 | 值 | 说明 |
|---|---|---|
| 原始记录 | 204(BP1/BP2 method records) | thesis-era coumarin/FtsZ |
| 唯一结构 | 90 | SMILES 校验/去盐/去重 |
| 重对接 | 90/90 | Vina 1.2.7 + Open Babel 3.1.1,ex4 |
| 受体 ensemble | 5 构象 | ProDy ANM 扰动(Cα-RMSD 0.65 Å) |
| ensemble 对接 | 450/450 | ex3 |
| legacy vs ensemble Spearman | BP1 ≈ 0.53;BP2 ≈ 0.80 | `enopt_style_correlations.csv` |
| top weighted 候选 | BP1 CHEMBL329500 −7.69 kcal/mol;BP2 85Z −9.70 kcal/mol | 优于 legacy worksheet |
| 标签 | 无 | EnOpt-style(softmax Spearman 加权),非监督 |

# Figure Legends —— Draft v0(2026-09-05 QA)

> 每张图内容需在定稿时与来源仓库数据核对,并按目标期刊导出 300+ dpi TIFF/PDF/SVG。

- **Fig. 1(fig1_framework.png)** 可复现 ensemble-screening 的通用框架示意(β2-AR 与 FtsZ 两案例共用):库组装 → 受体/配体准备 → ensemble 对接 → ligand × conformation 分数矩阵 → 显式合并规则(consensus / 监督模型 / 无标签 rank-consistency)→ 验证 → 候选短名单。
- **Fig. 2(fig2_beta2ar_overview.png)** β2-AR 主研究数据总览:库规模与制备成功/失败、5 构象对接规模、标签来源(训练 actives + DUD-E decoys)。
- **Fig. 3(fig3_score_distributions.png;fig3b_top_hits.png)** 5 构象 Vina 分数分布(a)与 top-hit 加权分数对比(b)。
- **Fig. 4(fig4_decoy_validation.png)** DUD-E 硬测试验证:Supervised EnOpt 与 oriented mean/best 基线的 AUROC(95% CI)与 top-1%/5% 富集对比。
- **Fig. 5(fig5_per_heavy_atom.png)** per-heavy-atom 特征实验:raw vs raw+per-ha vs per-ha only 的 AUROC/富集与榜单分子量分布(大分子虚高修正)。
- **Fig. 6(fig6_ex4_ex8.png)** exhaustiveness 4 vs 8 稳健性:AUROC/CI 重叠、ex4/ex8 排名 Spearman、top-100 保留率。
- **Fig. 7(fig7_top200_chem.png)** raw+per-ha top-200 化学评审:Bemis–Murcko 骨架/Butina 簇多样性、对 206 个训练 actives 的最大 Tanimoto 新颖性、PAINS/Brenk 旗标、MW/heavy-atom 分布。
- **Fig. 8(fig8a_ftsz_workflow.png;fig8b_ftsz_rerank.png;fig8c_ftsz_heatmap.png)** FtsZ 无标签迁移案例:(a)重构工作流总览;(b)EnOpt-style 重排 vs legacy 排序;(c)5-构象 ensemble 分数矩阵热图。

# References(provisional)
1. Cherezov V, et al. High-resolution crystal structure of an engineered human β2-adrenergic G protein-coupled receptor. Science 2007;318:1258–1265. (PDB 2RH1)
2. Rasmussen SGF, et al. Crystal structure of the β2 adrenergic receptor–Gs protein complex. Nature 2011;477:549–555. (PDB 3SN6)
3. Bhatt NM, Wang A, Durrant JD. Teaching old docks new tricks with machine learning enhanced ensemble docking. Sci Rep 2024;14:20722. doi:10.1038/s41598-024-71699-3 (EnOpt;标题措辞待核)
4. Houston DR, Walkinshaw MD. Consensus docking: improving the reliability of docking in a virtual screening context. J Med Chem 2003;46:837–843.
5. Trott O, Olson AJ. AutoDock Vina: improving the speed and accuracy of docking with a new scoring function, efficient optimization, and multithreading. J Comput Chem 2010;31:455–461.
6. Eberhardt J, Santos-Martins D, Tillack AF, Forli S. AutoDock Vina 1.2.0: new docking methods, expanded force field, and Python bindings. J Chem Inf Model 2021;61:3891–3898.
7. Mysinger MM, Carchia M, Irwin JJ, Shoichet BK. Directory of useful decoys, enhanced (DUD-E): better ligands and decoys for better benchmarking. J Med Chem 2012;55:6582–6594.
8. Mendez D, Gaulton A, Bento AP, et al. ChEMBL: towards direct deposition of compound data. Nucleic Acids Res 2019;47:D930–D940.
9. Bemis GW, Murcko MA. The properties of known drugs. 1. Molecular frameworks. J Med Chem 1996;39:2887–2893.
10. Baell JB, Holloway GA. New substructure filters for removal of pan assay interference compounds (PAINS) from screening libraries and for their exclusion in bioassays. J Med Chem 2010;53:2719–2740.
11. Landrum G. RDKit: open-source cheminformatics. https://www.rdkit.org (accessed 2026-09-05).
12. Brenk R, Schipani A, James D, et al. Lessons learnt from assembling screening libraries for drug discovery for neglected diseases. ChemMedChem 2008;3:435–444.
