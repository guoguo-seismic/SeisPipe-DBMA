# SeisPipe-DBMA

**Seismic Processing Pipeline with DBMANet, GaMMA & ADLoc**  
A modular workflow for automatic phase picking, association, and location of microseismic and local earthquakes.

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📌 Overview

**SeisPipe-DBMA** is an end‑to‑end earthquake location pipeline designed for dense seismic networks. It integrates:

- **DBMANet** – A deep‑learning phase picker based on Dilated Convolutions and Bidirectional Mamba, achieving high sensitivity for P and S arrivals.
- **GaMMA** – A Gaussian Mixture Model‑based associator that robustly links picks to events.
- **ADLoc** – A travel‑time location routine using 2D Eikonal solvers and iterative station‑term corrections.

The pipeline is tailored for the **TexNet** dataset but can be adapted to other networks with minimal configuration.

---

## 🚀 Features

- ✅ **Automatic resampling** of continuous waveforms to a target sampling rate (e.g., 100 Hz).
- ✅ **DBMANet phase picking** with configurable thresholds for P and S waves.
- ✅ **Standardized pick format** for compatibility with GaMMA.
- ✅ **GaMMA association** with automatic DBSCAN-based event clustering and 1D velocity model support.
- ✅ **ADLoc precise location** with 2D Eikonal travel‑time calculation and iterative station‑term corrections.
- ✅ **Visualization tools** for epicenter maps, depth histograms, pick distribution, and catalog comparison.
- ✅ **Modular design** – each step can be run independently.

---

## 📦 Installation

### Clone the repository
```bash
git clone https://github.com/yourusername/SeisPipe-DBMA.git
cd SeisPipe-DBMA
```

### Set up a conda environment (recommended)
```bash
conda create -n seispipe python=3.10
conda activate seispipe
```

### Install dependencies
```bash
pip install -r requirements.txt
```

### Required packages
- `obspy`
- `numpy`, `pandas`, `scipy`
- `torch` (with CUDA if available)
- `mamba-ssm`
- `cartopy`, `matplotlib`
- `pyproj`
- `gamma` (GaMMA) – install from [https://github.com/AI4EPS/GaMMA](https://github.com/AI4EPS/GaMMA)
- `adloc` – install from [https://github.com/AI4EPS/ADLoc](https://github.com/AI4EPS/ADLoc)

> **Note**: `mamba-ssm` and `gamma` may require separate installation instructions. Please refer to their official repositories.

---

## 🧩 Pipeline Steps

The workflow is organized as a series of Jupyter notebooks or Python scripts:

1. **Waveform Preparation**  
   - Scan `.mseed` files, resample to target frequency (e.g., 100 Hz).
   - Group data by station and day.

2. **Phase Picking (DBMANet)**  
   - Load pre‑trained DBMANet model (e.g., trained on STEAD or custom dataset).
   - Slide a window over continuous waveforms, output P/S probability curves.
   - Extract picks above user‑defined thresholds.

3. **Pick Standardization**  
   - Convert DBMANet output to a standard format (`station_id`, `phase_time`, `phase_type`, `phase_score`).

4. **Association (GaMMA)**  
   - Use GaMMA to associate picks into events.
   - Requires station geometry and a 1D velocity model.
   - Outputs event catalog and associated picks.

5. **Precise Location (ADLoc)**  
   - Relocate events using the 2D Eikonal travel‑time solver.
   - Iteratively estimate station terms.
   - Produce final event locations with residuals.

6. **Visualization & Quality Control**  
   - Plot epicenter maps, depth histograms, pick distribution, and comparison with reference catalogs.

---

## 🚀 Quick Start (Example)

```python
# Run the entire pipeline from a single driver script (example)
from seispipe import (
    resample_waveforms,
    run_dbmanet_picking,
    standardize_picks,
    run_gamma_association,
    run_adloc_location
)

# 1. Resample
resample_waveforms(waveform_root="data/waveforms", target_fs=100.0)

# 2. Phase picking
picks_csv = run_dbmanet_picking(
    waveform_root="data/waveforms",
    model_weights="models/dbmanet_stead.pt",
    output_dir="output/picks",
    p_threshold=0.02,
    s_threshold=0.08
)

# 3. Standardize
standardize_picks(picks_csv, output_csv="output/picks_standardized.csv")

# 4. Association
events = run_gamma_association(
    picks_csv="output/picks_standardized.csv",
    station_csv="data/stations.csv",
    config="config.json"
)

# 5. Relocation
events_relocated = run_adloc_location(
    picks_csv="output/gamma_picks.csv",
    events_csv="output/gamma_events.csv",
    station_csv="data/stations.csv",
    config="config.json"
)
```

---

## 📁 Input Data Format

- **Waveforms**: MiniSEED files organized as `YEAR/DOY/NET.STA.LOC.CHAN.mseed`
- **Station file**: CSV with columns: `Network Code`, `Station Code`, `Longitude (WGS84)`, `Latitude (WGS84)`, `Elevation`
- **Event catalog (optional)**: CSV with `time`, `longitude`, `latitude`, `depth_km` for validation

---

## 📊 Outputs

| Step | Output Files |
|------|--------------|
| Resampling | Modified MiniSEED files (overwrites) |
| Phase picking | `DBMANet_picks.csv` |
| Standardization | `DBMANet_picks_standardized.csv` |
| Association | `gamma_events.csv`, `gamma_picks.csv` |
| ADLoc location | `adloc_events.csv`, `adloc_picks.csv`, `adloc_stations.csv` |
| Figures | `epicenter_map.png`, `depth_histogram.png`, `comparison.png`, etc. |

---

## 🧠 DBMANet Model

DBMANet is a hybrid network combining:
- **Dilated Convolutional Blocks** for multi‑scale feature extraction.
- **Bidirectional Mamba** for long‑range temporal dependency modeling.
- **Stochastic Depth** for regularization.

The model is trained on the **STEAD** dataset (or your own labeled data). Pre‑trained weights can be downloaded from the [Releases](https://github.com/yourusername/SeisPipe-DBMA/releases) page.

---

## 📖 Citation

If you use this pipeline in your research, please cite the following:

- **DBMANet**: *[Your DBMANet paper]* (if published)
- **GaMMA**: Zhu et al. (2022) *GaMMA: A Gaussian Mixture Model‑based Associator for Microseismic Monitoring*
- **ADLoc**: *[ADLoc reference]*

---

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request.

---

## 📄 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- TexNet for providing the waveform and station data.
- The developers of Obspy, PyTorch, Mamba, GaMMA, and ADLoc.

---

**Happy seismic processing!** 🌍🔍
