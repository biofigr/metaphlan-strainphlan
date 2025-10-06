# MetaPhlAn & StrainPhlAn
BioFigR service for the compositional analysis of shotgun metagenomic data using MetaPhlAn and StrainPhlAn.

---

## VM details

- CloudCIX (https://www.cloudcix.com/)
- Ubuntu Server 20.04 LTS
- 20 vCPU
- 60GB RAM

---

### Assumptions

- Clean FASTQ data ready to use
- Fresh VM, no prior Snapshot

---

## Install MetaPhlAn

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh && bash miniconda.sh -b -p $HOME/miniconda && rm miniconda.sh && eval "$($HOME/miniconda/bin/conda shell.bash hook)" && conda init

conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main

conda --version

conda create -y -n metaphlan -c conda-forge -c bioconda metaphlan=4.* bowtie2=2.*
```


```bash
nohup metaphlan --install --db_dir ~/metaphlan_db -x mpa_vOct22_CHOCOPhlAnSGB_202212 &
```

---

## Run MetaPhlAn

```bash
mkdir metaphlan_out

bash ./scripts/gen_sample_txt.sh <folder/with/fastq/files> ./sample_lists/samples_metaphlan.txt

nohup bash -c "bash ./scripts/run_metaphlan.sh --samples ./sample_lists/samples_metaphlan.txt --outdir metaphlan_out --db_dir ~/metaphlan_db --index mpa_vOct22_CHOCOPhlAnSGB_202212 --threads 4" &

merge_metaphlan_tables.py ./metaphlan_out/*/*.profile.tsv > ./metaphlan_out/merged_metaphlan_profile.tsv
```

---

## Run StrainPhlAn

```bash
bzip2 -k ~/metaphlan_db/mpa_vOct22_CHOCOPhlAnSGB_202212.fna

nohup bash -c "bash scripts/cp_run_strainphlan.sh > strainphlan.log" &
```


