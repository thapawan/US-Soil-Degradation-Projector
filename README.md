# US-Soil-Degradation-Projector

**Projecting Soil Degradation Vulnerability Across the Contiguous United States under Changing Climate and Land Use (2071–2100)**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![R 4.2+](https://img.shields.io/badge/R-4.2+-blue.svg)](https://www.r-project.org/)
[![DOI](https://img.shields.io/badge/DOI-10.5281/zenodo.XXXXXX-blue)](https://doi.org/10.5281/zenodo.XXXXXX)
[![Paper](https://img.shields.io/badge/Paper-Communications_Sustainability-red)](https://doi.org/10.1038/s44458-026-XXXX-X)

---

## 📖 Overview

This repository contains the complete code, data processing pipelines, model workflows, and visualization scripts for the study:

> **"Soil degradation vulnerability across the contiguous United States is projected to intensify under future climate and land-use change"**  
> *[Your Name], [Co-authors]*  
> *Communications Sustainability (2026)*

We use a **Soil Degradation Proxy (SDP)** integrating four key indicators:
- **Soil Organic Carbon (SOC)**
- **Water Erosion Rate (t/ha/yr)**
- **Electrical Conductivity (EC, dS/m)**
- **Soil pH**

A **Random Forest** machine learning model is trained on historical data (1985–2014) and applied to future scenarios (2031–2060, 2071–2100) under two Shared Socioeconomic Pathways (**SSP2-4.5** and **SSP5-8.5**) using an ensemble of 18 CMIP6 climate models and spatially explicit land-use projections from **LUCAS LUC North America**.

---

## 📂 Repository Structure

```
US-Soil-Degradation-Projector/
│
├── data/                           # Data directory (not synced; see Zenodo)
│   ├── raw/                        # Original data (soil, climate, land use)
│   ├── processed/                  # Cleaned and harmonized datasets
│   └── outputs/                    # Model predictions and SDP maps
│
├── src/                            # Source code
│   ├── 01_data_acquisition/        # Scripts to download CMIP6, NLCD, soil data
│   ├── 02_data_harmonization/      # Harmonize soil profiles to CONUS grid
│   ├── 03_sdp_calculation/         # Compute SDP from raw indicators
│   ├── 04_model_training/          # Random Forest training & validation
│   ├── 05_projection/              # Future SDP projections (2071-2100)
│   ├── 06_driver_decomposition/    # Climate vs. land-use attribution
│   ├── 07_visualization/           # Maps, figures, uncertainty plots
│   └── utils/                      # Common functions (R/Python)
│
├── notebooks/                      # Jupyter/RMarkdown exploratory analysis
│   ├── 01_EDA_soil_data.ipynb
│   ├── 02_sdp_spatial_patterns.Rmd
│   └── 03_uncertainty_analysis.ipynb
│
├── results/                        # Final figures, tables, supplementary
│   ├── figures/                    # Fig 1-6 (main text)
│   ├── tables/                     # Summary statistics
│   └── supplementary/              # Extended data figures
│
├── docs/                           # Documentation
│   ├── methodology.md              # Detailed methods
│   ├── data_sources.md             # All data sources with DOIs
│   └── reproducibility_checklist.md
│
├── environment/                    # Environment configurations
│   ├── environment_R.yml           # Conda env for R
│   ├── environment_python.yml      # Conda env for Python
│   └── Dockerfile                  # Full reproducible container
│
├── .gitignore
├── LICENSE
├── README.md                       # This file
├── CITATION.cff                    # Citation metadata
└── Makefile                        # Automated workflow runner
```

---

## 🚀 Getting Started

### Prerequisites

- **R** (≥ 4.2) with `randomForest`, `caret`, `sf`, `raster`, `tidyverse`
- **Python** (≥ 3.10) with `xarray`, `netCDF4`, `rioxarray`, `geopandas`, `scikit-learn`
- **Docker** (optional, for full containerized reproducibility)
- **GNU Make** (for workflow automation)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/US-Soil-Degradation-Projector.git
cd US-Soil-Degradation-Projector

# Create and activate the R environment (Conda)
conda env create -f environment/environment_R.yml
conda activate soil-deg-us

# Or use Docker
docker build -t soil-deg-us .
docker run -it -v $(pwd):/workspace soil-deg-us
```

### Download Required Data

Due to size constraints, raw and processed data are hosted on Zenodo. Download automatically via:

```bash
make data
```

Or manually from: [https://doi.org/10.5281/zenodo.XXXXXX](https://doi.org/10.5281/zenodo.XXXXXX)

---

## 🔄 Workflow

Run the entire pipeline from data to figures:

```bash
make all
```

Or step-by-step:

| Command | Description |
| :--- | :--- |
| `make data` | Download and verify data integrity |
| `make harmonize` | Harmonize soil, climate, land-use to common grid |
| `make sdp` | Calculate historical SDP (1985–2014) |
| `make train` | Train Random Forest models (18 climate models) |
| `make project` | Project future SDP (near, far; SSP2-4.5, SSP5-8.5) |
| `make drivers` | Climate vs. land-use driver decomposition |
| `make figures` | Generate all main and supplemental figures |
| `make validate` | Run cross-validation and uncertainty assessment |
| `make clean` | Remove intermediate files |

---

## 📊 Key Outputs

| Output | Description | Location |
| :--- | :--- | :--- |
| `sdp_historical.tif` | Historical SDP (0–1) at 1 km resolution | `data/outputs/` |
| `sdp_future_ssp585_2071.tif` | Future SDP under SSP5-8.5 (2071–2100) | `data/outputs/` |
| `sdp_change_map.png` | Figure 1: Spatial change map | `results/figures/` |
| `driver_attribution.gpkg` | Dominant driver per site (climate/land-use/both) | `data/outputs/` |
| `uncertainty_std.tif` | Ensemble standard deviation | `data/outputs/` |

---

## 🧪 Reproducibility

This repository adheres to best practices for computational reproducibility:

- **Containerized environment:** Docker + Conda ensures identical software versions.
- **Random seeds:** Fixed seeds for all stochastic processes (`set.seed(2026)`).
- **Data DOIs:** All external data referenced with persistent identifiers.
- **Scripted end-to-end pipeline:** One command (`make all`) rebuilds all results.
- **Validation metrics:** Reported R², MSE, and uncertainty bounds for each model.

To verify reproducibility:

```bash
make validate
```

Expected output:

```
RF Model Performance (18-model ensemble):
  Median R²: 0.58 (IQR: 0.55–0.61)
  Median MSE: 0.009 (IQR: 0.008–0.010)
  Spatial CV RMSE: 0.12
```

---

## 🗺️ Data Sources

| Data Type | Source | DOI / Link |
| :--- | :--- | :--- |
| Soil properties | USDA-NCSS, RaCA | [link] |
| Soil texture | SoilGrids 2.0 (CONUS extract) | 10.5194/soil-7-217-2021 |
| Land use/cover (historical) | USGS NLCD 2019 | 10.5066/P9KZCM54 |
| Land use (future) | LUCAS LUC North America v1.1 | 10.5281/zenodo.XXXXXXXX |
| Climate (historical) | CMIP6 — historical (1985–2014) | 10.5194/gmd-9-1937-2016 |
| Climate (future) | CMIP6 — ScenarioMIP (SSP2-4.5, SSP5-8.5) | 10.5194/esd-12-253-2021 |
| Topography | USGS 3DEP (1 arc-second) | 10.5066/F7PR7TFF |

---

## 📝 Citation

If you use this code or data in your research, please cite:

```bibtex
@article{YourName2026_US_SoilDegradation,
  title     = {Soil degradation vulnerability across the contiguous United States is projected to intensify under future climate and land-use change},
  author    = {Your Name and Coauthor One and Coauthor Two},
  journal   = {Communications Sustainability},
  volume    = {X},
  pages     = {XXX–XXX},
  year      = {2026},
  doi       = {10.1038/s44458-026-XXXX-X},
  note      = {Code and data available at https://github.com/yourusername/US-Soil-Degradation-Projector}
}
```

**Software citation:**

```bibtex
@software{YourName2026_Code,
  author    = {Your Name and Coauthor One},
  title     = {US-Soil-Degradation-Projector: v1.0},
  year      = {2026},
  doi       = {10.5281/zenodo.XXXXXX},
  url       = {https://github.com/yourusername/US-Soil-Degradation-Projector},
  version   = {1.0}
}
```

---

## 🤝 Contributing

We welcome contributions! Please:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/amazing-improvement`).
3. Run `make validate` to ensure nothing breaks.
4. Submit a pull request.

For major changes, please open an issue first to discuss.

---

## 📧 Contact

Lead corresponding author: **Your Name** — `your.email@university.edu`

For code-specific questions, please open a GitHub Issue.

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

The underlying data remain subject to their original licenses (USDA, USGS, CMIP6).

---

## 🙏 Acknowledgments

- European study by Afshar et al. (2026) for the SDP framework
- USDA Natural Resources Conservation Service (NRCS)
- USGS Land Cover Program
- CMIP6 climate modeling groups
- LUCAS LUC team for North America land-use projections

---

## ⚠️ Disclaimer

This research is provided "as is" without warranty of any kind. The views expressed are those of the authors and do not necessarily reflect the views of any funding agency or institution.

---

