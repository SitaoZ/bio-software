## AlphaFetcher
下载AlphaFold结构

- 安装
```bash
pip install alphafetcher

```

- 下载
```bash
from alphafetcher import AlphaFetcher

# Instantiate the fetcher
# The base_savedir parameter allows you to set a base directory where files will be saved.
# Inside this directory, two separate directories for pdb and cif files will be created.
fetcher = AlphaFetcher(base_savedir="my_savedir")

# Add desired Uniprot access codes
fetcher.add_proteins(["A1KXE4", "H0YL14", "B2RXH2", "A8MVW5"])

# Retrieve metadata
fetcher.fetch_metadata(multithread=True, workers=4)
# Metadata available at fetcher.metadata_dict

# Commence download of specified files
fetcher.download_all_files(pdb=True, cif=True, multithread=True, workers=4)
```
