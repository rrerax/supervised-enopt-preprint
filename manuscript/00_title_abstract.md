# 00 · Title & Abstract

## Title(工作默认,见 README 候选表)
From consensus to learned ranking: reproducible ensemble docking for β2-adrenergic receptor screening, with a label-free FtsZ transfer case

## Abstract(DRAFT v0 —— 下一步打磨为 v1)

High-throughput virtual screening against a single rigid receptor structure is cheap but unreliable: one scoring run cannot capture the conformational plasticity of G-protein-coupled receptors, and raw docking scores carry systematic biases such as molecular-size dependence. The ensemble-optimizer (EnOpt) concept addresses the first problem by combining per-conformation docking scores into one ranking, but its value depends on how scores are combined. We present a reproducible two-stage workflow that applies supervised EnOpt reranking to the β2-adrenergic receptor (β2-AR). 29,865 library molecules were docked against five active/inactive β2-AR conformations (149,325 docking runs, AutoDock Vina 1.2). An XGBoost model trained on known β2-AR actives (48, later expanded to 206 literature actives) and validated against 2,978 property-matched DUD-E decoys separated actives from decoys with out-of-fold AUROC 0.696 (95% CI 0.655-0.737), statistically indistinguishable from a correctly oriented five-pocket average but with roughly twofold top-1% enrichment. Appending per-heavy-atom-normalized scores raised AUROC to 0.751 (0.712-0.791) and removed the large-molecule ranking bias. Re-docking the validation set at exhaustiveness 8 left all conclusions unchanged (confidence intervals overlap), showing the remaining bottleneck is receptor and pose modelling rather than docking sampling. A chemical review of the top-200 shortlist shows the recommended candidates are diverse and novel (170 scaffolds; 192/200 have max-Tanimoto < 0.4 to training actives). As a transfer case study, the same ensemble framework was applied to a legacy coumarin/FtsZ screen reconstructed from thesis spreadsheets without experimental labels, improving ranking stability but not permitting activity claims. All code, score tables, trained models, and reports are provided.

## v1 TODO
- [ ] 数字与 outline 速查表逐项核对(§5)
- [ ] 定稿后标题与摘要互相呼应(关键词:ensemble docking / supervised rerank / GPCR / DUD-E / size bias)
- [ ] 加 3-5 个关键词