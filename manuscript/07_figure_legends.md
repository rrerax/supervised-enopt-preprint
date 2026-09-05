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
