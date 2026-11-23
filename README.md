# Pipeline Preprocessing Ulasan E-Commerce Berbahasa Indonesia

**Eksplorasi Data, Normalisasi Teks, dan Evaluasi Kualitas**

## Identitas Mahasiswa

- **Nama:** Oji Ferdian
- **NIM:** 24146110
- **Mata Kuliah:** Natural Language Processing (NLP)
- **Dataset:** [dipawidia/ecommerce-product-reviews-sentiment](https://huggingface.co/datasets/dipawidia/ecommerce-product-reviews-sentiment)

---

## Ringkasan Proyek

Proyek ini membangun pipeline preprocessing teks ulasan produk e-commerce berbahasa Indonesia. Berbeda dari sekadar menjalankan fungsi pembersihan, notebook ini juga mengukur **tahap mana yang paling berpengaruh**, **seberapa besar kosakata menyusut**, dan **informasi apa yang berpotensi hilang** setelah preprocessing.

Dataset berisi **11.606 ulasan** dengan distribusi seimbang: **5.803 negatif** dan **5.803 positif**. File dataset tidak diubah. Kolom `translate` hanya ditampilkan sebagai informasi dan tidak dipakai dalam analisis.

Alur kerja:

> Audit dataset -> eksplorasi label dan panjang -> rancangan normalisasi -> perbandingan konfigurasi -> audit kualitas -> contoh sebelum/sesudah -> evaluasi dampak -> diskusi

---

## Struktur Repositori

```text
.
├── preprocessing_ulasan_ecommerce.ipynb   # Notebook utama (sudah tereksekusi lengkap)
├── requirements.txt                       # Dependensi Python
├── .gitignore                             # File yang diabaikan Git
└── README.md                              # Dokumentasi proyek
```

---

## Isi Notebook

| Bagian | Pembahasan |
| :--- | :--- |
| 1. Audit dataset | Jumlah baris, kolom, nilai kosong, duplikat, panjang teks, kosakata awal, komposisi label, dan sebaran panjang ulasan |
| 2. Perancangan pipeline | Case folding, normalisasi, cleaning, tokenisasi, stopword removal, stemming, kamus slang 121 entri, dan uji tiga ulasan |
| 3. Perbandingan konfigurasi | Konfigurasi A (dasar), B (+stopword), C (+stemming), kata terbanyak, bigram, dan perubahan akibat stemming |
| 4. Audit kualitas | Ulasan kosong, ulasan <=2 token, duplikat setelah pembersihan, dan dampak per sentimen |
| 5. Contoh sebelum/sesudah | 10 ulasan stratifikasi (5 negatif, 5 positif) |
| 6. Dampak preprocessing | Perubahan karakter, token, kosakata, distribusi token, dan kurva pertumbuhan kosakata |
| 7. Analisis negasi | Apakah kata negasi tetap bertahan, beserta ringkasan angka kunci |
| 8-10. Diskusi dan penutup | Interpretasi hasil, keterbatasan, rekomendasi, dan kesimpulan |

---

## Keputusan Desain Preprocessing

Pipeline memakai lima tahap utama:

1. **Case folding** - mengubah seluruh teks menjadi huruf kecil.
2. **Normalisasi dan cleaning** - menyeragamkan slang dan pengulangan huruf, lalu menghapus URL, mention, hashtag, angka, tanda baca, dan emoji.
3. **Tokenisasi** - memecah teks menjadi token kata.
4. **Stopword removal** - menghapus kata umum dengan preservasi kata penting.
5. **Stemming** - mengubah kata berimbuhan menjadi bentuk dasar menggunakan Sastrawi.

Beberapa keputusan yang membedakan pipeline ini:

- **Kamus slang 121 entri**, misalnya `gak` -> `tidak`, `gabisa` -> `tidak bisa`, `bgt` -> `banget`, `ongkir` -> `ongkos kirim`.
- **Normalisasi pengulangan huruf**, misalnya `bagusss` -> `bagus`, `cepeeeet` -> `cepet`.
- **Preservasi 30 kata penting** dari daftar stopword Sastrawi: negasi, intensifier, dan konjungsi kontras. Dari 809 stopword Sastrawi, **787** yang benar-benar dihapus.
- **Stemming dengan cache** agar kata yang sama tidak di-stem berulang kali.

---

## Hasil Utama

### Perbandingan sebelum dan sesudah preprocessing

| Indikator | Sebelum | Sesudah | Perubahan |
| :--- | ---: | ---: | ---: |
| Rata-rata panjang karakter | 103,57 | 67,71 | -34,6% |
| Median panjang karakter | 78 | 53 | -32,1% |
| Rata-rata jumlah kata | 15,74 | 10,90 | -30,7% |
| Median jumlah kata | 12 | 8 | -33,3% |
| Kata terbanyak | 234 | 126 | -46,2% |
| Total token | 182.713 | 126.547 | -30,7% |
| Kosakata unik | 12.558 | 9.293 | -26,0% |
| Ulasan mengandung negasi | 4.020 | 4.242 | +5,5% |

### Perbandingan konfigurasi

| Konfigurasi | Rata-rata token | Median token | Kosakata unik | Ulasan kosong | Ulasan <=2 token |
| :--- | ---: | ---: | ---: | ---: | ---: |
| A. Dasar | 15,88 | 12 | 11.784 | 4 | 409 |
| B. +Stopword | 10,90 | 8 | 11.301 | 20 | 759 |
| C. +Stemming | 10,90 | 8 | 9.293 | 20 | 759 |

### Kualitas data setelah preprocessing

- **20 ulasan (0,17%)** menjadi kosong.
- **739 ulasan (6,37%)** tersisa 1-2 token.
- **759 ulasan (6,54%)** memiliki <=2 token.
- **250 teks bersih (2,15%)** menjadi duplikat meskipun sebelumnya berbeda.
- Dampak per sentimen: negatif 9 kosong dan 342 pendek; positif 11 kosong dan 417 pendek.

---

## Interpretasi Hasil

1. **Stopword removal adalah tahap yang paling memangkas token.** Rata-rata token turun dari 15,88 menjadi 10,90 setelah stopword removal.
2. **Stemming tidak mengubah jumlah token, tetapi menurunkan kosakata.** Kosakata turun dari 11.301 menjadi 9.293 (turun 17,8%) karena bentuk turunan menyatu ke kata dasar.
3. **Normalisasi slang membuat negasi lebih mudah dikenali.** Jumlah ulasan bernegasi justru naik 5,5% karena bentuk implisit seperti `gabisa`, `gada`, dan `gpp` terurai menjadi bentuk eksplisit.
4. **Kosakata akhir menyusut 26,0%.** Penurunan ini lebih kecil daripada pemangkasan token karena stemming hanya mengganti bentuk kata, bukan menghapus token.
5. **Ada trade-off kualitas.** Sebagian ulasan menjadi kosong atau terlalu pendek, sehingga konteks sentimennya hilang. Duplikat baru juga muncul setelah pembersihan.

---

## Keterbatasan

1. Kamus slang hanya mencakup bentuk yang ditemukan di dataset ini; singkatan lain masih bisa lolos.
2. Emoji dihapus, padahal emoji kadang membawa sinyal sentimen yang kuat.
3. Stemming Sastrawi kadang terlalu agresif, misalnya `perhatikan` menjadi `perhati`.
4. Stemmer Sastrawi tidak menangani kata Inggris; sebagian sudah ditambal kamus slang, tetapi kata lain yang belum terdaftar masih bisa lolos.
5. Sebagian ulasan menjadi kosong atau sangat pendek setelah preprocessing.
6. Dataset masih mengandung duplikat dan kemungkinan label noise.
7. Notebook ini berhenti pada preprocessing; belum ada evaluasi model klasifikasi.

---

## Rekomendasi

1. Lengkapi kamus normalisasi dan lakukan validasi manual pada sampel.
2. Pertimbangkan pemetaan emoji ke penanda sentimen sebelum emoji dihapus.
3. Bandingkan pipeline dengan dan tanpa stemming saat melatih model.
4. Tangani ulasan kosong atau <=2 token secara eksplisit.
5. Hapus atau tandai duplikat sebelum pemodelan.
6. Lanjutkan ke TF-IDF dan model baseline seperti Naive Bayes atau regresi logistik untuk mengukur apakah preprocessing ini benar-benar membantu.

---

## Cara Menjalankan Notebook

### 1. Siapkan folder proyek

Letakkan file berikut dalam satu folder:

- `preprocessing_ulasan_ecommerce.ipynb`
- `labeledReview.datasetFix.json`

### 2. Buat dan aktifkan virtual environment (opsional)

```bash
# Windows
python -m venv venv
.\venv\Scripts\activate

# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependensi

```bash
pip install -r requirements.txt
```

### 4. Jalankan Jupyter Notebook / Jupyter Lab

```bash
jupyter notebook preprocessing_ulasan_ecommerce.ipynb
# atau
jupyter lab
```

Jalankan semua cell dengan **Restart & Run All**. Notebook memuat dataset dari file `labeledReview.datasetFix.json` di folder yang sama, sehingga tidak memerlukan koneksi internet.

---

## Lingkungan Eksekusi

Notebook terakhir dieksekusi dengan:

- Python 3.13.3
- pandas 3.0.4
- numpy 2.5.0
- matplotlib 3.11.1
- seaborn 0.13.2
- PySastrawi 1.2.1

Seluruh output, tabel, dan grafik tersimpan di dalam notebook.

---

## Catatan Dataset

- Dataset: `dipawidia/ecommerce-product-reviews-sentiment`
- Jumlah data: 11.606 ulasan
- Kolom: `review`, `sentimen`, `translate`
- Label: `0` = negatif, `1` = positif
- Distribusi: 5.803 negatif dan 5.803 positif
- File dataset **tidak diubah** oleh notebook
