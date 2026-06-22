## Open Babel

Open Babel —— 化学工具箱
Open Babel 是一款化学工具箱，旨在兼容多种化学数据格式。这是一个开放的协作项目，允许用户针对分子建模、化学、固态材料、生物化学及相关领域的各类数据进行搜索、转换、分析或存储。

提供即用型程序及完整的开发者工具包

支持读取、写入和转换超过 110 种化学文件格式

利用 SMARTS 及其他方法筛选和搜索分子文件

支持分子建模、化学信息学及生物信息学应用

涵盖有机化学、无机化学、固态材料及核化学等领域

### 支持的格式
```bash
/home/zhusitao/miniconda3/envs/SurfDock/bin/obabel -L formats
```

### sdf to mol2
```bash
/home/zhusitao/miniconda3/envs/SurfDock/bin/obabel -isdf O75170_ligand.sdf -omol2 -OO75170_ligand.mol2
```
