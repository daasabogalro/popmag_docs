# Welcome to PopMAG Docs!

---

**Documentation**: <a href="https://popmag.readthedocs.io/en/latest/" target="_blank">https://popmag.readthedocs.io/en/latest/</a>

**Source Code**: <a href="https://github.com/daasabogalro/popmag" target="_blank">https://github.com/daasabogalro/popmag</a>

---

**PopMAG** is a pipeline that integrates genome-resolved metagenomics data with population genomics tools to analyze metagenome-assembled genomes (MAGs) and their population-level variations. The pipeline processes MAGs alongside paired-end sequencing short reads (or BAM files) to perform quality assessment, abundance profiling, variant calling, and population genomics analyses, ending in an interactive visualization dashboard built with shiny.

The pipeline is organized into five main phases:

*    MAG Quality control and preprocessing.
*    Microbial community profiling.
*    Abundance calculation and variant calling.
*    Population genomics and functional analysis.
*    Visualization and reporting. 

**PopMAG** can aid to understand both the functional potential and population dynamics of metagenome-assembled genomes, particularly in the context of comparative genomics and temporal or spatial studies.

!!! info
    This project is under active development.

## Quick start

First, you will need to prepare the samplesheets with your input data. You need to provide **PopMAG** with three files:

*   A MAGs samplesheet.
*   A reads (or BAM files) samplesheet.
*   A metadata file (optional).

You can build each one of them following the `preparing input data` section. 

??? note "About the command `nextflow run daasabogalro/popmag`"

    The command `nextflow run` access the repository `daasabogalro/popmag` from github, detects the `main.nf` file in it, and executes the pipeline a using <a href="https://www.docker.com" class="external-link" target="_blank">Docker</a>, which is the default execution profile.

    You can read more about it in the <a href="https://www.nextflow.io/docs/latest/sharing.html" target="_blank">Nextflow documentation</a>.


``` nextflow
nextflow run daasabogalro/popmag \
    -profile docker \
    --mag_paths mags_samplesheet.csv \
    --reads_paths reads_samplesheet.csv \
    --metadata metadata.csv \ 
    --outdir results
```

The most important command for this program to run is `nextflow run daasabogalro/popmag -profile docker`

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.
