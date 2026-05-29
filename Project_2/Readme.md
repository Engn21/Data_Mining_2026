# Analysis of Nigerian Internally Displaced Persons (IDP) Sites

**Author:** Engin Samet Dede

## Overview

This project applies unsupervised machine learning to identify vulnerable demographic profiles among IDP sites in northern Nigeria. The goal is to help humanitarian planners allocate resources more effectively by pinpointing which sites concentrate high-risk populations (elderly, infants) and where those sites are geographically located.

## Dataset

`20161019-sample-data.xlsx` — OCHA snapshot of Nigerian IDP sites (October 2016), containing age-group population counts (infants, children, youth, adults, elderly by sex) and geographic coordinates for each site.

## Methodology

| Step | Technique | Purpose |
|---|---|---|
| Data cleaning | Pandas | Fix data-entry errors, drop missing rows |
| Compositional transform | Centered Log-Ratio (CLR) | Remove size effect from age-group counts |
| Clustering | K-Means (k=2) | Group sites by demographic profile |
| Cluster selection | Silhouette score | Determine optimal number of clusters |
| Visualization | PCA biplot | Interpret which age groups drive cluster separation |
| Geographic analysis | Chi-square test + scatter map | Test whether vulnerable sites concentrate in specific states |

## Key Findings

- **Two clusters emerged:** a *Vulnerable (Elderly-Heavy)* cluster and a *Working-Age Adults* cluster.
- **PCA biplot** shows elderly arrows driving separation along PC1; youth/adult arrows point in the opposite direction.
- **Geographic concentration:** Vulnerable sites are disproportionately located in the northeast — YOBE (100%), TARABA (90.9%), and BORNO (53.6%) — suggesting aid for elderly and mobility-impaired populations should be concentrated in that region rather than spread nationally.

## Requirements

```bash
pip install pandas numpy matplotlib scikit-learn scipy openpyxl
```

## Usage

Open `Project_IDP_DEDE_ENGIN_SAMET.ipynb` in Jupyter or Google Colab and run cells sequentially. The data file `20161019-sample-data.xlsx` must be in the same directory (or mounted via Google Drive on Colab).

