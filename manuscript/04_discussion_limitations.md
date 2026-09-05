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