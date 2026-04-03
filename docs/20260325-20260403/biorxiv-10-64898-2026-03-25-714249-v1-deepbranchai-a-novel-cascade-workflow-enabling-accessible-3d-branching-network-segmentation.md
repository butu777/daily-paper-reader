---
title: "DeepBranchAI: A Novel Cascade Workflow Enabling Accessible 3D Branching Network Segmentation"
title_zh: DeepBranchAI：一种实现易用的三维分支网络分割的新型级联工作流
authors: "Maltsev, A. V., Hartnell, L., Ferrucci, L."
date: 2026-03-29
pdf: "https://www.biorxiv.org/content/10.64898/2026.03.25.714249v1.full.pdf"
tags: ["query:ai-3d"]
score: 9.0
evidence: 3D分支网络分割与重建
tldr: 针对3D分支网络（如线粒体、血管）分割中存在的拓扑连接性易断裂及标注成本极高的问题，本文提出DeepBranchAI级联工作流。该方法通过“随机森林初步标注-专家修正-2D到3D深度学习演进”的反馈循环，将标注时间从数月缩短至数周。实验证明该模型在保持拓扑结构方面表现优异，且具有极强的跨领域迁移能力。
source: biorxiv
selection_source: fresh_fetch
figures_json: "[{\"url\": \"assets/figures/biorxiv/biorxiv-10-64898-2026-03-25-714249-v1/fig-001.webp\", \"caption\": \"Figure 1. Cascade Training Framework Stage 1: Volume Preparation. (A) Schematic showing mitochondria interspersed between myofibrils in human skeletal muscle. Many of them are in contact with each other, forming a nearly continuous branching network. (B) Preprocessing includes cross-correlation alignment to ensure 3D continuity and artifact removal\", \"page\": 6, \"index\": 1, \"width\": 980, \"height\": 1194}, {\"url\": \"assets/figures/biorxiv/biorxiv-10-64898-2026-03-25-714249-v1/fig-002.webp\", \"caption\": \"Figure 4. DeepBranchAI Segmentation Performance. Pixel-to-pixel comparison (DSC = 0.97) between DeepBranchAI segmentation (green) and ground truth (red) showing high overlap and discrepancies primarily contained in highly dense regions near the capillary.\", \"page\": 13, \"index\": 2, \"width\": 980, \"height\": 498}, {\"url\": \"assets/figures/biorxiv/biorxiv-10-64898-2026-03-25-714249-v1/fig-003.webp\", \"caption\": \"Figure 2. Cascade Training Framework Stage 2: Iterative Ground Truth Construction. (A) The process starts with a 2D Weka random forest classifier trained on minimal annotations. Experts correct the output and retrain iteratively until accuracy plateaus. (B) The refined segments are then used to train a 2D nnU-Net, which generates probability maps across full volumes. Experts do a final refinement pass on these maps, producing ground truth volumes that feed back into further training. 3.5 Training DeepBranchAI Classifier\", \"page\": 8, \"index\": 3, \"width\": 980, \"height\": 578}, {\"url\": \"assets/figures/biorxiv/biorxiv-10-64898-2026-03-25-714249-v1/fig-004.webp\", \"caption\": \"Table 2: 5-fold cross-validation performance for DeepBranchAI (3D nnU-Net)\", \"page\": 12, \"index\": 4, \"width\": 790, \"height\": 213}, {\"url\": \"assets/figures/biorxiv/biorxiv-10-64898-2026-03-25-714249-v1/fig-005.webp\", \"caption\": \"Figure 3. Cascade Training Framework Stage 3: DeepBranchAI and Deployment. The 3D nnU-Net model that we dub DeepBranchAI is trained using high-quality ground truth s with chunk sizes large enough to capture local details and long-range topological relationships. Trained models segment new volumes via chunk-wise processing, generating probability maps that are stitched and thresholded to produce final segmentations.\", \"page\": 9, \"index\": 5, \"width\": 980, \"height\": 665}]"
motivation: 3D分支网络分割面临拓扑结构对微小误差极度敏感以及高质量3D标注数据极度匮乏的瓶颈。
method: 提出一种从随机森林到3D nnU-Net的级联训练工作流，利用正向反馈循环逐步将稀疏标注转化为高质量的3D训练集。
result: "在线粒体网络验证中达到0.942的Dice系数，并在跨尺度的血管网络迁移学习中仅用10%数据即达到97.05%的准确率。"
conclusion: DeepBranchAI显著降低了复杂3D网络分割的门槛，为生物及自然科学领域的拓扑保真分割提供了高效、开源的解决方案。
---

## 摘要
三维分支网络作为穿过体积空间的路径，广泛存在于生物、自然和人造系统中。为了进行分析，需要通过分割来正确地重建网络的全部或部分。这带来了一个独特的挑战，因为微小的体素误分类可能导致零星的连通性偏移，使得连接的元素看起来断开了（假阴性），甚至被放大（假阳性）。解决这种拓扑脆弱性需要生成三维模型，因为二维逐层切片方法无法在 x、y 和 z 轴上保持连通性。然而，追踪三维架构比使用二维策略需要更多的分析资源，因为生成体积标注需要专家花费大量时间进行手动标注。这造成了一个根本性的标注瓶颈：在训练数据稀缺的情况下，深度学习模型往往会过拟合现有体积数据，而无法泛化到新的体积数据。我们提出了一种级联训练工作流，通过正反馈循环克服了这一瓶颈，其中训练好的模型成为后续体积数据的标注辅助工具。该工作流始于利用极少量标签生成初始草图的随机森林，随后通过专家精修，循环往复地逼近地面真值（ground truth）。随着精修数据的积累，训练从二维架构过渡到三维架构，从而系统地将稀疏数据集扩展为全面的训练集。最终结果是一个针对保持拓扑结构的分割而优化的三维 nnU-Net 模型。我们将所得模型命名为 DeepBranchAI。在由聚焦离子束扫描电子显微镜（FIB-SEM，15nm 体素分辨率）生成的高度分支线粒体网络上进行的训练验证显示，在五折交叉验证中达到了 0.942 的 Dice 相似系数（DSC）。迁移学习到血管网络（VESSEL12 数据集，CT 体积，体素大小差异达 30,000 倍）的结果显示，仅使用 10% 的目标数据进行训练，其相对于地面真值的准确率就达到了 97.05%，验证了所学特征代表了领域通用的拓扑原理。该工作流将标注时间从数月缩短至数周，同时将稀疏的初始标签转化为稳健的训练集。完整的实现、训练权重和验证代码均已开源。

## Abstract
Three-dimensional branching networks exist throughout biological, natural, and man-made systems as pathways through volumetric space. Segmentation is required to correctly reconstruct the networks in whole or in part for analysis. This presents a unique challenge as minor voxel misclassifications can cause sporadic connectivity shifts, whereby connected elements appear to disconnect (false negatives) or to even become amplified (false positives). Addressing this topological vulnerability requires the generation of 3D models since 2D slice-by-slice approaches cannot maintain connectivity across x, y, and z axes. Yet tracking 3D architecture demands substantially more analytical resources than using a 2D strategy as generating volumetric annotations requires extraordinary amounts of expert time to manually annotate. This creates a fundamental annotation bottleneck: with sparse training data available, deep learning models tend to overfit available volumes and fail to generalize to novel volumes. We present a cascade training workflow that overcomes this bottleneck through a positive feedback loop in which trained models become annotation aids for subsequent volumes. The workflow begins with random forests that generate initial drafts from minimal labels, followed by expert refinement that cycle ever closer to the ground truth. As refined data accumulates, training transitions from 2D to 3D architectures, which systematically expand sparse datasets into comprehensive training sets. The outcome is a 3D nnU-Net model optimized for topology-preserving segmentation. We dub our resulting model DeepBranchAI. Training validation on heavily branching mitochondrial networks, generated by focused ion beam scanning electron microscopy (FIB-SEM, 15nm voxel resolution) achieved Dice Similarity Coefficient (DSC) = 0.942 across 5-fold cross-validation. Transfer learning to vascular networks (VESSEL12 dataset, CT volumes, 30,000-fold voxel size difference) training on as little as 10% of target data achieved 97.05% accuracy against ground truth, validating that learned features represent domain-general topological principles. This workflow reduces annotation time from months to weeks while transforming sparse initial labels into robust training sets. Complete implementation, trained weights, and validation code are provided open source.

---

## 论文详细总结（自动生成）

这是一份关于论文《DeepBranchAI: A Novel Cascade Workflow Enabling Accessible 3D Branching Network Segmentation》的深度结构化总结：

### 1. 核心问题与研究动机
*   **核心挑战**：三维分支网络（如线粒体、血管、神经回路）具有**拓扑脆弱性**。微小的体素分类错误（假阴性或假阳性）会导致网络连接断裂或错误合并，严重影响功能分析。
*   **标注瓶颈**：高质量的3D分割需要全体积标注，这极其耗费专家时间（有时需数人/年）。数据稀缺导致深度学习模型容易过拟合，无法泛化。
*   **研究目标**：开发一种高效的工作流，在减少人工标注负担的同时，训练出能够保持拓扑连通性的稳健3D分割模型。

### 2. 方法论：级联训练工作流（Cascade Workflow）
该方法的核心思想是建立一个**人机协作的正反馈循环**，将传统机器学习（ML）与深度学习（DL）结合：
*   **阶段 A：预处理与数据集策划**。对FIB-SEM图像进行交叉相关对齐、干扰切片移除和去噪（Wavelet-FFT滤波），确保3D连续性。
*   **阶段 B：级联标注生成**。
    1.  **初步标注**：使用2D Weka（随机森林）进行极少量标注（5-10分钟），生成初步草图。
    2.  **专家精修与迭代**：专家修正错误并回传模型迭代，直到准确率达到平台期。
    3.  **过渡到深度学习**：利用精修后的局部切片训练2D nnU-Net，生成全卷概率图，专家基于此进行最终精修，产生高质量3D地面真值（Ground Truth）。
*   **阶段 C：训练DeepBranchAI分类器**。使用3D nnU-Net架构，采用大尺寸块（360×360×128体素）进行训练，以捕捉长程拓扑关系。

### 3. 实验设计与对比
*   **主要数据集**：
    *   **线粒体网络**：通过FIB-SEM成像的10名受试者骨骼肌活检样本（15nm等向分辨率）。
    *   **迁移学习验证**：使用**VESSEL12数据集**（肺部血管CT扫描），其体素体积比线粒体数据大30,000倍。
*   **Benchmark与对比方法**：
    *   对比了 **2D U-Net**、**3D U-Net** 和 **2D nnU-Net**。
    *   评估指标包括：Dice相似系数（DSC）、灵敏度、特异性、准确率、绝对体积差（AVD）和Cohen's Kappa系数。

### 4. 资源与算力
*   **硬件配置**：单台工作站，配备 **NVIDIA RTX A6000 (48 GB VRAM)**，128 GB RAM，24线程处理器。
*   **训练时长**：
    *   2D Weka 迭代：约30分钟/次。
    *   2D nnU-Net 训练：4-6小时。
    *   **3D nnU-Net 完整训练（20个卷）**：2-3天。

### 5. 实验数量与充分性
*   **实验规模**：进行了5折交叉验证（按受试者分组，确保无数据泄露）。
*   **消融与对比**：明确对比了2D与3D架构的性能差异，验证了3D上下文对拓扑保持的必要性。
*   **跨领域验证**：通过极具挑战性的跨模态（SEM到CT）、跨尺度（3万倍差异）迁移学习实验，验证了模型的通用性。
*   **评价**：实验设计客观、公平，通过多指标评价体系（尤其是Kappa系数和AVD）弥补了Dice指标在拓扑评估上的不足。

### 6. 主要结论与发现
*   **性能优异**：DeepBranchAI在线粒体分割中达到 **0.942 DSC**，显著优于2D方法（0.726-0.879）。
*   **标注效率**：该工作流将标注时间从**数月缩短至数周**。
*   **强泛化性**：在仅使用10%目标数据进行微调的情况下，DeepBranchAI在血管CT数据上达到了 **97.05% 的准确率**，证明模型学到了通用的“分支拓扑”特征而非单纯的纹理。

### 7. 优点与亮点
*   **人机协同**：不追求完全自动化，而是将专家知识作为“特征”融入工作流，解决了冷启动标注难题。
*   **拓扑保真**：通过大尺寸3D Patch训练，有效解决了分支结构易断裂的问题。
*   **开源贡献**：提供了完整的代码、预训练权重和Jupyter Notebook教程，极具实用价值。

### 8. 不足与局限
*   **深度限制**：模型要求最小深度为128层，对于极薄的体积数据不适用。
*   **拓扑指标**：虽然使用了AVD和Kappa，但尚未在训练损失函数中直接集成专门的拓扑损失（如clDice），这在未来仍有提升空间。
*   **硬件门槛**：3D nnU-Net对显存要求较高（建议24GB以上），限制了在低配设备上的部署。

（完）
