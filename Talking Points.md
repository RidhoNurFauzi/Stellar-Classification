# Talking Points — Proyek Klasifikasi SDSS

## Kalau ditanya "Ceritakan proyek ini" (versi 60 detik)

"Saya membangun model klasifikasi untuk membedakan bintang, galaksi, dan kuasar dari data SDSS. Yang saya tekankan bukan cuma akurasi modelnya — 98% dengan Random Forest — tapi proses berpikirnya. Misalnya, saat awal EDA saya menemukan 22% data 'terlihat' error pada fitur redshift, tapi setelah saya telusuri per kelas, ternyata itu bukan error, itu karakteristik normal bintang. Kalau saya langsung hapus tanpa cek, saya kehilangan seperlima data secara tidak perlu."

## Kalau ditanya "Tantangan terbesar apa?"

"Menganalisis kenapa model salah mengklasifikasikan sebagian kuasar sebagai galaksi. Awalnya saya cek sampel kecil — 5 baris — dan kelihatannya model cuma 'ragu-ragu' di batas keputusan. Tapi setelah saya jalankan pada keseluruhan 282 baris yang salah, ternyata sepertiganya justru model yakin lebih dari 90% padahal salah. Itu pelajaran penting buat saya: jangan simpulkan pola dari sampel kecil, harus divalidasi ke seluruh data sebelum menulis kesimpulan."

## Kalau ditanya "Kenapa pilih Random Forest, bukan XGBoost yang lebih cepat?"

"Ini trade-off yang saya sadari betul, bukan pilihan asal. XGBoost 40 kali lebih cepat dilatih dengan performa hanya turun tipis. Saya tetap pilih Random Forest karena selisih train-test score-nya lebih kecil (generalisasi lebih baik), tapi saya dokumentasikan XGBoost sebagai alternatif kalau nanti kebutuhannya berubah ke arah efisiensi, misalnya kalau model harus di-retrain rutin dengan data yang terus bertambah."

## Kalau ditanya "Apa keterbatasan proyek ini?"

"Dua hal utama. Pertama, saya menemukan model ini sangat bergantung pada satu fitur, redshift — kalau fitur itu tidak tersedia, akurasi anjlok dari 98% ke 57%, nyaris tidak lebih baik dari tebakan acak. Itu saya catat sebagai risiko nyata, bukan cuma detail teknis, karena redshift itu mahal didapat secara astronomi. Kedua, saya belum menuntaskan akar penyebab kesalahan model sampai ke level fitur individual — itu perlu SHAP values yang belum sempat saya kerjakan, dan saya jujur tulis itu sebagai batasan, bukan saya tutup-tutupi."

## Kalau ditanya "Bagaimana kamu tahu model tidak overfitting?"

"Saya bandingkan skor di data latih vs data uji untuk semua model. Random Forest sempat mencapai skor sempurna 100% di data latih, yang jadi tanda peringatan overfitting. Tapi selisihnya dengan data uji cuma sekitar 2-3%, masih dalam batas wajar, jadi saya simpulkan modelnya cukup general, bukan sekadar menghafal."

## Angka-angka penting untuk diingat cepat

- Accuracy model terbaik: **98%**
- Baseline (tebakan naif): **60%**
- Penurunan akurasi tanpa `redshift`: dari 98% → **57%**
- Kesalahan `QSO`→`GALAXY`: **282 dari 3.810** (7,4%)
- Dari kesalahan itu, **33% terjadi dengan keyakinan model >90%**