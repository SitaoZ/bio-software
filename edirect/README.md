## EDIRECT 使用
[NCBI book](https://www.ncbi.nlm.nih.gov/books/NBK179288/)

[杭州疾控Entrez Direct](https://indexofire.github.io/pathongs/book/C01_Common-Utility/03_entrez/)

```bash 
# 安装
sh -c "$(curl -fsSL ftp://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh)"
# 将 edirect 添加到 PATH
export PATH=${HOME}/edirect:${PATH}

# 基本语法
esearch -db <数据库名称> -query <查询关键词>
# -db: 指定检索的 NCBI 数据库（如 pubmed, nuccore, protein, gene 等）。
# -query: 检索的关键词或复杂布尔逻辑查询（使用 AND, OR, NOT）。

```

- 基础查询
```bash 
esearch -db pubmed -query "lung cancer AND smoking"

```
- 结合efetch下载

```bash 
esearch -db nuccore -query "Homo sapiens BRCA1[Gene]" | efetch -format fasta > brca1.fasta

```

- 结合 xtract 解析 ID

```bash 
esearch -db gene -query "human[orgn] AND tp53[gene]" | xtract -pattern IdList -element Id
```

- 下载SNP
```bash
# 1. 获取所有SNP的UID（仅ID，速度最快）
esearch -db snp -query "[起始坐标]:[结束坐标][Base Position] AND [染色体号][CHR] AND txid9606[Organism]"

# 2. 下载XML格式的完整数据（信息最全，推荐） 
esearch -db snp -query "[起始坐标]:[结束坐标][Base Position] AND [染色体号][CHR] AND txid9606[Organism]" | efetch -format xml > [输出文件名].xml

# 3. 下载FASTA格式的序列数据
esearch -db snp -query "[起始坐标]:[结束坐标][Base Position] AND [染色体号][CHR] AND txid9606[Organism]" | efetch -format fasta > [输出文件名].fasta

```
