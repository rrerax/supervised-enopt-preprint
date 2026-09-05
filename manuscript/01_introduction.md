# 01 · Introduction(draft pending)

写作任务(每段 3-5 句,引用见 outline §6):
1. 单构象刚性受体的局限;GPCR TM6 激活运动改变口袋 → ensemble docking 的必要性。
2. 组合问题:每个分子有"一组分数",合成方式(mean/best/learned)决定榜单;EnOpt(Bhatt 2024)提出 data-driven 合成。
3. 系统性偏差:Vina 分数与分子大小相关 → 大分子虚高;per-heavy-atom 视角。
4. 机器学习可信度:OOF 防自评、property-matched decoys(DUD-E)、95% CI、失败案例透明化。
5. 本文贡献 + 脉络:EnOpt 思路最早在作者 FtsZ 硕士重构中 pilot(无标签 consensus),本文在 β2-AR 上升级为有 DUD-E 验证的监督 EnOpt,并展示框架可迁移回无标签场景。

素材: Beta2AR `docs/method_notes.md`、`README.md` Stage 1-5;outline §3。