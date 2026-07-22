# Cell-Ranger-analysis
Cell Ranger analysis for Chromium GEM-X Flex Fixed RNA Profiling
If you are using Chromium GEM-X Flex (Fixed RNA Profiling
For Chromium GEM-X Flex Fixed RNA Profiling, the recommended pipeline is cellranger multi, not cellranger count. Starting with Cell Ranger 7.0, all Flex datasets (formerly Fixed RNA Profiling) are processed through the multi pipeline because they rely on probe-based alignment and a configuration CSV. 

Step 1. Prepare your files
```bash
project/
├── fastqs/
│   ├── Sample1_S1_L001_R1_001.fastq.gz
│   ├── Sample1_S1_L001_R2_001.fastq.gz
│   └── ...
├── refdata-gex-GRCh38-2024-A/
├── Chromium_Human_Transcriptome_Probe_Set_v2.0_GRCh38.csv
└── config.csv
```
The probe set CSV is distributed with Cell Ranger (or available from 10x Genomics) and is required for Flex analysis. With Cell Ranger ≥9.0, the transcriptome reference is optional for Gene Expression-only Flex analyses, but many users still provide it.

Step 2. Create the config CSV
Multiplex Flex
If multiple samples were hybridized with different probe barcodes before pooling, you also need a [samples] section, for example:
[gene-expression]
reference,/data/refdata-gex-GRCh38-2024-A
probe-set,/data/Chromium_Human_Transcriptome_Probe_Set_v2.0_GRCh38.csv
[libraries]
fastq_id,fastqs,feature_types
Pool1,/data/fastqs,Gene Expression
[samples]
sample_id,probe_barcode_ids,description
P01,BC001,Patient1
P02,BC002,Patient2
P03,BC003,Patient3
The exact barcode IDs depend on the probe barcode plate you used.

Step 3. Run Cell Ranger
```bash
cellranger multi \
    --id=Flex_run1 \
    --csv=config.csv \
    --localcores=32 \
    --localmem=120
```
or on SLURM:
```bash
#!/bin/bash
#SBATCH --cpus-per-task=32
#SBATCH --mem=128G
#SBATCH --time=48:00:00

module load cellranger

cellranger multi \
    --id=Flex_run1 \
    --csv=config.csv \
    --localcores=$SLURM_CPUS_PER_TASK \
    --localmem=120
```

Step 4. Outputs
```bash
Flex_run1/
├── outs/
│   ├── web_summary.html
│   ├── metrics_summary.csv
│   ├── per_sample_outs/
│   │      ├── Sample1/
│   │      │      ├── filtered_feature_bc_matrix/
│   │      │      └── raw_feature_bc_matrix/
│   │      └── ...
│   └── molecule_info.h5
```
For multiplexed Flex, each sample has its own matrix under per_sample_outs/.

Step 5. Load into Seurat
```R
library(Seurat)

counts <- Read10X(
    "Flex_run1/outs/per_sample_outs/Sample1/count/filtered_feature_bc_matrix"
)

obj <- CreateSeuratObject(counts)
```
