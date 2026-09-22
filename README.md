# 🌌 Klasifikasi Objek Langit: Bintang, Galaksi, dan Kuasar (SDSS DR17)

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-1.0%2B-orange.svg)](https://scikit-learn.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-1.5%2B-red.svg)](https://xgboost.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Proyek klasifikasi *Machine Learning* end-to-end untuk mengelompokkan objek astronomi secara otomatis berdasarkan data fotometri dan spektroskopi dari **Sloan Digital Sky Survey (SDSS)** Data Release 17.

---

## 📊 Ringkasan Eksekutif

| Parameter | Detail Kinerja / Spesifikasi |
|---|---|
| **Dataset & Ukuran** | 100.000 observasi, 17 fitur prediktor, SDSS Data Release 17 |
| **Target Kelas** | 3 Kelas: `GALAXY` (59,4%), `STAR` (21,6%), `QSO` (19,0%) |
| **Model Terbaik** | **Random Forest (Hyperparameter Tuned)** |
| **Akurasi Uji (Accuracy)** | **97,96%** |
| **F1-Score Macro** | **97,61%** |
| **ROC-AUC Macro** | **0,9958** |
| **Model Alternatif (Deployment)** | **XGBoost** (Akurasi 97,61%, waktu latih 65x lebih cepat) |

---

## 📑 Problem Definition & Context Astronomi

### 1. Masalah & Tujuan Bisnis/Sains
Klasifikasi manual terhadap jutaan objek langit dari survei teleskopik membutuhkan waktu dan sumber daya komputasi/manusia yang sangat besar. Proyek ini bertujuan membangun jalur pipa *Machine Learning* otomatis untuk membedakan tiga jenis objek utama:
* **Bintang (*Star*)**: Objek lokal di galaksi Bimasakti dengan pergeseran merah (*redshift*) mendekati nol.
* **Galaksi (*Galaxy*)**: Sistem bintang dan gas berskala besar dengan nilai *redshift* moderat.
* **Kuasar (*Quasar/QSO*)**: Inti galaksi aktif (*Active Galactic Nuclei*) yang sangat jauh dan terang, dicirikan oleh nilai *redshift* tinggi.

### 2. Fitur & Eliminasi Metadata
Dari 17 fitur mentah, dilakukan **domain-specific feature selection**:
* **Fitur Utama Terpilih (6 Kolom)**:
  * **Fotometri (Pita Warna / Magnitudo)**: `u` (Ultraviolet), `g` (Green), `r` (Red), `i` (Near Infrared), `z` (Far Infrared).
  * **Spektroskopi**: `redshift` (Pergeseran merah akibat ekspansi alam semesta).
* **Fitur Tereliminasi (11 Kolom Metadata)**: `obj_ID`, `spec_obj_ID`, `alpha`, `delta`, `run_ID`, `rerun_ID`, `cam_col`, `field_ID`, `plate`, `MJD`, `fiber_ID` dieliminasi karena bersifat identifikasi instrumen/lokasi dan tidak merepresentasikan sifat fisik cahaya objek.

---

## 🖼️ Visualisasi Utama & Temuan Analisis

### 1. Distribusi Fitur & Analisis Hubungan (*EDA*)
| Histogram Fitur Utama | Scatter Plot Sebelum Cleaning | Scatter Plot Setelah Cleaning |
|---|---|---|
| ![Histogram](./images/Histogram%20setelah%20cleaning.png) | ![Scatter Before](./images/scatter%20plot%20sebelum%20cleaning.png) | ![Scatter After](./images/scatter%20plot%20setelah%20cleaning.png) |

* **Pembersihan Data**: Data cleaning berhasil mengisolasi *outlier* pengukuran ekstrem pada fotometri (seperti nilai `-9999`) tanpa menghapus sampel `redshift` kecil ($z < 0,01$) yang secara domain merupakan ciri khas objek Bintang (`STAR`).

---

### 2. Evaluasi Model Terbaik (Random Forest & XGBoost)
| Confusion Matrix (Random Forest) | Feature Importance (Random Forest) | ROC Curve (Random Forest) |
|---|---|---|
| ![CM RF](./images/Confusion%20Matrix%20Random%20Forest.png) | ![FI RF](./images/Feature%20Importance%20-%20Random%20Forest.png) | ![ROC RF](./images/ROC%20Curve%20-%20Random%20Forest.png) |

* **Kebijakan Keputusan Fitur**: `redshift` mendominasi tingkat kepatuhan model (>80%), disusul oleh responsibilitas pita fotometri `u`, `g`, `z`, `r`, dan `i`.
* **Metrik ROC-AUC**: Nilai $0,9958$ mengonfirmasi daya pisah yang hampir sempurna di seluruh rentang ambang batas *probability threshold*.

---

### 3. Error Analysis Kuisar (`QSO` Terprediksi `GALAXY`)
| Color Index QSO Salah vs QSO Benar vs GALAXY | Sebaran Fitur Warna |
|---|---|
| ![Color Index](./images/Perbandingan%20Color%20Index%20QSO%20Salah%20vs%20QSO%20Benar%20vs%20GALAXY.png) | ![Fitur Warna](./images/Perbandingan%20Sebaran%20Fitur%20Warna%20QSO%20Salah%20vs%20QSO%20Benar%20vs%20GALAXY.png) |

* **Akar Penyebab Misklasifikasi**: Evaluasi terhadap 269 sampel kesalahan mengonfirmasi bahwa `QSO` yang salah diprediksi sebagai `GALAXY` memiliki rentang *low-redshift* ($z \approx 0,60$) serta profil *color index* yang tumpang tindih (*overlap*) secara fisik dengan galaksi, sehingga model memprediksi dengan keyakinan tinggi ($P(\text{GALAXY}) > 0,85$).

---

## 📈 Perbandingan Kinerja Model

Evaluasi lengkap pada data uji (20.000 sampel) dari baseline hingga model ensemble:

| Model | Accuracy | Precision (Macro) | Recall (Macro) | F1-Macro | ROC-AUC (Macro) | Waktu Latih |
|---|---|---|---|---|---|---|
| **Baseline (DummyClassifier)** | 0,5900 | 0,2000 | 0,3333 | 0,2500 | - | - |
| **Logistic Regression** | 0,9578 | 0,9521 | 0,9507 | 0,9514 | 0,9879 | **0,73 dtk** |
| **XGBoost** | 0,9761 | 0,9712 | 0,9738 | 0,9725 | 0,9955 | **1,29 dtk** |
| **Random Forest (Default)** | 0,9795 | 0,9752 | 0,9768 | 0,9760 | 0,9955 | 43,20 dtk |
| **Random Forest (Tuned)** 🏆 | **0,9796** | **0,9753** | **0,9769** | **0,9761** | **0,9958** | 84,07 dtk |

> **Rekomendasi Produksi:** **XGBoost** direkomendasikan untuk arsitektur *real-time retraining* karena efisiensi waktu latih yang **65x lebih cepat** dengan penurunan performa yang sangat minimal (<0,35%).

---

## 💡 Kesimpulan & Rekomendasi Arsitektur (*Two-Stage Pipeline*)

1. **Efektifitas Model**: Model *machine learning* berhasil melompati performa baseline naif secara signifikan (F1-Macro $0,25 \rightarrow 0,9761$).
2. **Skema Arsitektur Murni vs Spektroskopi**:
   * **Stage 1 (Photometric Screening)**: Apabila data `redshift` belum tersedia, gunakan model berbasis fotometri murni (`u, g, r, i, z`) yang memiliki akurasi **87,00%** (*F1-macro* **0,84**) sebagai filter awal berbiaya komputasi rendah.
   * **Stage 2 (Spectroscopic Verification)**: Gunakan model utama **Random Forest Tuned** (akurasi **97,96%**) setelah pengamatan spektroskopi menghasilkan nilai `redshift`.

---

## 🛠️ Tech Stack & Alat
* **Bahasa**: Python 3.8+
* **Manipulasi & Analisis Data**: `pandas`, `numpy`
* **Pemodelan ML**: `scikit-learn`, `xgboost`
* **Visualisasi Data**: `matplotlib`, `seaborn`
* **Penyimpanan Artefak**: `joblib`

---

## 📁 Struktur Proyek & Artefak

```text
.
├── data/
│   └── star_classification.csv       # Dataset mentah SDSS DR17
├── models/
│   ├── model_random_forest_final.joblib
│   ├── preprocessor.joblib
│   └── label_encoder.joblib
├── images/                           # Hasil visualisasi & grafik proyek
│   ├── Confusion Matrix Logistic Regression.png
│   ├── Confusion Matrix Random Forest.png
│   ├── Confusion Matrix XGBoost.png
│   ├── Correlation Heatmap of Numerical Columns sebelum cleaning.png
│   ├── Correlation Heatmap of Numerical Columns setelah cleaning.png
│   ├── Feature Importance - Random Forest.png
│   ├── Feature Importance - XGBoost.png
│   ├── Histogram setelah cleaning.png
│   ├── Koefisien - Logistic Regression.png
│   ├── Perbandingan Color Index QSO Salah vs QSO Benar vs GALAXY.png
│   ├── Perbandingan Sebaran Fitur Warna QSO Salah vs QSO Benar vs GALAXY.png
│   ├── PR Curve - Logistic Regression.png
│   ├── PR Curve - Random Forest.png
│   ├── PR Curve - XGBoost.png
│   ├── ROC Curve - Logistic Regression.png
│   ├── ROC Curve - Random Forest.png
│   ├── ROC Curve - XGBoost.png
│   ├── scatter plot sebelum cleaning.png
│   └── scatter plot setelah cleaning.png
├── klasifikasi_sdss.ipynb            # Jupyter Notebook pengerjaan utama
└── README.md