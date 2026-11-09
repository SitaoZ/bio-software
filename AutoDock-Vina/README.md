# AutoDock-Vina 
## 1. Preparing the receptor

该命令指定 `1iep_receptorH.pdb` 为输入文件，并将 `1iep_receptor` 用作输出文件的基本名称。根据 `-p` 选项的要求，程序将生成一个受体 PDBQT 文件；而根据 `-v` 选项以及用于定义搜索框的参数（`--box_size` 和 `--box_center`），程序还将生成一个包含搜索框尺寸信息的 TXT 文件以及一个对应的 PDB 文件。其中，TXT 文件可用作对接计算的配置文件，而 PDB 文件则可用于在 PyMOL 等其他软件中对搜索框进行可视化展示。

```bash 
# ./AutoDock-Vina/example/basic_docking/data/1iep_receptorH.pdb
mk_prepare_receptor.py -i 1iep_receptorH.pdb -o 1iep_receptor -p -v \
--box_size 20 20 20 --box_center 15.190 53.903 16.917
```



## 2. Preparing the ligand
```bash 
mk_prepare_ligand.py -i 1iep_ligand.sdf -o 1iep_ligand.pdbqt
```

 ## 3. (Optional) Generating affinity maps for AutoDock F
```bash 
mk_prepare_receptor.py -i 1iep_receptorH.pdb -o 1iep_receptor -p -v -g \
--box_size 20 20 20 --box_center 15.190 53.903 16.917

autogrid4 -p 1iep_receptor.gpf -l 1iep_receptor.glg
```


## 4. Running AutoDock Vina

### 4.a. Using AutoDock4 forcefield
```bash 
vina  --ligand 1iep_ligand.pdbqt --maps 1iep_receptor --scoring ad4 \
        --exhaustiveness 32 --out 1iep_ligand_ad4_out.pdbqt
```

### 4.b. Using Vina forcefield
```bash
vina --receptor 1iep_receptor.pdbqt --ligand 1iep_ligand.pdbqt \
       --config 1iep_receptor.box.txt \
       --exhaustiveness=32 --out 1iep_ligand_vina_out.pdbqt
```
### 4.c. Exporting results to SDF
```bash
mk_export.py 1iep_ligand_vina_out.pdbqt -s 1iep_ligand_vina_out.sdf
mk_export.py 1iep_ligand_ad4_out.pdbqt -s 1iep_ligand_ad4_out.sdf
```


## 5. Expected results
### 5.a. Using AutoDock forcefield
```bash
Scoring function : ad4
Ligand: 1iep_ligand.pdbqt
Exhaustiveness: 32
CPU: 0
Verbosity: 1

Reading AD4.2 maps ... done.
Performing docking (random seed: 1045208650) ...
0%   10   20   30   40   50   60   70   80   90   100%
|----|----|----|----|----|----|----|----|----|----|
***************************************************

mode |   affinity | dist from best mode
    | (kcal/mol) | rmsd l.b.| rmsd u.b.
-----+------------+----------+----------
1       -14.72          0          0
2       -14.63      0.862      1.051
3       -13.12      1.152      1.877
4        -11.7      4.989      11.38
5       -11.44      3.619      11.51
6       -11.39       1.36      2.222
7       -11.21      3.773      12.06
8       -10.71      2.043      13.49
9       -10.41      1.748      2.955
```
### 5.b. Using Vina forcefield

使用 Vina forcefield，对于该示例体系，Vina 的计算结果应大致相同，其中最优评分约为 -13 kcal/mol。

```bash
Scoring function : vina
Rigid receptor: 1iep_receptor.pdbqt
Ligand: 1iep_ligand.pdbqt
Grid center: X 15.19 Y 53.903 Z 16.917
Grid size  : X 20 Y 20 Z 20
Grid space : 0.375
Exhaustiveness: 32
CPU: 0
Verbosity: 1

Computing Vina grid ... done.
Performing docking (random seed: -1622165383) ...
0%   10   20   30   40   50   60   70   80   90   100%
|----|----|----|----|----|----|----|----|----|----|
***************************************************

mode |   affinity | dist from best mode
    | (kcal/mol) | rmsd l.b.| rmsd u.b.
-----+------------+----------+----------
1       -13.23          0          0
2       -11.29     0.9857      1.681
3       -11.28      3.044      12.41
4       -11.15      3.813      12.24
5       -9.746      3.313      12.36
6       -9.132      1.736      13.47
7       -9.079      2.559      12.78
8       -8.931      3.951      12.69
9       -8.762      3.541      12.21
```
### refernence
[vina-document](https://autodock-vina.readthedocs.io/en/latest/docking_basic.html)




### AutoDock-Vina 结果解读
```bash 
1. Vina运行结束后，主要生成文件 1iep_ligand_vina_out.pdbqt，包含所有可能的配体构象（根据你设置的num_modes数量）。

用记事本打开 1iep_ligand_vina_out.pdbqt 文件，每个model信息的格式如下：

2. REMARK VINA RESULT:这里的 -13.223 表示结合自由能，单位是kcal/mol；
具体来说：<-13.223 表示结合能力非常强了

3. REMARK INTER: 这里的 -19.111 是指蛋白与配体之间真正的相互作用能，包括Van der Waals、Hydrogen Bond、Hydrophobic和electrostatic，这一项越负越好，说明蛋白和配体之间作用很强。

4. REMARK INTRA: 这里的 -0.477 是指配体自身内部能量，例如配体为了进入结合位点，发生了扭转、弯曲或者构象变化，这些都会产生内部能量，数值越接近0越自然。
```

[reference](https://zhuanlan.zhihu.com/p/2058121151537209384)


### AutoDock-Vina 可视化
```bash
1. 打开PyMol > File > Open > 1iep_receptorH.pdb（受体）
2. File > Open > 1iep_ligand_vina_out.pdbqt（配体构象）
3. 右侧点击受体文件 1iep_receptorH.pdb > S > surface
4. 为了更加突出配体的位置，可以修改受体的透明度：set transparency, 0.7, 1iep_receptorH.pdb
5. 点击1IEP > H > cartoon:
6. 保存图片:File > Export Image As > PNG

```
[reference](https://blog.csdn.net/2403_86665173/article/details/143081298)
