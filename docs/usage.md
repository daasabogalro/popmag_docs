# Usage

This page explains how to prepare your input data and run the PopMAG pipeline.

## Input Files Overview

PopMAG requires three input files:

| File | Format | Description |
|------|--------|-------------|
| MAG samplesheet | TSV | Paths to your metagenome-assembled genomes |
| Reads samplesheet | TSV | Paths to paired-end sequencing reads |
| Metadata file | CSV | Sample metadata for visualization |

## MAG Samplesheet

The MAG samplesheet is a **tab-separated** file listing all your metagenome-assembled genomes.

### Format

```
sample_id	mag_id	mag_path
```

| Column | Description |
|--------|-------------|
| `sample_id` | Sample identifier |
| `mag_id` | Unique identifier for each MAG |
| `mag_path` | Path to the MAG FASTA file |

!!! note
    The `sample_id` is used to group MAGs and generate the genomes database that will be used downstream in the competitive mapping phase. All MAGs with the same ID will be concatenated in one file. 

### Example

Create a file named `mag_samplesheet.tsv`:

```tsv
sample_id	mag_id	mag_path
SAMPLE_1	CONCOCT_59	/path/to/MAGs/CONCOCT_59.fa
SAMPLE_1	METABAT_12	/path/to/MAGs/METABAT_12.fa
SAMPLE_1	MAXBIN_03	/path/to/MAGs/MAXBIN_03.fa
SAMPLE_2	CONCOCT_22	/path/to/MAGs/CONCOCT_22.fa
SAMPLE_2	METABAT_08	/path/to/MAGs/METABAT_08.fa
```

!!! tip "File Extensions"
    PopMAG automatically detects MAG file extensions (`.fa`, `.fasta`, `.fna`). All input MAGs should use the same extension.

## Reads Samplesheet

The reads samplesheet is a **tab-separated** file listing paired-end sequencing reads for each sample.

### Format

```
sample_id	forward	reverse
```

| Column | Description |
|--------|-------------|
| `sample_id` | Sample identifier |
| `forward` | Path to forward reads (R1) |
| `reverse` | Path to reverse reads (R2) |

### Example

Create a file named `reads_samplesheet.tsv`:

```tsv
sample_id	forward	reverse
SAMPLE_1	/path/to/reads/SAMPLE_1_R1.fastq.gz	/path/to/reads/SAMPLE_1_R2.fastq.gz
SAMPLE_2	/path/to/reads/SAMPLE_2_R1.fastq.gz	/path/to/reads/SAMPLE_2_R2.fastq.gz
SAMPLE_3	/path/to/reads/SAMPLE_3_R1.fastq.gz	/path/to/reads/SAMPLE_3_R2.fastq.gz
```

!!! note "Compressed Files"
    Reads can be gzip-compressed (`.fastq.gz` or `.fq.gz`). This is recommended to save storage space.

## Metadata File

The metadata file is a **comma-separated** file containing sample information for visualization in the Shiny dashboard.

### Format

```
sample_id,Metadata_1,Metadata_2,...,Metadata_n
```

| Column | Description |
|--------|-------------|
| `sample_id` | **Required.** Sample identifier (must match reads samplesheet) |
| Additional columns | Any metadata variables (e.g., timepoint, location, treatment) |

### Example

Create a file named `metadata.csv`:

```csv
sample_id,timepoint,location,pH,temperature
SAMPLE_1,T0,Site_A,7.2,25.5
SAMPLE_2,T1,Site_A,7.1,26.0
SAMPLE_3,T0,Site_B,6.8,24.5
```

!!! warning "Required Column"
    The `sample_id` column is **mandatory**.

## Running the Pipeline

### Basic Run

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --mag_paths mag_samplesheet.tsv \
    --reads_paths reads_samplesheet.tsv \
    --metadata metadata.csv \
    --outdir results
```

### Using a Parameters File

For complex runs, you can specify parameters in a YAML file:

```yaml
# params.yml
mag_paths: "mag_samplesheet.tsv"
reads_paths: "reads_samplesheet.tsv"
metadata_file: "metadata.csv"
outdir: "results"

# Quality filtering
min_completeness: 90
max_contamination: 5

# Skip optional steps
skip_metacerberus: false
skip_instrain_compare: false
```

Then run with:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    -params-file params.yml
```

You can also make use of the [nf-params.yml](https://github.com/daasabogalro/PopMAG/blob/main/nf-params.yml) file available in the PopMAG repository. 

### Skipping Steps

You can skip certain pipeline steps if not needed:

```bash
# Skip CheckM2 quality assessment (if MAGs are pre-filtered)
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --skip_checkm2 \
    ...

# Skip MetaCerberus functional annotation
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --skip_metacerberus \
    ...

# Skip InStrain comparison between samples
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --skip_instrain_compare \
    ...
```

### Using Pre-downloaded Databases

To avoid re-downloading databases on each run:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --checkm2_db /path/to/checkm2_database \
    --metacerberus_db /path/to/metacerberus_database \
    ...
```

## Resuming Runs

If a run fails or is interrupted, resume from the last successful step:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --mag_paths mags.tsv \
    --reads_paths reads.tsv \
    --metadata metadata.csv \
    --outdir results \
    -resume
```

!!! tip "Work Directory"
    Nextflow stores intermediate files in the `work/` directory. Keep this directory intact to enable resuming.

## Output Structure

Results are organized in the output directory:

```
results/
├── checkm2/                    # MAG quality reports
├── filtered_bins/              # Quality-filtered MAGs
├── dRep/                       # Dereplication results
├── competitive_mapping/        # Bowtie2 alignments
├── coverm/                     # Abundance calculations
├── prodigal/                   # Gene predictions
├── metacerberus/               # Functional annotations
├── instrain_profile/           # InStrain profiles
├── instrain_compare/           # Sample comparisons
├── VCFs/                       # Variant call files
├── pogenom/                    # Population genetics metrics
├── singleM/                    # Community profiles
├── merged_reports/             # Combined analysis reports
└── pipeline_info/              # Execution reports
```

See the [Visualization](visualization.md) page for detailed descriptions of each output.
