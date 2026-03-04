## Annovar 
`Annovar` 是一款高效的软件工具，用来对变异进行注释 [annovar paper](!https://academic.oup.com/nar/article/38/16/e164/1749458)。
Annovar可利用最新信息对从不同基因组（包括人类基因组 hg18、hg19、hg38、hs1 (T2T-CHM13) 以及小鼠、蠕虫、苍蝇、酵母等）中检测到的遗传变异进行功能注释。
给定一个包含染色体、起始位置、终止位置、参考核苷酸和观察到的核苷酸的变异列表，ANNOVAR 可以执行：

- **基于基因的注释**  
确定 SNP 或 CNV 是否导致蛋白质编码变化以及受影响的氨基酸。用户可以灵活使用 RefSeq 基因、UCSC 基因、ENSEMBL 基因、GENCODE 基因、AceView 基因或许多其他基因定义系统。

- **基于区域的注释**  
识别特定基因组区域中的变异，例如，44 个物种中的保守区域、预测的转录因子结合位点、节段重复区域、GWAS 命中、基因组变异数据库、DNAse I 超敏位点、ENCODE H3K4Me1/H3K4Me3/H3K27Ac/CTCF 位点、ChIP-Seq 峰、RNA-Seq 峰或基因组间隔上的许多其他注释。

- **基于过滤器的注释**  
识别特定数据库中记录的变体，例如，变体是否在 dbSNP 中报告，1000 基因组计划、NHLBI-ESP 6500 外显子组或外显子组聚合联盟 (ExAC) 或基因组聚合数据库 (gnomAD) 中的等位基因频率是多少，计算 SIFT/PolyPhen/LRT/MutationTaster/MutationAssessor/FATHMM/MetaSVM/MetaLR 分数，查找 GERP++ 分数 <2 或 CADD>10 的基因间变体，或特定突变的许多其他注释。


| Value      | Default precedence | Explanation | Sequence Ontology |
| :--------  | :-----: | :----: | ----:|
| exonic     |  1   |  variant overlaps a coding  |  exonic_variant (SO:0001791) |
| splicing   |  1   |  variant is within 2-bp of a splicing junction (use -splicing_threshold to change this) |  splicing_variant(SO:0001568)  |
| ncRNA      |  2   |  variant overlaps a transcript without coding annotation in the gene definition |  non_coding_transcript_variant (S):0001619) |
| UTR5       |  3   | variant overlaps a 5' untranslated region |   5_prime_UTR_variant(SO:0001632)   |
| UTR3       |  3   | variant overlaps a 3' untranslated region |  3_prime_UTR_variant (SO:0001624) |
| intronic   |  4   | variant overlaps an intron | intron_variant (SO:0001627)| 
| upstream   |  5   | variant overlaps 1-kb region upstream of transcription start site | upstream_gene_variant (SO:0001631) |
| downstream |  5   | variant overlaps 1-kb region downstream of transcription end site | downstream_gene_variant (SO:0001632) |
| intergenic |  6   | variant is in intergenic region | intergenic_variant (SO:0001628) |


## download
需要申请才能下载，https://www.openbioinformatics.org/annovar/annovar_download_form.php  
**学术免费**

## 数据库下载和使用
```bash
# 下载注释用数据库，数据在到humandb目录
# 1. hg19
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar refGene humandb/
perl annotate_variation.pl -buildver hg19 -downdb cytoBand humandb/
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar exac03 humandb/ 
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar avsnp151 humandb/ 
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar dbnsfp47a humandb/
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar gnomad211_exome humandb/

# 2. hg38
perl annotate_variation.pl -buildver hg38 -downdb -webfrom annovar refGene humandb/
perl annotate_variation.pl -buildver hg38 -downdb cytoBand humandb/
perl annotate_variation.pl -buildver hg38 -downdb -webfrom annovar exac03 humandb/ 
perl annotate_variation.pl -buildver hg38 -downdb -webfrom annovar avsnp151 humandb/ 
perl annotate_variation.pl -buildver hg38 -downdb -webfrom annovar dbnsfp47a humandb/
perl annotate_variation.pl -buildver hg38 -downdb -webfrom annovar gnomad312_genome humandb/


# 3.使用table_annovar.pl对 example/ex1.avinput输入文件进行注释
perl table_annovar.pl example/ex1.avinput humandb/ -buildver hg19 -out myanno -remove -protocol refGene,cytoBand,exac03,avsnp147,dbnsfp30a -operation gx,r,f,f,f -nastring . -csvout -polish -xref example/gene_xref.txt

```

- annovar的标准输入格式.avinput的具体内容如下

第一至五列为必须，分别是染色体号，起始位点，结束位点，参考等位基因 reference allele 以及 替代等位基因 alternative allele，第五列之后可自由添加所需要的信息
```bash
1 948921 948921 T C comments: rs15842, a SNP in 5' UTR of ISG15
1 1404001 1404001 G T comments: rs149123833, a SNP in 3' UTR of ATAD3C
1 5935162 5935162 A T comments: rs1287637, a splice site variant in NPHP4
1 162736463 162736463 C T comments: rs1000050, a SNP in Illumina SNP arrays
1 84875173 84875173 C T comments: rs6576700 or SNP_A-1780419, a SNP in Affymetrix SNP arrays
1 13211293 13211294 TC - comments: rs59770105, a 2-bp deletion
1 11403596 11403596 - AT comments: rs35561142, a 2-bp insertion
1 105492231 105492231 A ATAAA comments: rs10552169, a block substitution
1 67705958 67705958 G A comments: rs11209026 (R381Q), a SNP in IL23R associated with Crohn's disease
2 234183368 234183368 A G comments: rs2241880 (T300A), a SNP in the ATG16L1 associated with Crohn's disease
16 50745926 50745926 C T comments: rs2066844 (R702W), a non-synonymous SNP in NOD2
16 50756540 50756540 G C comments: rs2066845 (G908R), a non-synonymous SNP in NOD2
16 50763778 50763778 - C comments: rs2066847 (c.3016_3017insC), a frameshift SNP in NOD2
13 20763686 20763686 G - comments: rs1801002 (del35G), a frameshift mutation in GJB2, associated with hearing loss
13 20797176 21105944 0 - comments: a 342kb deletion encompassing GJB6, associated with hearing loss

```

- vcf也支持  

```bash
perl table_annovar.pl example/ex2.vcf humandb/ -buildver hg19 -out myanno -remove -protocol refGene,cytoBand,exac03,avsnp147,dbnsfp30a -operation g,r,f,f,f -nastring . -vcfinput -polish
```

- annotate_variation.pl  
快速完成单一注释
```bash
# 基于基因
perl annotate_variation.pl -geneanno -dbtype refGene -buildver hg19 example/ex1.avinput humandb/
#基于区域
perl annotate_variation.pl -regionanno -dbtype cytoBand -buildver hg19 example/ex1.avinput humandb/ 
#基于筛选
perl annotate_variation.pl -filter -dbtype exac03 -buildver hg19 example/ex1.avinput humandb/

```
