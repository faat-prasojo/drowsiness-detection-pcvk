# Deteksi Kantuk Berdasarkan Citra Wajah

Proyek mata kuliah **Pengolahan Citra dan Visi Komputer (PCVK)** — Program Studi D-IV Teknik Informatika, Jurusan Teknologi Informasi, Politeknik Negeri Malang.

Sistem mendeteksi indikasi kantuk dari video wajah menggunakan **Eye Characteristic Ratio (ECR)** dan **Random Forest**, dengan acuan utama artikel:

> P. S. Lamba dkk., "Detection of Driver Drowsiness Using Adaptive Eye Characteristic Ratio for Enhanced Road Safety," *IEEE Access*, vol. 13, hlm. 84313–84328, 2025, doi: 10.1109/ACCESS.2025.3566981.

## Ringkasan

| Item | Keterangan |
|---|---|
| Dataset | UTA-RLDD (UTA Real-Life Drowsiness Dataset) |
| Data | 180 video, 60 subjek, ±30 jam rekaman |
| Kelas | `alert` (0), `low vigilant` (5), `drowsy` (10) |
| Pembagian data | 5-fold berbasis subjek (12 subjek / 36 video per fold uji) |
| Deteksi wajah | Dlib frontal face detector (HOG + SVM) |
| Landmark | 68 titik wajah (Dlib), 6 titik per mata (indeks 36–41 dan 42–47) |
| Fitur utama | ECR, statistika ECR, kedipan/menit, durasi kedipan, durasi tutup terpanjang, PERCLOS |
| Fitur pembanding | EAR (Eye Aspect Ratio) |
| Classifier | Random Forest (utama), Decision Tree, KNN, Multinomial Naive Bayes |
| Metrik | Accuracy, Precision, Recall, Macro-F1, Confusion Matrix |

## Alur Sistem

```mermaid
flowchart LR
    A[Video UTA-RLDD] --> B[Ekstraksi frame<br/>target ±15 fps]
    B --> C[Grayscale<br/>normalisasi lebar 500 px]
    C --> D[Deteksi wajah<br/>Dlib HOG + SVM]
    D --> E[68 facial landmarks]
    E --> F[6 titik per mata<br/>indeks 36-47]
    F --> G[Perhitungan ECR<br/>per frame]
    G --> H[Ambang adaptif / global]
    H --> I[Fitur temporal<br/>kedipan, PERCLOS, durasi tutup]
    I --> J[Random Forest<br/>3 kelas]
    J --> K[Alert / Low Vigilant / Drowsy]
```

## Rumus ECR

Untuk setiap mata, dengan `p1`–`p6` adalah enam landmark yang mengelilingi kelopak mata:

```
ECR = (|p2 - p5| + |p3 - p6|) / (2 * |p1 - p4|)
```

Nilai akhir ECR adalah rata-rata mata kiri dan kanan. Mata terbuka menghasilkan ECR lebih besar, mata tertutup menurunkan ECR.

## Rencana Tahapan

| Minggu | Fokus | Keluaran |
|---|---|---|
| 1 | Data & persiapan | Unduh UTA-RLDD, catat fps/resolusi/subjek, skema label, pembagian data per subjek, Colab + Dlib, rangkuman jurnal |
| 2 | Landmark & ECR | 68 landmark + overlay, ECR & EAR, grafik ECR per frame, ambang adaptif per subjek, validasi mata terbuka/tertutup |
| 3 | Fitur & model | Deteksi kedipan berbasis durasi, aturan TIRED / DROWSINESS ALERT, ekstraksi fitur ke CSV, pelatihan RF/DT/KNN/Multinomial NB |
| 4 | Evaluasi & laporan | Metrik dan ROC/AUC, ablation & analisis kesalahan, video demo beranotasi, Bab 3–5, slide presentasi |

## Tim

| No | Nama | NIM |
|---|---|---|
| 1 | Abim Mustawa | 244107020078 |
| 2 | Adi Luhung | 244107020088 |
| 3 | Faatihurrizki Prasojo | 244107020142 |

Kelas **TI-3D** — Dosen Pengampu: **Dr. Ulla Delfana Rosiani, ST., MT.**

## Dataset

UTA-RLDD tidak disertakan di repositori ini. Dataset dapat diunduh melalui:

- Situs resmi: <https://sites.google.com/view/utarldd/home>
- Artikel dataset: R. Ghoddoosian, M. Galib, dan V. Athitsos, "A Realistic Dataset and Baseline Temporal Model for Early Drowsiness Detection," *CVPR Workshops*, 2019. <https://arxiv.org/abs/1904.07312>

Setiap subjek memiliki tiga video (satu untuk tiap kelas). Label berasal dari penilaian subjek terhadap kondisi predominant-nya, bukan diagnosis klinis.

## Catatan Metodologis

- Video dari subjek yang sama tidak boleh muncul di data latih dan data uji (subject-wise split) agar tidak terjadi kebocoran data.
- Ambang ECR ditentukan dari data latih atau sesi kalibrasi tersendiri, **bukan** dari video subjek uji.
- Karena frame rate UTA-RLDD bervariasi (<30 fps), durasi kedipan dan penutupan mata dinyatakan dalam **detik**, bukan jumlah frame.
- Label video tidak diperlakukan sebagai label tiap frame; fitur dirangkum pada tingkat video.

## Referensi

1. P. S. Lamba dkk., "Detection of Driver Drowsiness Using Adaptive Eye Characteristic Ratio for Enhanced Road Safety," *IEEE Access*, vol. 13, 2025.
2. R. Ghoddoosian, M. Galib, dan V. Athitsos, "A Realistic Dataset and Baseline Temporal Model for Early Drowsiness Detection," *CVPR Workshops*, 2019.
3. J. P. Immanuel, E. E. Karta, dan R. B. Dika, "Deteksi Kelelahan Mata Berdasarkan Citra Wajah MRL Eye Dataset dengan Menggunakan GLCM, PCA, dan SVM," *JIKSI*, vol. 13, no. 2, 2025.
4. H. Z. Ilmadina, D. Apriliani, dan D. S. Wibowo, "Deteksi Pengendara Mengantuk dengan Kombinasi Haar Cascade Classifier dan Support Vector Machine," *Jurnal Informatika: Jurnal Pengembangan IT*, vol. 7, no. 1, 2022.
5. E. Quiles-Cucarella dkk., "Multi-Index Driver Drowsiness Detection Method Based on Driver's Facial Recognition Using Haar Features and Histograms of Oriented Gradients," *Sensors*, vol. 24, no. 17, 2024.

---

© 2026 Kelompok 2 — TI-3D, Politeknik Negeri Malang.
