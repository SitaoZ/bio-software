## Pysradb


- 下载项目的源数据
```bash
# 导出详细元数据到 TSV 文件
pysradb metadata SRP506497 --detailed > SRP506497_metadata.tsv

# 查看所有列名
head -n 1 SRP506497_metadata.tsv | tr '\t' '\n' | nl

# 预览核心编号与常见名称列的对应关系
cut -f 1,2,3,4,5 SRP506497_metadata.tsv | head -n 10



```
