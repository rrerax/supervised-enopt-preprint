# 02 · Methods(骨架,素材=两仓库 scripts)

- 2.1 通用框架:输入库 → 配体/受体准备 → 多构象对接 → 分子×构象分数矩阵 → 合成器(consensus/supervised)→ 验证 → 候选短名单。(对应 Fig 1)
- 2.2 β2-AR:5 构象(2RH1/5D5A inactive;4LDE/3SN6/4LDL active);ChEMBL37 30k 库(29,865 可对接);Vina 1.2 ex4;正例 48→206;DUD-E decoys 2,978;XGBoost 3 折 OOF(raw 5 特征;raw+per-ha 10;+HAC 11);ex8 重对接验证集与 top-200。
- 2.3 FtsZ:旧工作簿重构(按内容推断列含义)、RDKit 清洗、双口袋(ezPocket)、Vina rerun、ANM 5 构象、EnOpt-style 秩一致性权重(无标签)。
- 2.4 评估:AUROC+95%CI、EF1/EF5、化学评审(Butina/Bemis-Murcko/Tanimoto/PAINS-Brenk)。

脚本引用: Beta2AR `scripts/01-11`、`revalidation_ex8/`;FtsZ `scripts/*.py`。