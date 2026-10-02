# Catostomidae JSDM Exploration

Exploratory single- and joint species distribution models (SSDM/JSDM) for North American suckers (Catostomidae) at reference river and stream sites, built with `Hmsc` following Chapters 5–7 of *Joint Species Distribution Modelling with Applications in R* by Ovaskainen and Abrego.

## Questions

1. **Ch. 5:** How do watershed temperature and erodibility affect the distribution of white suckers (*Catostomus commersonii*) across the northeastern United States?
2. **Ch. 6:** How do temperature and species traits (maximum temperature tolerance), with phylogenetic structure, shape the distribution of suckers?
3. **Ch. 7:** How do temperature, erodibility and aggregated ecoregion affect sucker distributions, and what raw and residual associations exist between species?

## Methods

- **Data preparation** (`tidyverse`, `rfishbase`): NRSA fish counts, habitat, landscape, site and water-chemistry tables are joined for first visits to sites in the NAP and SAP aggregated ecoregions; sites are classed as reference or non-reference from chemistry and habitat thresholds, randomly split 70/30 into construction (C) and validation (V) sets, subset to Catostomidae and converted to presence/absence
- **Phylogeny** (`ape`, `DECIPHER`, `seqinr`): mitochondrial sequences pulled from GenBank (`read.GenBank`), aligned (`AlignSeqs`), converted to a similarity distance matrix and built into a neighbour-joining tree (`nj`), then pruned to species with trait data (`mito_tree.nex`, 13 species)
- **Traits**: maximum total length, temperature tolerance, substrate and current preferences from the FishTraits database (`trait_subset.csv`)
- **Ch. 5 SSDM** (`Hmsc`): probit model of white sucker occurrence with `~ temp + erosion` and a spatial random effect on site coordinates; preceded by exploratory logistic GLMs
- **Ch. 6 JSDM**: probit model with `~ temp`, trait formula `~ temptol` (maximum temperature), the phylogeny (`phyloTree`) and a site-level random effect; trait–environment effects examined with Gamma parameters (`plotGamma`)
- **Ch. 7 JSDM**: probit model of all 28 sucker species with `~ aggecoreg + temp + erosion` and a site-level random effect, plus an intercept-only model; variance partitioning, Beta support plots, species associations (`computeAssociations`, `corrplot`) and biplots
- **Model fitting and checks**: `sampleMcmc` with 2 chains; convergence assessed with trace plots, effective sample size and Gelman diagnostics; explanatory power from AUC and Tjur R² (`evaluateModelFit`)
- **Validation**: predictions for validation sites; accuracy at a 0.5 threshold (Ch. 5) and observed/expected (O/E) species richness (Ch. 6–7)

## Data

- **Community and environment**: 2018–2019 National Rivers and Streams Assessment (NRSA), US EPA. Reference-site criteria follow the NRSA 2018–19 technical support document (USEPA 2024); environmental variables unaffected by stress follow Meador & Carlisle (2009). Steps 1–5 of the data preparation script are by K. Voss.
- **Traits**: FishTraits database (Frimpong and Angermeier 2008), via ScienceBase.
- **Phylogeny**: recreated from the GenBank accessions in the mitochondrial dataset of Yang, Mayden & Naylor (2024).

## Repository contents

| File | Description |
|---|---|
| `Data_Preparation/Data_Prep_Code.R` | Builds the sucker community, environment and reference tables, the trait subset and the phylogeny |
| `Data_Preparation/NRSA1819_*.csv` | Raw NRSA 2018–2019 fish count, habitat, landscape, site and water-chemistry data |
| `Data_Preparation/FishTraits_14.3.csv` | Raw FishTraits database |
| `Data_Preparation/Table S1*.xlsx`, `Table S2*.xlsx` | Mitochondrial and nuclear sample tables from Yang, Mayden & Naylor (2024) |
| `Data_Preparation/mito_sequences`, `mito_aligned.fasta`, `mitochondrial_data.csv` | GenBank sequences, alignment and mitochondrial sample data |
| `Data_Preparation/*_subset.csv` | Intermediate fish count, environmental and reference tables |
| `Data_Preparation/com_sci_names.csv` | Common-to-scientific name lookup |
| `Data_Preparation/Data_Prep.RData` | Saved data preparation workspace |
| `suckersabundancedata.csv`, `suckerspresencedata.csv` | Analysis datasets (abundance and presence/absence) for reference sites |
| `trait_subset.csv`, `mito_tree.nex` | Trait subset and pruned phylogeny |
| `Chapter_5_White_Suckers_SSDM.R` / `.Rmd` / `.html` | Ch. 5 white sucker SSDM: script, report source and rendered report |
| `Chapter_6_Suckers_Niche_JSDM.R` / `.Rmd` / `.html` | Ch. 6 traits and phylogeny JSDM |
| `Chapter_7_Suckers_Biotic_JDSM.R`, `Chapter_7_Suckers_Biotic_JSDM.Rmd` / `.html` | Ch. 7 biotic interactions JSDM |
| `Chapter_*_Workspace.RData` | Saved model workspaces loaded by each chapter's `.Rmd` |

## Reproducing the analysis

Open `catostomidae_JDSM_exploration.Rproj` in RStudio and run `Data_Preparation/Data_Prep_Code.R`, then the Chapter 5, 6 and 7 scripts in order. The `.R` scripts fit the models; the `.Rmd` files knit the reports from the saved workspaces. Paths in `setwd()` and `load()` are hard-coded to `~/R/catostomidae_JDSM_exploration/` and may need editing.

```r
install.packages(c("tidyverse", "rfishbase", "readxl", "ape", "seqinr", "Hmsc",
                   "mapview", "abind", "ggspatial", "glmmTMB", "corrplot"))
BiocManager::install("DECIPHER")
```

## References

The PDF of this paper is kept locally but not tracked in this repository.

- Yang, L., Mayden, R. L., & Naylor, G. J. P. (2024). Phylogeny and polyploidy evolution of the suckers (Teleostei: Catostomidae). *Biology*, 13(12), 1072. https://doi.org/10.3390/biology13121072

## Author

**Kurt Riggin**: [GitHub](https://github.com/kriggithub) · [ORCID](https://orcid.org/0009-0004-4700-1251)
