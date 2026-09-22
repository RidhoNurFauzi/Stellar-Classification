# 🌌 Klasifikasi Objek Langit: Bintang, Galaksi, dan Kuasar (SDSS)

Proyek klasifikasi *Machine Learning* untuk mengelompokkan objek langit secara otomatis berdasarkan data spektral dari **Sloan Digital Sky Survey (SDSS)**.

---

## 📊 Ringkasan Eksekutif

| Parameter | Keterangan |
|---|---|
| **Dataset** | 100.000 observasi, 17 fitur prediktor, SDSS Data Release 17 |
| **Target Kelas** | 3 Kelas: `GALAXY` (59,4%), `STAR` (21,6%), `QSO` (19,0%) |
| **Model Terbaik** | **Random Forest (Tuned)** |
| **Akurasi Uji (Accuracy)** | **97,96%** |
| **F1-Score Macro** | **97,61%** |
| **ROC-AUC (Macro)** | **0,9958** |

---

## 🔍 Highlight Utama & Temuan Analisis

* **Data Cleaning Berbasis Domain Astronomi**:
  Memisahkan nilai `redshift` kecil ($z < 0,01$) yang terbukti **valid** sebagai karakteristik fisik Bintang (`STAR`) dari outlier instrumen asli (`-9999`) pada fotometri yang dieliminasi.
* **Seleksi Fitur Terarah**:
  Mengeliminasi 11 fitur metadata administratif dan hanya mempertahankan **6 fitur spektral utama** (`u`, `g`, `r`, `i`, `z`, dan `redshift`) untuk mencegah *overfitting*.
* **Error Analysis Kuantitatif Kuisar (`QSO` → `GALAXY`)**:
  * Evaluasi terhadap 269 sampel kesalahan mengungkapkan bahwa kesalahan terpusat pada *low-redshift* ($z \approx 0,60$) dan profil warna intermediat (*reddening*).
  * Distribusi $P(\text{GALAXY})$ menunjukkan rata-rata **0,814** (median **0,851**), dengan **39,4% sampel** salah diklasifikasikan dengan keyakinan ekstrem ($P > 0,90$). Kesalahan terjadi bukan karena keraguan model di batas perbatasan ($P \approx 0,50$), melainkan karena sinyal fotometri sampel memang menyerupai galaksi.
* **Pengujian Risiko Ketergantungan Fitur (`redshift`)**:
  Tanpa data `redshift` (fotometri murni), akurasi model berada di angka **87,00%** (*F1-macro* **0,84**). Hal ini mengonfirmasi bahwa model berbasis fotometri murni tetap solid dan layak difungsikan sebagai penyaring awal (*photometric screening*) sebelum observasi spektroskopi.

---

## 📈 Perbandingan Performa Model

| Model | Accuracy | F1-Macro | ROC-AUC (Macro) | Waktu Latih |
|---|---|---|---|---|
| **Baseline (DummyClassifier)** | 0,6000 | 0,2500 | - | - |
| **Logistic Regression** | 0,9578 | 0,9514 | 0,9879 | **0,73 detik** |
| **XGBoost** | 0,9761 | 0,9725 | 0,9955 | **1,29 detik** |
| **Random Forest (Default)** | 0,9795 | 0,9760 | 0,9955 | 43,20 detik |
| **Random Forest (Tuned)** 🏆 | **0,9796** | **0,9761** | **0,9958** | 84,07 detik |

> **Catatan Produksi:** **XGBoost** direkomendasikan sebagai alternatif utama deployment jika membutuhkan *retraining* berkala, karena memberikan performa yang hampir setara dengan waktu pelatihan **65x lebih cepat** dibanding Random Forest (Tuned).

---

## 🖼️ Visualisasi Utama

<!-- [TAMBAHKAN GRAFIK 1 DI SINI] -->
<!-- Contoh: ![Confusion Matrix](images/confusion_matrix.png) -->
* **Confusion Matrix & Feature Importance**: Menunjukkan tingkat presisi klasifikasi per kelas dan dominasi kontribusi variabel `redshift`.

<!-- [TAMBAHKAN GRAFIK 2 DI SINI] -->
<!-- Contoh: ![Color Index Boxplot](images/color_index_boxplot.png) -->
* **Analisis Color Index**: Menampilkan tumpang tindih (*overlap*) sebaran warna antara `QSO salah`, `QSO benar`, dan `GALAXY`.

---

## 🛠️ Tech Stack

* **Bahasa**: Python
* **Pengolahan Data**: `pandas`, `numpy`
* **Machine Learning**: `scikit-learn`, `xgboost`
* **Visualisasi Data**: `matplotlib`, `seaborn`
* **Model Persistence**: `joblib`

---

## 📁 Struktur Notebook / Proyek

```text
.
├── data/
│   └── star_classification.csv       # Dataset SDSS DR17
├── models/
│   ├── model_random_forest_final.joblib
│   ├── preprocessor.joblib
│   └── label_encoder.joblib
├── images/                           # Tempat menyimpan grafik visualisasi
├── klasifikasi_sdss.ipynb            # Jupyter Notebook utama
└── README.md