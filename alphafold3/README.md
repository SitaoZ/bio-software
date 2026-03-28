# AlphaFold3
利用 conda 部署更能稳定使用的 AF3.  
环境：anaconda，python 3.11，Rocky 9（理论上主流Linux发行版皆可），CUDA 12  
[参考](https://zhuanlan.zhihu.com/p/1949839940293751742)

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

- 注意dm-haiku 与 jax jax.lib匹配的问题

```bash
# 查看版本
pip show jax jaxlib
# 升级版本
pip install --upgrade jax jaxlib
# 注意dm-haiku版本
pip install -U dm-haiku

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

```bash
mv af3.bin.zst /home/af3_wts/    # 放在你自己的目录
cd /home/af3_wts/
unzstd af3.bin.zst    # Decompress the model
```
- d 从存储库安装 AlphaFold 3
可能存在 zlib 链接不正确的问题。请使用以下环境变量来解决此问题：
```bash
cd ${ALPHAFOLD3DIR}

# Export paths for zlib
export CXXFLAGS="-I$(dirname $(find ${CONDA_PREFIX} -name zlib.h | head -n 1))"
export LDFLAGS="-L$(dirname $(find ${CONDA_PREFIX} -name libz.so | head -n 1)) -lz"

# Install AlphaFold 3 without additional dependencies
# install 这一步可能由于网上稳定，会断，可以多试几次
python -m pip install --no-deps . 
```

- e 构建附加组件

```bash
cd ${CONDA_PREFIX}/bin

# Execute the build script
./build_data  # Execute
```

- f 测试安装  
通过显示帮助消息来验证安装是否成功：
```bash
cd ${ALPHAFOLD3DIR}

# Display the help message
python run_alphafold.py --help
```

- g 设置便捷运行 run_alphafold.py

AF3 的主程序入口是 run_alphafold.py，但每次输入完整路径较为麻烦。可建立辅助运行的 bash 脚本run_af3.sh。

```bash
#!/bin/bash
ALPHAFOLD3DIR="/run/media/Programs/alphafold3"    # 你的af3目录
#HMMER3_BINDIR="/usr/bin" # Path to HMMER binaries (**installed via OS package manager or specify your path**)
HMMER3_BINDIR="${CONDA_PREFIX}/bin/" # Path to Conda binarys (**installed via conda**)
DB_DIR="/home/GeneticDatabase"    # 你的数据库目录
MODEL_DIR="/home/af3_wts"         # 你的权重参数目录
WORK_DIR=$(pwd)
OUTPUT_DIR="${WORK_DIR}/af_output/${BASE_NAME}"
LOG_FILE="${OUTPUT_DIR}/af3_run.log"
JSON_FILE=$1                       # 输入文件

python $ALPHAFOLD3DIR/run_alphafold.py \
    --jackhmmer_binary_path="${HMMER3_BINDIR}/jackhmmer" \
    --nhmmer_binary_path="${HMMER3_BINDIR}/nhmmer" \
    --hmmalign_binary_path="${HMMER3_BINDIR}/hmmalign" \
    --hmmsearch_binary_path="${HMMER3_BINDIR}/hmmsearch" \
    --hmmbuild_binary_path="${HMMER3_BINDIR}/hmmbuild" \
    --db_dir="${DB_DIR}" \
    --model_dir="${MODEL_DIR}" \
    --json_path="${WORK_DIR}/${JSON_FILE}" \
    --output_dir="${OUTPUT_DIR}"
```

赋予可执行权限：

```bash
chmod +x run_af3.sh
```

在 bash 文件中赋予别名：

```bash
alias af3="sh /path/to/run_af3.sh"       # 该文件的存放路径
```

现在，在激活的 conda 环境、适当的工作目录中时，您只需执行以下命令即可运行 AlphaFold 3：

```bash
conda activate Alphafold3
af3 fold_input.sh
```


其他注意事项
硬件要求
要在资源有限的系统上运行 AlphaFold 3，至少需要配备 8 或 12 GB VRAM 的 Amper NVIDIA GPU。但是，为了获得最佳性能，建议使用专业 GPU（例如 NVIDIA A100、H100）或高端消费级 GPU（例如 RTX 3090、4090 或最新的 5090），因为它们提供卓越的内存和处理能力，可显著提升 AlphaFold 3 的运行效率。

CUDA 和 NVIDIA 驱动
确认您拥有与已安装的 NVIDIA 软件包匹配的正确版本的 CUDA 和 NVIDIA 驱动程序。建议安装最新的显卡驱动程序，特别是每个发行版提供的驱动程序，尤其是在 Linux 上安装时。

其他建议
磁盘空间：数据库（627GB）、模型（1.1GB）和 Conda 环境（6.7GB）需要大量磁盘空间。请确保您至少有 700GB 的可用空间。为了获得最佳性能，建议使用 1TB 的专用 NVMe 硬盘。
更新和支持：请定期检查 AlphaFold 3 官方代码库，了解更新情况以及依赖项的潜在变更。

