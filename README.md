# ![biofigr/MetaPhlAn](site/assets/img/prof_logo_black-on-noBG_condensed.png)

## Shotgun Metagenomics -- Marker-Based Analysis
BioFigR service for the analysis of shotgun metagenomic data using MetaPhlAn4, HUMAnN3, and StrainPhlAn4.

---

### VM details

- CloudCIX (https://www.cloudcix.com/)
- Ubuntu Server 20.04 LTS, 20 vCPU, 60GB RAM

### Need to install conda for new VMs

```bash
cd ~

wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh && bash miniconda.sh -b -p $HOME/miniconda && rm miniconda.sh && eval "$($HOME/miniconda/bin/conda shell.bash hook)" && conda init

conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main

source ~/.bashrc

conda --version
```

---

## Create a Conda environment

- Load the environment(s) from my YAML file in repo envs/

```bash
conda env create -f envs/sra_env.yaml

conda env list

conda activate sra
```

- Or create a new conda environment and install packages
```bash
conda create -n sra -c bioconda -c conda-forge sra-tools

conda env list

conda activate sra

conda install -c bioconda entrez-direct

fasterq-dump --version
```

---

### Download FASTQ files:

-- I have taken the SRA sample accession list from MetaPhlAn4.1 tutorial page:
```bash
cat > sra_ids.txt <<EOF
SRS014476
SRS014494
SRS014459
SRS014464
SRS014470
SRS014472
EOF
```

-- Fetch real SRR IDs from sample accession IDs:
```bash
cat sra_ids.txt | while read srs; do
  esearch -db sra -query $srs | efetch -format runinfo | cut -d',' -f1 | grep SRR
done > srr_ids.txt
```

-- Output:
```bash
head srr_ids.txt
SRR061436
SRR061452
```

-- SRA One:
```bash
prefetch SRR061436
fasterq-dump --split-files -e 4 -t tmp -O fastq SRR061436
```

-- SRA Two:
```bash
prefetch SRR061452
fasterq-dump --split-files -e 4 -t tmp -O fastq SRR061452
```

-- Check the files and compress:
```bash
for i in *.fastq; do grep -c '@' $i; done
# Check some reads in files

for i in *.fastq; do gzip $i; done
# Compress to better represent starting FASTQ samples
```

```bash
conda deactivate

conda env export --name sra --no-builds > sra_env.yaml
# Export the envronment

head sra_env.yaml
```

---

## Install MetaPhlAn

```bash
conda create -y -n metaphlan -c conda-forge -c bioconda metaphlan=4.* bowtie2=2.*

conda activate metaphlan
```

```bash
nohup metaphlan --install --db_dir ~/metaphlan_db -x mpa_vOct22_CHOCOPhlAnSGB_202212 &
```

---

## Run MetaPhlAn

--- On current VM setup (20 vCPU and 60GB RAM), a metagenome sample with ~20M PE150 reads will reach 40-50% %MEM usage
--- If the %MEM usage hits 100%, the job will fail
--- Monitor a headless VM using the bash command "top"

```bash
# Remember to activate the environment:
conda activate metaphlan

mkdir metaphlan_out

# Ensure FASTQ files are Gzip compressed (```bash for i in *.fastq; do gzip $i; done```)

metaphlan ./fastq/SRR061436_1_100k.fastq.gz --input_type fastq --db_dir ~/metaphlan_db/ --mapout sample.mapout -o sample_profile.txt -s sample.samout

# nohup bash -c "bash scripts/run_metaphlan.sh --samples samples.txt --outdir metaphlan_out --db_dir ~/metaphlan_db --index mpa_vOct22_CHOCOPhlAnSGB_202212 --threads 4" &

merge_metaphlan_tables.py ./metaphlan_out/*/*.profile.tsv > ./metaphlan_out/merged_metaphlan_profile.tsv
```

---

## Run StrainPhlAn

```bash
bzip2 -k ~/metaphlan_db/mpa_vOct22_CHOCOPhlAnSGB_202212.fna

nohup bash -c "bash scripts/cp_run_strainphlan.sh > strainphlan.log" &

bzip2 -c sample.samout > sample.samout.bz2

sample2markers.py -i sample.samout.bz2 -o strain_out/ -d ~/metaphlan_db/

mkdir sample_markers

sample2markers.py -i */*sam.bz2 -o sample_markers -n 8 -d ~/metaphlan_db/mpa_vJan25_CHOCOPhlAnSGB_202503.pkl

```

```bash
awk -F'\t' '
NR==1 {for(i=2;i<=NF;i++) hdr[i]=$i; next}
$1 ~ /s__/ {
  c=0;
  for(i=2;i<=NF;i++) if(($i+0)>0) c++;
  if(c>=4) printf("%s\t%d\n",$1,c);
}
' "merged_metaphlan_profile.tsv" | sort -k2,2nr | tee "species_overlap_n2.tsv"

# This prints taxa from the merged MetaPhlAn4 output with >=4 species needed for the next StrainPhlAn4 step.
```

```bash
strainphlan -s *.bz2 -d ~/metaphlan_db/mpa_vJan25_CHOCOPhlAnSGB_202503.pkl -c s__Porphyromonas_endodontalis --output_dir strainphlan_out
```
