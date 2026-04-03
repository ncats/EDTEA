# DTSEA

Drug Target Set Enrichment Analysis workflows for high-throughput compound
screening data.

This repository contains two R Markdown workflows for identifying target-level
vulnerabilities from compound screening experiments:

- `PairedSampleAnalysis.Rmd` for studies with two phenotype groups
- `SingleSampleAnalysis.Rmd` for per-sample analysis without paired group
  comparisons

The repository also includes example Excel workbooks and `.gmt` drug-target
mapping libraries so the workflows can be run directly as templates.

## Repository Contents

- `PairedSampleAnalysis.Rmd`: paired-sample DTSEA workflow using multiple
  enrichment methods through `EnrichmentBrowser`
- `SingleSampleAnalysis.Rmd`: single-sample workflow using GSVA, Newman
  statistics, and GSEA
- `Example_Paired_Data.xlsx`: example input workbook for the paired workflow
- `Example_Single_Sample_Data.xlsx`: example input workbook for the
  single-sample workflow
- `MIPE_5_DSEA_Sets.gmt`: example compact target-mapping library
- `Pharos_DSEA_Sets.gmt`: larger target-mapping library

## Requirements

Use a recent R installation and install the packages required by the workflow
you want to run.

The single-sample workflow checks for these packages at runtime:

- `dplyr`
- `tidyr`
- `tibble`
- `readxl`
- `readr`
- `data.table`
- `openxlsx`
- `fgsea`
- `GSVA`
- `heatmaply`
- `NewmanOmics`

The paired workflow also depends on packages used directly in the notebook,
including:

- `EnrichmentBrowser`
- `dplyr`
- `readxl`
- `tidyr`
- `tibble`
- `xlsx`
- `ggplot2`
- `UpSetR`
- `gridExtra`

Example install command:

```r
install.packages(c(
  "dplyr", "tidyr", "tibble", "readxl", "readr", "data.table",
  "openxlsx", "ggplot2", "UpSetR", "gridExtra", "xlsx"
))

if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
}

BiocManager::install(c("EnrichmentBrowser", "fgsea", "GSVA"))
```

If `NewmanOmics` is not available from your standard package sources, install it
from the source used by your group before rendering the single-sample workflow.

## Input Format

Both workflows expect a multi-sheet Excel workbook.

### Paired workflow

`PairedSampleAnalysis.Rmd` expects:

- Sheet 1: metadata and run settings
- Sheet 2: abundance matrix

The metadata sheet defines:

- `Analysis_Name`
- `Root_Directory`
- `Transformations`
- `Mapping_Library`
- `Tests`
- a `Column` / `Type` layout identifying exactly one `Compound` column and the
  sample columns
- phenotype labels for each sample column

The abundance sheet should contain one row per compound and one column per
sample, with sample columns matching the metadata exactly.

### Single-sample workflow

`SingleSampleAnalysis.Rmd` expects:

- Sheet 1: metadata and run settings
- Sheet 2: long-form assay data
- optional sample metadata sheet

The metadata sheet defines:

- `Analysis_Name`
- `Root_Directory`
- `Sample_Name`
- `Compound_Name`
- `Abundance`
- `Phenotype`
- `Transformations`
- `Mapping_Library`

The assay data sheet should contain one row per compound-sample observation.

## Quick Start

1. Clone the repository.
2. Open the desired `.Rmd` file in RStudio or render it with `rmarkdown`.
3. Edit the `data_sheet` variable near the top of the notebook to point to your
   input workbook.
4. Make sure the workbook metadata points to a valid writable
   `Root_Directory` and a valid `.gmt` mapping library.
5. Render the notebook.

Example:

```r
rmarkdown::render("PairedSampleAnalysis.Rmd")
rmarkdown::render("SingleSampleAnalysis.Rmd")
```

The example workbooks in this repository can be used as templates for new
analyses.

## Outputs

Each workflow writes results to a timestamped output directory under the
user-specified `Root_Directory`.

### Paired workflow outputs

- `<Analysis_Name>_results.Rda`
- `<Analysis_Name>_results.xlsx`
- `<Analysis_Name>_all_pvals.csv`
- `<Analysis_Name>_pval_distributions.png`
- `<Analysis_Name>_upset.png` when significant sets are detected
- `<Analysis_Name>_significant_target-level_boxplots.pdf`

### Single-sample workflow outputs

- `<Analysis_Name>_GSVA_data.csv`
- `<Analysis_Name>_GSVA_results.csv`
- `<Analysis_Name>_GSEA_results.csv`

## Notes

- Both workflows stop if the target output directory already exists, which helps
  avoid overwriting prior analyses.
- The mapping libraries in `.gmt` format encode compound-to-target or
  target-set relationships used for enrichment testing.
- The paired workflow is intended for two-group comparisons; use the
  single-sample workflow when that assumption does not hold.

## License

This repository is released under the [MIT License](LICENSE).
