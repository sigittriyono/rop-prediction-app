# DrillSense — ROP Prediction

DrillSense adalah aplikasi web untuk memperkirakan **Rate of Penetration (ROP)** pemboran menggunakan model **Random Forest**. Aplikasi menyediakan input parameter pemboran, prediksi ROP, visualisasi rig, interpretasi hasil, serta ringkasan evaluasi model.

- **Demo aplikasi:** https://rop-prediction-app.vercel.app/
- **Repository:** https://github.com/sigittriyono/rop-prediction-app

## Fitur

- Prediksi ROP dari 19 parameter input.
- Bantuan pengisian fitur menggunakan nilai median imputasi dan sample yang tersedia di antarmuka.
- Visualisasi rig pemboran, konteks kedalaman, WOB, Rotary RPM, torque, dan standpipe pressure.
- Kategori hasil prediksi berbasis ambang visual aplikasi.
- Informasi waktu inferensi dan perbandingan terhadap referensi sample.
- Panel analitik yang menampilkan metrik evaluasi, perbandingan model, dan feature importance dari eksperimen.

## Teknologi

- **HTML, CSS, JavaScript** untuk antarmuka web.
- **Random Forest Regressor** untuk prediksi ROP.
- **JSON** untuk menyimpan struktur pohon model dan nilai median imputasi yang digunakan oleh halaman utama.
- **Chart.js** untuk visualisasi grafik analitik.
- **Vercel** untuk deployment aplikasi statis.

Halaman utama, `public/index.html`, memuat model dari `/rop_rf_model.json` dan nilai median dari `/rop_imputation_medians.json`. Prediksi dihitung di browser melalui struktur pohon Random Forest dalam JSON; halaman utama saat ini tidak menggunakan backend API terpisah untuk inferensi.

## Struktur Repository

```text
rop-prediction-app/
├── artifacts/
│   ├── dataset/
│   │   ├── well1.csv
│   │   └── well2.csv
│   ├── rop_final_rf.onnx
│   ├── rop_imputation_medians.json
│   ├── rop_rf_model.json
│   └── sample_data.json
├── notebooks/
│   └── Kasus_11_ROP_BOSQUUUUU!!!.ipynb
├── public/
│   ├── index.html
│   ├── ROP-Prediction.html
│   ├── rop_final_rf.onnx
│   ├── rop_imputation_medians.json
│   ├── rop_rf_model.json
│   └── sample_data.json
└── reports/
    └── Laporan_ROPP.docx
```

### Artefak utama

| File | Keterangan |
|---|---|
| `public/index.html` | Halaman utama aplikasi DrillSense. |
| `public/ROP-Prediction.html` | Halaman aplikasi versi lama yang masih memuat alur inferensi ONNX Runtime Web. |
| `public/rop_rf_model.json` | Struktur model Random Forest yang digunakan halaman utama `index.html` untuk prediksi. |
| `public/rop_imputation_medians.json` | Nilai median yang dapat digunakan untuk membantu melengkapi input fitur. |
| `public/sample_data.json` | Data sample dalam format JSON. |
| `public/rop_final_rf.onnx` | Model ONNX yang digunakan oleh halaman versi lama `ROP-Prediction.html`; tidak dimuat oleh `public/index.html`. |
| `notebooks/` | Notebook analisis, pelatihan, dan evaluasi model. |
| `artifacts/dataset/` | Dataset sumur yang digunakan dalam eksperimen. |
| `reports/` | Dokumen laporan proyek. |

## Menjalankan Secara Lokal

Aplikasi berupa halaman web statis dan mengambil artefak dengan `fetch()`. Karena itu, jalankan menggunakan web server lokal, bukan dengan membuka HTML langsung melalui `file://`.

### Menggunakan Python

Dari direktori root repository, jalankan:

```bash
python -m http.server 8000 --directory public
```

Kemudian buka <http://localhost:8000> di browser.

### Menggunakan server statis lain

Kamu juga bisa menggunakan server statis pilihanmu. Pastikan direktori `public/` menjadi document root agar file HTML dan artefak JSON tersedia pada path yang diharapkan aplikasi. Tidak diperlukan proses build frontend khusus untuk halaman HTML statis ini.

## Cara Menggunakan

1. Buka aplikasi melalui demo online atau server lokal.
2. Isi parameter pemboran secara manual, atau gunakan opsi pengisian nilai/sample yang disediakan pada antarmuka.
3. Pastikan seluruh fitur yang diperlukan telah terisi.
4. Tekan **Jalankan Prediksi**.
5. Tinjau estimasi ROP dalam `ft/hr`, kategori visual, waktu inferensi, dan visualisasi rig.
6. Buka bagian analitik untuk melihat metrik evaluasi dan feature importance yang ditampilkan aplikasi.

## Parameter Input

Halaman utama menggunakan 19 fitur berikut. Pastikan nilai dan satuannya sesuai dengan definisi data yang digunakan saat pelatihan model.

1. Rotary Torque
2. Rotary RPM
3. Standpipe Pressure
4. Weight on Bit (WOB)
5. Hook Load
6. Differential Pressure
7. Inclination
8. Time of Penetration
9. Total Mud Volume
10. Gamma at Bit
11. Bit Depth
12. Bit RPM
13. Line Wear
14. Block Height
15. Min Pressure
16. Min Hook Load
17. Min Torque
18. Min RPM
19. Min WOB

**Catatan:** `Hole Depth` pada visualisasi rig digunakan sebagai konteks visual dan bukan salah satu dari 19 fitur model pada halaman utama.

## Evaluasi Model

Angka di bawah ini adalah metrik yang tertanam pada panel analitik `public/index.html`.

| Model / evaluasi | MAE (ft/hr) | RMSE (ft/hr) | R² |
|---|---:|---:|---:|
| Random Forest — Validation CSV 2 | 1.7791 | 2.4145 | 0.997873 |
| Random Forest — External Test CSV 1 | 1.0252 | 1.4064 | 0.999367 |
| CatBoost — Validation | 2.3479 | 3.7960 | 0.994744 |
| XGBoost — Validation | 2.5089 | 4.3817 | 0.992997 |

Metrik ini merepresentasikan hasil evaluasi yang dicatat pada eksperimen proyek. Hasil tersebut tidak menjamin performa yang sama pada sumur, kondisi geologi, atau kondisi operasi yang berbeda.

## Catatan Interpretasi

Kategori **ROP rendah**, **ROP sedang**, dan **ROP tinggi** ditentukan oleh ambang visual yang digunakan antarmuka. Kategori tersebut membantu membaca keluaran model dan bukan standar universal untuk menilai keselamatan atau optimalitas operasi pemboran. Keputusan operasional perlu mempertimbangkan data lapangan, konteks sumur, prosedur, dan penilaian teknis yang relevan.

Nilai *feature importance* menunjukkan kepentingan relatif fitur menurut model pada eksperimen yang dilaporkan. Nilai tersebut tidak dengan sendirinya membuktikan hubungan sebab-akibat.

## Pengembangan dan Deployment

- Ubah tampilan atau logika antarmuka utama pada `public/index.html`.
- Pastikan `public/rop_rf_model.json` dan `public/rop_imputation_medians.json` tersedia agar pemuatan model dan nilai median dapat dilakukan.
- Uji input manual serta opsi sample menggunakan server lokal sebelum deployment.
- Untuk deployment statis di Vercel, pastikan konfigurasi publikasi menyajikan isi direktori `public/`.

## Tim Pengembang

Proyek ini dikembangkan oleh **Kelompok 2 — Sains Data, UPN “Veteran” Jawa Timur**.

| Nama | NIM/Institusi | Peran | LinkedIn |
|---|---|---|---|
| Muhammad Nabil Putra A. | UPNVJT | Lead Technical Writer | [Profil LinkedIn](https://www.linkedin.com/in/nabil-putra/) |
| Laisal Kaffi | UPNVJT | Model Architect Specialist | [Profil LinkedIn](https://www.linkedin.com/in/laisal-kaffi-3040b1217/) |
| Hafizh Akbar Azzidhani | UPNVJT | Deployment & Edge Specialist | [Profil LinkedIn](https://www.linkedin.com/in/hafizh-akbar-135b27433/) |
| M. C. Raka Anugrah | UPNVJT | Data & Pipeline Specialist | [Profil LinkedIn](https://www.linkedin.com/in/m-c-raka-anugrah/) |
| Sigit Triyono | UPNVJT | Frontend & UX Specialist | [Profil LinkedIn](https://www.linkedin.com/in/sigit-triyono) |

---

DrillSense adalah aplikasi prediksi dan visualisasi berbasis model untuk keperluan analisis dan pembelajaran. Hasilnya merupakan estimasi model dan perlu dipertimbangkan sesuai konteks penggunaannya.
