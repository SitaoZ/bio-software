# AlphaFold3
利用 conda 部署更能稳定使用的 AF3.  
环境：anaconda，python 3.11，Rocky 9（理论上主流Linux发行版皆可），CUDA 12


## 0. 环境配置
- a. 使用 Python 3.11 创建 Conda 环境：
```bash
# Create the environment
conda create -n Alphafold3 python=3.11

# Activate the environment
conda activate Alphafold3

# Prevent Python from using ~/.local for user-installed packages
# This ensures the environment is fully isolated ("closed")
conda env config vars set PYTHONUSERBASE=intentionally-disabled

# Reactivate the environment for the change to take effect
conda deactivate
conda activate Alphafold3
```
注意：在安装和执行 AlphaFold 3 期间，需要激活 AlphaFold3 Conda 环境。
为什么设置 PYTHONUSERBASE=intentionally-disabled？目的是告知 pip 和 Python 完全忽略 ~/.local，确保所有软件包仅来自 Conda 环境。这可以防止全局或用户安装的 Python 软件包的干扰，从而保持环境的可复现性和独立性。


- b. 安装开发工具和依赖项  
在 conda 环境中安装必要的开发工具和依赖项，而不是在系统操作系统上安装。请注意，HMMER 是使用 conda 安装的，不需要单独安装或编译，尽管有些工具可能无法通过这种方式安装。

```bash
# Install cmake, gcc, and gxx
conda install -c conda-forge cmake gcc gxx -y

# Install Boost for Python 3.11 and numpy
conda install -c conda-forge boost boost-cpp numpy -y # Boost for Python 3.11

# Install compression libraries
conda install -c conda-forge bzip2 zstd -y

# Install git and zlib
conda install -c conda-forge git zlib -y

# Install HMMER
conda config --add channels bioconda 
conda install -c conda-forge hmmer gsl=2.6 -y

# Install and Upgrade pip within the Alphafold3 environment
conda install pip -y
pip install --upgrade pip  # Update pip (specific to the AF3 environment)

```

- c. 使用 pip 安装所需的 Python 包  
```bash
pip install pandas==2.2.3 matplotlib==3.10.0 absl-py==2.1.0 chex==0.1.87 \
    dm-haiku==0.0.13 dm-tree==0.1.8 filelock==3.16.1 \
    "jax[cuda12]==0.4.34" jax-cuda12-pjrt==0.4.34 jax-triton==0.2.0 \
    jaxlib==0.4.34 jaxtyping==0.2.34 jmp==0.0.4 ml-dtypes==0.5.0 \
    numpy==2.1.3 nvidia-cublas-cu12==12.6.3.3 \
    nvidia-cuda-cupti-cu12==12.6.80 nvidia-cuda-nvcc-cu12==12.6.77 \
    nvidia-cuda-runtime-cu12==12.6.77 nvidia-cudnn-cu12==9.5.1.17 \
    nvidia-cufft-cu12==11.3.0.4 nvidia-cusolver-cu12==11.7.1.2 \
    nvidia-cusparse-cu12==12.5.4.2 nvidia-nccl-cu12==2.23.4 \
    nvidia-nvjitlink-cu12==12.6.77 opt-einsum==3.4.0 pillow==11.0.0 \
    rdkit==2024.3.5 scipy==1.14.1 tabulate==0.9.0 toolz==1.0.0 \
    tqdm==4.67.0 triton==3.1.0 typeguard==2.13.3 \
    typing-extensions==4.12.2 zstandard==0.23.0


```

## 1. 安装 AlphaFold 3

- a. Clone AlphaFold 3 Repository

```bash
# Set the desired application directory
cd /run/media/Programs  # 你存放AF3项目的路径

# Clone the AlphaFold 3 repository
git clone https://github.com/google-deepmind/alphafold3.git

# Define the AlphaFold 3 directory variable
cd alphafold3
export ALPHAFOLD3DIR=$(pwd)

```

- b. 下载数据库   
fetch_databases.sh 脚本会下载 AlphaFold 3 所需的数据库。默认情况下，它会将数据库下载到你的主目录。
```bash
# Run the script to download the databases
./fetch_databases.sh
mv ~/public_databases /home/GeneticDatabase    # 路径自行决定
# 重要提示：请确保您有足够的磁盘空间，因为数据库相当大（约 627GB）。
```

- c. 获取模型参数

需要自行申请访问 AlphaFold 3 模型参数：

1.填写 AlphaFold 团队提供的官方申请表。  
2.访问权限的授予将由 Google DeepMind 决定。他们计划在 2-3 个工作日内回复请求。您只能使用直接来自 Google 的 AlphaFold 3 模型参数。使用须遵守以下使用条款。  
3.申请获批后，您将收到 af3.bin.zst 的下载链接。  
