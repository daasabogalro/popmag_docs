# Configuration

This page provides a complete reference for all PopMAG parameters and configuration options.

## Parameter Reference

### Input/Output Options

Parameters for specifying input data and output locations.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--outdir` | string | `results` | The output directory where results will be saved. Use absolute paths for cloud storage. |
| `--mag_paths` | string | `mag_paths.tsv` | Path to the samplesheet with MAG IDs and file locations. |
| `--reads_paths` | string | `reads_paths.tsv` | Path to the samplesheet with reads IDs and file locations. |
| `--metadata_file` | string | `metadata.csv` | Path to the metadata file. The `sample_id` column is required. |
| `--publish_dir_mode` | string | `copy` | How to publish output files. Options: `copy`, `copyNoFollow`, `link`, `move`, `rellink`, `symlink`. |

!!! tip "Publish Directory Modes"
    - `copy`: Copy files to output directory (safest, uses more disk space)
    - `symlink`: Create symbolic links (saves space, but links break if work directory is deleted)
    - `link`: Create hard links (saves space, works only on same filesystem)

### Quality Control Options

Parameters for MAG quality assessment and filtering.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--checkm2_db` | string | `null` | Path to a pre-downloaded CheckM2 database. If not provided, the database will be downloaded automatically. |
| `--checkm2_db_version` | integer | `14897628` | Specific CheckM2 database version to download (Zenodo record ID). |
| `--skip_checkm2` | boolean | `false` | Skip CheckM2 quality assessment. Use if MAGs are already quality-filtered. |
| `--min_completeness` | integer | `90` | Minimum completeness percentage for a MAG to pass filtering. |
| `--max_contamination` | integer | `5` | Maximum contamination percentage for a MAG to pass filtering. |

!!! note "Quality Thresholds"
    The default thresholds (`min_completeness: 90`, `max_contamination: 5`) correspond to "high-quality" MAGs according to MIMAG standards. Adjust these based on your analysis requirements.

### Functional Annotation Options

Parameters for gene prediction and functional annotation.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--metacerberus_db` | string | `null` | Path to a pre-downloaded MetaCerberus database. If not provided, the database will be downloaded automatically. |
| `--skip_metacerberus` | boolean | `false` | Skip MetaCerberus functional annotation. |
| `--annotation_db` | string | `ALL` | Which annotation databases to use in MetaCerberus. |

### SNV/VCF Options

Parameters for variant calling and VCF generation.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--min_coverage` | integer | `0` | Minimum read coverage for an SNV to be included in the VCF. |
| `--min_var_freq` | integer | `0` | Minimum variant frequency for an SNV to be included. |
| `--include_nonvariant` | boolean | `false` | Include positions without variants in the VCF output. |
| `--vcf_prefix` | string | `""` | Prefix string for VCF filenames. |
| `--vcf_suffix` | string | `""` | Suffix string for VCF filenames. |

### InStrain Options

Parameters for population genomics analysis with InStrain.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--skip_instrain_compare` | boolean | `false` | Skip InStrain compare step (comparison between samples). |

### Visualization Options

Parameters for the Shiny dashboard.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--shiny_timeout` | integer | `1500` | Timeout in seconds for the Shiny app. The app will automatically close after this duration. |
| `--skip_shiny` | boolean | `false` | Skip running the Shiny App. |

## Resource Configuration

PopMAG uses process labels to allocate resources. The default configuration is defined in `conf/base.config`:

### Process Labels

| Label | CPUs | Memory | Time |
|-------|------|--------|------|
| `process_single` | 1 | 6 GB | 4 h |
| `process_low` | 2 | 12 GB | 4 h |
| `process_medium` | 6 | 36 GB | 8 h |
| `process_high` | 12 | 72 GB | 16 h |
| `process_long` | - | - | 20 h |
| `process_high_memory` | - | 200 GB | - |

!!! info "Dynamic Resources"
    Resources scale with retry attempts. For example, `process_low` uses `12 GB * task.attempt`, so a first retry would use 24 GB.

### Custom Resource Configuration

Create a custom config file to override default resources:

```groovy
// custom.config
process {
    withLabel: process_high {
        cpus = 24
        memory = 128.GB
        time = 24.h
    }
    
    withName: INSTRAIN_PROFILE {
        cpus = 16
        memory = 96.GB
    }
}
```

Run with your custom config:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    -c custom.config \
    ...
```

### Special Profiles

| Profile | Description |
|---------|-------------|
| `debug` | Enable debug output and hash dumping |

### Combining Profiles

Profiles can be combined with commas:

```bash
# Docker on Apple Silicon
nextflow run daasabogalro/PopMAG -profile docker,debug ...

# Singularity with debug output
nextflow run daasabogalro/PopMAG -profile singularity,debug ...
```

## Using Parameters Files

For complex configurations, use a YAML parameters file:

```yaml
# params.yml

# Input/Output
mag_paths: "/data/project/mags.tsv"
reads_paths: "/data/project/reads.tsv"
metadata_file: "/data/project/metadata.csv"
outdir: "/data/project/results"

# Quality Control
min_completeness: 90
max_contamination: 5
checkm2_db: "/databases/checkm2"

# Functional Annotation
metacerberus_db: "/databases/metacerberus"
skip_metacerberus: false

# SNV Options
min_coverage: 5
min_var_freq: 0.05

# Visualization
shiny_timeout: 3600
```

Run with:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    -params-file params.yml
```

### Check Process Resources

You cand add to your run command to see resource usage:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    -with-report report.html \
    -with-trace trace.txt \
    -with-timeline timeline.html \
    ...
```

These reports are automatically generated in `results/pipeline_info/` by default.
