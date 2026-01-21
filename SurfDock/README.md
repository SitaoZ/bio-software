## SurfDock 

## Install 
```bash
conda create -y -n SurfDock python==3.10
conda activate SurfDock
# conda install -y --channel=https://conda.anaconda.org/conda-forge --channel=https://conda.anaconda.org/pytorch --channel=https://conda.anaconda.org/pyg mamba && conda clean -ya
conda install -y pytorch==2.5.0 pytorch-cuda=12.4 -c pytorch -c nvidia
conda install -y --channel=https://conda.anaconda.org/conda-forge --channel=https://conda.anaconda.org/pytorch --channel=https://conda.anaconda.org/pyg numpy scipy pandas &&conda clean -ya

conda install -y --channel=https://conda.anaconda.org/conda-forge --channel=https://conda.anaconda.org/pytorch --channel=https://conda.anaconda.org/pyg openff-toolkit==0.15.2 openmm==8.1.1 openmmforcefields==0.12.0 pdbfixer==1.9 && conda clean -ya
conda install -y --channel=https://conda.anaconda.org/conda-forge --channel=https://conda.anaconda.org/pytorch --channel=https://conda.anaconda.org/pyg babel==2.13.1 biopandas==0.4.1 openbabel==3.1.1 plyfile==1.0.1 prody==2.4.0 torch-ema==0.3 torchmetrics==1.2.1 && conda clean -ya
# conda install -y pyg -c pyg

pip install sympy==1.13.1
pip install torch_geometric
# pip install sympy==1.13.1
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.5.0+cu124.html
pip install -U --no-cache-dir spyrmsd scikit-learn==1.3.2 accelerate==0.15.0 biopython==1.79 e3nn==0.5.1 huggingface-hub==0.17.3 mdanalysis==2.4.0 posebusters==0.2.7 rdkit==2023.3.1 tokenizers==0.13.3 transformers==4.29.2 wandb==0.16.1
# pip install pymesh
pip install pymesh==1.0.0
pip install https://github.com/nuvolos-cloud/PyMesh/releases/download/v0.3.1/pymesh2-0.3.1-cp310-cp310-linux_x86_64.whl
conda install loguru
pip install dimorphite_dl
pip install prefetch_generator
conda install mx::reduce
conda install conda-forge::openbabel
```

## 报错解决

- torch GLIBC
```bash
# 1. pytorch
mv libcurand.so.10 libcurand.so.10.back
cp /home/zhusitao/miniconda3/envs/SurfDock/lib/libcurand.so.10.3.0.86 .
ln -s libcurand.so.10.3.0.86 libcurand.so.10
```

- pymesh libstdc++.so.6
```bash
# 2. pymesh
cd /home/zhusitao/miniconda3/envs/SurfDock/lib/python3.10/site-packages/pymesh
mv ./lib/libstdc++.so.6 ./lib/libstdc++.so.copy.6
```
- pymesh版本
```bash
pymesh2 是您需要的库，而不是 pymesh。
pip install pymesh
pip install https://github.com/nuvolos-cloud/PyMesh/releases/download/v0.3.1/pymesh2-0.3.1-cp310-cp310-linux_x86_64.whl
请按照我们的 README 中的这个命令来安装 pymesh。
```

- APBS 版本问题，与对应的GLIBC 不支持 (导致第一步不生成.ply表面文件)
```bash
# /data/zhusitao/project/songLab/14.SurfDock/SurfDock/comp_surface/tools/transfer/APBS-3.4.1.Linux/share/apbs/tools/bin/multivalue
cd /data/zhusitao/project/songLab/14.SurfDock/SurfDock/comp_surface/tools/transfer/APBS-3.4.1.Linux/share/apbs/tools/bin
ln -s /data/zhusitao/project/songLab/14.SurfDock/software/share/apbs/tools/bin/multivalue

# /data/zhusitao/project/songLab/14.SurfDock/SurfDock/comp_surface/tools/transfer/APBS-3.4.1.Linux/bin/apbs
cd /data/zhusitao/project/songLab/14.SurfDock/SurfDock/comp_surface/tools/transfer/APBS-3.4.1.Linux/bin/
ln -s /data/zhusitao/project/songLab/14.SurfDock/software/bin/apbs
```

- computeAPBS.py
由于程序识别。in文件的路径存在问题，
所以需要将
```bash
os.system(make_apbs)
```
```bash
pwd_dir = os.path.dirname(tmp_file_base)
os.chdir(pwd_dir)
os.system(make_apbs)
```
