# Segmentasi Polip pada Citra Kolonoskopi dengan U-Net

<!--
  TEMPLATE LAPORAN — Tugas P10 Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA.
  Petunjuk lengkap: https://mctm.web.id/ai-bm/tugas

  Cara memakai:
  - Ganti semua teks yang diawali "✍️" dengan tulisan kelompokmu, lalu hapus tanda ✍️-nya.
  - Blok komentar seperti ini adalah petunjuk; tidak tampil di GitHub. Boleh dihapus.
  - Gambar diambil dari folder hasil/ yang dibuat otomatis oleh notebook.
  - Jangan hapus judul-judul bagian (##).
-->

**Tugas P10 — Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA**

## Anggota kelompok

| Nama | NIM | Username GitHub | Kontribusi utama |
|---|---|---|---|
| Muharrahmi | 123430058 |muharrahmi58| ✍️ misal: menjalankan eksperimen, menulis Bagian 3 |
| Rosa Verettina Pasaribu  |123430089| rosaverettina-wq | ✍️ |
| Elfrita Andriyanti Hutabarat | 123430080 | elfrita10 | ✍️ |

## 1. Pertanyaan

Augmentasi apa yang membantu U-Net menemukan polip pada citra kolonoskopi, dan augmentasi apa yang tidak? Kenapa?

## 2. Data & pengaturan

<!-- Isi dari Bagian 2 notebook. Pastikan CEPAT = False untuk hasil akhir. -->

- Dataset: Kvasir-SEG (1000 citra kolonoskopi + mask polip), diperkecil ke 128 × 128 piksel.
- Data uji: 200 citra (tetap). Data latih: 300 citra per ulangan.
- Model: U-Net mini (±0,5 juta parameter), 40 epoch, 5 ulangan per skenario.
- Dijalankan di: Google Colab T4.

![Contoh data](hasil/contoh_data.png)

## 3. Skenario augmentasi & hipotesis

| Skenario | Augmentasi (kode) | Alasan klinis | Hipotesis: Dice naik/turun/sama? |
|---|---|---|---|
| Tanpa augmentasi | — | Pembanding | — |
| ✍️ | ✍️ `v2....` | ✍️ | ✍️ |
| ✍️ | ✍️ `v2....` | ✍️ | ✍️ |

Contoh augmentasi yang dicoba:

![Contoh augmentasi](hasil/augmentasi_flip.png)
<!-- Ganti nama file sesuai skenario kalian (lihat isi folder hasil/). -->

## 4. Hasil

<!-- Tempel isi hasil/ringkasan.md di sini. -->

| Skenario | Dice (rata-rata ± sb) | IoU (rata-rata ± sb) | Dice tiap ulangan |
|---|---|---|---|
| Tanpa augmentasi | 0.662 ± 0.021 | 0.553 ± 0.021 | 0.632, 0.644, 0.669, 0.687, 0.680 |
| Flip | 0.685 ± 0.010 | 0.577 ± 0.006 | 0.683, 0.699, 0.681, 0.669, 0.693 |
| Kecerahan & kontras | 0.631 ± 0.016 | 0.531 ± 0.011 | 0.634, 0.661, 0.615, 0.621, 0.627 |
| Rotasi | 0.697 ± 0.011 | 0.587 ± 0.015 | 0.692, 0.691, 0.691, 0.690, 0.718 |

![Perbandingan Dice](hasil/perbandingan.png)

## 5. Analisis peta kesalahan

![Peta kesalahan](hasil/peta_kesalahan.png)

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->

✍️

## 6. Pembahasan & kesimpulan

Perbandingan Hasil Eksperimen dengan Hipotesis:
Berdasarkan hasil visualisasi dan metrik Dice Coefficient yang diperoleh, berikut adalah analisis perbandingan terhadap hipotesis awal:
1. Hipotesis Augmentasi Geometri (Flip & Rotasi) - Sesuai Hipotesis
   - Hipotesis Awal: Mengingat posisi dan sudut pandang kamera kolonoskop bersifat dinamis, augmentasi Flip dan Rotasi dihipotesiskan dapat meningkatkan performa model U-Net pada polip dengan sudut/orientasi yang tidak biasa.
   - Hasil & Evaluasi: Hipotesis ini terbukti benar. Pada kasus polip di sudut ekstrem (baris 1), model baseline (tanpa augmentasi) mengalami kegagalan total (Dice = 0.00). Namun, penerapan Flip dan Rotasi berhasil memicu deteksi polip secara signifikan dengan nilai Dice mencapai 0.85 (Flip) dan 0.76 (Rotasi).
2. Hipotesis Augmentasi Fotometrik (Kecerahan & Kontras) — Tidak Sesuai Hipotesis
   - Hipotesis Awal: Dihipotesiskan bahwa memvariasikan kecerahan dan kontras warna akan membantu U-Net mengenali polip kecil atau polip yang tersamarkan oleh pencahayaan redup pada mukosa usus.
   - Hasil & Evaluasi: Hipotesis ini kurang terbukti / sebagian salah. 
     - Pada polip berukuran kecil dengan kontras rendah (baris 2), augmentasi kecerahan & kontras hanya memberikan sedikit kenaikan nilai Dice (0.27). 
     - Mengapa Hipotesis Ini Kurang Berhasil? Penyesuaian kecerahan/kontras (ColorJitter) tidak menambah variasi morfologi/bentuk geometri objek. U-Net utamanya mengandalkan batas dan bentuk fitur struktural (kontur) polip. Mengubah intensitas warna saja tidak cukup memberi informasi baru bagi model untuk memisahkan polip dari lipatan dinding usus.
3. Catatan Perbedaan Kecil (Variabilitas Metrik)
   - Pada sampel polip berukuran sedang hingga besar (baris 3 dan 4), perbedaan nilai Dice antara Tanpa Augmentasi (0.98), Rotasi (0.93 - 0.97), dan Kecerahan & Kontras (0.95 - 0.97) tergolong sangat tipis. 
   - Perbedaan margin kecil ini masih berada dalam batas variansi acak (belum tentu signifikan secara statistik), sehingga pada sampel yang mudah/jelas, semua skenario augmentasi memberikan performa yang relatif setara.

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

✍️

## 8. Penggunaan AI

<!-- Wajib diisi, walau tidak memakai AI (tulis "Tidak memakai AI").
     Contoh: "Claude Code (CLI) untuk menulis skenario augmentasi dan memperbaiki error di Bagian 4.
     Draf Bagian 6 ditulis sendiri, lalu dirapikan bahasanya dengan bantuan AI." -->

✍️

## Cara menjalankan ulang

```bash
uv sync
uv run jupyter lab segmentasi_polip.ipynb
```

Atau buka notebook di Google Colab (Runtime → Change runtime type → T4 GPU).

## Referensi

- Jha, D., dkk. (2020). Kvasir-SEG: A Segmented Polyp Dataset. *MultiMedia Modeling (MMM 2020)*, 451–462.
- Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. *MICCAI*. arXiv:1505.04597.
- ✍️ tambahkan referensi lain yang kalian pakai
