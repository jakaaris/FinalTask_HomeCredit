# 📈 Prediksi Skor Kredit Home Credit



Proyek ini adalah studi kasus *end-to-end* *data science* untuk memprediksi risiko gagal bayar (default) pinjaman. Tujuannya adalah untuk membantu Home Credit membuat keputusan kredit yang lebih akurat, terutama bagi populasi *unbanked* (tanpa riwayat kredit bank).

> **❓ Pertanyaan Bisnis Utama:** Bagaimana kita bisa secara akurat menilai kelayakan kredit seseorang yang tidak memiliki riwayat kredit tradisional?

---

## 🎯 Masalah yang Diselesaikan

Tantangan utama dalam proyek ini adalah menyeimbangkan dua tujuan bisnis yang saling bertentangan:

1.  **Manajemen Risiko:** Meminimalkan kerugian finansial (NPL - Non-Performing Loans) dengan mengidentifikasi klien yang berisiko tinggi untuk gagal bayar.
2.  **Inklusi Keuangan:** Tidak salah menolak klien yang sebenarnya mampu bayar, yang merupakan target pasar utama Home Credit.

Solusinya adalah membangun model *machine learning* yang menghasilkan **skor probabilitas** gagal bayar, bukan keputusan "Ya/Tidak" yang kaku.

---

## 💾 Dataset yang Digunakan

Kita menggunakan dataset `application_train_1.csv` yang berisi beragam data pemohon, termasuk:
* **Data Demografis:** Usia (`DAYS_BIRTH`), Pendidikan, Status Pernikahan.
* **Data Finansial Klien:** Total Pendapatan (`AMT_INCOME_TOTAL`), Tipe Pendapatan.
* **Data Pinjaman:** Jumlah Pinjaman (`AMT_CREDIT`), Angsuran (`AMT_ANNUITY`).
* **Data Eksternal (Sangat Penting):** `EXT_SOURCE_1`, `EXT_SOURCE_2`, `EXT_SOURCE_3` (Skor dari biro kredit lain).

---

## 💡 Temuan Kunci & Rekomendasi Aksi

Eksplorasi data mengungkapkan beberapa wawasan yang dapat ditindaklanjuti, bahkan sebelum pemodelan:

### 1. 🔍 Insight: Data Eksternal adalah Kunci
* **Temuan:** Fitur `EXT_SOURCE_2` dan `EXT_SOURCE_3` adalah prediktor terkuat. Klien dengan skor eksternal rendah memiliki risiko gagal bayar yang *jauh* lebih tinggi.
* **Rekomendasi Aksi:**
    1.  **Prioritaskan Kualitas Data:** Pastikan integrasi data dengan penyedia eksternal ini selalu valid dan *up-to-date*.
    2.  **Mitigasi Risiko:** Untuk klien dengan skor `EXT_SOURCE` sangat rendah, pertimbangkan untuk menawarkan produk dengan plafon pinjaman lebih kecil atau DP yang lebih tinggi, alih-alih langsung ditolak.

### 2. ⏳ Insight: Stabilitas Hidup = Stabilitas Finansial
* **Temuan:** Klien yang **lebih muda** (`DAYS_BIRTH`) dan **baru bekerja** (`DAYS_EMPLOYED`) secara signifikan lebih berisiko.
* **Rekomendasi Aksi:**
    1.  **Marketing Tertarget:** Buat *campaign* yang menargetkan segmen yang lebih stabil (misal: "Pinjaman Karyawan Mapan" untuk yang telah bekerja > 3 tahun).
    2.  **Produk "Pemula":** Ciptakan produk pinjaman "First-Credit" dengan plafon rendah untuk segmen muda/baru bekerja, bantu mereka membangun riwayat kredit positif dengan risiko terkendali.



---

## 🛠️ Proses Pembuatan Model Final

Model final kami tidak dibuat begitu saja. Model ini adalah hasil dari alur kerja yang sistematis:

1.  **Data Cleaning:** Menangani nilai anomali (seperti `365243` di `DAYS_EMPLOYED`) dan menghapus kolom dengan *missing values* lebih dari 50%.
2.  **Imputasi:** Mengisi sisa *missing values* menggunakan **Median** (untuk angka) dan **Modus** (untuk kategori).
3.  **Encoding:** Mengubah fitur kategorikal (teks) menjadi angka menggunakan **One-Hot Encoding**.
4.  **Scaling:** Menerapkan **StandardScaler** pada semua fitur numerik. Ini sangat penting untuk performa Logistic Regression.
5.  **Penanganan Imbalance:** Tantangan terbesar data ini adalah **sangat tidak seimbang** (~8% default). Kami mengatasinya dengan menggunakan parameter `class_weight='balanced'` saat melatih model.

---

## 🏆 Performa Model Final

Kami menguji dua model: `Logistic Regression` (sesuai permintaan) dan `Random Forest`.

* **Model Pilihan:** **Logistic Regression**
* **Performa:** **AUC-ROC = 0.745** (74.5%)
* **Alasan:** Model ini tidak hanya memenuhi *requirement* tugas, tetapi juga terbukti memiliki **AUC-ROC tertinggi** (0.745 vs ~0.741 dari Random Forest). Dalam industri keuangan, model yang lebih sederhana dan mudah dijelaskan (*interpretable*) seperti Logistic Regression seringkali lebih disukai, dan dalam kasus ini, model tersebut juga yang paling akurat.



---

## 🚀 Rekomendasi Implementasi Bisnis

Output utama dari proyek ini adalah file `submission.csv` yang berisi skor probabilitas untuk setiap klien. Kami merekomendasikan implementasi **Sistem "Traffic Light"** untuk analis kredit:

* 🟢 **Risiko Rendah (Skor < 0.2):** **Auto-Approve** (Setujui Otomatis).
* 🟡 **Risiko Menengah (Skor 0.2 - 0.4):** **Manual Review** (Perlu tinjauan analis). Model dapat menandai fitur apa yang membuat klien ini berisiko (misal: `EXT_SOURCE` rendah).
* 🔴 **Risiko Tinggi (Skor > 0.4):** **Auto-Reject** (Tolak Otomatis).

Sistem ini mengotomatisasi keputusan yang jelas (risiko sangat rendah atau sangat tinggi) dan memfokuskan waktu berharga analis kredit pada kasus yang ambigu (risiko menengah).
