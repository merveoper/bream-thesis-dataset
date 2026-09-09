# BREAM Thesis Dataset — Documentation

*Rotating-machinery vibration data for predictive-maintenance research.*
*Released as the dataset accompanying the doctoral thesis*
*M. Öper, "Design of a Digital Twin for Predictive Maintenance Applications in the Steel Industry", Kadir Has University, 2026.*

**DOI:** not yet assigned. <!-- fill in after the GitHub/Zenodo release -->
**Version:** <!-- fill in -->
**Licence:** <!-- fill in -->

---

## 1. Overview

The dataset contains RMS vibration-velocity recordings from a laboratory rotating-machinery rig, together with one industrial field recording and two derived signals used in the forecasting experiments of the thesis.

Two fault types are represented in the laboratory data, **rotating unbalance** and **parallel shaft misalignment**, each recorded at several severity levels, together with a separate normal-condition (no-fault) recording.

Every exported file is a CSV with a single column, `Value`, holding RMS vibration velocity in **mm/s** (the raw sensor output multiplied by 1000).

---

## 2. Laboratory recordings

### 2.1 Test rig and acquisition

- Single continuous shaft driven by an electric motor (Gamak AGM3EL 80 H 4b, 0.75 kW), supported by two bearings, with a disc mounted at the end of the shaft outside the second bearing.
- Speed set through an ABB ACS355 variable-frequency drive and held at **1000 rpm (16.67 Hz)** for all laboratory measurements.
- Sensor: IFM VSA001 (±25 g, 1–6000 Hz), mounted at the bearing nearest the fault. The same sensor type is used in the field deployment.
- Sampling interval **655 ms**, chosen from the acquisition unit's available settings (1310, 655, 328, 164 ms) as the shortest interval giving an acceptably clean signal.
- Nominal recording length **10 minutes** per file. From the file sizes in Section 3, individual files contain roughly 890–1115 points; the nominal 10 min / 655 ms gives about 916.

### 2.2 Fault conditions

- **Unbalance:** eccentric masses (bolt-and-nut assemblies, weighed before mounting) attached to the disc at 11.8, 22.6, 34.3, 45.8, 52.8, 62.4 and 73.9 g, plus a 0.0 g (no added mass) set.
- **Misalignment:** 0.4 mm shims inserted between motor and shaft, up to five shims, giving offsets of 0.0, 0.4, 0.8, 1.2, 1.6 and 2.0 mm.
- **Normal:** no unbalance mass and no shim.

### 2.3 Dataset structure

| Group | Condition | Levels | Files per level |
|---|---|---|---|
| Unbalance — statistical | rotating unbalance | 34.3 g | 50 |
| Unbalance — calibration | rotating unbalance | 0.0, 11.8, 22.6, 45.8, 52.8, 62.4, 73.9 g | 3 |
| Misalignment — statistical | parallel misalignment | 1.2, 2.0 mm | 50 |
| Misalignment — calibration | parallel misalignment | 0.0, 0.4, 0.8, 1.6 mm | 3 |
| Normal | no fault | — | 50 |

The 50-file *statistical* sets were recorded to estimate measurement variability (between- and within-recording components) and to support classification experiments. The 3-file *calibration* sets trace the severity–amplitude relation used to calibrate the physical model.

Two no-fault baselines are present: the **unbalance 0.0 g** calibration set (3 files, mean 1.820 mm/s) and the **Normal** statistical set (50 files, mean 2.356 mm/s).

---

## 3. Verified measurements

Means were computed directly from the exported files and are reported to three decimals in mm/s.

### 3.1 Unbalance (mass → RMS)

| Mass (g) | RMS mean (mm/s) | files | points |
|---:|---:|---:|---:|
| 0.0 | 1.820 | 3 | 2 970 |
| 11.8 | 2.809 | 3 | 2 871 |
| 22.6 | 5.716 | 3 | 2 853 |
| 34.3 | 10.864 | 50 | 48 654 |
| 45.8 | 13.497 | 3 | 2 991 |
| 52.8 | 15.947 | 3 | 2 901 |
| 62.4 | 19.222 | 3 | 2 895 |
| 73.9 | 25.306 | 3 | 2 892 |

RMS increases monotonically with mass across all eight levels.

### 3.2 Misalignment (offset → RMS)

| Offset (mm) | RMS mean (mm/s) | files | points |
|---:|---:|---:|---:|
| 0.0 | 4.014 | 3 | 2 898 |
| 0.4 | 6.100 | 3 | 3 346 |
| 0.8 | 6.800 | 3 | 2 825 |
| 1.2 | 8.299 | 50 | 47 853 |
| 1.6 | 8.800 | 3 | 2 673 |
| 2.0 | 11.666 | 50 | 46 268 |

RMS increases monotonically with offset across all six levels.

### 3.3 Normal baseline

| Condition | RMS mean (mm/s) | files | points |
|---|---:|---:|---:|
| Normal | 2.356 | 50 | 48 633 |

---

## 4. Signals used in the forecasting experiments

Three further signals accompany the laboratory recordings. They differ in origin and should not be treated as equivalent to the laboratory measurements.

| Name | Origin | Kind | Samples |
|---|---|---|---:|
| field | Borçelik operational recording | measured | 85 000 |
| demo | real laboratory segments arranged into a designed degradation path | semi-synthetic | 40 000 |
| twin | digital-twin pipeline output | generated | 65 000 |

All three are block-RMS velocity series in mm/s with the same `Value` column and use the ISO 10816-3 severity thresholds **2.8, 11.2 and 14 mm/s** for the stated equipment class. In the thesis each is split 80–20 in time into training and test partitions.

- **field** — Block-RMS series from an industrial fan at the FNDE measurement point, recorded with the same sensor type as the laboratory data. The fan operated at variable speed and no rotational-speed signal was recorded, so the RMS level reflects both degradation and speed variation. The record contains two degradation events of the same unbalance fault separated by a documented maintenance interval at samples 36 909–37 161, giving Event 1 = [0, 36 909) and Event 2 = [37 162, 85 000). The indicator peaks near 40 mm/s during Event 1. Event 2 was reserved as the held-out validation event in the thesis. Shared with the permission of Borçelik.
- **demo** — A designed fault progression constructed by arranging real laboratory-rig measurement segments into a degradation path. It serves as the "demonstration record", the controlled evaluation environment of the forecasting comparison.
- **twin** — Generated by the digital-twin pipeline for the unbalance mechanism: a designed ISO-band trajectory with the hierarchical (T1) noise model. The shared `Value` series is the noisy `rms_signal`, not the clean `rms_target`. Values range from 0.26 to 22.0 mm/s, within the calibrated severity range of the generator.

---

## 5. Repository layout

```
export_manifest.json          group-level inventory (paths, file counts, points, RMS means)
DATASET_README.md             this document
normal/                       normal_001.csv … normal_050.csv
unbalance_statistical/
    034p3g/                   unbalance_034p3g_001.csv … _050.csv
unbalance_calibration/
    000p0g/ 011p8g/ 022p6g/ 045p8g/ 052p8g/ 062p4g/ 073p9g/
                              unbalance_<level>_001.csv … _003.csv
misalignment_statistical/
    1p2mm/ 2p0mm/             misalignment_<level>_001.csv … _050.csv
misalignment_calibration/
    0p0mm/ 0p4mm/ 0p8mm/ 1p6mm/
                              misalignment_<level>_001.csv … _003.csv
derived/
    field_value.csv           85 000 samples
    demo_value.csv            40 000 samples
    twin_value.csv            65 000 samples
    provenance.json           origin and event indices of the three signals
```

Level codes use `p` for the decimal point: `034p3g` is 34.3 g, `1p2mm` is 1.2 mm. Unbalance levels are zero-padded to three integer digits. File indices within a level are zero-padded to three digits and carry no meaning beyond recording order.



---

## 6. Citation

Until a DOI is assigned, please cite the thesis:

> Öper, M. (2026). *Design of a Digital Twin for Predictive Maintenance Applications in the Steel Industry*. Ph.D. thesis, Kadir Has University, Istanbul.

<!-- Replace with the DOI citation after the Zenodo release. -->

---

## 7. Further information

The tables and notes above describe what the shared files contain and how they were recorded, which should be sufficient to load and interpret the data. Full methodological detail — the calibration procedure, the noise model, the experimental design, and the forecasting pipeline — is given in the thesis.
