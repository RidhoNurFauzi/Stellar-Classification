# 🌌 Klasifikasi Bintang, Galaksi, dan Kuasar (SDSS)

Proyek klasifikasi machine learning untuk mengelompokkan objek langit berdasarkan data spektral dari Sloan Digital Sky Survey (SDSS).

## 📊 Ringkasan

| | |
|---|---|
| **Dataset** | 100.000 observasi, 17 fitur, SDSS |
| **Target** | 3 kelas: GALAXY, QSO (kuasar), STAR |
| **Model terbaik** | Random Forest (tuned) |
| **Accuracy** | 98% |
| **F1-macro** | 97% |

## 🔍 Highlight Analisis

- **Data cleaning berbasis pemahaman domain**: membedakan nilai `redshift` kecil yang *valid* (ciri bintang) dari nilai `-9999` yang benar-benar *error* pengukuran — bukan menghapus keduanya secara membabi buta.
- **Perbandingan 4 model** (baseline, Logistic Regression, Random Forest, XGBoost) dengan evaluasi menyeluruh: precision/recall/F1, cross-validation, ROC-AUC, PR-AUC.
- **Error analysis kuantitatif**: menelusuri penyebab kesalahan klasifikasi lewat 4 pendekatan berbeda (fitur mentah, color index, jarak ke centroid, predict_proba), dan merevisi kesimpulan setelah data diperluas dari sampel kecil ke seluruh populasi kesalahan.
- **Analisis risiko model**: menguji apa yang terjadi jika fitur paling penting (`redshift`) tidak tersedia — relevan untuk menilai kelayakan model di dunia nyata, bukan cuma di atas kertas.

## 🛠️ Tech Stack

`Python` · `pandas` · `scikit-learn` · `XGBoost` · `matplotlib` · `seaborn`

## 📁 Struktur Notebook

1. Problem Definition & Data Understanding
2. Exploratory Data Analysis
3. Data Cleaning
4. Feature Selection & Preprocessing
5. Baseline Model
6. Build Model (3 algoritma)
7. Evaluation
8. Penanganan Ketidakseimbangan Kelas
9. Hyperparameter Tuning
10. Overfitting Check
11. Error Analysis
12. Risiko Ketergantungan Fitur
13. Model Deployment (save/load)
14. Kesimpulan

## 🚀 Cara Menjalankan

```bash
pip install pandas scikit-learn xgboost matplotlib seaborn joblib
jupyter notebook klasifikasi_sdss.ipynb
```

## 📌 Dataset

Data bersumber dari [SDSS](https://www.sdss.org/) (Sloan Digital Sky Survey), tersedia publik di [Kaggle](https://www.kaggle.com/datasets/fedesoriano/stellar-classification-dataset-sdss17).

## 📄 Lisensi

MIT License — bebas dipakai untuk belajar atau dikembangkan lebih lanjut.