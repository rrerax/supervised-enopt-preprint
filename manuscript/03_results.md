# 03 · Results(骨架,数字以 outline §5 速查表为准)

- 3.1 β2-AR 数据集与对接(Stage 1:29,865 × 5 = 149,325)。
- 3.2 监督 EnOpt 基线(Stage 2,48 正例,OOF 0.6647,注明其负样本假设被 Stage 3 取代)。
- 3.3 DUD-E 硬测试(Stage 3:0.696 vs oriented mean 0.719,CI 重叠;top-1% EF 3.9 vs 1.9;合并账目 0.718 vs 0.671)。
- 3.4 per-heavy-atom(Stage 4:0.696→0.751,EF1 7.3;top-200 MW 393→358)。
- 3.5 回顾与 top-200 化学评审(仅 50 已知活性→无统计功效;170 scaffolds/192 novel/75 PAINS)。
- 3.6 ex8 稳健性(Stage 5:CI 重叠;Spearman 0.87-0.91;CHEMBL776 仍垫底)。
- 3.7 FtsZ 迁移案例(90 结构 450 dock;BP2 legacy-vs-ensemble Spearman ~0.80;无标签边界说明)。