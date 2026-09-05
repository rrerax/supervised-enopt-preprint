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
