## Scanpy
Scanpy is a scalable toolkit for analyzing single-cell gene expression data built jointly with anndata.

## install
```bash
pip install 'scanpy[leiden]'
```


## celltypist
在已有的 Scanpy 分析基础上，可以通过**自动细胞注释**来快速预测细胞类型。这里介绍两种主流且易于实现的方法：基于参考数据集的**标签转移**和基于基因集的**富集打分**。

---

### 🤖 方法一：基于参考数据集的标签转移 (Label Transfer)

这种方法利用一个已标注好的参考数据集，通过比较基因表达谱的相似性，为你的数据“迁移”标签。推荐的工具是 **CellTypist**，它提供了一系列高质量的预训练模型。

以下是在你原有代码基础上，使用 CellTypist 进行自动注释的完整步骤。

#### 1. 安装 CellTypist

```python
# 在终端或 Jupyter 中安装
!pip install celltypist
```

#### 2. 导入并准备数据

确保你的 `adata` 对象已经完成了之前的所有分析步骤（质控、标准化、高变基因、PCA、UMAP、聚类等）。

#### 3. 运行 CellTypist 自动注释

```python
import celltypist
import scanpy as sc

# 1. 查看可用的预训练模型（可选）
# celltypist.models.models_description()

# 2. 下载并加载一个模型
# 'Immune_All_Low.pkl' 是一个常用的免疫细胞模型
model = celltypist.models.Model.load(model='Immune_All_Low.pkl')

# 3. 进行细胞类型预测
# majority_voting=True 会基于细胞所在的亚群进行投票，提高预测的稳健性
predictions = celltypist.annotate(adata, model=model, majority_voting=True)

# 4. 将预测结果添加到 AnnData 对象中
adata = predictions.to_adata()

# 5. 将预测的细胞类型和置信度分数提取到 .obs 中，方便后续使用
adata.obs['cell_type_celltypist'] = adata.obs['majority_voting']
adata.obs['cell_type_confidence'] = adata.obs['conf_score']

# 6. 可视化预测结果
sc.pl.umap(adata, color=['cell_type_celltypist', 'cell_type_confidence'], legend_loc='on data')
```

#### 4. 模型选择与自定义

*   **模型选择**：`celltypist.models.models_description()` 可以查看所有可用模型。除了免疫模型，还有其他组织或通用模型可供选择。
*   **自定义模型**：如果你有自己标注好的参考数据，也可以用 `celltypist.train()` 训练专属模型。

### 🧬 方法二：基于基因集的富集打分 (Gene Set Scoring)

这种方法不需要参考数据集，而是根据你提供的细胞类型标记基因列表，为每个细胞计算一个“富集分数”，分数最高的细胞类型即为其预测类型。

#### 1. 准备标记基因字典

你需要根据你的组织或研究背景，定义一个包含细胞类型及其标记基因的字典。

```python
# 示例：免疫细胞的标记基因（请根据你的数据替换）
marker_genes = {
    "T cells": ["CD3D", "CD3E", "CD8A"],
    "B cells": ["MS4A1", "CD79A"],
    "Macrophages": ["CD68", "CD163"],
    "NK cells": ["NKG7", "KLRD1"],
    # ... 添加更多你关注的细胞类型
}

# 重要：只保留在你数据中存在的基因
for cell_type, genes in marker_genes.items():
    marker_genes[cell_type] = [g for g in genes if g in adata.var_names]
```

#### 2. 计算富集分数并进行注释

`sc.tl.score_genes` 函数可以为每个细胞计算特定基因集的平均表达量。

```python
import numpy as np

# 1. 为每种细胞类型计算一个分数
for cell_type, genes in marker_genes.items():
    if genes:  # 确保基因列表不为空
        sc.tl.score_genes(adata, gene_list=genes, score_name=f'score_{cell_type}')

# 2. 找出每个细胞得分最高的细胞类型
score_columns = [f'score_{ct}' for ct in marker_genes.keys()]
# 创建一个数据框，只包含这些分数列
scores_df = adata.obs[score_columns]

# 为每个细胞分配得分最高的细胞类型
adata.obs['cell_type_scored'] = scores_df.idxmax(axis=1).str.replace('score_', '')

# 3. 可视化结果
sc.pl.umap(adata, color=['cell_type_scored'], legend_loc='on data')
```

### 💡 选择建议

*   **`CellTypist`**：**首选方法**。它基于大规模的、高质量的人工注释参考数据集，预测结果通常更准确、更客观。尤其适合免疫细胞等常见细胞类型。
*   **`基因集打分`**：当没有合适的参考模型，或者你关注的是非常罕见的细胞类型时，这是一个不错的备选方案。但它的准确性高度依赖于你提供的标记基因列表的质量。

### 🔍 结果验证与整合

无论使用哪种自动注释方法，得到的结果都应视为**初步预测**，需要进行验证和整合。

*   **验证**：使用 `sc.pl.dotplot` 或 `sc.pl.matrixplot` 可视化你信任的经典标记基因在不同预测细胞类型中的表达情况，来评估注释的合理性。
*   **整合**：你可以将自动注释的结果（如 `cell_type_celltypist`）与之前的手动注释（`cell_type`）相结合。例如，用自动注释填补未知的簇，或作为手动注释的参考。
*   **保留原始聚类**：请不要覆盖 `adata.obs['leiden']` 列，它记录了无监督的聚类结果，是进行各种验证和深入分析的基础。
