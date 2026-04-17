# Workflow and Operations

## 1) Cross-domain map

The repository is organized by analysis domain. Each domain usually includes:

- a domain landing page
- links to Galaxy workflow(s)
- links to shared Galaxy histories
- supporting diagrams and/or result artifacts

| Domain | Primary folder | Typical assets |
|---|---|---|
| Genomics | `genomics/` | multiple step-specific docs, deploy workflows, update scripts, result artifacts |
| Cheminformatics | `cheminformatics/` | docking/scoring workflow docs and deploy workflows |
| Proteomics | `proteomics/` | per-dataset reanalysis pages and workflow links |
| Direct RNAseq | `direct-rnaseq/` | preprocessing + epigenetics workflow docs |
| ARTIC | `artic/` | ARTIC protocol-specific workflow and required BED/amplicon files |
| Evolution | `evolution/` | Observable notebooks |
| Data | `data/`, `data-availability/` | mirrored data access and availability notebooks |

## 2) Genomics pipeline decomposition

Genomics is the deepest and most operationally active section.

```mermaid
flowchart TD
    A[1-PreProcessing] --> B[2-Assembly]
    A --> C[4-Variation]
    B --> D[3-MRCA]
    C --> E[5-Annotation]
    E --> F[6-RecombinationSelection]
    C --> G[updates]
```

Key workflow files (genomics deploy):

- `Genomics-1-PreProcessing_with_download.ga`
- `Genomics-1-PreProcessing_without_downloading_from_SRA.ga`
- `Genomics-2-Assembly.ga`
- `Genomics-3-MRCA.ga`
- `Genomics-4-Paired_End_Alignment.ga`
- `Genomics-4-Single_End_Alignment.ga`
- `Genomics-4-Variant_Calling_Lofreq.ga`
- `Genomics-4-all-in-one-subworkflow.ga`
- `Genomics-5-S-analysis.ga`
- `Genomics-6-RecombinationSelection.ga`

Note: both `Genomics-4-parallel-download.ga` and `Genomics-4-parallel-download.g` exist.

## 3) Scheduled update operation (variation)

A daily automation (`.github/workflows/fetch_accessions.yaml`) updates accessions and triggers variation workflows.

```mermaid
sequenceDiagram
    participant Cron as GitHub Schedule
    participant GA as GitHub Actions
    participant Scripts as genomics/4-Variation scripts
    participant GH as GitHub PR
    participant Galaxy as Galaxy instances

    Cron->>GA: daily trigger (00:00 cron)
    GA->>Scripts: run fetch_sra_acc.sh
    GA->>Scripts: run metadata_stats.ipynb via papermill
    Scripts-->>GA: updated accession/metadata files
    GA->>GH: create/update PR (branch accession_updates)
    GA->>Scripts: run covid_genome.py with today's Illumina accessions
    Scripts->>Galaxy: invoke workflows on org/eu/fr with API keys
```

Operational behavior:

- accession metadata is refreshed from ENA-based query logic (`fetch_sra_acc.sh`)
- newly seen accessions are appended to `accession_and_date.tsv`
- summary notebook in `genomics/4-Variation/updates/` is executed
- action opens/updates a PR
- variation workflows are invoked remotely through `bioblend` in `covid_genome.py`

## 4) `covid_genome.py` execution model

`genomics/4-Variation/covid_genome.py` is a CLI wrapper that:

1. Connects to a Galaxy instance (`bioblend.galaxy.GalaxyInstance`)
2. Ensures workflow availability (imports embedded `FULL_WORKFLOW` if needed)
3. Creates or reuses a target history
4. Uploads required inputs:
   - accessions file
   - NC_045512.2 FASTA
5. Invokes workflow using input names
6. Optionally monitors invocation

```mermaid
flowchart LR
    A[CLI args] --> B[Connect to Galaxy API]
    B --> C{Workflow exists?}
    C -- No --> D[Import embedded FULL_WORKFLOW]
    C -- Yes --> E[Reuse workflow]
    D --> F[Prepare history]
    E --> F
    F --> G[Upload input datasets]
    G --> H[Invoke workflow inputs_by=name]
    H --> I[Optional invocation monitor]
```

## 5) Cheminformatics operation model

Cheminformatics is structured as a compound-screening pipeline:

- ligand enumeration
- active-site preparation
- docking
- scoring (SuCOS and TransFS)
- filtering/selection
- all-in-one combined workflow

It has explicit deployment support via:

- `cheminformatics/deploy/all_covid_tools.yaml`
- `cheminformatics/deploy/workflows/*.ga`
- deployment instructions in `cheminformatics/deploy/README.md`

## 6) Other domain workflow patterns

- **Proteomics**: Each subfolder (`PXD*` / `mPXD*`) represents a study-specific rerun with linked Galaxy resources.
- **Direct RNAseq**: Two-stage pattern (preprocessing + methylation analysis).
- **ARTIC**: Amplicon-aware workflow variants and specific required primer/amplicon metadata files.
- **Evolution/Data availability**: Observable notebook embeds as interactive external analyses.

## 7) Reproducibility strategy in this repository

Reproducibility is achieved by publishing, for each analysis area:

- exact workflow links
- shared Galaxy histories
- static companion docs and figures
- deployable workflow files for self-hosted Galaxy instances
