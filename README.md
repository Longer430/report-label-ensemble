# Report Label Ensemble · 报告标签融合技能

从医学报告或现有多来源标签表中构建可追溯的研究候选标签，明确缺失、冲突、融合规则，并组织公平的训练对照。

这是一个 Codex 技能包：包含工作流程、提示词、公式说明与检查要求，不包含自动调用模型或训练的独立执行程序。实际处理复用用户项目中的工具。

## 能做什么

- 按原文证据提取阳性、阴性、不确定和未提及，检查解剖位置、侧别、时间与严重程度。
- 融合兼容来源，记录来源、权重、分歧和缺失；不把“未提及”直接当作阴性。
- 导入既有合并标签，保留原数值与哈希，明确其未经验证的来源和假设。
- 在相同病例、划分、输入和训练预算下比较标签方案。

## 安装与使用

将仓库克隆到 Codex 的个人技能目录中。已有同名目录时先比较内容，不要覆盖本地修改。

Windows PowerShell：

```powershell
$skillHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
git clone https://github.com/Longer430/report-label-ensemble.git (Join-Path $skillHome 'skills/report-label-ensemble')
```

打开新的 Codex 会话后，可以这样使用：

```text
使用 $report-label-ensemble 检查我的几份报告标签表，先核对来源、评分含义和缺失处理，再制定可追溯的融合方案。
```

```text
使用 $report-label-ensemble 比较新旧标签。固定病例、划分、输入缓存、初始化、种子和训练预算，先小批量验证，再运行配对实验。
```

技能本身不需要 API 密钥。实际模型调用、数据处理和训练依赖所选项目的运行环境与用户授权。

## 关于 Kaggle v5 的已知与未知

本技能参考了 RSNA Knee 多来源报告标签的公开讨论和数据，但**不是已验证的 v5 完整复现**。

双来源前身的数值重建为：

```text
score = (P + S) / 2
confidence = 1 - abs(P - S)
```

低置信度滑膜炎另有积液上限。该前身规则在 51,433 个纳入核查的单元格上匹配；排除了合并置信度为 1 的格子，并只核查两个来源共同覆盖的研究。**不能把这个公式直接称作 v5 的置信度公式。** 完整范围与来源见[核查说明](references/kaggle-predecessor-formula.md)。

本技能默认的[透明融合方案](references/fusion.md)是另行定义的实验政策，不冒充作者未公开的算法。置信权重不等于医学正确率，AI 候选标签不等于医生或 MRI 金标准。

## 文件导航

| 文件 | 内容 |
|---|---|
| [SKILL.md](SKILL.md) | 技能入口和流程 |
| [extraction.md](references/extraction.md) | 原文证据提取提示词 |
| [fusion.md](references/fusion.md) | 融合规则、缺失及冲突处理 |
| [kaggle-predecessor-formula.md](references/kaggle-predecessor-formula.md) | 前身公式及 v5 尚缺信息 |
| [source-research.md](references/source-research.md) | 公开讨论来源 |
| [rsna-integration.md](references/rsna-integration.md) | 可选 RSNA 项目集成指引 |

仓库只发布技能文档与配置，不附带患者报告、影像、标签数据、模型权重或凭据。集成文档提到的项目脚本不包含在本仓库中，使用前需检查用户实际项目。

通俗地说：这个技能是一份“让几个来源分别判断、核对证据、处理分歧、再比较训练效果”的操作说明书；它不会因为几个人意见一致，就宣称医学判断一定正确。

## 版权与来源

Copyright (c) 2026 Longer430.

本仓库采用 [MIT License](LICENSE)：允许使用、修改、商用和再发布，须保留版权及许可证声明；按许可证条款不提供担保。

本仓库技能文档及配置的版权署名为 Longer430。引用的 Kaggle 数据集、讨论和上游代码归各自权利人所有；本仓库的声明不改变其授权条件。来源与授权范围见 [NOTICE](NOTICE)。
