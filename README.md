# DTSEA

Drug Target Set Enrichment Analysis workflows for high-throughput compound
screening data.

This repository contains two R Markdown workflows for identifying target-level
vulnerabilities from high-throughput compound screening experiments:

- `PairedSampleAnalysis.Rmd` for experiments with two phenotype groups to be tested for differential drug target-level sensitivities
- `SingleSampleAnalysis.Rmd` for per-sample sensitivity analysis, using all other samples as a reference background 

The markdown files use data and metadata stored in excel manifests, examples of which are present for both workflows. The repository also includes example `.gmt` drug-target
mapping libraries, although custom libraries are accommodated as well.

## Repository Contents

- `PairedSampleAnalysis.Rmd`: paired-sample DTSEA workflow using multiple
  enrichment methods through `EnrichmentBrowser`
- `SingleSampleAnalysis.Rmd`: single-sample workflow using GSVA and single sample GSEA
- `Example_Paired_Data.xlsx`: example input workbook for the paired workflow
- `Example_Single_Sample_Data.xlsx`: example input workbook for the
  single-sample workflow
- `MIPE_5_DSEA_Sets.gmt`: example compact target-mapping library from the NCATS MIPE 5.0 drug screening platform
- `Pharos_DSEA_Sets.gmt`: larger target-mapping library derived from the [Pharos druggable genome database](https://academic.oup.com/nar/article/51/D1/D1405/6851109)

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

## Input Format

Both workflows expect a multi-sheet Excel workbook.

### Paired workflow

`PairedSampleAnalysis.Rmd` expects:

- Sheet 1: metadata and run settings
- Sheet 2: Long-form assay data, typically abundance
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

- `<Analysis_Name>_results.Rda`: all testing outputs for convenient loading into another R session
- `<Analysis_Name>_results.xlsx`: full results across all run tests
- `<Analysis_Name>_all_pvals.csv`: Convenient dataframe for comparing pvalues across tests
- `<Analysis_Name>_pval_distributions.png`: Compare p value distributions by test
- `<Analysis_Name>_upset.png`: Significant set counts in common across tests
- `<Analysis_Name>_significant_target-level_boxplots.pdf`: Boxplots for all compounds mapping to significant targets

### Single-sample workflow outputs

- `<Analysis_Name>_GSVA_data.csv`: Results of GSVA transformation
- `<Analysis_Name>_GSVA_results.csv`: GSVA statistical outputs
- `<Analysis_Name>_GSEA_results.csv`: Single Sample GSEA statistical outputs

## Notes

- Both workflows stop if the target output directory already exists, which helps
  avoid overwriting prior analyses.
- The mapping libraries in `.gmt` format encode compound-to-target or
  target-set relationships used for enrichment testing.
- The paired workflow is intended for two-group comparisons; use the
  single-sample workflow when that assumption does not hold.

## License

This repository is released under the [GNU General Public License v2.0 only](LICENSE).
