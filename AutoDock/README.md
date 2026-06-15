## AutoDock

```bash
# 安装
conda install hcc::autodock-gpu

# 对蛋白质进行加氢
conda install hcc::adfr-suite

```

## 对接步骤
```bash
# 1蛋白质准备
得到去除配体和结晶水的蛋白质放到工作路径后，采用ADFR Suite中的prepare_receptor 的命令对蛋白质进行加氢。
prepare_receptor -r 1iep_receptorH.pdb -o 1iep_receptor.pdbqt -A hydrogens

# 2 配体准备
进行Docking需要配体分子的3D结构，可以为mol/mol2/sdf格式。得到配体的3D结构文件后，放到工作路径后，通过ADFR Suite中的prepare_ligand 的命令得到配体的pdbqt文件。
prepare_ligand -l 1iep_ligand.mol2 -o 1iep_ligand.pdbqt
```
