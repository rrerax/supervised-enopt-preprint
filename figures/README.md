# figures/ 图计划(映射到来源仓库,定稿时把成品复制进本目录)

| 编号 | 内容 | 现状 | 来源/待做 |
|---|---|---|---|
| Fig 1 | 通用框架示意(两靶点共用) | ✅ v0 已画 | `figures/fig1_framework.png`(本目录) |
| Fig 2 | β2-AR 数据总览 | ✅ v0 已画 | `figures/fig2_beta2ar_overview.png`(本目录) |
| Fig 3 | 构象分数分布 + top hits | 复用 | Beta2AR `results/figures/score_distributions.png`、`enopt_weighted_top_hits.png` |
| Fig 4 | DUD-E 验证 | 复用 | Beta2AR `results/supervised_enopt_decoy/fig_decoy_validation.png` |
| Fig 5 | per-heavy-atom 对比 | 复用 | Beta2AR `results/feature_experiment_heavy_atom/fig_feature_comparison.png` |
| Fig 6 | ex4 vs ex8 稳健性 | 复用 | Beta2AR `results/ex8_rerank/fig_ex4_vs_ex8.png` |
| Fig 7 | top-200 化学评审 | 复用 | Beta2AR `results/review_top200_ha/fig_top200_properties_vs_actives.png` |
| Fig 8 | FtsZ case | 复用 | FtsZ `results/figures/workflow_summary.png`、`enopt_style_reranking_vs_legacy.png`、`ensemble_score_matrix_heatmap.png` |

> 注意:成品多为 PNG,投稿前按目标期刊要求导出 TIFF/PDF/SVG 300+ dpi;编号与顺序随正文微调。
> 2026-09-05: Fig 1(两行式框架图)与 Fig 2(规模/分数分布/标签三面板)已生成 v0;定稿前按期刊要求重排与换字体。

## Fig 3-8 素材已拷入(2026-09-05)
| 文件 | 内容 | 来源 |
|---|---|---|
| fig3_score_distributions.png | 5 构象分数分布 | Beta2AR results/figures |
| fig3b_top_hits.png | top-hit weighted 分数 | Beta2AR results/figures |
| fig4_decoy_validation.png | DUD-E 验证 | Beta2AR results/supervised_enopt_decoy |
| fig5_per_heavy_atom.png | per-ha 特征对比 | Beta2AR results/feature_experiment_heavy_atom |
| fig6_ex4_ex8.png | ex4 vs ex8 稳健性 | Beta2AR results/ex8_rerank |
| fig7_top200_chem.png | top-200 化学评审 | Beta2AR results/review_top200_ha |
| fig8a_ftsz_workflow.png | FtsZ 工作流总览 | FtsZ results/figures |
| fig8b_ftsz_rerank.png | FtsZ EnOpt-style 重排 | FtsZ results/figures |
| fig8c_ftsz_heatmap.png | FtsZ 构象分数热图 | FtsZ results/figures |

> 这些是从来源仓库复制的当前版本;定稿时需从来源仓库重导出为投稿规格(300+dpi TIFF/PDF/SVG)。