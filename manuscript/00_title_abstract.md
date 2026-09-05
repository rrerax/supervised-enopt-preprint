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
- [ ] 投稿前: 与 Methods/Results 数字互核;按目标期刊控制词数;补充 funding/author 信息后微调“we/our”表述。