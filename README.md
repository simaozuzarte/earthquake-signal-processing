# Seismic Data Processing and Analysis

**A Machine Learning pipeline for discriminating seismic sensor types using Digital Signal Processing.**

This project was developed within the scope of the Signal Processing curricular unit in the Master's of Data Science and Engineering. It focuses on the analysis of seismic waveform data to automatically distinguish between signals captured by different sensor types: **Broadband**, **Short-Period**, and **Accelerometer**.

## Project Overview

### Objective
To build an automated Machine Learning classification system capable of discriminating between three specific seismic sensor types based on their signal characteristics.

### The Challenge
Seismic networks employ various sensors, each with unique frequency responses and sensitivity ranges. Identifying the sensor type solely from the raw waveform is complex due to:
* **Data Imbalance:** Short-Period (EHZ) sensors are significantly rarer than Broadband (HHZ) or Accelerometers (HNZ).
* **Signal Overlap:** Different sensors recording the same event can look visually similar in the time domain.

### The Solution
We developed a pipeline that:
1.  **Balances the Dataset:** Collected data from 4 major Southern California earthquakes to ensure equal representation (50 samples per sensor type per event).
2.  **Processes Signals:** Uses `ObsPy` for detrending, tapering, and normalization.
3.  **Extracts Physics-Informed Features:** Goes beyond generic statistics by engineering domain-specific features like the **"1Hz Cliff Ratio"** to capture spectral fingerprints.
4.  **Classifies:** Uses a **Random Forest** model to predict sensor types with high accuracy.

---

## Dataset

The dataset consists of **600 raw seismic waveforms** obtained from the [Southern California Earthquake Data Center (SCEDC)](https://scedc.caltech.edu/).

* **Events Analyzed:**
    * Ridgecrest (2019-07-06, M7.1)
    * Ridgecrest (2019-07-04, M6.4)
    * Lone Pine (2020-06-24, M5.8)
    * La Habra (2014-03-29, M5.1)
* **Target Sensors (High-Sample-Rate 100Hz):**
    * **HHZ (Broadband):** Sensitive to low-frequency, long-period waves.
    * **EHZ (Short-Period):** Captures moderate-to-high frequencies; rejects low frequencies.
    * **HNZ (Accelerometer):** Detects strong, high-frequency motion.

---

## Methodology

### 1. Signal Processing
* **Time-Domain:** Normalization and Envelope computation to analyze energy evolution over time.
* **Frequency-Domain:** Fast Fourier Transform (FFT) to analyze spectral content (0.1 - 50 Hz).

### 2. Feature Engineering
We extracted a mix of statistical and spectral features. Crucially, visual analysis revealed a distinct spectral transition around 1 Hz, leading to the creation of **Physics-Informed Features**:
* **Spectral Centroid:** Center of mass of the spectrum.
* **Energy < 1Hz:** Distinguishes Broadband sensors.
* **Energy > 20Hz:** Distinguishes Accelerometers.
* **1Hz Cliff Ratio:** A custom feature measuring the sharp drop-off in energy below 1 Hz, specific to Short-Period sensors.

### 3. Classification
* **Model:** Random Forest Classifier.
* **Training:** 80% Training / 20% Testing split.

---

## Results

The final model achieved an **81% Overall Accuracy**.

| Metric | Performance | Observations |
| :--- | :--- | :--- |
| **Broadband** | **F1: 0.80** | Excellent detection due to distinct low-frequency energy. |
| **Accelerometer** | **F1: High** | Well-separated by high-frequency content (>20Hz). |
| **Short-Period** | **F1: 0.70** | Hardest to classify, but significantly improved by the "Cliff Ratio" feature. |

**Key Insight:** The **1Hz Cliff Ratio** emerged as the 2nd most important feature, proving that domain-specific feature extraction outperforms generic statistical summaries.

---

## Installation & Usage

### Prerequisites
The project requires Python and the following libraries:

```bash
pip install obspy pandas numpy matplotlib scipy sklearn seaborn
```

### Running the Project
1. Clone the repository.
2. Open the Jupyter Notebook ``PS_earthquakes2.ipynb``.
3. Run the cells sequentially.

Note: The data collection step connects to the SCEDC client and downloads waveforms dynamically. Ensure you have an active internet connection.

### Future Directions
* Streaming Data: Validate the model on continuous, real-time data streams rather than pre-cut event windows.
* Deep Learning: Investigate CNNs applied directly to spectrogram images to automate feature extraction.
* Edge Deployment: Deploy lightweight Random Forest models onto Raspberry Pi-based seismic nodes for decentralized network management.

### Authors
* [Carolina Dias]() 
* [Mariana Pereira](https://github.com/mfaria-p) 
* [Simão Bernardo](https://github.com/simaozuzarte) 
* [Sofia Fernandes]() 

> Repository originally published by [@mfaria-p](https://github.com/mfaria-p). Collaborative project developed as part of the Signal Processing curricular unit — Master's in Data Science and Engineering.
