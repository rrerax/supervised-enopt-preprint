# Supervised EnOpt Preprint(工作仓库 / working repo)

**Status, 22 October 2026**: 对接停在五套构象的平均排序上。29,865 个分子里，奥西那林（CHEMBL776）排 25,527，3,4-儿茶酚胺（CHEMBL434）排 26,697，都在后面；分数跟着 logP。写进坐标文件的氢没有三处同时在线上，最小化前 H15 到 Ser203 是 4.161 Å、17.2°，最小化后只剩对 Ser207 的那条氢；另外五个异构体没有通过同一条线。磷脂 4 Å 以内是 0，不改变排序。这个低排名配体不送进平衡，也不做相对自由能。不从这套 Ser203–Ser207，也不从 `popc_3sn6_deposit_min.pdb` 再往下算。留下的结论是：排序跟着 logP，这两个已知配体排在后面；后来摆出的接触没有留在坐标文件里。记录在 `rrerax/Beta2AR_SupervisedEnOpt_Screening` 的 `followup_2026-10-16`。本稿正文还没有按这个结论改写。

**Status, 26 October 2026**: 上面两个名次用的是中性胺，分不清是 logP 共线，还是中性胺这个输入。GPR-53 保持五套受体、原盒子和穷举度 4，按 pH 7.4 把碱性脂肪胺质子化后对接 14,586 个分子。定向五套平均上，CHEMBL776 排 14,360，CHEMBL434 排 14,362，库共 14,586；两个平均都是 −7.28 kcal/mol。分数仍跟着 logP，Spearman 0.445。改成质子化胺没有把这两个苯乙醇胺往前带。没有训练模型。完整分数表不在本仓库。

**Status**: draft v0 已完成(2026-09-05 QA 更新)· 单篇 preprint(β2-AR 主线 + FtsZ 无标签迁移案例);逐条进度见 `docs/qa_report_2026-09-05.md`
**施工图**: `docs/preprint_outline.md`(自 Beta2AR 仓库镜像,此后以本仓库为准)

## 一句话主线
把"多构象对接 + 用已知活性学习分数组合"(监督 EnOpt)做成一套可复现工作流,并诚实回答:它比简单平均好在哪(top-1% 富集)、per-heavy-atom 如何修大分子虚高(0.696→0.751)、提高 exhaustiveness 是否有效(ex4/ex8 CI 重叠,瓶颈在受体建模)、同一框架能否迁移到无标签旧数据(FtsZ)。

## 两个来源仓库
| 角色 | 仓库 | 内容 |
|---|---|---|
| 主线 β2-AR | `Beta2AR_EnOpt_GPCR_Screening`(GitHub: rrerax/Beta2AR_SupervisedEnOpt_Screening) | 30k 分子 × 5 构象;DUD-E decoy 验证;per-ha 特征;ex8 重对接;top-200 化学评审 |
| Case study FtsZ | `FtsZ_Coumarin_HTVS_Reconstruction`(D:\Desktop\project;GitHub 镜像 rrerax/FtsZ-Coumarin-Virtual-Screening-Reconstruction) | 硕士项目(答辩已完成):旧筛选重构 + 5-构象 EnOpt-style pilot(无实验标签) |

## 标题候选(2026-09-05 新拟,待定稿)
| # | 标题 | 备注 |
|---|---|---|
| ★ 已定稿 | **From consensus to learned ranking: reproducible ensemble docking for β2-adrenergic receptor screening, with a label-free FtsZ transfer case** | 2026-09-05 选定 |
| N1 | Learning to rank docking-score ensembles: supervised reranking for the β2-adrenergic receptor | 简洁聚焦 |
| N2 | What data can teach ensemble docking: a DUD-E-validated supervised EnOpt screen of β2-AR | 强调验证链 |
| N3 | Ensemble docking, learned ranking, and honest limits: size-bias correction in a supervised β2-AR screen | 强调"诚实边界"卖点 |
| N5 | Reproducible supervised ensemble docking: β2-AR benchmarks and a label-free transfer case on FtsZ | 备选 |

中文工作标题(答辩/组会用):《从共识重排到监督重排:β2-肾上腺素受体多构象对接的可复现筛选与 FtsZ 无标签迁移案例》

**✅ 标题已定稿(2026-09-05): 采用推荐款**;中文工作标题仅作答辩/组会参考。

## 目录
- `manuscript/` — 逐节草稿(00 标题+摘要 → 05 数据可用性)
- `docs/preprint_outline.md` — 施工图:章节骨架、图/表映射、数字速查表、参考文献
- `figures/README.md` — 图 1-8 映射(哪些复用来源仓库、哪些新画)
- `source_repos.md` — 来源仓库路径与角色备忘

## 进度(2026-09-05 QA 更新;详见 `docs/qa_report_2026-09-05.md`)
- [x] 标题定稿(推荐款);Abstract v1 / Introduction v0 / Methods v0 / Results v0 / Discussion v0 / Data & Code v0
- [x] Fig 1/2 新画 + Fig 3-8 素材入库;正文补图引用(Fig. 1-8)
- [x] Table 1-3 补齐(`manuscript/06_tables.md`);图注 `manuscript/07_figure_legends.md`
- [x] 参考文献扩展至 12 条(仍为 provisional,见 `manuscript/01_introduction.md`)
- [x] 自动拼装单文件 `manuscript/manuscript_full.md` + Word 快照 Draft4
- [ ] 发布前:联网核验参考文献(citation-management);确定目标期刊+作者分工+funding/致谢;图件 300+dpi TIFF/PDF/SVG 重导出与视觉校对;GitHub 发布(GitHub Desktop,网络直连失败)
- [ ] 可选:训练模型 pkl/榜单备份至 Zenodo
