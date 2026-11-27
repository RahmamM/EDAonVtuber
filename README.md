# Analisis Eksploratif Hubungan Jumlah Pengguna Unik Live Chat dengan Jumlah Super Chat pada Kanal VTuber di YouTube

Proyek UTS mata kuliah **Data Science**. Analisis mencakup **Maret 2021–Juli 2022**, dengan satu pengamatan untuk setiap kombinasi kanal dan bulan.

## Pertanyaan penelitian

Bagaimana arah dan kekuatan hubungan antara jumlah pengguna unik live chat dan jumlah Super Chat yang tercatat pada kanal VTuber di YouTube dalam bulan yang sama?

## Dataset

Proyek menggunakan **VTuber 1B Elements versi 7** oleh Yasuaki Uechi (2022). [Unduh dataset di Kaggle](https://www.kaggle.com/datasets/uetchy/vtuber-livechat-elements/versions/7) · [Dokumentasi VTuber 1B](https://github.com/sigvt/vtuber-livechat-dataset#dataset-breakdown)

| Berkas | Baris | Isi |
| --- | ---: | --- |
| `chat_stats.csv` | 12.468 | Statistik live chat dari 1.248 kanal, termasuk `uniqueChatters` atau jumlah akun berbeda yang berpartisipasi. |
| `superchat_stats.csv` | 10.942 | Statistik Super Chat, termasuk `superChats` atau jumlah Super Chat yang tercatat. |
| `channels.csv` | 1.358 | Metadata kanal, termasuk afiliasi. |

Tabel live chat dan Super Chat dipasangkan menggunakan `channelId` dan `period`. Metadata kanal ditambahkan menggunakan `channelId`.

## Alur analisis

1. Periksa dimensi, tipe data, keunikan kunci, dan nilai hilang sebelum dan sesudah penggabungan.
2. Ringkas distribusi kedua hitungan dan bandingkan skala asli dengan `log1p`. Siapkan indikator afiliasi, rasio pesan per pengguna, dan target Super Chat bulan kalender berikutnya.
3. Visualisasikan hubungan dengan diagram pencar dan heatmap. Hitung korelasi Spearman secara gabungan dan per bulan, lalu ulangi korelasi gabungan tanpa Maret 2021.
4. Tandai pencilan dengan IQR dan Z-score. Periksa konsistensi hitungan, cocokkan nilai maksimum dengan CSV sumber, dan uji kepekaan korelasi terhadap perlakuan pencilan.

## Temuan utama

Pada **10.941 kanal-bulan dengan kedua hitungan tercatat**, jumlah pengguna unik live chat yang lebih tinggi cenderung disertai lebih banyak Super Chat pada bulan yang sama (**Spearman ρ = 0,723**).

| Pemeriksaan | Hasil |
| --- | --- |
| Korelasi antarkanal pada setiap bulan | Positif di seluruh 17 bulan, dengan ρ antara 0,677 dan 0,789. |
| Korelasi gabungan tanpa Maret 2021 | ρ = 0,725 pada 10.620 pasangan. |
| Median Super Chat menurut kuartil pengguna unik | 27 pada kuartil terendah dan 770 pada kuartil tertinggi. |
| Sensitivitas terhadap penghapusan pencilan IQR | Korelasi tetap positif, dengan ρ = 0,601 pada 8.928 pasangan. |

Sebanyak **1.527 dari 12.468 pengamatan live chat (12,25%)** belum memiliki jumlah Super Chat yang diketahui. Pengamatan ini tetap disimpan dengan nilai Super Chat kosong. Hasil korelasi berlaku untuk pasangan dengan sedikitnya satu Super Chat tercatat. Kanal yang sama dapat muncul pada beberapa bulan, dan cakupan Super Chat pada Maret 2021 bersifat parsial karena pencatatan dimulai pada 16 Maret.

## Berkas proyek

| Berkas | Isi |
| --- | --- |
| [EDA0nvTuber.ipynb](EDA0nvTuber.ipynb) | Notebook gabungan yang memuat pemuatan data, pemeriksaan kualitas, transformasi, visualisasi, korelasi, dan uji sensitivitas pencilan. |
| Laporan EDA (DOCX) | Laporan UTS Bab 1–9 beserta tabel, visualisasi, dan lampiran kode. |

## Cara menjalankan di Google Colab

1. Unduh VTuber 1B Elements versi 7 dan ekstrak tiga CSV pada tabel dataset.
2. Buka `EDA0nvTuber.ipynb` di Google Colab.
3. Buat folder `data/` di direktori kerja Colab, biasanya `/content/data/`, dan unggah ketiga CSV ke sana. Untuk folder lain, sesuaikan `DATA_DIR` pada sel pertama.
4. Pilih **Runtime → Run all**. Grafik tersimpan di `figures/` dan hasil utama dapat dicocokkan dengan tabel di atas.

Untuk Jupyter lokal, buka notebook dari root repo dan simpan CSV di `data/`. Pasang dependensi dengan `python -m pip install jupyterlab numpy pandas "matplotlib>=3.10" scipy scikit-learn`, lalu jalankan seluruh sel.

Tambahkan `/data/` dan `/figures/` ke `.gitignore` untuk menyimpan CSV dan ekspor grafik di komputer lokal. Grafik hasil analisis juga tersedia di output notebook.

## Pengembangan lanjutan

Laporan mengusulkan **regresi binomial negatif** untuk memprediksi jumlah Super Chat pada bulan berikutnya. Setelah cakupan pencatatan dan nilai nol diverifikasi, evaluasi dilakukan berdasarkan urutan waktu. Metrik yang diusulkan adalah **MAE**, dengan jumlah Super Chat bulan berjalan pada kanal yang sama sebagai prediksi acuan.
