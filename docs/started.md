# Getting Started

This guide will help you install and run PopMAG for the first time.

## Prerequisites

### Nextflow

PopMAG requires [Nextflow](https://www.nextflow.io/) version **24.04.2** or later. Nextflow official installation instructions can be found in [https://www.nextflow.io/docs/latest/install.html](https://www.nextflow.io/docs/latest/install.html)

#### Self-Install

```bash
curl -s https://get.nextflow.io | bash
chmod +x nextflow
sudo mv nextflow /usr/local/bin/
```

#### Mamba/conda

```bash
mamba create --name nf-env bioconda::nextflow
source activate nf-env
```

After installation, verify with:

```bash
nextflow info
```

### Container Runtime

PopMAG uses containerized tools for reproducibility. You need **at least one** of the following:

| Runtime | Recommended For | Installation |
|---------|-----------------|--------------|
| **Docker** | Local machines, workstations | [docs.docker.com](https://docs.docker.com/get-docker/) |
| **Singularity/Apptainer** | HPC clusters | [apptainer.org](https://apptainer.org/docs/admin/main/installation.html) |
| **Conda/Mamba** | Environments without containers | [conda.io](https://docs.conda.io/en/latest/miniconda.html) |

!!! tip "HPC Users"
    Most HPC systems have Singularity/Apptainer pre-installed. Check with `singularity --version` or `apptainer --version`.

## Installation

PopMAG doesn't require explicit installation. Nextflow will automatically download the pipeline from GitHub on first run.

### Option 1: Run directly from GitHub (Recommended)

```bash
nextflow run daasabogalro/PopMAG --help
```

### Option 2: Clone the repository

```bash
git clone https://github.com/daasabogalro/PopMAG.git
cd PopMAG
nextflow run main.nf --help
```

## Execution Profiles

PopMAG supports multiple execution profiles for different environments:

| Profile | Description | Command |
|---------|-------------|---------|
| `docker` | Run with Docker containers | `-profile docker` |
| `singularity` | Run with Singularity containers | `-profile singularity` |
| `apptainer` | Run with Apptainer containers | `-profile apptainer` |

## Test Run

Before running on your own data, verify the installation with a minimal test:

```bash
# Create a test directory
mkdir popmag_test && cd popmag_test

# Run with Docker
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --help
```

This will display all available parameters, confirming the pipeline is correctly installed.

## First Run with Your Data

### 1. Prepare Input Files

You need three input files:

1. **MAG samplesheet** (`mag_paths.tsv`) - List of your MAG files
2. **Reads samplesheet** (`reads_paths.tsv`) - List of your paired-end reads
3. **Metadata file** (`metadata.csv`) - Sample metadata

See the [Usage](usage.md) page for detailed format specifications.

### 2. Run the Pipeline

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --mag_paths mag_paths.tsv \
    --reads_paths reads_paths.tsv \
    --metadata metadata.csv \
    --outdir results
```

### 3. Monitor Progress

Nextflow will display real-time progress in your terminal. You can also:

- Check the `results/pipeline_info/` folder for execution reports
- View `execution_timeline.html` for a visual timeline
- Check `execution_report.html` for detailed statistics

## Resuming Failed Runs

If a run fails or is interrupted, you can resume from where it left off:

```bash
nextflow run daasabogalro/PopMAG \
    -profile docker \
    --mag_paths mag_paths.tsv \
    --reads_paths reads_paths.tsv \
    --metadata metadata.csv \
    --outdir results \
    -resume
```

The `-resume` flag tells Nextflow to reuse cached results from completed processes.

## Resource Requirements

PopMAG can be resource-intensive depending on the number of MAGs and the number of samples in you dataset. 

!!! note "Resource Configuration"
    See the [Configuration](config.md) page for customizing resource allocations.

## Troubleshooting

### Common Issues

**Docker permission denied**
```bash
# Add your user to the docker group
sudo usermod -aG docker $USER
# Log out and back in
```

**Nextflow version too old**
```bash
# Update Nextflow
nextflow self-update
```

### Getting Help

- Check the [GitHub Issues](https://github.com/daasabogalro/PopMAG/issues) for known problems
- Open a new issue with your error message and `nextflow.log` file
- Include your Nextflow version (`nextflow -version`) and execution profile

## Next Steps

- [Usage](usage.md) - Learn how to prepare input files
- [Configuration](config.md) - Customize pipeline parameters
- [Visualization](visualization.md) - Explore your results
