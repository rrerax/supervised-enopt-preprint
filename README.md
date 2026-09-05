# Supervised EnOpt Preprint(工作仓库 / working repo)

**Status**: scaffolding · 2026-09-05 · 单篇 preprint(β2-AR 主线 + FtsZ 无标签迁移案例)
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

## 下一步(checklist 详见 outline §8)
- [ ] 选定标题(未定时默认采用推荐款,随时可改)
- [ ] Abstract v1 + Introduction 草稿
- [ ] 新画 Fig 1(框架示意)+ Fig 2(β2-AR 数据总览)
- [ ] 汇总 Table 1-3
- [ ] 数据可用性初稿(需确认 30k 库与模型公开方式)