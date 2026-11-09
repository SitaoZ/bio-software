## AutoDock

```bash
# 安装
conda install hcc::autodock-gpu

# 对蛋白质进行加氢
conda install hcc::adfr-suite

```

## 对接步骤
```bash
# step1 ligand (sdf to mol2)
/home/zhusitao/miniconda3/envs/SurfDock/bin/obabel -isdf ${sample}_ligand.sdf -omol2 -O${sample}_ligand.mol2

# step2 ligand
prepare_ligand4.py -l ${sample}_ligand.mol2 -o ${sample}_ligand.pdbqt

# step3 receptor
prepare_receptor4.py -r ${sample}_protein_processed.pdb -o ${sample}_protein_processed.pdbqt -A hydrogens

# step4 prepare_gpf4.py
/home/zhusitao/miniconda3/envs/AutoDock/MGLToolsPckgs/AutoDockTools/Utilities24/prepare_gpf4.py -l ${sample}_ligand.pdbqt -r ${sample}_protein_processed.pdbqt -y -o ${sample}_protein_processed.gpf

# step5 map production
autogrid4 -p ${sample}_protein_processed.gpf -l ${sample}_protein_processed.glg

# step6 docking
autodock_gpu_64wi -ffile  ${sample}_protein_processed.maps.fld -lfile ${sample}_ligand.pdbqt -nrun 20 -resnam ${sample}_ligand

```

## 一些参考
[知乎例子](https://zhuanlan.zhihu.com/p/579692272)
