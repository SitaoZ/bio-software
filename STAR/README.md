## STAR

STAR: ultrafast universal RNA-seq aligner

## Install
```bash
# Get latest STAR source from releases
wget https://github.com/alexdobin/STAR/archive/2.7.11b.tar.gz
tar -xzf 2.7.11b.tar.gz
cd STAR-2.7.11b

# Alternatively, get STAR source using git
git clone https://github.com/alexdobin/STAR.git

# Compile
cd STAR-2.7.11b/source
make STAR
```
- conda
```bash
conda install bioconda::star
```


- Step1: Genome index build
这一步需要基因组的FASTA和GTF文件
```bash
# the file system needs at least 100GB of disk space available for a typical mammalian genome.
STAR --runThreadN 6 \
	--runMode genomeGenerate \
	--genomeDir /data/songlab/zhusitao/database/Animal/Mouse/ensembl-112/STAR_Index \
	--genomeFastaFiles /data/songlab/zhusitao/database/Animal/Mouse/ensembl-112/Mus_musculus.GRCm39.dna.toplevel.fa \
	--sjdbGTFfile /data/songlab/zhusitao/database/Animal/Mouse/ensembl-112/Mus_musculus.GRCm39.103.gtf \
	--sjdbOverhang 149
```

- Step2: Mapping
```bash
STAR \
--runThreadN 8 \
--limitBAMsortRAM 20000000000 \
--outFilterType BySJout \
--outFilterMismatchNmax 10  \
--genomeDir /data/songlab/zhusitao/database/Animal/Mouse/ensembl-112/STAR_Index \
--readFilesIn .rm_rRNA_1.fq.gz \
.rm_rRNA_2.fq.gz \
--readFilesCommand 'zcat' \
--outFileNamePrefix  name_prefix \
--outSAMtype BAM Unsorted \
--quantMode TranscriptomeSAM GeneCounts \
--outSAMattributes All  \
--outSAMstrandField intronMotif \
--outBAMcompression 6 \
--outReadsUnmapped Fastx

```
